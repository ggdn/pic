# Installation Kubernetes

```bash
$ minikube start --cpus 3 --memory 9500 --insecure-registry="registry.192.168.99.100.nip.io:443"
$ minikube addons enable ingress
$ minikube docker-env
$ eval $(minikube docker-env)
$ helm init
$ kubectl apply -f namespace.yaml
$ kubectl create clusterrolebinding permissive-binding --clusterrole=cluster-admin --user=admin --user=kubelet --group=system:serviceaccounts:env-production
```

## Jenkins
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout jenkins.192.168.99.100.nip.io.key -out jenkins.192.168.99.100.nip.io.crt -subj "//CN=jenkins.192.168.99.100.nip.io\O=jenkins.192.168.99.100.nip.io"
$ kubectl create secret tls jenkins-tls --key jenkins.192.168.99.100.nip.io.key --cert jenkins.192.168.99.100.nip.io.crt --namespace=pic
$ kubectl apply -f jenkins-volume-helm.yaml
$ helm install --name jenkins -f .\jenkins-helm.yaml stable/jenkins --namespace pic
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


469e1ebe00069fb884371a4c280a0af9112ea852
