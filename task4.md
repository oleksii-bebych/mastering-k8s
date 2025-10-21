### Початківцям: 
- спробувати побудувати gitops кільце для аплікейшену з imageupdate автоматизацією

```
$ k get pods -A
NAMESPACE              NAME                                                      READY   STATUS    RESTARTS   AGE
default                app-kbot-59b56f7994-7xwrn                                 1/1     Running   0          81s
envoy-gateway-system   envoy-envoy-gateway-system-eg-5391c79d-86576fdcbb-lvmst   2/2     Running   0          105s
envoy-gateway-system   envoy-gateway-797c7c4c-7jmrr                              1/1     Running   0          2m30s
flux-system            flux-operator-c9777d8b5-97gjw                             1/1     Running   0          2m52s
flux-system            helm-controller-84d65fcc75-7776p                          1/1     Running   0          2m21s
flux-system            kustomize-controller-7d79567847-sm2qn                     1/1     Running   0          2m21s
flux-system            notification-controller-5ffc55c4c-f2t8q                   1/1     Running   0          2m21s
flux-system            source-controller-6fb5d9cc5-zzndh                         1/1     Running   0          2m21s
kube-system            coredns-668d6bf9bc-j5lvl                                  1/1     Running   0          3m23s
kube-system            coredns-668d6bf9bc-vbzpf                                  1/1     Running   0          3m23s
kube-system            etcd-preview-control-plane                                1/1     Running   0          3m27s
kube-system            kindnet-564d7                                             1/1     Running   0          3m23s
kube-system            kindnet-8p28m                                             1/1     Running   0          3m18s
kube-system            kindnet-s67m9                                             1/1     Running   0          3m18s
kube-system            kube-apiserver-preview-control-plane                      1/1     Running   0          3m31s
kube-system            kube-controller-manager-preview-control-plane             1/1     Running   0          3m29s
kube-system            kube-proxy-f8gfr                                          1/1     Running   0          3m18s
kube-system            kube-proxy-jhjgr                                          1/1     Running   0          3m18s
kube-system            kube-proxy-knc2c                                          1/1     Running   0          3m23s
kube-system            kube-scheduler-preview-control-plane                      1/1     Running   0          3m31s
local-path-storage     local-path-provisioner-7dc846544d-xbgk9                   1/1     Running   0          3m23s


# Get LoadBalancer IP
LB_IP=$(kubectl get svc -o jsonpath='{.items[?(@.metadata.name matches "envoy-envoy-gateway.*")].status.loadBalancer.ingress[0].ip}' -n envoy-gateway-system)

# Test the main endpoint
curl $LB_IP -HHost:kbot.example.com
Welcome to kbot server!
Version: v2.2.5-452a7af-amd64


```

### На досвіді: 
- розірвати кільцe gitops, перейти на gitless та вирішити проблему imageupdate

### Max: 
- налаштувати ephemeral env https://fluxcd.control-plane.io/operator/resourcesets/github-pull-requests/ 

 

Спробуйте знайти свій власний підхід до реліз флоу конфігурації інфраструктури та аплікейшн!