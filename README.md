
# Intro

Install k0s in china with airgap mode while some images (hosted by `k8s.gcr.io`) are blocked.

# Prerequisites

- Install Multipass for creating a experimental VM: https://multipass.run/docs/installing-on-macos
- Create a experimental VM

```bash
multipass launch --name k0s --mem 4G --disk 20G --cpus 2
```

# Usage

```bash
# connect to the VM
multipass exec k0s -- sudo -i

# install k0s
curl -sSLf https://get.k0s.sh | sudo K0S_VERSION=v1.24.3+k0s.0 sh

# download k0s airgap images builde
mkdir -p /var/lib/k0s/images
curl https://github.com/k0sproject/k0s/releases/download/v1.24.3%2Bk0s.0/k0s-airgap-bundle-v1.24.3+k0s.0-arm64 \
  -o /var/lib/k0s/images/bundle_file

# create k0s.yaml
k0s config create > /etc/k0s/k0s.yaml

# update thie `default_pull_policy` from `IfNotPresent` to `Never`
sed -i "s/default_pull_policy\: IfNotPresent/default_pull_policy: Never/g" /etc/k0s/k0s.yaml

# install k0s controller with single node mode
k0s install controller -c /etc/k0s/k0s.yaml --single

# start k0s cluster
k0s start

# verify k0s cluster status
k0s status

# wait for a while, then check the cluster node status wihch should be `Ready`
k0s kubectl get nodes

# check the cluster pods' status which should be `Running`
k0s kubectl get pods -A
```

# reference

- https://docs.k0sproject.io/v1.24.3+k0s.0/airgap-install/
- https://laogen.cc/multipass-use/