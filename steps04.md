### Docker networking

 * `docker network ls`
 * `docker network create --driver <driver> <name>`
 * `docker network inspect <name>`
 * `docker run --network=<name> ...`
 
 
#### Drivers

 * bridge (default) - isolated network on a single host
 * overlay - network which spans multiple hosts
 * weave/kuryr/contiv - network plugins which implement multi host networks based on existing implementations

### Exercise

* Create a new bridge network on your host
* Bring up three containers based on "tolleiv/elaticsearch_w_hq" within that network
  * Make sure one of them forwards port 9200 to your host so you can inspect the cluster state
  * Make sure one of them is named so the others can use it for discovery with `--discovery.zen.ping.unicast.hosts=<name_one>`
  * Limit resources of each container
* Load some sample data with:
  * `docker run --rm -e ES_URL=<name_one>:9200 --network=<nw> tolleiv/es_sampledata`

### Docker Swarm

TBD ... https://docs.docker.com/engine/swarm/

### Exercise

* Create a new overlay network access all participant hosts
* Every participant should start "tolleiv/elaticsearch_w_hq" container
* Import sample data and inspect the cluster state