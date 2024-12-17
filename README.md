# SimpleCNI
A CNI made as part of my thesis work. Written in bash based on bash-cni
It can handle multi interface setups with multus.

## Setup
Using multus, place the bash script in the kube node's bin folder. and create a custom network attachment definition like this:
```
apiVersion: "k8s.cni.cncf.io/v1"
kind: "NetworkAttachmentDefinition"
metadata:
    name: "normal"
spec:
  config: '{
        "cniVersion": "0.3.1",
        "name": "simpleCNI",
        "type": "simpleCNI",
        "network": "10.244.3.0/24",
        "subnet": "10.244.3.0/24",
        "args": {
          "cni": {
            "labels": [
              {"key": "bridge_name","value": "custombridge"},
            ]
          }
        }
      }'
```
For pod level annotation support, create a service account with cluster wide pod read access, and place its token in the TOKEN_HERE part of the script.
## Features
All features can be put into either network attachment definition labels, or pod annotations, the naming convention in pod annotations is the network attachment definition, prepended by `local.bash-cni/$CNI_IFNAME.$1`
| Annotation | value | meaning |
| ---------- | ----- | ------- |
| bridge_name | string | the name of the bridge the pod interface should be connected to |
| ip | IP address | the interface's IP address (doesn't have to be unique) |
| mac | MAC address | the interface's mac address (doesn't have to be unique) |
| sysctl.<sysctl address> | sysctl value | custom sysctl setups |
| pair.sysctl.<sysctl address under .net.ipv4.conf.intname...> | sysctl value | custom sysctl setups on the veth pair of the interface |
