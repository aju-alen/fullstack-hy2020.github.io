---
mainImage: ../../../images/part-12.svg
part: 12
letter: a
lang: en
---

<div class="content">

Software development includes the whole lifecycle from envisioning the software to programming and to releasing the software to the end-users and even maintaining it. This part will introduce containers, a modern tool utilized in the latter parts of the software lifecycle.

Containers encapsulate your application into a single package. This package will then include all of the dependencies with the application. As a result, each container can run isolated from the other containers.

Containers allow the applications within to **only** access the contents of the container and the resources given to that container. More accurately, they are OS-level virtualization. The easiest-to-compare technology is a virtual machine (VM). VMs are used to run multiple operating systems on a single physical machine. They have to run the whole operating system, and a container runs the software using the host operating system. The resulting difference between VMs and containers is that there is little overhead when running containers; they only need to run a single process.

As containers are relatively lightweight, at least compared to virtual machines, they can be quick to scale. And as they isolate the software running inside, it enables the software to run identically almost anywhere. As such, they are the go-to option in any cloud environment or application with more than a handful of users.

Cloud services like AWS, Google Cloud, and Microsoft Azure all support containers in multiple different forms. These include AWS Fargate and Google Cloud Run, both of which run containers as serverless - where the application container does not even need to be running if it is not used. You can also install container runtime on most machines and run containers there yourself - including your personal machine. 

So containers are used in clouds and development. What are the benefits of using one? Here are two relatable and common scenarios:

> Scenario 1: You are developing a new application that needs to run on the same machine as a legacy application. Both require different versions of Node installed.

You can probably use nvm, virtual machines, or dark magic to get them running at the same time. However, containers are an excellent solution as you can run both applications in their respective containers. They are isolated from each other and do not interfere.

> Scenario 2: Your application runs on your machine. You need to move the application to a server.

It is not uncommon that the application just does not run there. It may be due to some missing dependency or other differences in the environments. Here containers are an excellent solution since you can run the application in the same execution environment both on your machine and the server. It is not perfect: different hardware can be an issue, but you can limit the differences between environments.

Sometimes you may hear about the “Works in my container” issue - this is often a usage error.

### About this part ###

In this part, the focus of our attention will not be on the software code. Instead, we are interested in the configuration of the environment in which the software is executed. As a result, the exercises may not contain any coding, the applications are available to you through GitHub and your tasks will include configuring them. The exercises are to be submitted to <i>a single GitHub repository</i> which will include all of the source code and configuration you do during this part.

You will need basic knowledge of Node, Express, and React. Only the core parts, 1 through 5, are required to be completed before this part.

</div>

<div class="tasks">

# Warning

Since we are stepping right outside of our comfort zone as JavaScript developers, this part may require you to take a detour and familiarize yourself with shell / command line / command prompt / terminal before getting started.

If you have only ever used a graphical user interface and never touched e.g. Linux or terminal on Mac, or if you get stuck in the first exercises I recommend doing the Part 1 of "Computing tools for CS studies" first: <https://tkt-lapio.github.io/en/>. Skip the section for "SSH connection" and Exercise 11. Otherwise, it includes everything you are going to need to get started here!

### Exercise 12.1

#### Exercise 12.1: Using a computer (without graphical user interface)

Step 1: Read the text below the Warning header.

Step 2: Run _curl http://helsinki.fi_ and save the output into a file. Submit that file into your repository.

</div>
<div class="content">

### Submitting exercises and earning credits ###

