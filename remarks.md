# preparation

vagrant plugin install vagrant-disksize



# Setup

The scripts are adapated from https://github.com/stblassitude/k3s-rook with flannel  disabled.

`vagrant up`

`cp dynamic-config/config ~/.kube/config`

# Enable Ingress

Add the following to /etc/hosts to test the ingress

```
192.168.33.20   whoami.192.168.33.20.xip.io
192.168.33.20   kubernetes-dashboard.192.168.33.20.xip.io
192.168.33.20   traefik.192.168.33.20.xip.io
192.168.33.20   ceph-dashboard.192.168.33.20.xip.io
```

# Network Policy

After vagrant up, setup calico as follows.

https://docs.projectcalico.org/getting-started/kubernetes/k3s/multi-node-install

Run the demo

https://docs.projectcalico.org/security/tutorials/kubernetes-policy-demo/kubernetes-demo

