pve 192.168.18.252
| nom | ip |
|--- | --- |
|tlsm1 | 192.168.18.241|
|tlsm2 | 192.168.18.242|
|tlsm3 | 192.168.18.243|
|tlsn1 | 192.168.18.244|
|tlsn2 | 192.168.18.245|
|tls33 | 192.168.18.246|


https://www.virtualizationhowto.com/2024/01/proxmox-kubernetes-install-with-talos-linux/#h-install-talos-linux-in-proxmox

https://github.com/siderolabs/talos/releases

https://blog.stephane-robert.info/docs/cloud/outscale/kubernetes-talos/


# Installation kubectl

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

# Installation talosctl

```
curl -sL https://talos.dev/install | sh
```

# Bootstrap du 1er master

## Génération du machine configuration file pour le controle plane

```
CONTROL_PLANE_IP=192.168.18.241
talosctl gen config talos-kluster https://$CONTROL_PLANE_IP:6443 \
--kubernetes-version 1.34.4 --output-dir _out
```

On applique la configuration du node control plane

```
talosctl apply-config --insecure --nodes $CONTROL_PLANE_IP --file controlplane.yaml
```

# Bootstrap du cluster

```
export TALOSCONFIG="talosconfig"
talosctl config endpoint $CONTROL_PLANE_IP
talosctl config node $CONTROL_PLANE_IP
talosctl bootstrap
```

## Récupérer le kubeconfig

```
talosctl kubeconfig .
export KUBECONFIG=kubeconfig
```

# Bootstrap des worker

```
export WORKER_IP=192.168.18.244
talosctl apply-config --insecure --nodes $WORKER_IP --file worker.yaml
```
