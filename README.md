# dok8s

(Yet another) Collection of scripts to create a [kubernetes](https://kubernetes.io/) cluster with multiple nodes using kubeadm from the latest stable version of kubernetes.

By default master uses 2gb, nodes use 4gb droplets. They can be changed with MASTER_SIZE and NODE_SIZE env. variables. All droplets are based on the ubuntu 16.04 image. 

dok8s uses [Flannel](https://coreos.com/flannel/docs/latest/kubernetes.html) for networking. Also installs [dashboard](https://github.com/kubernetes/dashboard/), [Prometheus](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/) and [Grafana](https://grafana.com/) for monitoring. 

It uses [DigitalOcean Load Balancer](https://www.digitalocean.com/products/load-balancer/) and configures the [DigitalOcean Firewall](https://www.digitalocean.com/products/cloud-firewalls/)

## Requires

- [DigitalOcean](https://www.digitalocean.com/) account (DIGITALOCEAN_ACCESS_TOKEN)
- [doctl](https://github.com/digitalocean/doctl) and [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Getting Started

To create a cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=XxX REGION=nyc3 MASTER_NAME=master NODE_NAME=node NODE_COUNT=3 ./dok8s-create
./dok8s-create
- Creating the master
- Waiting master to finish installation
- Waiting nodes to be ready
- Creating the LoadBalancer
- Creating the master firewall
- Creating the node firewall
- Installation completed

$ export KUBECONFIG=admin.conf
$ kubectl get nodes
NAME      STATUS    ROLES     AGE       VERSION
master    Ready     master    10m       v1.11.0
node1     Ready     <none>    8m        v1.11.0
node2     Ready     <none>    8m        v1.11.0
node3     Ready     <none>    8m        v1.11.0
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

Now access Dashboard at:

[`http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/`](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

To destroy the cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=XxX MASTER_NAME=master NODE_NAME=node NODE_COUNT=3 ./dok8s-destroy
- Destroying the droplets
- Destroying the tags
- Destroying the load balancer
- Destroying the node firewall
- Destroying the master firewall
- Destroy completed
```

# Acknowledgements

Uses bits and pieces from following projects:

- https://github.com/janakiramm/do-k8s
- https://github.com/kelseyhightower/kubernetes-the-hard-way
- https://github.com/kris-nova/kubicorn
