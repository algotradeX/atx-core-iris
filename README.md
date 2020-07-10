# atx-core-iris
📮 Iris is a server which handles internal messaging and event system. It uses AxonIQ 4.x via docker, kubernetes and miniqube. Iris includes a axon-server and a axon-dashboard.

--------------------------------------------------------
Running Axon Server locally
---------------------------

To run Axon Server locally, all you need to do is put the server JAR file in
the directory where you want it to live, and start it using:

    java -jar axonserver.jar

You will see that it creates a subdirectory data where it will store its
information. Open a browser to with URL http://localhost:8024 to view the dashboard.

Running Axon Server in a Docker container
-----------------------------------------

To run Axon Server in Docker you can use the image provided on Docker Hub:

    $ docker run -d --name my-axon-server -p 8024:8024 -p 8124:8124 axoniq/axonserver
    ...some container id...
    $

WARNING:
    This is not a supported image for production purposes. Please use with caution.

If you want to run the clients in Docker containers, and are not using something
like Kubernetes, use the "--hostname" option of the docker command to set a useful
name like "axonserver":

    $ docker run -d --name my-axon-server -p 8024:8024 -p 8124:8124 --hostname axonserver axoniq/axonserver

When you start the client containers, you can now use "--link axonserver" to provide
them with the correct DNS entry. The Axon Server-connector looks at the
"axon.axonserver.servers" property to determine where Axon Server lives, so don't
forget to set that to "axonserver" for your apps.

Running Axon Server in Kubernetes and Minikube
-----------------------------------------------

WARNING:
    Although you can get a pretty functional cluster running locally using Minikube,
    you can run into trouble when you want to let it serve clients outside of the
    cluster. Minikube can provide access to HTTP servers running in the cluster,
    for other protocols you have to run a special protocol-agnostic proxy like you
    can with "kubectl port-forward <pod-name> <port-number>".
    For non-development scenarios, we don't recommend using Minikube.

Deployment requires the use of a YAML descriptor, an working example of which
can be found in the "kubernetes" directory. To run it, use the following commands
in a separate window:

    $ kubectl apply -f kubernetes/axonserver.yaml
    statefulset.apps "axonserver" created
    service "axonserver-gui" created
    service "axonserver" created
    $ kubectl port-forward axonserver-0 8124
    Forwarding from 127.0.0.1:8124 -> 8124
    Forwarding from [::1]:8124 -> 8124


You can now run your app, which will connect throught the proxied gRPC port. To see
the Axon Server Web GUI, use "minikube service --url axonserver-gui" to obtain the
URL for your browser. Actually, if you leave out the "--url", minikube will open
the the GUI in your default browser for you.

To clean up the deployment, use:

    $ kubectl delete sts axonserver
    statefulset.apps "axonserver" deleted
    $ kubectl delete svc axonserver
    service "axonserver" deleted
    $ kubectl delete svc axonserver-gui
    service "axonserver-gui" deleted

Use "axonserver" (as that is the name of the Kubernetes service) for your clients
if you're going to deploy them in the cluster, which is what you'ld probably want.
Running the client outside the cluster, with Axon Server inside, entails extra
work to enable and secure this, and is definitely beyond the scope of this example.

Configuring Axon Server
=======================

Axon Server uses sensible defaults for all of its settings, so it will actually
run fine without any further configuration. However, if you want to make some
changes, below are the most common options. You can change them using an
"axonserver.properties" file in the directory where you run Axon Server. For the
full list, see the Reference Guide. https://docs.axoniq.io/reference-guide/axon-server

* axoniq.axonserver.name
  This is the name Axon Server uses for itself. The default is to use the
  hostname.
* axoniq.axonserver.hostname
  This is the hostname clients will use to connect to the server. Note that
  an IP address can be used if the name cannot be resolved through DNS.
  The default value is the actual hostname reported by the OS.
* server.port
  This is the port where Axon Server will listen for HTTP requests,
  by default 8024.
* axoniq.axonserver.port
  This is the port where Axon Server will listen for gRPC requests,
  by default 8124.
* axoniq.axonserver.event.storage
  This setting determines where event messages are stored, so make sure there
  is enough diskspace here. Losing this data means losing your Events-sourced
  Aggregates' state! Conversely, if you want a quick way to start from scratch,
  here's where to clean.
* axoniq.axonserver.snapshot.storage
  This setting determines where aggregate snapshots are stored.
* axoniq.axonserver.controldb-path
  This setting determines where the message hub stores its information.
  Losing this data will affect Axon Server's ability to determine which
  applications are connected, and what types of messages they are interested
  in.
* axoniq.axonserver.accesscontrol.enabled
  Setting this to true will require clients to pass a token.
* axoniq.axonserver.accesscontrol.token
  This is the token used for access control.

The Axon Server HTTP server
===========================

Axon Server provides two servers; one serving HTTP requests, the other gRPC.
By default these use ports 8024 and 8124 respectively, but you can change
these in the settings as described above.

The HTTP server has in its root context a management Web GUI, a health
indicator is available at "/actuator/health", and the REST API at "/v1'. The
API's Swagger endpoint finally, is available at "/swagger-ui.html", and gives
the documentation on the REST API.









---------------------------------------------------------
Development
---------------------------------------------------------

**Pull Docker Image for AxonServer built with JDK11**

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

**Run Docker Image and expose 8024 for AxonDashboard and 8124 for GRPC**

Sample command: docker run -d --name <my-axon-server> -p 8024:8024 -p 8124:8124 axoniq/axonserver <some-container-id>

```
$ docker run -p 8024:8024 -p 8124:8124 axoniq/axonserver:4.2.4-jdk11
```
----------------------------------------------------------------
**Axon Dashboard**
----------------------------------------------------------------
You can view axon server dashboard at http://localhost:8024/#overview

----------------------------------------------------------------
Resources
----------------------------------------------------------------

http://progressivecoder.com/building-microservices-with-axon-server-and-spring-boot/


https://dzone.com/articles/running-axon-server-going-from-local-developer-ins


https://github.com/AxonIQ/reference-guide

----------------------------------------------------------------







