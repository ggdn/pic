# Installation Kubernetes

```bash
$ minikube start --cpus 3 --memory 9500 --insecure-registry="registry.192.168.99.100.nip.io:443" --kubernetes-version=v1.11.2
$ minikube addons enable ingress
$ minikube addons enable heapster
$ minikube docker-env
$ eval $(minikube docker-env)
$ helm init
$ kubectl apply -f namespace.yaml
$ kubectl create clusterrolebinding permissive-binding --clusterrole=cluster-admin --user=admin --user=kubelet --group=system:serviceaccounts:env-production
```

## EFK
```bash
$ kubectl create -f ./fluentd-elasticsearch/es-statefulset.yaml
$ kubectl create -f ./fluentd-elasticsearch/es-service.yaml
$ kubectl create -f ./fluentd-elasticsearch/fluentd-es-configmap.yaml
$ kubectl create -f ./fluentd-elasticsearch/fluentd-es-ds.yaml
$ kubectl create -f ./fluentd-elasticsearch/kibana-deployment.yaml
$ kubectl create -f ./fluentd-elasticsearch/kibana-service.yaml
```

## Jenkins
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout jenkins.192.168.99.100.nip.io.key -out jenkins.192.168.99.100.nip.io.crt -subj "//CN=jenkins.192.168.99.100.nip.io\O=jenkins.192.168.99.100.nip.io"
$ kubectl create secret tls jenkins-tls --key jenkins.192.168.99.100.nip.io.key --cert jenkins.192.168.99.100.nip.io.crt --namespace=pic
$ kubectl apply -f jenkins-volume-helm.yaml
$ helm install --name jenkins -f jenkins-helm.yaml stable/jenkins --namespace pic
```

Pour avoir le mot de passe de l'utilisateur 'admin' :
```bash
printf $(kubectl get secret --namespace pic jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

## Docker registry
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout registry.192.168.99.100.nip.io.key -out registry.192.168.99.100.nip.io.crt -subj "//CN=registry.192.168.99.100.nip.io\O=registry.192.168.99.100.nip.io"
$ kubectl create secret tls registry-tls --key registry.192.168.99.100.nip.io.key --cert registry.192.168.99.100.nip.io.crt --namespace=pic
$ kubectl apply -f docker-registry.yaml
```

## Prometheus / Grafana (non utilisé)
```bash
$ helm install --name prometheus stable/prometheus -f prometheus-helm.yaml --namespace monitoring
$ helm install --name grafana stable/grafana -f grafana-helm.yaml --namespace monitoring
$ kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

## KeyClock
```bash
$ helm install --name keycloak-db --set postgresUser=admin --set postgresPassword=password --set postgresDatabase=keycloak-db stable/postgresql --namespace env-production
$ kubectl apply -f keyclock.yaml --namespace=env-production
```


469e1ebe00069fb884371a4c280a0af9112ea852
