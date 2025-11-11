# Falco

The Falco Project officially supports three ways to run Falco in production:

* Running Falco directly on a host
* Running Falco in a container
* Deploying Falco to a Kubernetes cluster

TBD

Setup Falco with Falcosidekick and Elasticsearch to test that it can be used
for threat hunting. Show also how we can bypass Falco rules.

https://github.com/falcosecurity/falcosidekick-ui
https://falco.org/docs/getting-started/falco-kubernetes-quickstart/

```
helm repo add falcosecurity https://falcosecurity.github.io/charts
helm repo update
```

```
helm install --replace falco \
             --namespace falco \
             --create-namespace \
             --set falcosidekick.enabled=true \
             --set falcosidekick.webui.enabled=true \
              falcosecurity/falco
```




```
docker run --rm -it \
           --name falco \
           --privileged \
           -v /sys/kernel/tracing:/sys/kernel/tracing:ro \
           -v /var/run/docker.sock:/host/var/run/docker.sock \
           -v /proc:/host/proc:ro \
           -v /etc:/host/etc:ro \
           falcosecurity/falco:0.42.0
```