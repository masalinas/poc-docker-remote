# Description
PoC execute docker commands on a remote Docker Host from CLI

## Option 01: Configuring SSH connection

- Send your public key from your host to the server so that it can recognize you automatically.
```shell
ssh-copy-id USER_REMOTE@DOCKER_REMOTE_SERVER
```

- Send your docker commands using ssh. You must validate using the USER_REMOTE_PASSWORD:
```shell
docker -H ssh://USER_REMOTE@DOCKER_REMOTE_SERVER ps
```

## Option 02: configuring TCP connection
- Override the default systemd configuration
```shell
sudo systemctl edit docker.service
```
This will open an editor. Add exactly these lines at the top and save the file:
```shell
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd
```
Verification: The system will save these changes to a new override path (`/etc/systemd/system/docker.service.d/override.conf`) without breaking the original file

- Configure the daemon to listen on TCP
Now you must tell Docker to allow connections both locally and over the network. Open or create the main Docker configuration file:

```shell
sudo nano /etc/docker/daemon.json
```

Add the following JSON block (if the file already has content, add the "hosts" line separated by a comma from previous entries):
```shell
{
  "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"]
}
```

- Reload the system and restart Docker:

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- Send your docker commands using tcp.

```shell
docker -H=<REMOTE_DOCKER_IP:2375> ps
```

> [!CAUTION]
> This option not use any TLS conection between host and remote docker server

## Option 03: usign a custom image
First we must proxy the Docker API to be access on remote mode\

Install this container to offer our docker host API in remote mode:

```shell
docker run --name docker-remote-api -d -p 2375:2375 -v /var/run/docker.sock:/var/run/docker.sock jarkt/docker-remote-api
```

- Send your docker commands using tcp.

```shell
docker -H=<REMOTE_DOCKER_IP:2375> ps
```

## Some links
[Run commands on remote Docker host](https://gist.github.com/kekru/4e6d49b4290a4eebc7b597c07eaf61f2)
