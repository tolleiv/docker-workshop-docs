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

![image](https://www.docker.com/sites/default/files/WhatIsDocker_1_kernal-2_1.png)

---
    docker run -it --name jq alpine sh
      apk --update add jq
      ex
      exit
    docker commit jq docker-jq
    docker rm jq
    docker run --rm -it docker-jq jq --version

  * `docker tag docker-jq $REGISTRY/$(whoami)/docker-jq`
  * `docker push $REGISTRY/$(whoami)/docker-jq`
  * `docker rmi docker-jq`
  * `docker pull $REGISTRY/$(whoami)/docker-jq`
  * `docker run --rm -it $REGISTRY/$(whoami)/docker-jq jq --version`
  
 ---
  
![image](https://docs.docker.com/engine/getstarted/tutimg/tagger.png)
 