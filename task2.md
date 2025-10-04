
### 1. Початковий рівень. Базуючись на досвіді першого завдання:

- згенеруйте 4 маніфести для etcd, kube-apiserver, kube-scheduler та kube-controller-manager
- розгорніть control plane за допомогою kubelet staticPod

```
export PATH=$PATH:/opt/cni/bin:kubebuilder/bin
sudo PATH=$PATH:/opt/cni/bin:/usr/sbin /opt/cni/bin/containerd -c /etc/containerd/config.toml &

$ sudo PATH=$PATH:/opt/cni/bin:/usr/sbin kubebuilder/bin/kubelet \
    --kubeconfig=/var/lib/kubelet/kubeconfig \
    --config=/var/lib/kubelet/config.yaml \
    --root-dir=/var/lib/kubelet \
    --cert-dir=/var/lib/kubelet/pki \
    --hostname-override=$(hostname) \
    --pod-infra-container-image=registry.k8s.io/pause:3.10 \
    --node-ip=$HOST_IP \
    --cloud-provider=external \
    --cgroup-driver=cgroupfs \
    --max-pods=4  \
    --v=1 &


$ sudo cp static-pods-manifests/etcd.yaml /etc/kubernetes/manifests/



$ sudo ctr --namespace k8s.io container ls
CONTAINER                                                           IMAGE                                              RUNTIME                  
417a894c9dac9a41a593181699410c3e0657a26cb4bf7d543bc0dde34f96d576    docker.io/hubimage/registry-k8s-io-etcd:3.5.9-0    io.containerd.runc.v2      
 
$ sudo ctr --namespace k8s.io tasks ls
TASK                                                                PID      STATUS    
417a894c9dac9a41a593181699410c3e0657a26cb4bf7d543bc0dde34f96d576    33793    RUNNING



$ sudo cp static-pods-manifests/kube-apiserver.yaml /etc/kubernetes/manifests/



$ sudo ctr --namespace k8s.io container ls
CONTAINER                                                           IMAGE                                              RUNTIME                  
17aaf4882ff88f11eb77add76ebedb7d6c3184edeaa289fb6cecb9537e6a8600    registry.k8s.io/kube-apiserver:v1.30.0             io.containerd.runc.v2 


$ sudo ctr --namespace k8s.io tasks ls
TASK                                                                PID      STATUS    
17aaf4882ff88f11eb77add76ebedb7d6c3184edeaa289fb6cecb9537e6a8600    46664    RUNNING


$ sudo kubebuilder/bin/kubectl get no
NAME                STATUS   ROLES    AGE   VERSION
codespaces-4a7786   Ready    <none>   25s   v1.30.0


$ sudo cp static-pods-manifests/kube-scheduler.yaml /etc/kubernetes/manifests/
$ sudo cp static-pods-manifests/controller-manager.yaml /etc/kubernetes/manifests/


$ sudo kubebuilder/bin/kubectl get po -n kube-system -w
NAME                                        READY   STATUS    RESTARTS   AGE
etcd-codespaces-4a7786                      1/1     Running   0          34m
kube-apiserver-codespaces-4a7786            1/1     Running   0          34m
kube-controller-manager-codespaces-4a7786   1/1     Running   0          5s
kube-scheduler-codespaces-4a7786            1/1     Running   0          9m49s

```

- розгорніть кастомний deployment (наприклад, nginx) на 3 реплікі.
```
sudo kubebuilder/bin/kubectl create deployment nginx --image=nginx --replicas=3

$ sudo kubebuilder/bin/kubectl get po -A -w
NAMESPACE     NAME                                        READY   STATUS    RESTARTS   AGE
default       nginx-bf5d5cf98-9mgcr                       0/1     Pending   0          2s
default       nginx-bf5d5cf98-fgsft                       0/1     Pending   0          2s
default       nginx-bf5d5cf98-x74tp                       0/1     Pending   0          2s
kube-system   etcd-codespaces-4a7786                      1/1     Running   0          40m
kube-system   kube-apiserver-codespaces-4a7786            1/1     Running   0          40m
kube-system   kube-controller-manager-codespaces-4a7786   1/1     Running   0          6m12s
kube-system   kube-scheduler-codespaces-4a7786            1/1     Running   0          15m

$ sudo kubebuilder/bin/kubectl describe po nginx-bf5d5cf98-9mgcr


  Warning  FailedScheduling  15s   default-scheduler  0/1 nodes are available: 1 Too many pods. preemption: 0/1 nodes are available: 1 No preemption victims found for incoming pod.

```

Питання: Чому не працює? Як пофіксити?
```
parameter for Kubelet     --max-pods=4

$ sudo PATH=$PATH:/opt/cni/bin:/usr/sbin kubebuilder/bin/kubelet \
    --kubeconfig=/var/lib/kubelet/kubeconfig \
    --config=/var/lib/kubelet/config.yaml \
    --root-dir=/var/lib/kubelet \
    --cert-dir=/var/lib/kubelet/pki \
    --hostname-override=$(hostname) \
    --pod-infra-container-image=registry.k8s.io/pause:3.10 \
    --node-ip=$HOST_IP \
    --cloud-provider=external \
    --cgroup-driver=cgroupfs \
    --max-pods=50  \
    --v=1 &

$ sudo kubebuilder/bin/kubectl get po -A

NAMESPACE     NAME                                        READY   STATUS    RESTARTS   AGE
default       nginx-bf5d5cf98-9mgcr                       1/1     Running   0          7m42s
default       nginx-bf5d5cf98-fgsft                       1/1     Running   0          7m42s
default       nginx-bf5d5cf98-x74tp                       1/1     Running   0          7m42s
kube-system   etcd-codespaces-4a7786                      1/1     Running   0          48m
kube-system   kube-apiserver-codespaces-4a7786            1/1     Running   0          48m
kube-system   kube-controller-manager-codespaces-4a7786   1/1     Running   0          13m
kube-system   kube-scheduler-codespaces-4a7786            1/1     Running   0          23m
```

### 2. Просунутий рівень.
- розгорніть control plane
- створіть debug privileged container з image verizondigital/kubectl-flame:v0.2.4-perf
- зробіть профілювання kube-apiserver: збір семплів з PID (perf record -F 99 -g -p ...)
- побудуйте flame graph (perf script -i /tmp/out | FlameGraph/stackcollapse-perf.pl | FlameGraph/flamegraph.pl > flame.svg)
- скопіюйте flame.svg з контейнера та збережіть у вашому репо

 
### 3. Мах. 
- розгорніть CCM (Cloud Controller Manager) для свого улюбленого провайдера
- налаштуйте cloud-provider external у конфігурації control plane
- налаштуйте 169.254.169.254/32 на localhost
- створіть сервіс акаунт та налаштуйте cloud.conf та sa.json
- розгорніть mock metadata server
- запустіть cloud-controller-manager
- зареєструйте ноду в клауді
- розгорніть довільний деплоймент
- створіть лоадбалансер та отримайте реальну ip адресу

PS. Реєструвати ноду в кластері не потрібно (але якщо хочеться - ок).
PS. Не забудьте згорнути клауд ресурси по завершеню (якщо вийде).