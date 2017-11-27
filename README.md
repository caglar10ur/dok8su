# dok8s

(Yet another) Collection of bash scripts to create a [kubernetes](https://kubernetes.io/) cluster with 4 nodes using kubeadm.

Master uses 2gb, nodes uses 4gb droplets. They are all based on the ubuntu 16.04 image.

Uses [Project Calico v2.6](https://www.projectcalico.org/) for networking. Also installs [dashboard](https://github.com/kubernetes/dashboard/), [grafana/influxdb/heapster](https://github.com/kubernetes/heapster/) for monitoring.
Deploys [Sock Shop](https://github.com/microservices-demo/microservices-demo) and exposes it using [DigitalOcean Load Balancer](https://www.digitalocean.com/products/load-balancer/).

## Requires

- [DigitalOcean](https://www.digitalocean.com/) account (DIGITALOCEAN_ACCESS_TOKEN)
- [doctl](https://github.com/digitalocean/doctl) and [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Getting Started

To create a cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=XxX REGION=nyc3 ./dok8s-create
- Creating the master
ID          Name      Public IPv4       Private IPv4    Public IPv6    Memory    VCPUs    Disk    Region    Image                 Status    Tags    Features              Volumes
12345567    master    1.2.3.4           10.10.10.10                    2048      2        40      nyc3      Ubuntu 16.04.3 x64    active            private_networking
- Waiting master to finish installation
- Creating nodes
ID          Name     Public IPv4        Private IPv4      Public IPv6    Memory    VCPUs    Disk    Region    Image                 Status    Tags    Features              Volumes
12345567    node1    1.2.3.4            10.10.10.10                      4096      2        40      nyc3      Ubuntu 16.04.3 x64    active            private_networking
12345567    node2    1.2.3.4            10.10.10.10                      4096      2        40      nyc3      Ubuntu 16.04.3 x64    active            private_networking
12345567    node3    1.2.3.4            10.10.10.10                      4096      2        40      nyc3      Ubuntu 16.04.3 x64    active            private_networking
- Waiting nodes to be ready
- Creating grafana/influxdb/heapster
- Creating sock shop app
- Creating the LoadBalancer
ID                                      IP    Name     Status    Created At              Algorithm      Region    Tag         Droplet IDs                   SSL      Sticky Sessions                                Health Check                                                                                                                      Forwarding Rules
XXXXXXXXXXXXXXXX                              nodes    new       2017-11-27T06:04:53Z    round_robin    nyc3      k8s-node    1234556,12345567,12345567     false    type:none,cookie_name:,cookie_ttl_seconds:0    protocol:http,port:30001,path:/,check_interval_seconds:10,response_timeout_seconds:5,healthy_threshold:5,unhealthy_threshold:3    entry_protocol:tcp,entry_port:80,target_protocol:tcp,target_port:30001,certificate_id:,tls_passthrough:false
- Installation completed

$ export KUBECONFIG=admin.conf
$ kubectl get nodes
NAME      STATUS    ROLES     AGE       VERSION
master    Ready     master    3m        v1.8.4
node1     Ready     <none>    2m        v1.8.4
node2     Ready     <none>    2m        v1.8.4
node3     Ready     <none>    1m        v1.8.4
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

Now access Dashboard at:

[`http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/`](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

and Grafana at:

[`http://localhost:8001/api/v1/namespaces/kube-system/services/monitoring-grafana/proxy/`](http://localhost:8001/api/v1/namespaces/kube-system/services/monitoring-grafana/proxy/)

To destroy the cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=XxX ./dok8s-destroy
- Destroying the droplets
- Destroying the tags
- Destroying the load balancer
- Destroy completed
```

# Acknowledgements

Uses bits and pieces from following projects:

- https://github.com/janakiramm/do-k8s
- https://github.com/kelseyhightower/kubernetes-the-hard-way
- https://github.com/kris-nova/kubicorn
