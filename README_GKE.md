# Installation Kubernetes

```bash
$ gcloud beta container --project "winter-campus-211113" clusters create "mytest" --zone "us-central1-a" --username "admin" --cluster-version "1.10.7-gke.2" --machine-type "n1-standard-4" --image-type "COS" --disk-type "pd-standard" --disk-size "100" --scopes "https://www.googleapis.com/auth/cloud-platform" --preemptible --num-nodes "1" --enable-cloud-logging --enable-cloud-monitoring --network "projects/winter-campus-211113/global/networks/default" --subnetwork "projects/winter-campus-211113/regions/us-central1/subnetworks/default" --addons HorizontalPodAutoscaling,HttpLoadBalancing,KubernetesDashboard --enable-autoupgrade --enable-autorepair
$ gcloud container clusters get-credentials mytest
$ gcloud container clusters describe mytest | grep password
$ gcloud config config-helper --format=json
$ kubectl create rolebinding client-user-rolebinding --clusterrole=admin --user=client --username admin --password AToT6lKq1HDWICSp
$ kubectl create clusterrolebinding client-admin-crb --clusterrole=admin --user=client --username admin --password AToT6lKq1HDWICSp
$ gcloud projects add-iam-policy-binding winter-campus-211113 --member=user:criblevibes@gmail.com --role=roles/container.admin
$ gcloud compute addresses create web-static-ip --region=us-central1
$ gcloud compute addresses describe web-static-ip --region=us-central1
$ helm init
$ kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default
$ kubectl proxy
$ kubectl apply -f namespace.yaml
$ kubectl create clusterrolebinding permissive-binding --clusterrole=cluster-admin --user=admin --user=kubelet --group=system:serviceaccounts:env-production
$ helm install --name nginx-ingress stable/nginx-ingress -f nginx-helm-values.yaml
```

## EFK
```bash
$ helm repo add akomljen-charts https://raw.githubusercontent.com/komljen/helm-charts/master/charts/
$ helm install --name es-operator \
    --namespace logging \
    --set rbac.create=false \
    akomljen-charts/elasticsearch-operator
$ helm install --name efk --namespace logging akomljen-charts/efk -f fluentd-helm-gke.yaml
$ kubectl apply -f kibana-ingress-gke.yaml -n=logging
```
## Jenkins
```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout jenkins.35.193.56.167.nip.io.key -out jenkins.35.193.56.167.nip.io.crt -subj "//CN=jenkins.35.193.56.167.nip.io\O=jenkins.35.193.56.167.nip.io"
$ kubectl create secret tls jenkins-tls --key jenkins.35.193.56.167.nip.io.key --cert jenkins.35.193.56.167.nip.io.crt --namespace=pic
$ helm install --name jenkins -f jenkins-helm-gke.yaml stable/jenkins --namespace pic
```

Pour avoir le mot de passe de l'utilisateur 'admin' :
```bash
printf $(kubectl get secret --namespace pic jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

## Prometheus / Grafana (non utilisé)
```bash
$ helm install --name prometheus stable/prometheus -f prometheus-helm-gke.yaml --namespace monitoring
$ kubectl apply -f grafana-google-secret.yaml
$ helm install --name grafana stable/grafana -f grafana-helm-gke.yaml --namespace monitoring
$ kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

## KeyClock
```bash
$ helm install --name keycloak stable/keycloak -f keycloak-helm.yaml --namespace env-production
$ kubectl get secret --namespace env-production keycloak-http -o jsonpath="{.data.password}" | base64 --decode; echo
```
