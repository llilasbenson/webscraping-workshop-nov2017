
## 1. Getting Started: Running Docker

Docker is an application that makes it possible to run a virtual copy of the Linux operating system within your primary OS. We will be using Ubuntu, a version of Linux that is often used to run web servers. Ordinarily, you would launch an Ubuntu server and then install the programs you need, one by one; Docker lets us speed up that process by defining our system's initial configuration in a plain text file, known as a Dockerfile. You can view the Dockerfile we are currently using [here](https://hub.docker.com/r/llilasbenson/webscraping-workshop-nov2017/).

For more details on how Docker works, see this [overview](https://docs.docker.com/engine/docker-overview/).

### How to run Docker on the PCL Data Lab Macs

1. Open a new terminal window.

2. Enter the following command in the terminal window to download the Docker image files we'll be using. This could take several minutes.


```
docker pull llilasbenson/webscraping-workshop-nov2017
```

When the download is complete, enter the following command and hit 'enter' twice to run the container. This will create a new directory called `sharedfolder` on your desktop.


```
docker run --name llilasbenson_ubuntu -ti -p 8889:8889 --volume ~/Desktop/sharedfolder/:/sharedfolder/ llilasbenson/webscraping-workshop-nov2017
```

The command above includes several options:
- The `--name` flag sets the name of our container as `llilasbenson_ubuntu`, while `-ti` tells Docker that we want to use an interactive terminal.
- `-p` maps port 8889 in our container to port 8889 in our local OS (more on this later).
- The `--volume` option defines a "shared volume" between the container and our local machine, a directory called `sharedfolder`.
- `llilasbenson/webscraping-workshop-nov2017` identifies the image we want to download, which is hosted on the Docker Hub website.

### Running Docker on your Mac

Open Terminal in macOS and launch the workshop Docker container:


```
docker rm -f llilasbenson_ubuntu
docker pull llilasbenson/webscraping-workshop-nov2017
docker run --name llilasbenson_ubuntu -ti -p 8889:8889 --volume ~/Desktop/sharedfolder/:/sharedfolder/ llilasbenson/webscraping-workshop-nov2017
```

### Running Docker on your PC

In Windows 10, open PowerShell and enter the following to launch the Docker container. Remember to insert your username where indicated in the file path.


```
docker rm -f llilasbenson_ubuntu
docker pull llilasbenson/webscraping-workshop-nov2017
docker run --name llilasbenson_ubuntu -ti -p 8889:8889 --volume C:\Users\***username_here***\Desktop\sharedfolder:/sharedfolder/ llilasbenson/webscraping-workshop-nov2017
```

## 2. Launching Jupyter

To launch a Jupyter notebook, open any browser and type :


```
localhost:8889
```

Click the "New" in the upper right, then choose "Python 3" from the drop-down menu.

![] (images/image-1.png)

You’re now in the Jupyter environment. Here, you can create a series of "cells" for individual chunks of code, which can be saved and run repeatedly. Click the ✚ icon on the top left to add a new cell.

Type a line of code that prints a string. To run the current cell, either click the `►❙` icon or go to the "Cell" menu and choose "Run Cells."


```
print("Hello Jupyter!")
```


![] (images/image-2.png)

Note that each cell’s output is displayed right below to the the code that produced it, which is a major benefit of working in Jupyter.
