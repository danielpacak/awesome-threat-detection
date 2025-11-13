# Elastic Stack

## TODO

1. [ ] Upgrade to more recent version of Elastic Stack (8.4 is pretty with limited support)
2. [ ] Test that Elastic Stack can detect Atlassian Confluence Pre-Auth RCE via OGNL Injection (CVE-2022-26134)
3. [ ] Do I need two instances of Elasticsearch running as Docker Compose services?

## Technical requirements

TBD I'm not sure how to split the test lab in LimaVM, i.e. one or two VMs?

## The Elastic Stack

The Elastic Stack is a set of tightly integrated products that are well suited
to address the requirements of a Detection Engineering lab. The Elastic Stack
includes the following components:

* Beats: A data shipper that is used to collect data from an endpoint and forward
  it to Elasticsearch or Logstash for preprocessing. Different packages exist for
  collecting different types of data from endpoints. Talk about Elastic Agent...
* Logstash: A powerful tool for transforming data and forwarding it to different
  destination systems. It offers a high degree of control over re-shaping data
  and has plugins that support forwarding data to a significant variety of destinations.
* Elasticsearch: This is the data storage component. While primarily designed for searching,
  it has evolved to become a capable data analysis engine.
* Kibana: This is the Elasticsearch frontend. Kibana allows users to explore and visualize data stored in
  Elasticsearch using a web browser. To read more about Kibana's vast feature set, visit Elastic's website:
  <https://www.elastic.co/kibana/features>.
* Integrations: This refers to the large collection of data ingestion components
  that are enabled by Elastic Agent and managed by Fleet Server. Once an endpoint has
  Elastic Agent installed on it, administrators can configure the collection of data from
  a variety of known data sources. Elastic Agent receives configuration details from
  Fleet Server about what data should be collected, and which server the data should be output to.
  These configuration details are defined as agent policies and are stored in Elasticsearch.

``` mermaid
flowchart TD
    subgraph limaVM["Ubuntu in LimaVM 192.168.5.15/24"]
        subgraph docker["Docker 172.17.0.1/16"]
            kibana("Kibana 172.18.0.5/16")
            elasticsearch1("Elasticsearch (es01) 172.18.0.3/16")
            elasticsearch2("Elasticsearch (es02) 172.18.0.4/16")
            fleetServer("Fleet server")
            kibana --> elasticsearch1
            fleetServer --> elasticsearch1
        end
        elasticAgent("Elastic agent")
    end

    elasticAgent <-- "policy data and heartbeat" --> fleetServer
    elasticAgent --> elasticsearch1
```

https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-docker
https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-docker-compose

Due to the increased memory requirements of Elastic, the default Docker settings
must be modified to handle this. If you installed Docker on a Linux-based system,
as outlined in the Elastic system configuration documentation at https://www.elastic.co/guide/en/elasticsearch/reference/8.4/vm-max-map-count.html,
then you need to modify the vm.max_map_count setting of the Docker hosts via the sysctl command.

On Linux, this can be achieved by issuing the following command:

```
sudo sysctl -w vm.max_map_count=262144
```

You can make this change survive reboots by modifying /etc/sysctl.conf.

Using a new terminal, change your working directory to the esk subdirectory of the project
root folder and issue the `docker compose up -d` command.

If everything has gone well, you will be able to access your Elastic Stack by going to http://[ip address of your docker host]:5601, or http://localhost:5601 if
you are running Docker on the same machine.

You can log in by using elastic as the username and the password specified in the .env file for the
ELASTIC_PASSWORD value.

Click the hamburger menu in the top-left corner of the page. Under the Management section, click Fleet.
After navigating there, you will be presented with the screen, which walks you through setting up Fleet Server.
For the Fleet Server host field, use https://[ip address of your docker host]:8220

Select Generate Fleet Server policy. A sequence of commands for installing Fleet on a centralized how will be provided.

https://192.168.5.15:8220 or https://172.17.0.1:8220

Linux Tar
```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.4.3-linux-x86_64.tar.gz
tar xzvf elastic-agent-8.4.3-linux-x86_64.tar.gz
cd elastic-agent-8.4.3-linux-x86_64
sudo ./elastic-agent install \
  --fleet-server-es=http://localhost:9200 \
  --fleet-server-service-token=AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL3Rva2VuLTE3NjI4NTkyNTU5MDM6Q2Y5SW5XZFBSdkNfUElURUZlU05FQQ \
  --fleet-server-policy=fleet-server-policy
```

