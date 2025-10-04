### Check all workloads in the "default" namespace
```
$ sudo kubebuilder/bin/kubectl get all -n default
NAME                        READY   STATUS    RESTARTS   AGE
pod/demo-677cfb9d49-s4h9j   0/1     Pending   0          118s
```

### Identify the reason why the pod is not running
```
$ sudo kubebuilder/bin/kubectl describe pod/demo-677cfb9d49-s4h9j

Events:
 Type     Reason            Age    From               Message
 ----     ------            ----   ----               -------
 Warning  FailedScheduling  2m18s  default-scheduler  0/1 nodes are available: 1 node(s) had untolerated taint {node.cloudprovider.kubernetes.io/uninitialized: true}. preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling.
```

### The node has a taint. uninitialized=true looks strange for me, because the Node is Ready.
```
$ sudo kubebuilder/bin/kubectl describe node codespaces-4a7786
Name:               codespaces-4a7786
Taints:             node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule
```

### I decided to remove the taint instead of adding toleration to the Deployment
```
$ kubectl taint nodes ip-10-1-2-3.ec2.internal node.cloudprovider.kubernetes.io/uninitialized:NoSchedule-
```

### Deployments is running 
```
$ sudo kubebuilder/bin/kubectl get pods -n default
NAME                    READY   STATUS    RESTARTS   AGE
demo-677cfb9d49-s4h9j   1/1     Running   0          7m38s
```