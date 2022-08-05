# A demo on `PodDisruptionBudget`

This is a very simple project to demonstrate the capability of `PodDisruptionBudget`.

## Prerequisite

K8s server version 1.21 or above, else use apiVersoin: policy/v1beta1 in the poddisruptionbudget.yaml file
The cluster should at least have 2 or more nodes, if you want to drain the nodes (steps 4 - 7)

## Usage

1. Create the deployment `kubectl create -n <yourNameSpace> -f nginx-deploymnet.yaml`
2. You should have 2 `nginx` pods running. 
3. Use `kubectl get deploy -n <yourNameSpace>` to verify if they are scheduled.
4. Open up another terminal, and run `watch kubectl get pod`. 
   1. You should be able to see a list of all running pods. 
   2. The list should be refreshing once every few seconds. 
6. Now, take note of the node (_node 1_ from now on) on which those 4 pods are scheduled. 
7. Do `kubectl drain {{_node 1_}}`.
   1. You may need to supply `--ignore-daemonsets --force`
8. Take notice of the result of `watch kubectl get pod` in another terminal.
   1. Both pods should restart and become `0/1` 
   2. If you try to reach the pods either through a service or by port-forwarding, you should receive `Connection reset.`
   3. After a while, those two pods should be respawned on the other node (_node 2_)
9. Now, recover the state of node by `kubectl undrain {{_node 1_}}`
10. `kubectl apply -f manifests/pdb.yaml`
11. Do `kubectl drain {{_node 2_}}`. 
12. You should see the effect of `PodDisruptionBudget` now. 
