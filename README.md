# dok8s

*Disclaimer: Not For Production Use*

(Yet another) Collection of scripts to create a [kubernetes](https://kubernetes.io/) cluster with multiple nodes using kubeadm from the latest stable version of kubernetes.

By default master uses 2gb, nodes use 4gb droplets. They can be changed by setting the MASTER_SIZE and NODE_SIZE environment variables, respectively. All droplets are based on the ubuntu 16.04 image.

dok8s uses [Flannel](https://coreos.com/flannel/docs/latest/kubernetes.html) for networking. It also installs [dashboard](https://github.com/kubernetes/dashboard/), [metrics server](https://github.com/kubernetes-incubator/metrics-server), [Prometheus](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/) [with node_exporter] and [Grafana](https://grafana.com/).

It uses [Kubernetes Cloud Controller Manager for DigitalOcean](https://github.com/digitalocean/digitalocean-cloud-controller-manager), [Container Storage Interface (CSI) Driver for DigitalOcean Block Storage](https://github.com/digitalocean/csi-digitalocean) and configures the [DigitalOcean Firewall](https://www.digitalocean.com/products/cloud-firewalls/)

## Requires

- [DigitalOcean](https://www.digitalocean.com/) account (DIGITALOCEAN_ACCESS_TOKEN)
- [doctl](https://github.com/digitalocean/doctl) and [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Getting Started

To create a cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=<omitted> REGION=nyc3 TAG_PREFIX=nyc3-k8s MASTER_NAME=master NODE_NAME=node NODE_COUNT=3 MASTER_SIZE=s-2vcpu-2gb NODE_SIZE=s-2vcpu-4gb ./dok8s-create
- Creating the master
- Waiting master to finish installation
- Waiting nodes to be ready
- Creating the master firewall
- Creating the node firewall
- Installation completed

$ export KUBECONFIG=admin.nyc3-k8s.conf
$ kubectl get nodes
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   4m28s   v1.12.2
node1    Ready    <none>   116s    v1.12.2
node2    Ready    <none>   119s    v1.12.2
node3    Ready    <none>   2m8s    v1.12.2
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

Now access Dashboard at:

[`http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/`](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

To destroy the cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=<omitted> TAG_PREFIX=nyc3-k8s MASTER_NAME=master NODE_NAME=node ./dok8s-destroy
- Destroying the droplets
- Destroying the tags
- Destroying the node firewall
- Destroying the master firewall
- Destroy completed
```

## Environment variables

* REGION - default is nyc3
* MASTER_NAME - default is master
* NODE_NAME - default is node
* MASTER_SIZE - default is s-2vcpu-2gb
* NODE_SIZE - default is s-2vcpu-4gb
* NODE_COUNT - default is 3
* TAG_PREFIX - default is k8s

# Acknowledgements

Uses bits and pieces from following projects:

- https://github.com/janakiramm/do-k8s
- https://github.com/kelseyhightower/kubernetes-the-hard-way
- https://github.com/kris-nova/kubicorn
