---
title: Swarm Setup
summary: Docker swarm setup procedure
authors:
  - Idar Bergli
date: 2021-04-22
---

All tough an offical installation can be found at the [docker](https://docs.docker.com/engine/install/ubuntu/) page. We will describe it shortly here what to do.

Start by installing needed packages if theyr are not allready installed:

```
sudo apt-get update
```

```
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
```

Add the official GPG key from docker:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Then do the following to add the stable repository from arm64:

```
echo "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Then install docker by doing:

```
sudo apt-get update
```

```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

## Configure Swarm

So we will now start to setup the swarm cluster where we will use `swarm1` and `swarm2` as managers. So lets start with `swarm1`:

```
sudo docker swarm init
```

```
Swarm initialized: current node (y58ktz3t50hx9bf3vkfrllslp) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3wtznqt1vd037sgq4qz2tgrmrmg7na7qfxn6lqay7bc5ud0omp-9zh7iba7zitvb2q78o10m3nw0 192.168.10.158:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

As seen above we get an commando we can use to add worker nodes to the cluster, but we will firstly start by adding another manager and will use the command mention under the worker commando.

```
sudo docker swarm join-token manager
```

```
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3wtznqt1vd037sgq4qz2tgrmrmg7na7qfxn6lqay7bc5ud0omp-33hoq3y8yvzob61y8c30t2goi 192.168.10.158:2377
```

We do as commanded and run that command on the `swarm2` Pi.

```
sudo docker swarm join --token SWMTKN-1-3wtznqt1vd037sgq4qz2tgrmrmg7na7qfxn6lqay7bc5ud0omp-33hoq3y8yvzob61y8c30t2goi 192.168.10.158:2377
```

```
This node joined a swarm as a manager.
```

As a final joine we will add the last pi as a worker by using the command given when first initializing the swarm:

```
sudo docker swarm join --token SWMTKN-1-3wtznqt1vd037sgq4qz2tgrmrmg7na7qfxn6lqay7bc5ud0omp-9zh7iba7zitvb2q78o10m3nw0 192.168.10.158:2377
```

```
This node joined a swarm as a worker.
```

### Add Workers

If at a later time we would like to add more workers we can generate a new token so we do not need to save the token command given above. This can be done by running the following command to get a token:

```
sudo docker swarm join-token worker
```

## First service

As a way to control the docker swarm we will add a [portainer](https://www.portainer.io/) this is a open source solution to control docker the swarm graphicaly. We will follow the how to found [here](https://documentation.portainer.io/v2.0/deploy/ceinstallswarm/)

Start by getting the docker-compose file for portainer:

```
curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
```

Then add it as a stack

```
docker stack deploy -c portainer-agent-stack.yml portainer
```

