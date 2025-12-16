---
title: "Calculating Coherence Cluster Size On"
summary: "In one of the projects I had a problem regarding calculating the size of a Oracle Coherence cluster."
date: 2012-10-16

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Blog

content_meta:
  trending: false
---
In one of the projects I had a problem regarding calculating the size of a Oracle Coherence cluster. It doesn't seem to difficult since there is already a nice piece of code that can do it for us:
[Calculating Coherence cluster size](https://code.google.com/p/coherence-tools/source/browse/trunk/main/src/com/seovic/coherence/util/CalculateCacheSize.java?r=147)
The problem is that this code is working properly only if the Coherence cluster is not remote. So how can we access it?
We have to modify the following function
```groovy
public static MBeanServer getMBeanServer() {
  MBeanServer server = null;        for (Object o: MBeanServerFactory.findMBeanServer(null)) {
    server = (MBeanServer) o;            if (DOMAIN_DEFAULT.length() == 0 ||                server.getDefaultDomain().equals(DOMAIN_DEFAULT)) {
      break;
    }
    server = null;
  }
  if (server == null) {
    server = MBeanServerFactory.createMBeanServer(DOMAIN_DEFAULT);
  }
  return server;
}
```
To this one
```groovy
public static MBeanServerConnection getMBeanServer() throws IOException {
  JMXServiceURL url = new JMXServiceURL("service: //..."); JMXConnector jmxc = JMXConnectorFactory.connect(url,
  null); return jmxc.getMBeanServerConnection();
}
```
And modify the line
```
MBeanServer server = getMBeanServer();
```
To use the new interface
```
MBeanServerConnection server = getMBeanServer();
```
More information on how to define the Coherence JMX service URL and JMX for Coherence as such can be found here
[Managing Coherence using JMX](https://coherence.oracle.com/display/COH32UG/Managing+Coherence+using+JMX)