DEB
```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.4.3-amd64.deb
sudo dpkg -i elastic-agent-8.4.3-amd64.deb
sudo elastic-agent enroll \
  --fleet-server-es=http://localhost:9200 \
  --fleet-server-service-token=AAEAAWVsYXN0aWMvZmxlZXQtc2VydmVyL3Rva2VuLTE3NjI4NTkyNTU5MDM6Q2Y5SW5XZFBSdkNfUElURUZlU05FQQ \
  --fleet-server-policy=fleet-server-policy
sudo systemctl enable elastic-agent
sudo systemctl start elastic-agent
```

We only need the value of fleet-server-service-token. Copy this value from the web page and paste it into
the .env file in the fleet subdirectory.

You can now start the Fleet container using the docker compose up -d

With that, Fleet has been configured. Next, we'll perform some additional configurations
that are required for full functionality.

By default, the Fleet Server policy is configured to send output to the Elastic host (http://localhost:9200).
Hosts that we later connect to our Fleet Server may not be able to reach the Elasticsearch backend using that address.
Therefore, we need to change the default output so that telemetry from additional hosts gets
stored in Elasticsearch.

https://192.168.5.15:9200 or https://172.17.0.1:9200

For Advanced YAML configuration, enter `ssl.verification_mode: none`.

Install Elastic Agent (for Windows)
Install Elastic Agent (for Linux)

Linux TAR
```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.4.3-linux-x86_64.tar.gz
tar xzvf elastic-agent-8.4.3-linux-x86_64.tar.gz
cd elastic-agent-8.4.3-linux-x86_64
sudo ./elastic-agent install --url=https://192.168.5.15:8220 --insecure --enrollment-token=QzZ6TGNwb0JhZ3EzTXJVNkNZTFo6SjhWWW41T2JRamFZdTd6Q3JiV1lEdw==
```
```
Elastic Agent will be installed at /opt/Elastic/Agent and will run as a service. Do you want to continue? [Y/n]:Y
{"log.level":"warn","@timestamp":"2025-11-11T13:08:43.130+0100","log.logger":"tls","log.origin":{"file.name":"tlscommon/tls_config.go","file.line":104},"message":"SSL/TLS verifications disabled.","ecs.version":"1.6.0"}
{"log.level":"info","@timestamp":"2025-11-11T13:08:43.391+0100","log.origin":{"file.name":"cmd/enroll_cmd.go","file.line":471},"message":"Starting enrollment to URL: https://192.168.5.15:8220/","ecs.version":"1.6.0"}
{"log.level":"warn","@timestamp":"2025-11-11T13:08:43.503+0100","log.logger":"tls","log.origin":{"file.name":"tlscommon/tls_config.go","file.line":104},"message":"SSL/TLS verifications disabled.","ecs.version":"1.6.0"}
{"log.level":"info","@timestamp":"2025-11-11T13:08:44.223+0100","log.origin":{"file.name":"cmd/enroll_cmd.go","file.line":273},"message":"Successfully triggered restart on running Elastic Agent.","ecs.version":"1.6.0"}
Successfully enrolled the Elastic Agent.
Elastic Agent has been successfully installed.
```

``` console
$ systemctl status elastic-agent.service
● elastic-agent.service - Elastic Agent is a unified agent to observe, monitor and protect your system.
     Loaded: loaded (/etc/systemd/system/elastic-agent.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2025-11-11 13:08:43 CET; 31s ago
   Main PID: 96302 (elastic-agent)
      Tasks: 56 (limit: 9475)
     Memory: 810.8M
        CPU: 15.237s
     CGroup: /system.slice/elastic-agent.service
             ├─96302 elastic-agent
             ├─96415 /opt/Elastic/Agent/data/elastic-agent-90167b/install/filebeat-8.4.3-linux-x86_64/filebeat -E setup.ilm.enabled=false -E setup.template.enabled=false -E management.enabled=true -E log>
             ├─96469 /opt/Elastic/Agent/data/elastic-agent-90167b/install/metricbeat-8.4.3-linux-x86_64/metricbeat -E setup.ilm.enabled=false -E setup.template.enabled=false -E management.enabled=true -E>
             ├─96501 /opt/Elastic/Agent/data/elastic-agent-90167b/install/filebeat-8.4.3-linux-x86_64/filebeat -E setup.ilm.enabled=false -E setup.template.enabled=false -E management.enabled=true -E log>
             └─96543 /opt/Elastic/Agent/data/elastic-agent-90167b/install/metricbeat-8.4.3-linux-x86_64/metricbeat -E setup.ilm.enabled=false -E setup.template.enabled=false -E management.enabled=true -E>
```

DEB
```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.4.3-amd64.deb
sudo dpkg -i elastic-agent-8.4.3-amd64.deb
sudo elastic-agent enroll --url=https://192.168.5.15:8220 --enrollment-token=QzZ6TGNwb0JhZ3EzTXJVNkNZTFo6SjhWWW41T2JRamFZdTd6Q3JiV1lEdw== 
sudo systemctl enable elastic-agent 
sudo systemctl start elastic-agent
```

Kubernetes
```
---
# For more information refer to https://www.elastic.co/guide/en/fleet/current/running-on-kubernetes-managed-by-fleet.html
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: elastic-agent
  namespace: kube-system
  labels:
    app: elastic-agent
spec:
  selector:
    matchLabels:
      app: elastic-agent
  template:
    metadata:
      labels:
        app: elastic-agent
    spec:
      # Tolerations are needed to run Elastic Agent on Kubernetes master nodes.
      # Agents running on master nodes collect metrics from the control plane components (scheduler, controller manager) of Kubernetes
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule
      serviceAccountName: elastic-agent
      hostNetwork: true
      # 'hostPID: true' enables the Elastic Security integration to observe all process exec events on the host.
      # Sharing the host process ID namespace gives visibility of all processes running on the same host.
      hostPID: true
      dnsPolicy: ClusterFirstWithHostNet
      containers:
        - name: elastic-agent
          image: docker.elastic.co/beats/elastic-agent:8.4.3
          env:
            # Set to 1 for enrollment into Fleet server. If not set, Elastic Agent is run in standalone mode
            - name: FLEET_ENROLL
              value: "1"
            # Set to true to communicate with Fleet with either insecure HTTP or unverified HTTPS
            - name: FLEET_INSECURE
              value: "true"
            # Fleet Server URL to enroll the Elastic Agent into
            # FLEET_URL can be found in Kibana, go to Management > Fleet > Settings
            - name: FLEET_URL
              value: "https://192.168.5.15:8220"
            # Elasticsearch API key used to enroll Elastic Agents in Fleet (https://www.elastic.co/guide/en/fleet/current/fleet-enrollment-tokens.html#fleet-enrollment-tokens)
            # If FLEET_ENROLLMENT_TOKEN is empty then KIBANA_HOST, KIBANA_FLEET_USERNAME, KIBANA_FLEET_PASSWORD are needed
            - name: FLEET_ENROLLMENT_TOKEN
              value: "QzZ6TGNwb0JhZ3EzTXJVNkNZTFo6SjhWWW41T2JRamFZdTd6Q3JiV1lEdw=="
            - name: KIBANA_HOST
              value: "http://kibana:5601"
            # The basic authentication username used to connect to Kibana and retrieve a service_token to enable Fleet
            - name: KIBANA_FLEET_USERNAME
              value: "elastic"
            # The basic authentication password used to connect to Kibana and retrieve a service_token to enable Fleet
            - name: KIBANA_FLEET_PASSWORD
              value: "changeme"
            - name: NODE_NAME
              valueFrom:
                fieldRef:
                  fieldPath: spec.nodeName
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
          securityContext:
            runAsUser: 0
          resources:
            limits:
              memory: 500Mi
            requests:
              cpu: 100m
              memory: 200Mi
          volumeMounts:
            - name: proc
              mountPath: /hostfs/proc
              readOnly: true
            - name: etc-kubernetes
              mountPath: /hostfs/etc/kubernetes
              readOnly: true
            - name: var-lib
              mountPath: /hostfs/var/lib
              readOnly: true
            - name: cgroup
              mountPath: /hostfs/sys/fs/cgroup
              readOnly: true
            - name: varlibdockercontainers
              mountPath: /var/lib/docker/containers
              readOnly: true
            - name: varlog
              mountPath: /var/log
              readOnly: true
            - name: passwd
              mountPath: /hostfs/etc/passwd
              readOnly: true
            - name: group
              mountPath: /hostfs/etc/group
              readOnly: true
            - name: etcsysmd
              mountPath: /hostfs/etc/systemd
              readOnly: true
            - name: etc-mid
              mountPath: /etc/machine-id
              readOnly: true
      volumes:
        - name: proc
          hostPath:
            path: /proc
        - name: cgroup
          hostPath:
            path: /sys/fs/cgroup
        - name: varlibdockercontainers
          hostPath:
            path: /var/lib/docker/containers
        - name: varlog
          hostPath:
            path: /var/log
        # Needed for cloudbeat
        - name: etc-kubernetes
          hostPath:
            path: /etc/kubernetes
        # Needed for cloudbeat
        - name: var-lib
          hostPath:
            path: /var/lib
        # Needed for cloudbeat
        - name: passwd
          hostPath:
            path: /etc/passwd
        # Needed for cloudbeat
        - name: group
          hostPath:
            path: /etc/group
        # Needed for cloudbeat
        - name: etcsysmd
          hostPath:
            path: /etc/systemd
        # Mount /etc/machine-id from the host to determine host ID
        # Needed for Elastic Security integration
        - name: etc-mid
          hostPath:
            path: /etc/machine-id
            type: File
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: elastic-agent
subjects:
  - kind: ServiceAccount
    name: elastic-agent
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: elastic-agent
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: kube-system
  name: elastic-agent
subjects:
  - kind: ServiceAccount
    name: elastic-agent
    namespace: kube-system
roleRef:
  kind: Role
  name: elastic-agent
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: elastic-agent-kubeadm-config
  namespace: kube-system
subjects:
  - kind: ServiceAccount
    name: elastic-agent
    namespace: kube-system
roleRef:
  kind: Role
  name: elastic-agent-kubeadm-config
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: elastic-agent
  labels:
    k8s-app: elastic-agent
rules:
  - apiGroups: [""]
    resources:
      - nodes
      - namespaces
      - events
      - pods
      - services
      - configmaps
      # Needed for cloudbeat
      - serviceaccounts
      - persistentvolumes
      - persistentvolumeclaims
    verbs: ["get", "list", "watch"]
  # Enable this rule only if planing to use kubernetes_secrets provider
  #- apiGroups: [""]
  #  resources:
  #  - secrets
  #  verbs: ["get"]
  - apiGroups: ["extensions"]
    resources:
      - replicasets
    verbs: ["get", "list", "watch"]
  - apiGroups: ["apps"]
    resources:
      - statefulsets
      - deployments
      - replicasets
      - daemonsets
    verbs: ["get", "list", "watch"]
  - apiGroups:
      - ""
    resources:
      - nodes/stats
    verbs:
      - get
  - apiGroups: [ "batch" ]
    resources:
      - jobs
      - cronjobs
    verbs: [ "get", "list", "watch" ]
  # Needed for apiserver
  - nonResourceURLs:
      - "/metrics"
    verbs:
      - get
  # Needed for cloudbeat
  - apiGroups: ["rbac.authorization.k8s.io"]
    resources:
      - clusterrolebindings
      - clusterroles
      - rolebindings
      - roles
    verbs: ["get", "list", "watch"]
  # Needed for cloudbeat
  - apiGroups: ["policy"]
    resources:
      - podsecuritypolicies
    verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: elastic-agent
  # Should be the namespace where elastic-agent is running
  namespace: kube-system
  labels:
    k8s-app: elastic-agent
rules:
  - apiGroups:
      - coordination.k8s.io
    resources:
      - leases
    verbs: ["get", "create", "update"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: elastic-agent-kubeadm-config
  namespace: kube-system
  labels:
    k8s-app: elastic-agent
rules:
  - apiGroups: [""]
    resources:
      - configmaps
    resourceNames:
      - kubeadm-config
    verbs: ["get"]
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: elastic-agent
  namespace: kube-system
  labels:
    k8s-app: elastic-agent
---
```

Your newly enrolled host will appear in the list of agents. Elastic Agent can
be installed on many different operating systems, and this installation process
can be repeated for any additional host you want to enroll in the Elastic Stack.

https://www.elastic.co/docs/solutions/observability/infra-and-hosts/tutorial-observe-kubernetes-deployments

## Further reading

* https://matheuzsecurity.github.io/hacking/bypassing-elastic/
