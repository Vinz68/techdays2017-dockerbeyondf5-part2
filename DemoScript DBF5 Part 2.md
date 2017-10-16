# Docker Beyond F5 - Part 2 - Deep Dive #

## Session Details ##
Session by
*  [Alex Thissen](https://twitter.com/alexthissen) 
* [Rene van Osnabrugge](https://twitter.com/renevo)

Git Repo can be found here
[Github Repo Techdays 2017 (alexthissen)](https://github.com/alexthissen/techdays2017)

## Abstract ##
In the first part of the session we saw why containerized delivery is important and how to create an application within a container and move it to a cluster in an automated way. In this session we will dive under the hood and take you on this learning journey to unravel the underlying technology and find out how and why things work! 

## Demo 1 - Building and Running Docker Images ##

This demo is all about understanding how Visual Studio works with the Docker files. 

First we show on the command line that we do not have any images running with relation to the things we are showing. 

```
docker ps
docker images
docker ps -a
```

Note the images that are not there yet. Some base images are

Open and show VS2017 solution and run the solution. Explain the Add Docker Support button and what it adds. Make sure the startup project is on the docker-compose project.

Open the UI and show the webapp. Open the Swagger UI of the REST API and aa a score. `LX360 42`

After that, run the docker commands again on the command line and show that there are 2 images created with the `dev` tag.

Then talk about how Visual Studio uuses the special project and the generated Docker Files within the projects to create the containers.

Talk quickly through the DockerFile and mention the layering. 

Now that we know how the Docker file works, let's try to build and run the container from the command line. 
```
docker build .
```
Errors out because of missing folder. Explain the arg source construction. Show the obj/Docker/Publish directory and explain that Visual Studio sets the Source variable after building the solution in Release Build.

```
dotnet restore
dotnet publish -o bin/Debug/publish
docker build -t techdays2017/live-demo --build-arg source:bin/Debug/publish .
```
Watch casing of Debug as we are building in a Linux container. 

Error again. Open .dockerignore file to include !bin/Debug

Rerun `docker build -t techdays2017/live-demo  --build-arg source:bin/Debug/publish` .

Image is created and able to run separately. Run the image
```
docker run -it -p 5000:80 techdays2017/live-demo
```

## Demo 2 - Debugging ##
Back in Visual Studio Enable the breakpoints and run the solution again. By jumping through the breakpoint explain that you jump within containers. Show the environment name of the WebApp to see that it is running on a Linux machine.

Open `Index.cshtml` and change it so it shows the Techdays Picture. Do this while the containers are still running. After refresh the picture shows up...

*But containers are immutable? huh?*

* Open command line and do a `docker ps` to get the containerid of the GaminWebApp 
* with this id do a docker inspect and go to volume mappings. Show that there are multiple volumes mapped in this container. Explain how this works
* run `docker exec -it <container> bash`
* List files 
* Add a file on own file system and show how it pops up in the container
* exit the container.. *why does it not stop*
* Inspect container again and show volume mappings and talk about how debugging works in the container

# Demo 3 #
TO start the composition demo we first going to build the solution in Release Mode. After that we show that the directory obj/Docker/Publish is now filled with files. The docker files are ready to work.

* Open the Docker Compose file and talk through what you see and what the purpose is for this file. Normally you can use this to start up your composition. 
* Open the command line and run docker-compose up
```
docker-compose -f docker-compose.yml up
```
* Show that it fails and explain that not everything is started because of different environment settings
* Open the docker-compose.overrides file and explain what it does

* Open the docker-compose.production file and show it is different.
* open the debug files 

# Demo 4 - Deploying to registry #
go to the command line and try to build the docker-compose file. It works but why? Because the docker publish directories sre there.. So what if they were not?

Try again and show it fails. Show the docker-compose.ci.build.yml file and tell how it works

* Build the app again with this container and the docker-compose-production.yml

Then it is time to push to the registry. Explain why the registry is important (show slide)

* Open VSTS and show the pipeline
