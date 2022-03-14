# Description
PoC execute docker commands on a remote Docker Host from CLI

# Create a remote docker host
First we must proxy the Docker API to be access on remote mode

Install this container to offer our docker host API in remote mode:

```shell
docker run --name docker-remote-api -d -p 2375:2375 -v /var/run/docker.sock:/var/run/docker.sock jarkt/docker-remote-api
```

# Execute any command from CLI to Remote host
Execute any docker command. For example see all containers running in our remote docker host:

```shell
docker -H=<REMOTE_DOCKER_IP:2375> ps
```

# Some reference links
[Run commands on remote Docker host](https://gist.github.com/kekru/4e6d49b4290a4eebc7b597c07eaf61f2)
