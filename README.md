# atx-core-iris
📮 Iris: Server handling internal messaging and event system

--------------------------------------------------------

Pull Docker Image for AxonServer built with JDK11

Source: https://hub.docker.com/r/axoniq/axonserver/tags

```
$ docker pull axoniq/axonserver:4.2.4-jdk11

4.2.4-jdk11: Pulling from axoniq/axonserver
ab1fc7e4bf91: Pull complete
35fba333ff52: Pull complete
f0cb1fa13079: Pull complete
3d1dd648b5ad: Pull complete
a9f886e483d6: Pull complete
f0c295b9cf6e: Pull complete
afe560095725: Pull complete
dc8253cd29cd: Pull complete
6885295983c8: Pull complete
c03fce0b84bc: Pull complete
996cec1a2b5a: Pull complete
b4fe1ba84e72: Pull complete
e0cafd4d83ff: Pull complete
78db7ad16394: Pull complete
04fc619d38a1: Pull complete
b0f09b5eaaa8: Pull complete
d12ac6fc0fb3: Pull complete
Digest: sha256:1058812e04777442811c8298344378ad62c990507a53bb9257972d5617ef7a0a
Status: Downloaded newer image for axoniq/axonserver:4.2.4-jdk11
docker.io/axoniq/axonserver:4.2.4-jdk11
```
----------------------------------------------------------------

Run Docker Image and expose 8024 for AxonDashboard and 8124 for GRPC

```
$ docker run -p 8024:8024 -p 8124:8124 axoniq/axonserver:4.2.4-jdk11
```

----------------------------------------------------------------

http://progressivecoder.com/building-microservices-with-axon-server-and-spring-boot/










