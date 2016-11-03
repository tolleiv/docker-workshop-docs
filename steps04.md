### Docker networking

 * `docker network ls`
 * `docker network create --driver <driver> <name>`
 * `docker network inspect <name>`
 * `docker run --network=<name> ...`
 
 
#### Drivers

 * bridge (default) - isolated network on a single host
 * overlay - network which spans multiple hosts
 * weave/kuryr/contiv - network plugins which implement multi host networks based on existing implementations