Submit the exercises via the [submissions system](https://studies.cs.helsinki.fi/stats/) just like in the previous parts. Exercises in this part are submitted <i>to a different course instance</i>.

Completing this part on containers will get you 1 credit.

Once you have completed the exercises and want to get the credits, let us know through the exercise submission system that you have completed the course:

![Submitting exercises for credits](../../images/10/23.png)

You can download the certificate for completing this part by clicking one of the flag icons. The flag icon corresponds to the language of the certificate.

### Tools of the trade

The basic tools you are going to need vary between operating systems:

* [WSL 2 terminal](https://docs.microsoft.com/en-us/windows/wsl/install-win10) on Windows
* Terminal on Mac
* Command Line on a Linux

### Installing everything required for this part ###

We will begin by installing the required software. The installation step will be one of the possible obstacles. As we are dealing with OS-level virtualization, the tools will require superuser access on the computer. They will have access to your operating systems kernel.

The material is built around Docker, a set of products that we will use for containerization and the management of containers. Unfortunately, if you can not install Docker you probably can not complete this part.

As the install instructions depend on your operating system, you will have to find the correct install instructions from the link below. Note that they may have multiple different options for your operating system. 


- [Get Docker](https://docs.docker.com/get-docker/)

Now that that headache is hopefully over, let's make sure that our versions match. Yours may have a bit higher numbers than here:

```bash
$ docker -v
Docker version 20.10.5, build 55c4c88
```

### Containers and images

There are two core concepts when starting with containers and they are easy to confuse with one another:

A **container** is a runtime instance of an **image**.

Both of the following statements are true:

- Images include all of the code, dependencies and instructions on how to run the application 
- Containers package software into standardized units 

It is no wonder they are easily mixed up.

To help with the confusion, most everyone uses the word container to describe both. But you can never actually build a container or download one since containers only exist during runtime. Images, on the other hand, are **immutable** files. As a result of the immutability, you can not edit them after you create them. However, you can use existing images to create a new image by adding new layers on top of the existing ones.

Cooking metaphor:

* Image is pre-cooked, frozen treat.
* Container is the delicious treat.

[Docker](https://www.docker.com/) is the most popular containerization technology and pioneered the standard most others use now. In actuality, Docker is a set of products that help us manage images and containers. They will enable us to leverage all of the benefits of containers. The docker engine will take care of turning the immutable files called images into containers.

For managing the docker containers, there is also [Docker Compose](https://docs.docker.com/compose/). We can use it to **orchestrate** (control) multiple containers at the same time. We will use Docker Compose to set up complex local development environments quickly. In the final version of our Node development environment, even Node isn't a requirement anymore.

There are several concepts we need to go over. But we will skip those for now and learn about Docker first! One of my favourite features is the capability to handle the command for running containers, even if the image is not downloaded on our device yet.

The command structure is the following: _container run <i>IMAGE-NAME</i>_. So we will tell Docker to create a container from an image.

```bash
§ docker container run hello-world
```

There will be a lot of output, but I will split it into multiple sections, which we can decipher together. The lines are numbered by me so that it is easier to follow the explanation. Your output will not have the numbers.

```bash
1. Unable to find image 'hello-world:latest' locally
2. latest: Pulling from library/hello-world
3. b8dfde127a29: Pull complete
4. Digest: sha256:5122f6204b6a3596e048758cabba3c46b1c937a46b5be6225b835d091b90e46c
5. Status: Downloaded newer image for hello-world:latest
```

It has downloaded a new image for hello-world from a place called Docker Hub. Docker Hub is a free registry. And a registry is a place to store images. You can see the Docker Hub page for the image with your browser here: [https://hub.docker.com/_/hello-world](https://hub.docker.com/_/hello-world)

The first part of the message states that we did not have the image "hello-world:latest" yet. This reveals a bit of detail about images themselves; image names consist of multiple parts, kind of like an URL. An image name is in the following format: 

- _registry/organisation/image:tag_

In this case the 3 missing fields defaulted to: 
- _index.docker.io/library/hello-world:latest_

The second row shows the organisation name, "library" where it will get the image. In the Docker Hub url, the "library" is shortened to _.

The 3rd and 5th rows only show the status. But the 4th row may be interesting: each image has a unique digest based on the layers. The digest is used by Docker to identify that an image is the same. This is done when you try to pull the same image again.

So it did some pulling and then output information about the **image**. After that the status told us that a new version of hello-world:latest was indeed downloaded. You can try pulling the image with _docker image pull hello-world_ and see what happens.

The following output was from the container itself. It also explains what happened when we ran _docker container run hello-world_.

```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker container run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

The output contains a few new things for us to learn. Docker daemon is a background service that makes sure the containers are running, and we use the docker client to interact with the daemon. We now have interacted with the first image and created a container from the image. During the execution of that container, we received the output.

</div>

<div class="tasks">

### Exercise 12.2

Some of these exercises do not generate anything for you to submit.

Instead, use [script](https://man7.org/linux/man-pages/man1/script.1.html) to record commands you have used; try it yourself with _script_ to start recording, _echo "hello"_ to generate some output, and _exit_ to stop recording. It saves your actions into a file names "typescript".

If _script_ does not work, you can just copy-paste all commands you used into a text file.

#### Exercise 12.2: Running your second container

> Use _script_ to record what you do, save the generated file into the repository as your answer.

The hello-world output gave us an ambitious task to do. Do the following

Step 1. Run an Ubuntu container with the command given by hello-world

The step 1 will connect you straight into the container with bash. You will have access to all of the files and tools inside of the container.

Step 2. Create directory `/usr/src/app`

Step 3. Create a file `/usr/src/app/index.js`

Step 4. Run `exit` to quit from the container

Google should be able to help you with creating directories and files.

</div>

<div class="content">

### Ubuntu image

The command you just used to run the ubuntu container, _docker container run -it ubuntu bash_, contains a few additions to our hello-world. Let's see the --help to get a better understanding. I'll cut some of the output so we can focus on the relevant parts.

```bash
$ docker container run --help

Usage:  docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
Run a command in a new container

Options:
  ...
  -i, --interactive                    Keep STDIN open even if not attached
  -t, --tty                            Allocate a pseudo-TTY
  ...
```

The two options, or flags, in _-it_ make sure we can interact with the container. And after the image we used was ubuntu. Then we have the command to be executed inside the container when we start it. You can try other commands that the ubuntu image might be able to execute. As an example try _docker container run --rm ubuntu ls_. The _ls_ command will list all of the files in the directory and _--rm_ flag will remove the container after execution. Normally containers are not deleted automatically.

Let's continue with our first ubuntu container with the **index.js** file inside of it. It has stopped running since we exited it. We can list all of the containers with _container ls -a_, the _-a_ (or --all) will list containers that have already been exited.

```bash
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                       PORTS     NAMES
b8548b9faec3   ubuntu    "bash"    3 minutes ago    Exited (0) 6 seconds ago               hopeful_clarke
```

We have two options when addressing a container. The identifier in the first column can be used to interact with the container almost always. Plus most commands accept the container name as a more human-friendly method of working with them. The name of the container was automatically generated to be **"hopeful_clarke"** in my case.

The container has already exited, yet we can start it again with the start command that will accept the id or name of the container as a parameter: _start <i>CONTAINER-ID-OR-CONTAINER-NAME</i>_.

```bash
$ docker start hopeful_clarke
root@b8548b9faec3:/#
```

The start command will start the same container we had previously. Unfortunately, we forgot to start it with the flag _--interactive_ so we can not interact with it. Let's kill it with the _kill <i>CONTAINER-ID-OR-CONTAINER-NAME</i>_ command and try again. Killing sends a SIGKILL signal to the process forcing it to exit. We could have used the safer SIGTERM version, _stop <i>CONTAINER-ID-OR-CONTAINER-NAME</i>_, instead if we wanted to.

```bash
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                      PORTS     NAMES
b8548b9faec3   ubuntu    "bash"    13 minutes ago   Up 47 seconds                         hopeful_clarke

$ docker kill hopeful_clarke
hopeful_clarke

$ docker start -i hopeful_clarke
root@b8548b9faec3:/#
```

Let's edit the index.js and add something to execute. We are just missing the tools to edit the file. Nano will be a good text editor for now. I found the install instructions from Google. We will omit using sudo since we are already root.

```
root@b8548b9faec3:/# apt-get update
...

root@b8548b9faec3:/# apt-get -y install nano
...

root@b8548b9faec3:/# nano /usr/src/app/index.js
```

Now we have nano installed and can start editing files!
</div>

<div class="tasks">

### Exercise 12.3 - 12.4

#### Exercise 12.3: Ubuntu 101

> Use _script_ to record what you do, save the generated file into the repository as your answer.

Edit the _/usr/src/app/index.js_ file inside the container with the now installed nano and add the following line

```javascript
console.log('Hello World')
```

If you are not familiar with Nano you can ask for help in the chat or Google.

#### Exercise 12.4: Ubuntu 102

> Use _script_ to record what you do, save the generated file into the repository as your answer.

Install Node while inside the container and run the index file with _node /usr/src/app/index.js_ in the container.

The instructions for installing Node are sometimes hard to find, so here is something you can copy-paste:

```bash
curl -sL https://deb.nodesource.com/setup_16.x | bash
apt install -y nodejs
```

You will need to install the _curl_ into the container. It is installed in the same way as you did with _nano_.

</div>

<div class="content">

### Other docker commands

Now that we have Node installed in the container we can execute _Node /usr/src/app/index.js_ in the container! Let's create a new image from the container. The _commit <i>CONTAINER-ID-OR-CONTAINER-NAME</i> <i>NEW-IMAGE-NAME</i>_ will create a new image that includes the changes we have made. You can use _container diff_ to check for the changes between the original image and container before doing so.

```console
$ docker commit hopeful_clarke hello-node-world
```

You can list your images with _image ls_:
 
```console
$ docker-fs docker image ls
REPOSITORY                                      TAG         IMAGE ID       CREATED         SIZE
hello-node-world                                latest      eef776183732   9 minutes ago   252MB
ubuntu                                          latest      1318b700e415   2 weeks ago     72.8MB
hello-world                                     latest      d1165f221234   5 months ago    13.3kB
``` 
 
You can now run the new image as follows:
 
```console
docker run -it hello-node-world bash
root@4d1b322e1aff:/# node /usr/src/app/index.js
```

There are multiple ways to achieve the same conclusion. Let's go through a better solution. We will clean the slate with _container rm_ to remove the old container.

```bash
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                       PORTS     NAMES
b8548b9faec3   ubuntu    "bash"    31 minutes ago   Exited (0) 9 seconds ago               hopeful_clarke

$ docker container rm hopeful_clarke
hopeful_clarke
```

Create the index.js file and write _console.log('Hello, World')_ inside it. No need for containers yet.

Next, let's skip installing Node altogether. Since Docker images are found in Docker Hub we can use this: [https://hub.docker.com/_/Node](https://hub.docker.com/_/Node). That image has Node already installed, and we only need to pick a version.

By the way, the _container run_ accepts _--name_ flag that we can use to give a name for the container.

```bash
$ docker container run -it --name hello-node node:16 bash
```

While we are inside the container on this terminal, open another terminal and use the _container cp_ command to copy file from your own machine to the container.

```bash
$ docker cp ./index.js hello-node:/usr/src/app/index.js
```

And now we can run _node /usr/src/app/index.js_ in the container. We can commit this as another new image, but there is an even better solution. The next page will be all about building your images like a pro.

</div>