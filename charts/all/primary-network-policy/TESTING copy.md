# Summary
1. The ob

# Steps

1. Install nmstate operator using the ui.

2. Get nmstate running `oc apply -f ./nmstate.yaml`

2. Patch the network operator to allow multi-network policies
`oc patch network.operator.openshift.io cluster --type=merge --patch-file=multinetwork-enable-patch.yaml`
coc