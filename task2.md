
### 1. Початковий рівень. Базуючись на досвіді першого завдання:

- згенеруйте 4 маніфести для etcd, kube-apiserver, kube-scheduler та kube-controller-manager
- розгорніть control plane за допомогою kubelet staticPod
- розгорніть кастомний deployment (наприклад, nginx) на 3 реплікі.

Питання: Чому не працює? Як пофіксити?


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