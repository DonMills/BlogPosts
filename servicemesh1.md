### Service Meshes for Infrastructure Teams: An Introduction

Service meshes have been gradually growing in perception and usage over the past year or so.  You may have heard the term mentioned in relation to containers, or microservices, or Kubernetes, or any number of other industry buzzwords.  But what are they?  What makes them different? And most importantly, why should you care?

In this article I'm going to tell you the answers.  And then I'm going to explain briefly why I believe Service Meshes are not only important for developers, but can also be considered the next generation of security and network capabilities.
_Note: we will be primarily discussing service meshes based off of the 

####  What it is

A service mesh at it's most fundamental level is a _network overlay_.  Traffic that crosses the service mesh still rides on traditional switches and routers, but that traffic is encapsulated within another layer.  This is most analagous in the networking world to a typical [VXLAN implementation](https://en.wikipedia.org/wiki/Virtual_Extensible_LAN).

To gain access to the service mesh each endpoint (typically a container) has a dedicated 
