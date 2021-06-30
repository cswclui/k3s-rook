# Setup K3s Environment

The scripts are adapted from https://github.com/stblassitude/k3s-rook with flannel  disabled.


## preparation

vagrant plugin install vagrant-disksize

```
vagrant up 
```

copy kubeconfig to host

```
cp dynamic-config/config ~/.kube/config
```

## Resize disk size of the nodes and configure ceph cluster



```
vagrant ssh node-1

sudo resize2fs -p -F /dev/sda1

sudo /vagrant/bin/configure-cluster

exit
```

`kubectl apply -n rook-ceph -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/csi/cephfs/storageclass.yaml`


## Install calico 

After vagrant up, setup calico as follows. Wait for the nodes to be ready.

https://docs.projectcalico.org/getting-started/kubernetes/k3s/multi-node-install

```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml`

kubectl get nodes -o wide
```


Run the demo

https://docs.projectcalico.org/security/tutorials/kubernetes-policy-demo/kubernetes-demo


## Enable Ingress

Add the following to /etc/hosts to test the ingress

```
192.168.33.20   whoami.192.168.33.20.xip.io
192.168.33.20   kubernetes-dashboard.192.168.33.20.xip.io
192.168.33.20   traefik.192.168.33.20.xip.io
192.168.33.20   ceph-dashboard.192.168.33.20.xip.io
```

# install prometheus

## Add repos and install helm chart
```

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update

```

kubectl create ns prometheus

helm install prometheus prometheus-community/kube-prometheus-stack -n prometheus


# Launch Prometheus

kubectl get svc -n prometheus

kubectl port-forward svc/prometheus-kube-prometheus-prometheus 19090:9090 -n prometheus

Access promethues at localhost:19090

## Grafana

Get password of grafana.

 kubectl get secret -n prometheus prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

kubectl port-forward  -n prometheus  svc/prometheus-grafana 9999:80 


- username: admin, pw: prom-operator
- Access grafana at localhost:9999

# ceph dashboard

user: admin
password is in secret rook-ceph/rook-ceph-dashboard-password)


## To Uninstall

helm uninstall prometheus

# others
ceph toolbox

Allow running rootless containers
https://github.com/rootless-containers/usernetes


# Ref

- https://www.youtube.com/watch?v=QoDqxm7ybLc
- https://gitlab.com/nanuchi/youtube-tutorial-series/-/blob/master/prometheus-exporter/install-prometheus-commands.md
- https://hub.kubeapps.com/charts/prometheus-community/prometheus
