Shows how you can establish path based routing with ingress rules for nginx server and httpd server using nginx ingress controller. I have used helm for installing nginx ingress controller.

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
