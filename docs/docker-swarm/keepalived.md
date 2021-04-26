---
title: Keepalived
summary: Tutorial to configure keepalived for shared virtual IP
authors:
  - Idar Bergli
date: 2021-04-22
---

Even tough having a swarm that is self healing and is great for the scalability and availability we need a ways for clients to easly connect to the cluster.

To do this we would need to have a single IP to the world for external access. Docker Swarm aready provides the load-balancing capabilities ([routing mesh](https://docs.docker.com/engine/swarm/ingress/)), all we need for seamless HA is a virtual IP which will be provided by more than one docker node.

We will use [Keepalived](https://www.keepalived.org/) on atleast two docker swarm nodes to do this.

## Preperation

To be able to use the _Keepalived_ to work we need to prepare the system for this, as it is dependent on a kernel module. The _ip\_vs_ module is needed so that services are alowed to bind to non-local interface addresses.

```sh
echo "modprobe ip_vs" | sudo tee -a /etc/modules > /dev/null
modprobe ip_vs
```

### Setup Swarm Nodes

If we assume the following IP addresses:

- 192.168.10.2 - Swarm 1
- 192.168.10.3 - Swarm 2
- 192.168.10.4 - Swarm 3
- 192.168.10.5 - Virtual

**Run on Swarm 1**
```sh
docker run -d --name keepalived --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW --net=host \
  -e KEEPALIVED_UNICAST_PEERS="#PYTHON2BASH:['192.168.10.2', '192.168.10.3', '192.168.10.4']" \
  -e KEEPALIVED_VIRTUAL_IPS=192.168.10.5 \
  -e KEEPALIVED_PRIORITY=200 \
  osixia/keepalived:2.0.20
```

**Run on Swarm 2**
```sh
docker run -d --name keepalived --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW --net=host \
  -e KEEPALIVED_UNICAST_PEERS="#PYTHON2BASH:['192.168.10.2', '192.168.10.3', '192.168.10.4']" \
  -e KEEPALIVED_VIRTUAL_IPS=192.168.10.5 \
  -e KEEPALIVED_PRIORITY=100 \
  osixia/keepalived:2.0.20
```

**Run on Swarm 3**
```sh
docker run -d --name keepalived --restart=always \
  --cap-add=NET_ADMIN --cap-add=NET_BROADCAST --cap-add=NET_RAW --net=host \
  -e KEEPALIVED_UNICAST_PEERS="#PYTHON2BASH:['192.168.10.2', '192.168.10.3', '192.168.10.4']" \
  -e KEEPALIVED_VIRTUAL_IPS=192.168.10.5 \
  -e KEEPALIVED_PRIORITY=100 \
  osixia/keepalived:2.0.20
```

## Thats it!

Now the swarm nodes will talk to each other by using unicast (no need to un-firewall multicast addresses), and the one with the highest priority will be the master of the IP. When traffic arrives thrue the Virtual IP the Docker Swarm service mesh will route the trafic to the correct node.
