<!--
$theme: gaia
template: gaia
$size: 16:9
-->
#### Waiting for everyone to show up?

`docker pull alpine`
`docker pull busybox`
`docker pull registry:2`
`docker pull tolleiv/pokesrv`
`docker pull tolleiv/misc:randomquote`
`docker pull tolleiv/misc:randomquotes`
`docker pull tolleiv/misc:elasticsearch_w_hq`
`docker pull tolleiv/misc:es_sampledata`

----
### What you asked for ....

* Usecases
* Benefits
* Daemons & Scripts
* Automation
* App provisioning
* Docker playground
* Monitoring / Logging
* ... all the buzzwords ...

----

### Agenda*

* Local Docker 
* Docker images 
* Dockerfile configuration
* Multi-container usage
	* Docker network
	* Docker-compose

<!-- *footer:* Found a tpyo in the slides? Send a PR to github.com/tolleiv/docker-workshop-docs -->

----
### Not included*

* DevOps Culture
* Deployment (Pipeline) related considerations
* Tooling comparision
* History
* Benchmarks
* Container hardening
* Docker swarm
* Minecraft docker clients
<!-- *footer:* These things are important but not covered here -->

----

### Disclaimer

<.....>

----
## Pt.1 - (Local) Docker

![image](https://docs.docker.com/engine/article-img/architecture.svg)

----

### Docker container lifecycle
   `docker create` creates a container but does not start it.
   `docker rename` allows the container to be renamed.
   `docker run` creates and starts a container in one operation.
   `docker stop` stops a started container
   `docker restart` stops and starts a container.
   `docker pause` pauses a running container, "freezing" it in place.
   `docker unpause` will unpause a running container.
   `docker wait` blocks until running container stops.
   `docker kill` sends a SIGKILL to a running container.
   `docker rm` deletes a (stopped) container.

----
### Run whatever is in the container

   `docker run --rm tolleiv/misc:randomquote`
   
----
### Run a command within a container
  
  `cat a-file.txt | docker run --rm -i alpine wc -l`
  
----
### Run a script with an output stream (1)

  `docker run --rm --name "quotes" tolleiv/misc:randomquotes`
   \+ STRG+C ... ???

----
### Run a script with an output stream (2)

  `docker run --rm --name "quotes" tolleiv/misc:randomquotes`
  \->
  `docker ps`
  `docker kill quotes`
  
----
### Interactive containers w. TTY

   `docker run --rm -t -i --name "quotes" tolleiv/misc:randomquotes`
   \-> STRG+C
   
With a shell:
   
   `docker run --rm -t -i alpine sh`

----
### Attaching to detached containers

  `docker run -d --name "quotes" tolleiv/misc:randomquotes`

  `docker attach quotes`
  CTRL+P CTRL+Q

----
### Docker run flags so far

  * ` -d, --detach` Run container in background and print container ID
  * ` -i, --interactive` Keep STDIN open even if not attached
  * ` -t, --tty` Allocate a pseudo-TTY
  * `--rm` Automatically remove the container when it exits
  * `--name string` Assign a name to the container

----

### Monitoring & Resources

Resource statistics:

   `docker stats`
    ->
    
    CONTAINER           CPU %               MEM USAGE / LIMIT     MEM %               NET I/O               BLOCK I/O           PIDS
    a6793956f07f        0.74%               916 KiB / 1.953 GiB   0.04%               295.3 kB / 162.8 kB   0 B / 0 B           4
    
Health checks
    
    
   `docker run  --health-cmd='xxx' ...`
    
----
#### Resource limitation

   `docker run --name "quotes" -m 100M -d tolleiv/misc:randomquotes`

   `docker stats`

   `docker update -m 300M quotes`

----
#### Configuration

   `docker run --env TEST=works alpine env`

----
#### Ports

   `docker run -d -p hostPort:containerPort <image>`

   `docker run -d -p 8080:8080 tolleiv/pokesrv`
   
----
#### Volumes

   `docker run -d -v /hostDir:/containerDir <image>`
   
   `docker run -it -v $(pwd):/var/host alpine sh`

----
#### Reading *log* output

   `docker logs <container>`

----

#### Commands within running containers

   `docker exec <container> <cmd>`

   `docker run -d --name "quotes" tolleiv/misc:randomquotes`
   `docker exec quotes ps aux`

----
## Exercise

Run two `tolleiv/pokesrv` containers on your system and present your favorite Pokemon on both of them. Check the resource consumption of the containers.

<!-- *footer: List of Pokemon: https://en.wikipedia.org/wiki/List_of_Pok%C3%A9mon -->

----
## Exercise

What are possible ways to change the health state of a container which is started like this:

   `docker run -d --name=sick_boy --health-cmd='test -f /healthy.txt' --health-interval=2s busybox sleep 1d`

Health check can be run with:

   `docker inspect --format='{{.State.Health.Status}}' sick_boy`

----
### What did we learn so far?

* Benefits?
* Drawbacks?
* Problems?

----
## Pt.2 - Docker images

![image](https://docs.docker.com/engine/article-img/architecture.svg)

----
#### Layers of an image

![image](https://nvisium.com/blog/2014/10/15/docker-cache-friend-or-foe/1QndWJyZ7y4Ke9tZw87-uU73nXdKYKuQjMD3XTv3M6PPSvEYL2mBvPHFEO49BLPdcclgFxhM7pDs1E5G39VmRo4vg189grZ-0lz3OkpxpEWjQcWQJ20ixTxu6PUyTo5RjQ)

----
#### Why is this possible?

   ~> copy-on-write storage
   
   provided by???

   `docker info | head -n 10`

    Storage Driver: overlay
      Backing Filesystem: extfs
   
----
#### Storage drivers

![](https://docs.docker.com/engine/userguide/storagedriver/images/driver-pros-cons.png)

----
#### Where do we get our images from?

 * Central registry under [hub.docker.com](hub.docker.com) ~ [Documentation](https://docs.docker.com/docker-hub/)
 * Self hosted Docker registry software
   * [OpenSource Docker registry](https://docs.docker.com/registry/) - [Project on Github](https://github.com/docker/distribution)
   * [Docker Trusted Registry](https://docs.docker.com/docker-trusted-registry/)
   * [Jfrog Artifactory](https://www.jfrog.com/confluence/display/RTF/Docker+Registry)
* SaaS Providers
   * [Google Container Registry](https://cloud.google.com/container-registry/), [CoreOS quai.io](https://quay.io/),  [Amazon AWS ECR](https://aws.amazon.com/de/ecr/)

----
#### Using a registry
 * `REGISTRY=host:port` using a registry without auth
 * `docker login <url>` authenticating with a registry
 * Registry URI should be part of the image name
 * `docker push <image>`
 * `docker pull` ** will usually work without login too

----
#### Running a local registry
 * `docker run -d -p 5000:5000 --name registry registry:2`
   * `open http://localhost:5000/v2/`
* `export REGISTRY=localhost:5000`

----
#### Creating an image for jq (1)

    docker run -it --name jq alpine sh
       apk --update add jq
       exit
    docker commit jq docker-jq

----
#### Creating an image for jq (2)
Cleaning up the container

   `docker rm jq`

Inspecting the image

   `docker history <image>`

Using the image

   `docker run --rm -it docker-jq jq --version`
    
----
#### Using the local repository


  `docker tag docker-jq $REGISTRY/$(whoami)/docker-jq`
  
  `docker push $REGISTRY/$(whoami)/docker-jq`
  `docker rmi docker-jq`
  
  `docker pull $REGISTRY/$(whoami)/docker-jq`

  `docker run --rm -it $REGISTRY/$(whoami)/docker-jq jq --version`

----
#### Image tags structure

![image](https://docs.docker.com/engine/getstarted/tutimg/tagger.png)

 `docker tag <container> name`
 `docker tag <container> example.org/name`
 `docker tag <container> user/name`
 `docker tag <container> name:v1`
 `docker tag <container> name:latest`
 `docker tag <container> example.org/account/name:v2`

----
### Exercise

Build an image which contains Ruby and these gems:

 `puppet`, `puppet-lint`, `puppet-lint-file_line_match-check`, `rubocop`, `rubocop-checkstyle_formatter`, `metadata-json-lint`, `puppetlabs_spec_helper`, `rspec`, `facter`, `rspec-puppet-facts`, `ci_reporter`, `ci_reporter_rspec`, `exi`

Share the image through Artifactory with the others in the course. Use the image which your neightbor built to run puppet-lint and rubocop against the existing Puppet code.

----
### What did we learn so far?

* Benefits?
* Drawbacks?
* Problems?

----
## Pt.3 - Dockerfile configuration

![image](https://docs.docker.com/engine/article-img/architecture.svg)

----
#### Repeating configuration ...?

 * `docker history <image>` tells you all you need! right?
----
#### Dockerfile

   `Dockerfile`

    FROM busybox:ubuntu-14.04
    RUN echo "#!/bin/sh\n" > /test.sh
    RUN echo "echo \"this is version 1\"" >> /test.sh
    ENTRYPOINT ["sh", "/test.sh"]

   `docker build -t me/container:test . `
   `docker build -f /path/to/Dockerfile`

----
##### Instructions (1)

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
* [MAINTAINER](https://docs.docker.com/engine/reference/builder/#maintainer) Set the Author field of the generated images..
* [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime.  NOTE: does not actually make ports accessible.

----
##### Instructions (2)

* [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container.
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.

----
##### Instructions (3)

* [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
* [LABEL](https://docs.docker.com/engine/userguide/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.

----
#### Exercise

Find the smallest denomination of these images

 * Ubuntu - [hub.docker.com/_/ubuntu/](https://hub.docker.com/_/ubuntu/)
 * PHP - [hub.docker.com/_/php/](https://hub.docker.com/_/php/)
 * Golang - [hub.docker.com/_/golang/](https://hub.docker.com/_/golang/)
 * Busybox - [hub.docker.com/_/busybox/](https://hub.docker.com/_/busybox/)

----

#### Exercise

Write a Dockerfile for the puppet-lint image built before. Check with the others in the course how you could reach a low amount of layers `docker history <image> | wc -l`

----
### What did we learn so far?

* Benefits?
* Drawbacks?
* Problems?

----
## Pt.4 - Multi-container usage

![image](https://docs.docker.com/engine/article-img/architecture.svg)

----
#### Networking related commands

 * `docker network ls`
 * `docker network create --driver <driver> <name>`
 * `docker network inspect <name>`
 * `docker run --network=<name> ...`

----
#### Available drivers
 * bridge (default) - isolated network on a single host
 * overlay - network which spans multiple hosts
 * weave/kuryr/contiv - network plugins which implement multi host networks based on existing implementations

----
### Exercise

Create a new bridge network on your host
Bring up three containers based on `tolleiv/misc:elasticsearch_w_hq` within that network
  * Forward port 9200 to your host so you can inspect the cluster state
  * Name the first one to use it for discovery with `--discovery.zen.ping.unicast.hosts=<name_one>`
  * Limit resources of each container
Load some sample data with:
  `docker run --rm -e ES_URL=<name_one>:9200 --network=<nw> tolleiv/misc:es_sampledata`

----
### What did we learn so far?

* Benefits?
* Drawbacks?
* Problems?
----
## Pt.5 - Docker-compose

----
#### Example docker-compose.yml (1)

    web:
      build: ./app
      volumes:
        - "./app:/src/app"
      ports:
        - "80:3000"
      links:
       - redis
       - db
      environment:
        - PGHOST=db
        - PGDATABASE=postgres
        - PGUSER=postgres
    redis:
        image: redis:latest
        ports:
            - "6379:6379"
    db:  
      image: postgres

----
#### Example docker-compose.yml (2)

     dashing:
       build: .
       command: dashing start
       volumes:
         - ./lib:/dashing/lib
         - ./jobs:/dashing/jobs
         - ./spec:/dashing/spec
         - ./widgets:/dashing/widgets
         - ./dashboards:/dashing/dashboards
         - ./assets:/dashing/assets
       ports:
         - "3030:3030"
       env_file:
         - .env

----
#### Usage

 * `docker-compose build`
 * `docker-compose up`
 * `docker-compose down`
 * `docker-compose scale dashing=5`
 * `docker-compose run dashing rspec spec/`

----
### What did we learn so far?

* Benefits?
* Drawbacks?
* Problems?

----
#### Cleaning up

* Cleaning up containers
`docker ps -a -q | xargs docker rm`

* Cleaning up images
`docker images -q | xargs docker rmi`

* Forced cleanup
`docker ps -a -q | xargs docker stop ; docker images -q | xargs docker rmi -f`

<!-- *footer:* defining these as alias in your .bashrc / .zshrc might be useful -->