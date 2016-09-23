rancher-gocd-agent
=======================

Builds a docker image for gocd agent with Ruby 2.3, to work on Rancher. 


To build:

```
docker build -t <registry>/rancher-goagent-ruby:<version> .
```

To deploy:

```
docker run -td --name go-agent \
-v <work-volume> /opt/go-agent/work \
<registry>/rancher-goagent-ruby:<version>

```
