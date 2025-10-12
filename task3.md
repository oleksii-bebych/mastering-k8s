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

```
go install sigs.k8s.io/controller-tools/cmd/controller-gen@v0.16.4
go install sigs.k8s.io/controller-runtime/tools/setup-envtest@latest
go install github.com/onsi/ginkgo/v2/ginkgo@latest

KUBEBUILDER_ASSETS=$(setup-envtest use -p path 1.31.x)
export KUBEBUILDER_ASSETS

go test ./... -v

ginkgo -v ./...

?       github.com/oleksii-bebych/mastering-k8s/new-controller  [no test files]
?       github.com/oleksii-bebych/mastering-k8s/new-controller/api/v1alpha1     [no test files]
?       github.com/oleksii-bebych/mastering-k8s/new-controller/controllers      [no test files]
=== RUN   TestMainController
2025-10-12T13:29:26Z    DEBUG   controller-runtime.test-env     starting control plane
2025-10-12T13:29:36Z    DEBUG   controller-runtime.test-env     installing CRDs
2025-10-12T13:29:36Z    DEBUG   controller-runtime.test-env     reading CRDs from path  {"path": "../config/crd/bases"}
2025-10-12T13:29:36Z    DEBUG   controller-runtime.test-env     read CRDs from file     {"file": "apps.newresource.com_newresources.yaml"}
2025-10-12T13:29:36Z    DEBUG   controller-runtime.test-env     installing CRD  {"crd": "newresources.apps.newresource.com"}
2025-10-12T13:29:36Z    DEBUG   controller-runtime.test-env     adding API in waitlist  {"GV": "apps.newresource.com/v1alpha1"}
2025-10-12T13:29:36Z    DEBUG   controller-runtime.test-env     installing webhooks
=== RUN   TestMainController/test_crd_available
=== RUN   TestMainController/test_controller_startup
2025-10-12T13:29:36Z    INFO    Starting EventSource    {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource", "source": "kind source: *v1alpha1.NewResource"}
2025-10-12T13:29:36Z    INFO    Starting Controller     {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource"}
2025-10-12T13:29:36Z    INFO    Starting workers        {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource", "worker count": 1}
=== RUN   TestMainController/test_can_create_newresource
2025-10-12T13:29:37Z    INFO    Stopping and waiting for non leader election runnables
2025-10-12T13:29:37Z    INFO    Stopping and waiting for leader election runnables
2025-10-12T13:29:37Z    INFO    Stopping and waiting for warmup runnables
2025-10-12T13:29:37Z    INFO    Shutdown signal received, waiting for all workers to finish     {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource"}
2025-10-12T13:29:37Z    INFO    All workers finished    {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource"}
2025-10-12T13:29:37Z    INFO    Stopping and waiting for caches
2025-10-12T13:29:37Z    INFO    Stopping and waiting for webhooks
2025-10-12T13:29:37Z    INFO    Stopping and waiting for HTTP servers
2025-10-12T13:29:37Z    INFO    Wait completed, proceeding to shutdown the manager
--- PASS: TestMainController (13.80s)
    --- PASS: TestMainController/test_crd_available (0.00s)
    --- PASS: TestMainController/test_controller_startup (1.00s)
    --- PASS: TestMainController/test_can_create_newresource (2.01s)
PASS
ok      github.com/oleksii-bebych/mastering-k8s/new-controller/test     13.812s
2025/10/12 13:29:40 maxprocs: Leaving GOMAXPROCS=2: CPU quota undefined
Ginkgo detected a version mismatch between the Ginkgo CLI and the version of Ginkgo imported by your packages:
  Ginkgo CLI Version:
    2.26.0
  Mismatched package versions found:
    2.22.0 used by test

  Ginkgo will continue to attempt to run but you may see errors (including flag
  parsing errors) and should either update your go.mod or your version of the
  Ginkgo CLI to match.

  To install the matching version of the CLI run
    go install github.com/onsi/ginkgo/v2/ginkgo
  from a path that contains a go.mod file.  Alternatively you can use
    go run github.com/onsi/ginkgo/v2/ginkgo
  from a path that contains a go.mod file to invoke the matching version of the
  Ginkgo CLI.

  If you are attempting to test multiple packages that each have a different
  version of the Ginkgo library with a single Ginkgo CLI that is currently
  unsupported.
  
=== RUN   TestMainController
2025-10-12T13:29:45Z    DEBUG   controller-runtime.test-env     starting control plane
2025-10-12T13:29:48Z    DEBUG   controller-runtime.test-env     installing CRDs
2025-10-12T13:29:48Z    DEBUG   controller-runtime.test-env     reading CRDs from path  {"path": "../config/crd/bases"}
2025-10-12T13:29:48Z    DEBUG   controller-runtime.test-env     read CRDs from file     {"file": "apps.newresource.com_newresources.yaml"}
2025-10-12T13:29:48Z    DEBUG   controller-runtime.test-env     installing CRD  {"crd": "newresources.apps.newresource.com"}
2025-10-12T13:29:48Z    DEBUG   controller-runtime.test-env     adding API in waitlist  {"GV": "apps.newresource.com/v1alpha1"}
2025-10-12T13:29:48Z    DEBUG   controller-runtime.test-env     installing webhooks
=== RUN   TestMainController/test_crd_available
=== RUN   TestMainController/test_controller_startup
2025-10-12T13:29:48Z    INFO    Starting EventSource    {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource", "source": "kind source: *v1alpha1.NewResource"}
2025-10-12T13:29:48Z    INFO    Starting Controller     {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource"}
2025-10-12T13:29:48Z    INFO    Starting workers        {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource", "worker count": 1}
=== RUN   TestMainController/test_can_create_newresource
2025-10-12T13:29:49Z    INFO    Stopping and waiting for non leader election runnables
2025-10-12T13:29:49Z    INFO    Stopping and waiting for leader election runnables
2025-10-12T13:29:49Z    INFO    Stopping and waiting for warmup runnables
2025-10-12T13:29:49Z    INFO    Shutdown signal received, waiting for all workers to finish     {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource"}
2025-10-12T13:29:49Z    INFO    All workers finished    {"controller": "newresource", "controllerGroup": "apps.newresource.com", "controllerKind": "NewResource"}
2025-10-12T13:29:49Z    INFO    Stopping and waiting for caches
2025-10-12T13:29:49Z    INFO    Stopping and waiting for webhooks
2025-10-12T13:29:49Z    INFO    Stopping and waiting for HTTP servers
2025-10-12T13:29:49Z    INFO    Wait completed, proceeding to shutdown the manager
--- PASS: TestMainController (7.11s)
    --- PASS: TestMainController/test_crd_available (0.01s)
    --- PASS: TestMainController/test_controller_startup (1.00s)
    --- PASS: TestMainController/test_can_create_newresource (2.01s)
PASS

Ginkgo ran 1 suite in 11.394444432s
Test Suite Passed



$ curl -s http://localhost:8080/metrics | head

# HELP certwatcher_read_certificate_errors_total Total number of certificate read errors
# TYPE certwatcher_read_certificate_errors_total counter
certwatcher_read_certificate_errors_total 0
# HELP certwatcher_read_certificate_total Total number of certificate reads
# TYPE certwatcher_read_certificate_total counter
certwatcher_read_certificate_total 0
# HELP controller_runtime_active_workers Number of currently used workers per controller
# TYPE controller_runtime_active_workers gauge
controller_runtime_active_workers{controller="newresource"} 0
# HELP controller_runtime_conversion_webhook_panics_total Total number of conversion webhook panics
```


### 3. Мах: 
- запустити контролер в контрл плеін (в кубері) з leader election: true