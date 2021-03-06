dockin ipam cni
===

dockin cni used to manager pod network, interact with resource manager(rm), support:
- create single network
- create multiple network
- only support dockin-ipam ipam plugin
- only support bridge to manage network

dockin cni must work with
- dockin-cni, main plugin, used to call bridge to manage network, and communicate with rm
- dockin-ipam, used to assign ip
- bridge, used to manage network

cni configuration
--
configuration sample
```
{
    "cniVersion": "0.2.0",
    "name": "dockin-cni",
    "type": "dockin-cni",
    "confDir": "/etc/cni/dockin/net.d",
    "binDir": "/opt/cni/bin",
    "logFile": "/data/kubernetes/dockin-cni.log",
    "logLevel": "debug",
    "backend": "http://localhost:8080/rm"
}
```
all the parameters a described as follows:
- cniVersion, support version
- name, the name about this cni plugin
- type, binary execution file, always be dockin-cni
- confDir, the dir about network configuration
- binDir, the binary execution about bridge
- logFile, file to store the dockin-cni's log
- logLevel, log level, support error/info/debug
- backend, the rm service api address

rm data sample
---
```
{
    "code": 0,
    "reqId": "1234",
    "message": "success",
    "data": [
        {
            "type": "test",
            "podIp": "192.168.1.2",
            "subnetMask": "255.255.255.0",
            "gateway": "192.168.1.1",
            "ifName": "eth0",
            "master": true
        },
        {
            "type": "dockin",
            "podIp": "192.168.2.2",
            "subnetMask": "255.255.255.0",
            "gateway": "192.168.1.1",
            "ifName": "net0",
            "master": false
        }
    ]
}
```
in the sample:
- code, indicates the response code, 0 means success, otherwise failed
- message, indicates the description about this response, success or failed message
- data, is the array about network information
    - type, network type, 
    - podIp, the ip which will assign to this network
    - subnetMask, mask about this network
    - gateway, the gateway about this network
    - ifName, the network device name about this network, which will show in ifconfig or ip a
    - master, weather the main network, which will show in kubectl get pods, and this must be single in a pod
    
network configuration
---
network configuration is the bridge configuration, for more details:
>https://github.com/containernetworking/plugins/tree/master/plugins/main/bridge

network configuration are json files which stored in binDir set in the cni configuration.
and will pass to kubelet create network.

```
{
  "cniVersion": "0.2.0",
  "name": "dockin",
  "type": "bridge",
  "bridge": "br1"
}
``` 

- cniVersion, indicate the version support in this cni
- name, the network name, must be same with the rm type
- type, only support bridge to manage network
- bridge, the bridge name about this network, multiple network can assign different bridge name
