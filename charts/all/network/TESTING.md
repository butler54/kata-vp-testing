# Summary
1. Make sure you check interface names
2. Most likely failure point is the bond configuration

# Steps

1. Install nmstate operator using the ui.

2. Get nmstate running `oc apply -f ./nmstate.yaml`

2. Patch the network operator to allow multi-network policies
`oc patch network.operator.openshift.io cluster --type=merge --patch-file=multinetwork-enable-patch.yaml`

3. Update bond configuration
   1. `bond.yaml` needs to be updated with the free NICs
   2. Ensure LACP is configured as appropriate

4. Apply the `bond.yaml` NEEDS TO BE CONFIGURED WITH THE CORRECT DEVICE NAMES
   1. OR SKIP 
5. Apply the bridge mapping `bridge-mapping.yaml`  NEEDS TO BE CONFIGURED WITH THE CORRECT DEVICE NAME
   1. OR apply `bridge-mapping-single-port.yaml` NEEDS TO BE CONFIGURED WITH THE CORRECT DEVICE NAME
6. Setup the `nad.yaml` for the correct namespace
7. At this stage the NAD should exist
8. Deploy the pods and check connectivity
9. Test connectivity to VMs - only assumes ssh is available which is standard on RHEL
   1.  `oc exec -n ripple hello1 -it -- /bin/bash`
       1.  `curl http://10.0.1.4:22/`
       2.  `curl http://10.0.1.5:22/`
       3.  Both should return an open SSH header
   2.  Repeat for hello2: `oc exec -n ripple hello1 -it -- /bin/bash`
       1.  `curl http://10.0.1.4:22/`
       2.  `curl http://10.0.1.5:22/`
       3.  Both should return an open SSH header
10. Check connectivity from VMs
    1.  Login to VM 1 (or 2):
        1.  `curl http://10.0.1.2:8888/`
        2.  `curl http://10.0.1.3:8888/`
        3.  Result should be 'Hello Openshift!'
11. Apply Default deny:
    1.  All of the results above should result in hangs.
12. Apply:
  1. `oc apply -f ./egress-ipblock.yaml`
  2. `oc apply -f ./ingress-ipblock.yaml`
13. Allowed paths:
  1. VM1 -> POD1 8888 (TCP): `curl http://10.0.1.2:8888/`
  2. POD2 -> VM2 22 TCP:
     1. `oc exec -n ripple hello1 -it -- /bin/bash`
     2. `curl http://10.0.1.5:22/`

## Required nodes for the isolated networks
```
subnet: 10.0.1.0/24
pod hello1: 10.0.1.2
pod hello2: 10.0.1.3
VM 1:  10.0.1.4
VM 2:  10.0.1.5
```



/home/images/bonded-sno