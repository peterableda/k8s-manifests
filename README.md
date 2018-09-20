# k8s-manifests
Collection of Kubernetes manifests


## pypiserver
[pypiserver](https://github.com/pypiserver/pypiserver) is a minimal [PyPI](http://pypi.python.org/) compatible server.

### Setup
Create a directory on the host to store the package files:
```
mkdir -p /var/pypi/simple

# download an example package to your new directory
wget -P /var/pypi/simple/ https://files.pythonhosted.org/packages/b4/62/79a5aa98d2db64eb4925e7ae7b9de1fa9f2e78050b5410a69371ba13a86f/pyjokes-0.5.0.tar.gz
```

#### Start an unsecure pypiserver
Create a K8s deployment and a NodePort service for the pypiserver
```
kubectl apply -f pypiserver-deployment.yaml
kubectl apply -f pypiserver-nodeport-service.yaml
```

You can open the http://ip:30036/ url to access the pypiserever UI.

#### Start an secure pypiserver
Generate a new key, certificate and dhparam for your TLS proxy and create a K8s secret from it:
```
kubectl create secret generic pypiserver-ssl-secret --from-file=proxykey=keyfile --from-file=proxycert=crtfilr --from-file=dhparam=dhparamfile
```

Create a K8s deployment and a NodePort service for the pypiserver
```
kubectl apply -f pypiserver-tls-deployment.yaml
```

You can open the https://ip:30038/ url to access the pypiserever UI.

**Note**: As an MVP I deploy a sidecar [proxy](https://github.com/peterableda/nginx-ssl-proxy) container responsible for the TLS termination.

#### Start pypiserver and do TLS termination on the Ingress level
Create a K8s deployment and a ClusterIP service with Ingress rule configured for the pypiserver
```
kubectl apply -f pypiserver-deployment.yaml
kubectl apply -f pypiserver-ingress-service.yaml
```
