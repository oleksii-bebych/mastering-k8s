### 1. Початківці: 
- використовуючи гайд, створити, збілдати та запустити свій перший контролер.

```
$ kubectl apply -f config/crd/bases
customresourcedefinition.apiextensions.k8s.io/newresources.apps.newresource.com created

$ kubectl get crd
NAME                                CREATED AT
newresources.apps.newresource.com   2025-10-12T11:36:28Z

$ kubectl get newresource example-resource -o yaml
apiVersion: apps.newresource.com/v1alpha1
kind: NewResource
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps.newresource.com/v1alpha1","kind":"NewResource","metadata":{"annotations":{},"name":"example-resource","namespace":"default"},"spec":{"foo":"hello world"}}
  creationTimestamp: "2025-10-12T11:44:26Z"
  generation: 1
  name: example-resource
  namespace: default
  resourceVersion: "888"
  uid: 2a5cf37b-48c2-4587-9db8-13831085e4af
spec:
  foo: hello world
status:
  ready: true
```


### 2. Просунутий рівень: 
- розібратися з тестами, отримати метрики контролера




### 3. Мах: 
- запустити контролер в контрл плеін (в кубері) з leader election: true