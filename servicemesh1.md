### Service Meshes for Infrastructure/DevOps Teams: An Introduction

Service meshes as a concept have been gradually growing in perception and usage over the past year or so.  You may have heard the term mentioned in relation to containers, or microservices, or Kubernetes, or any number of other industry buzzwords.  Possibly you have a team of developers that is starting a new containerized project and to properly support them you need some visibility and control over thier traffic -- or you may have seen cloud providers beginning to offer their own service mesh products and been curious.  

But what are service meshes?  What makes them different from the way it's always been done? And most importantly, why should you care?

In this first article I'm going to propose some answers to those questions. And then I hope to convince you that service meshes will not only be incredibly important for developers, but can also be considered the next generation of security and network capabilities for the operations side.

_Note: we will be primarily discussing service meshes based off of the [Envoy proxy](https://www.envoyproxy.io/) created by Lyft.  These include istio, AWS App Mesh, and Google Cloud Traffic Manager._

####  What it is

A service mesh at it's most fundamental level is a _network overlay_.  Traffic that crosses the service mesh still rides on traditional switches and routers, but that traffic is encapsulated within another layer.  This is most analagous in the networking world to a typical [VXLAN implementation](https://en.wikipedia.org/wiki/Virtual_Extensible_LAN).

To gain access to the service mesh all traffic first enters an "onramp", which 
