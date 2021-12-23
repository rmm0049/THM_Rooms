# Playing With Containers
### Premise
Grinch Enterprises has been gloating about their attack on an underground forum. We know they were specifically targeting organizations in a campaign they've themed "Advent of Cyber" (AOC) - what a frustrating coincidence. Tracing the user back over time - we also encountered a reference to using AWS Elastic Container Registry (ECR) to store container images they use as infrastructure in their attacks. Let's see if we can find out more about the attack tooling Grinch Enterprises is using.

### Getting Started
When someone talks about "Docker" or "containers" they are often talking about multiple container technologies that work together. Specifcally, the term "Docker" is used to describe:
- Docker API - a local communication interface on a configured Linux machine, with standardized commands used to communicate with a Docker Daemon
- Docker Daemon - a process that runs on your machine (the Docker dameon), to interact with container components such as images, data volumes, and other container artifacts
- Docker Container Image Format - ultimately a .tar file. For Version 1, the docker image format was not strictly compliant with the OCI Image Specification. For our purposes, this won't change how we interact with the container images in this exercise, but it does slightly change the format and content of a container image.

### Learning Object - AWS ELast Container Registry (ECR)
Covering the basics of container images and AWS Elastic Container Registry - an online registry for public and private container images. We'll learn about how to retrieve a container image from an online registry and inspect the elements of that container image to ientify potential security issues.

### Docker Images and Amazon Elastic Container Registry
In a cloud-native computing environment, containers are a first-choice solution for deploying infrastructure. Similar to virtual machines, containers serve as the compute fabric for many running applications and hosted processes in the cloud.

Can run the following command to see the Docker images on a machine
`docker images`

Docker containers are stored in "repositories", which are a reference to file mappings the Docker daemon knows how to reach, which include the container .tar files. Each image in a repository will include an image tag, and images can be referenced using either their tag or Image ID.

For example:
`remnux/ciphy:latest`
or
`ec11b47184f6`

### Grinch Enterprise Attack Infrastructure
Tracing the grinch enterpries attack infrastructure back to a likely Elastic Container Registry that is publicly accessible:

You can retrieve the potential Grinch Enterprises image by running the following command
`docker pull public.ecr.aws/h0w1j9u3/grinch-aoc:latest`

To the run that container and interact with it:
`docker run -it public.ecr.aws/h0w1j9u3/grinch-aoc:latest`

which will open a shell inside the container image, as indicated by a $. Once inside the container you can do a litter bit of reconnaisance.

doing  `printenv` shows an environment variable called *api_key*

### Bonus Challenge
A container image is composed of a number of layers - perhaps there is sensitive information in an underlying container layer that the Grinch Enterprises didn't cleanup as part of their build process. One key reason developers might package their application in a container is that a container allows a developer to "freeze" an application and its dependencies into an image as part of the build process. The build process is part of the Software Developmental Lifecycle Process (SDLC), where application and their dependencies are packaged together and tested prior to mass distribution.

Once an image is built, running the container image will always result in the same configuration state as specified at build-time. Container images are built from source file known as a `Dockerfile`. These contain a list of step by step instructions that the Docker daemon uses to generate a container image.

We can download the container into a .tar file using
`docker save -o aoc.tar public.ecr.aws/h0w1j9u3/grinch-aoc:latest`

and then extact it using `tar -xvf`

The files extracted represent the various container image layers with the exception of the manifest.json file. This includes the "manifest" of container image layers that compose the final container image we were just inside. You can use `jq` tool to look to 'pretty print' the output of json

