## Dockerfile

#### CLI commands

  * `docker build .`
  * `docker build -f /path/to/Dockerfile`
  * `docker build -t <image>:<tag> .`

#### Example Dockerfiles

 * [Ubuntu](https://hub.docker.com/_/ubuntu/)
 * [PHP](https://hub.docker.com/_/php/)
 * [Golang](https://hub.docker.com/_/golang/)
 * [Busybox](https://hub.docker.com/_/busybox/)

#### Writing Dockerfiles

[Best practices](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
[Examples](https://docs.docker.com/engine/reference/builder/#dockerfile-examples)

    FROM busybox:ubuntu-14.04
    RUN echo "#!/bin/sh\n" > /test.sh
    RUN echo "echo \"this is version 1\"" >> /test.sh
    ENTRYPOINT ["sh", "/test.sh"]


##### Instructions

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
* [MAINTAINER](https://docs.docker.com/engine/reference/builder/#maintainer) Set the Author field of the generated images..
* [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime.  NOTE: does not actually make ports accessible.
* [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container.
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
* [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
* [LABEL](https://docs.docker.com/engine/userguide/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.


### Exercise

Write a Dockerfile for the image built in the last exercise. Check with the others in the course how you could reach a low amount of layers `docker history <image> | wc -l`



