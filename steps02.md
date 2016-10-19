## Docker registry

 * Central registry under [hub.docker.com](hub.docker.com) ~ [Documentation](https://docs.docker.com/docker-hub/)
 * Self hosted Docker registry software
   * [OpenSource Docker registry](https://docs.docker.com/registry/) - [Project on Github](https://github.com/docker/distribution)
   * [Docker Trusted Registry](https://docs.docker.com/docker-trusted-registry/)
   * [Jfrog Artifactory](https://www.jfrog.com/confluence/display/RTF/Docker+Registry)
* SaaS Providers
   * [Google Container Registry](https://cloud.google.com/container-registry/)
   * [CoreOS quai.io](https://quay.io/)
   * [Amazon AWS ECR](https://aws.amazon.com/de/ecr/)

#### Using a registry
 * `REGISTRY=host:port` using a registry without auth
 * `docker login <url>` authenticating with a registry
 * Registry URI should be part of the image name
 * `docker push <image>`
 * `docker pull` ** will usually work without login too

#### Running a local registry
 * `docker run -d -p 5000:5000 --name registry registry:2`
   * `open http://localhost:5000/v2/`
* `export REGISTRY=localhost:5000`

#### Creating an image

    docker run -it --name git alpine sh
      apk --update add git
      git version
      exit
    docker commit git docker-git
    docker rm git
    docker run --rm -it docker-git git version
    docker rmi docker-git

 Then tag your image under the registry namespace and push it there:

  * `docker tag docker-git $REGISTRY/$(whoami)/docker-git`
  * `docker push $REGISTRY/$(whoami)/docker-git`
  * `docker rmi docker-git`
  * `docker pull $REGISTRY/$(whoami)/hello-world`
 