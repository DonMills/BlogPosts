### Service Meshes for Infrastructure/DevOps Teams: An Introduction

Service meshes as a concept have been gradually growing in perception and usage over the past year or so.  You may have heard the term mentioned in relation to containers, or microservices, or Kubernetes, or any number of other industry buzzwords.  Possibly you have a team of developers that is starting a new containerized project and to properly support them you need some visibility and control over thier traffic -- or you may have seen cloud providers beginning to offer their own service mesh products and been curious.  

But what are service meshes?  What makes them different from the way it's always been done? And most importantly, why should you care?

In this first article I'm going to propose some answers to those questions. And then I'm going to try and convince you that service meshes will not only be incredibly important for developers, but can also be considered a next generation of security and network capabilities for the operations side.

_Note: we will be primarily discussing service meshes based off of the [Envoy proxy](https://www.envoyproxy.io/) created by Lyft.  These include istio, AWS App Mesh, and Google Cloud Traffic Manager._

###  What it is, and how it works

#### The overlay
A service mesh at it's most fundamental level is a _network overlay_.  Network traffic that crosses the service mesh still rides on traditional switches and routers (or their cloud analogues), but that traffic is encapsulated within another layer that the lower level devices do not see into.  You might compare it to putting a letter into an envelope and mailing it.  The sender and recipient can read the letter, but the mail system just passes the envelope around.  

This is most analagous in the networking world to a typical [VXLAN implementation](https://en.wikipedia.org/wiki/Virtual_Extensible_LAN).  Traffic enters the mesh near the source, rides on top of existing infrastructure, and then leaves the mesh near the destination.  It differs from a VXLAN however, in that it only carries HTTP traffic,  the encapsulating "envelope" is HTTPS, and instead of shared VTEPs there is an Envoy proxy instance per endpoint.  

This has three important implications for the network and security teams:
1. All traffic in the mesh will be encrypted by default
2. Many traditional security technologies (layer 2 IDS/IPS I'm looking at you here) will not function as desired on service mesh traffic
3. Endpoints in a service mesh can be placed anywhere that has layer 3 connectivity with the other mesh endpoints (on premise, in the cloud, etc).






