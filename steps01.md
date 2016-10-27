## Docker command line

### What?

![image](https://docs.docker.com/engine/article-img/architecture.svg)

### Docker container lifecycle
  * `docker create` creates a container but does not start it.
  * `docker rename` allows the container to be renamed.
  * `docker run` creates and starts a container in one operation.
  * `docker stop` stops a started container
  * `docker restart` stops and starts a container.
  * `docker pause` pauses a running container, "freezing" it in place.
  * `docker unpause` will unpause a running container.
  * `docker wait` blocks until running container stops.
  * `docker kill` sends a SIGKILL to a running container.
  * `docker rm` deletes a (stopped) container.
  * `docker update` updates a container's resource limits.

### CLI jobs / scripts
#### Run a simple script
  * `docker run --rm <image>`
  * `docker run --rm tolleiv/randomquote`

#### Run a script which requires input
  * `cat file.txt | docker run --rm -i <image> <cmd>`
  * `cat LICENSE | docker run --rm -i alpine wc -l`
  * `cat puppet.pp | docker run --rm -i tolleiv/puppet-lint rubocop` *** TBD

#### Run a detached script / daemon
  * `docker run --rm -d <image>`
  * `docker run --rm --name "quotes" -d tolleiv/randomquotes`
  * `docker ps`
  * `docker attach <id>`
  * `docker attach quotes`

### Monitoring
  * `docker stats`
  * `docker run  --health-cmd='xxx' ...`

#### Resource limitation
  * `docker run --name "quotes" -m 100M -d tolleiv/randomquotes`

### Configuration
 * `docker run --env TEST=works <image>`
 * `docker run --env TEST=works alpine env`

### Ports
  * `docker run -d -p hostPort:containerPort <image>`
  * `docker run -d -p 8080:8080 tolleiv/pokesrv`
  * `docker run -d -e POKEMON=pichu -p 8080:8080 tolleiv/pokesrv`

### Volumes
  * `docker run -d -v /hostDir:/containerDir <image>`
  * `docker run -it -v $(pwd):/var/host alpine sh`


### Commands within running containers
 * `docker exec <container> <cmd>`
 * `docker exec <container> bash`

## Exercise

Run two `tolleiv/pokesrv` containers on your system and present your favorite Pokemon on both of them. Check the resource consumption of the containers.

## Exercise

What are possible ways to change the health state of a container which is started like this:

    docker run -d --name=sick_boy --health-cmd='test -f /healthy.txt' --health-interval=2s busybox sleep 1d

Health check can be run with:

    docker inspect --format='{{.State.Health.Status}}' sick_boy

