# Cheat Sheet for Docker
This is a cheat sheet for docker. It is a collection of useful commands and tips for docker. 

## Basics 
To run docker container in interactive mode:
```bash
docker run -it <image_name> bash
```
To run docker container in detached mode (in background):
```bash
docker run -d <image_name>
```
Other useful flags:
* `--rm` - remove container after it stops
* `--name` - set name for container
* `-p <machine port>:<container port>` - expose port from container to host machine's localhost
* `-P` - exposes all container ports to a random port on the host machine
* `-e NAME="VALUE"` - set environment variable

To see ports that are exposed by container:
```bash
docker port <container_name>
```

To remove container we may use `docker rm` command:
```bash
docker rm <container_name>
```
