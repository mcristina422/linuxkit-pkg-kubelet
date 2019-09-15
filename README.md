# linuxkit-pkg-kubelet
LinuxKit Package for Kubelet

## Usage

For full usage examples see https://github.com/rmb938/k8s-on-linuxkit

### Mounts

See `build.yml` for mount information.

`/run/node` and `/var/node` are created in the root namespace and mounted in various ways to store configuration and database files.

### kubelet

When the kubelet container starts it extracts the CNI plugins into `/opt/cni/bin` that is bind mounted from the root namespace on `/var/node/opt/cni/bin`

Then it waits for the `/var/lib/kubelet/config.yaml` to exist. `/var` is bind mounted from the root namespace on `/var/node/var`.

Then the file `/var/lib/kubelet/kubeadm-flags.env` is sourced in and kubelet is started.

These files can be created via kubeadm or other means.

For more information see `kubelet.sh`

```yaml
- name: kubelet
  image: docker.pkg.github.com/rmb938/linuxkit-pkg-kubelet/kubelet:${VERSION}-amd64
  cgroupsPath: podruntime/kubelet
```

### kubeadm

When the kubeadm container start it waits for `/run/kubeadm-run` (bind mounted from `/run/node`) to be created.

The contents of that file must be `init` or `join`, this tells kubeadm that it should initialize a cluster or join an existing cluster.

Before creating `/run/kubeadm-run` you must first create `/run/kubeadm.yaml`. This file contains [kubeadm configuration](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/control-plane-flags/).

If this file is not created before kubeadm is ran it will fail.

```yaml
- name: kubeadm
  image: docker.pkg.github.com/rmb938/linuxkit-pkg-kubelet/kubelet:${VERSION}-amd64
  cgroupsPath: podruntime/kubeadm
  command: ["/usr/local/bin/kubeadm.sh"]
```
