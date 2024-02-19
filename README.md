# RKE Lab

## 0. Prerequisites
Setup 3 Ubuntu VMs (22.04) on VirtualBox
- Install Docker
- configure ssh access from host
- disable swap

Topology:
- master : 192.168.194.237
- worker1: 192.168.194.107
- worker2: 192.168.194.41

## 1. Setup Cluster
Define cluster.yml file.
Run:

```sh
rke up
```

Install rancher, launch with docker-compose:

```sh
docker-compose up
```

Add cluster to rancher:

```sh
kubectl create clusterrolebinding cluster-admin-binding --clusterrole cluster-admin --user david
curl --insecure -sfL https://192.168.194.200:8443/v3/import/g8q6q2ln64hbs6lh6l7h5ckdsnn2pwqjxdx2ltn2sqpmp542cmzmmx_c-m-sdt59kw5.yaml | kubectl apply -f -
```

## 2. Deploy app

nginx ingress controller was installed with rke.

Create namespace:

```sh
k apply -f ns.yml
```

Create container:

```sh
docker build . -t davnerson/my-flask-app
docker push davnerson/my-flask-app
```

Create manifests and deploy:

```sh
k apply -f manifest.yml
```

Create snapshot:

```sh
rke etcd snapshot-save --name snapshot.db --config cluster.yml
```

Recover:

```sh
rke etcd snapshot-restore --name snapshot.db --config cluster.yml
```
