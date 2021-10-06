# Pod with mulitple network interfaces

Contrail Networking has a functionality to provide multiple interfaces for
pods. When creating a pod or namespace you have to specify networks via
annotation. Also, you have to create NetworkAttachmentDefinition resource for
each additional network.

```
cat <<EOF | kubectl create -f -
---
apiVersion: "k8s.cni.cncf.io/v1"
kind: NetworkAttachmentDefinition
metadata:
 name: red-net
 annotations: {
   "opencontrail.org/cidr" : "20.20.20.0/24",
   "opencontrail.org/ip_fabric_snat": "true"
  }
spec:
 config: '{
   "cniVersion": "0.3.1",
   "type": "contrail-k8s-cni"
}'
EOF
```

```
cat <<EOF | kubectl create -f -
---
apiVersion: "k8s.cni.cncf.io/v1"
kind: NetworkAttachmentDefinition
metadata:
 name: green-net
 annotations: {
   "opencontrail.org/cidr" : "30.30.30.0/24",
   "opencontrail.org/ip_fabric_snat": "true"
  }
spec:
 config: '{
   "cniVersion": "0.3.1",
   "type": "contrail-k8s-cni"
}'
EOF
```

```
cat <<EOF | kubectl create -f -
---
apiVersion: v1
kind: Pod
metadata:
 name: multi-intf-pod
 annotations:
   k8s.v1.cni.cncf.io/networks: '[
     { "name": "red-net" },
     { "name": "green-net" }
   ]'
spec:
 containers:
   - name: ubuntuapp
     image: ubuntu-upstart
EOF
```
