# servers-ingress

Shows how you can establish path based routing with ingress rules for nginx server and httpd server using nginx ingress controller. 
I have used helm for installing nginx ingress controller.

## Steps

1. `watch kubectl get po --all-namespaces`
2. `helm init`
3. `watch kubectl get po --all-namespaces`
4. `helm install stable/nginx-ingress --name ingress-nginx`
5. `helm ls`
6. `kubectl create deployment --image nginx nginx`  
7. `kubectl scale --replicas=3 deployment.apps/nginx`  
8. `sudo kubectl create service nodeport nginx --tcp=80:80`  
9. `kubectl create -f httpd-deployment.yml`  
10. `kubectl scale --replicas=3 deployment.apps/httpd`  
11. `sudo kubectl create service nodeport httpd --tcp=80:80`  
12. `kubectl get svc`  
13. controlplane $ `curl 10.106.141.1`  
Will show `It works!` httpd webpage.   
14. `curl 10.110.33.212`  
will show the nginx webpage.  
15. controlplane $ `curl 10.105.21.105`  
default backend - 404controlplane  
17. `kubectl create -f ingress.yml`  

## Adding a domain

ingress-rules.yml
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: server-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: subdomain1.myapp.com
    http:
      paths:
      - path: /httpd
        backend:
          serviceName: httpd
          servicePort: 80
      - path: /nginx
        backend:
          serviceName: nginx
          servicePort: 80
   - host: subdomain2.myapp.com
     http:
      paths:
      - path: /httpd
        backend:
          serviceName: httpd
          servicePort: 80
      - path: /nginx
        backend:
          serviceName: nginx
          servicePort: 80
```

## Adding a secret

ingress-rules.yml
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: server-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - myapp.com
    secretName: myapp-secret-tls
  rules:
    - host: myapp.com
      http:
        paths:
        - path: /httpd
          backend:
            serviceName: httpd
            servicePort: 80
        - path: /nginx
          backend:
            serviceName: nginx
            servicePort: 80
```

myapp-secret.yml
```
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secret-tls
  namespace: default
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
type: kubernetes.io/tls
```
