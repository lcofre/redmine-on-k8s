# Redmine on Kubernetes
Thanks to Helm, Redmine can be installed with almost no hassle on Kubernetes. This repo explains how to start from an empty cluster on the cloud to deploy Redmine with HTTPS and with PostgreSQL as the database.

To keep components separated we use different namespaces.

## Steps

Install an Ingress controller
```
kubectl create namespace ingress-controller
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx && helm repo update

helm install ingress-controller ingress-nginx/ingress-nginx --namespace ingress-controller
```
You will get a public IP you can retrieve with `kubectl get services --watch`. Create your DNS domain with that IP.

Install cert-manager to manage HTTPS certificates automatically
```
kubectl create namespace cert-manager
helm repo add jetstack https://charts.jetstack.io && helm repo update

helm install cert-manager jetstack/cert-manager --set installCRDs=true --namespace cert-manager
```
Wait for cert-manager to finish installation with `kubectl get services --watch` and then add your email to `cluster-issuer.yaml` so it can be applied to the cluster
```
kubectl apply -f cluster-issuer.yaml
```

Lastly install Redmine with
```
helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update

helm install redmine -f values.yaml bitnami/redmine
```