# dok8su (u for unmanaged)

**BEWARE: This is probably not the project you are looking for. Please see [dok8s](https://do.co/dok8s) for DigitalOcean's managed Kubernetes solution**

> **Not For Production Use**

(Yet another) Collection of scripts to create an **unmanaged** [kubernetes](https://kubernetes.io/) cluster with multiple nodes using kubeadm from the latest stable version of kubernetes.

By default master uses 2gb and nodes use 4gb droplets. They can be configured via setting MASTER_SIZE and NODE_SIZE environment variables, respectively. Master and nodes uses the ubuntu 18.04 [bionic] image.

dok8su uses [Cilium](https://cilium.io/) for networking. It also installs [dashboard](https://github.com/kubernetes/dashboard/), [metrics server](https://github.com/kubernetes-incubator/metrics-server), [Prometheus](https://devopscube.com/setup-prometheus-monitoring-on-kubernetes/) [with node_exporter] and [Grafana](https://grafana.com/).

It uses [Kubernetes Cloud Controller Manager for DigitalOcean](https://github.com/digitalocean/digitalocean-cloud-controller-manager), [Container Storage Interface (CSI) Driver for DigitalOcean Block Storage](https://github.com/digitalocean/csi-digitalocean) and configures the [DigitalOcean Firewall](https://www.digitalocean.com/products/cloud-firewalls/)

## Requires

- [DigitalOcean](https://www.digitalocean.com/) account
- [DigitalOcean access token](https://www.digitalocean.com/docs/api/create-personal-access-token/)
- [doctl](https://github.com/digitalocean/doctl)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Getting Started

To create a cluster:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=<omitted> REGION=nyc3 TAG_PREFIX=nyc3-k8s MASTER_NAME=master NODE_NAME=node NODE_COUNT=3 MASTER_SIZE=s-2vcpu-2gb NODE_SIZE=s-2vcpu-4gb ./dok8su-create
- Creating the master
- Waiting master to finish installation
- Creating nodes
- Waiting nodes to be ready
- Deploying manifests
- Waiting load-balancers to be ready
- Creating the master firewall
- Creating the node firewall
- Installation completed (took 428 seconds)
- To learn your dashboard token, please run;
    kubectl --kubeconfig /home/dok8su/admin.k8s.conf -n kube-system describe secret dok8su-admin-token-lkzpl

$ kubectl --kubeconfig admin.k8s.conf get nodes
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   5m38s   v1.13.3
node1    Ready    <none>   4m21s   v1.13.3
node2    Ready    <none>   4m15s   v1.13.3
node3    Ready    <none>   4m13s   v1.13.3
```

To access Dashboard from your local workstation you must create a secure channel to your Kubernetes cluster. Run the following command:


```sh
$ kubectl  --kubeconfig admin.k8s.conf proxy
```

Now access Dashboard at:


[`http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/`](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

To destroy the cluster, run the following command:

```sh
$ DIGITALOCEAN_ACCESS_TOKEN=<omitted> TAG_PREFIX=nyc3-k8s MASTER_NAME=master NODE_NAME=node ./dok8su-destroy
- Destroying the droplets
- Destroying the tags
- Destroying the node firewall
- Destroying the master firewall
- Destroy completed
```

## Environment variables

Name | Default Value
--- | ---
REGION | nyc3
MASTER_NAME | master
NODE_NAME | node
MASTER_SIZE | s-2vcpu-2gb
NODE_SIZE | s-2vcpu-4gb
NODE_COUNT | 3
TAG_PREFIX | k8s

# Acknowledgements

Uses bits and pieces from following projects:

- https://github.com/janakiramm/do-k8s
- https://github.com/kelseyhightower/kubernetes-the-hard-way
- https://github.com/kris-nova/kubicorn
