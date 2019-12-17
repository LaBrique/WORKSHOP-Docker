# WORKSHOP-Docker
Learn the basics of Docker!

In this workshop, you will learn how Docker works, and how to build & run apps with it.  

## Pre-requisites
Have Docker installed on your machine.  
`$sudo apt install docker` should work for Ubuntu.

Add yourself to the 'docker' group.  
This is not necessary, but without doing this, you will have to *sudo* **EVERY** docker command you run.

Create the docker group.  
`$ sudo groupadd docker`

Add your user to the docker group.  
`$ sudo usermod -aG docker $USER`



Have a build-ready application.  
For the sake of simplicity, we will use [this one](https://github.com/LaBrique/WORKSHOP-Discord.js) for this workshop.
  
## Let's get started!

### First things first

Let's explore the *basics* of Docker.

What is Docker?  
Docker is a container platform, that can build and start apps inside of "containers". Those *containers* are midway between your regular app and a VM. It builds and run your app in a closed environment, with no other access to the host machine than the port it exposes.  
Also, it does not require an entire OS to run, like a VM would. Instead, it uses the minimal environment to run, making it way lighter than a VM.

There are 2 main concepts to grasp in order to begin using Docker:
#### Images:
Images are the pictural *image* of an app, that contains all the files needed for your app.
#### Containers:
Containers are the *instantiation* of an Image, it is what keeps your app up and alive.

Let's start by getting and Image.  
Run `$docker pull labrique/workshop:bot` to pull an Image that I gracefully made myself for this workshop.  
By running `$docker images` you can see a list of all the Images you have on your machine.

You can now run `$docker run --name "bot" labrique/workshop:bot` to start a container using this image.  
This container runs a Discord bot of mine, that you can add to your own server using [this link](https://discordapp.com/api/oauth2/authorize?client_id=567716464422027264&permissions=3072&scope=bot), or just interact with it on my [Workshop Discord Server](https://discord.gg/MUaPYxX).  
It will answer to "&ping", try it yourself! (Please note that several students may be running it at once, and thus cause the bot to answer several times to a single "&ping").

You can try and run `$docker ps` to see the status of your containers. Add `-a` to see *all* containers, even stopped ones.  
When you are done having fun with the sample bot, simply run `docker rm -f bot` to stop the container and delete it.


Now you know how to pull and run Images, but isn't it funnier to make your own?

### Dockerfile

In order to build an Image, you will need a *Dockerfile*.

Get your app:  
You can use the aforementioned app, download it and navigate to the app's root.  
This app uses node.js, but fear not! There is no need to install it, *Docker does not need it to make it work*.  
Don't forget to fill the `config.json` needed by the app.  
At the root of your app, create a file named *exactly* "Dockerfile".  
This file will allow us to tell docker how our Image should be built.

In a Dockerfile, you use instructions to build your image, step by step.  
The first step here will be to specify the base Image we will be using. For example, the Image `node:latest` is an Image which bears the bare minimum to run a node.js app.  
To do this, we will write:  
`FROM node:latest` as the first line of our Dockerfile.

When building an Image with a Dockerfile, the directory it is placed in is called the "Context", which means the Dockerfile can access those, and only those files, but it does *not* mean that they are directly copied to your image.  
To import our app into our Image, we will use the `COPY` instruction, which works as following: `COPY <src> <dest>`, `<src>` being the file or directory you wish to import (that can be `.`), and `<dest>` being the target file/directory *inside* the Image.  
Knowing this, import the necessary files to the directory `/app`, for ease of use.  
Use `WORKDIR /app` to move to the newly created directory.

Now that our files are imported, we need to build the app, using `npm`.  
To build our app with a Dockerfile, we will use the instruction `RUN`, which works as following: `RUN [<cmd>, <arg1>, <arg2>, ...]`, `<cmd>` being the command you wish to run, and `<arg>` being the args to your command.  
Thanks to `npm`'s simplicity, all we have to run is `npm install`.

And the last step to build our Image is to tell docker how should our container start.  
When the container starts, it executes a command, known as the *entrypoint*.  
Here we want to start the bot, so we'll use `node .`.  
The instruction is `CMD`, and has the same syntax as `RUN`.

If you could configure your Dockerfile properly, try and run `$docker build . -t bot`. `-t bot` will *tag* your Image with the name `bot`, instead of an unreadable hex string, which is quite useful to remember which Image does what.
If your build is a succes, you should be able to see your `bot` Image when running `$docker images`.

Now just run `$docker run --name "bot" bot` to start your app!
