**Example simple k8s deployment with 2 nginx containers**

This examples .yml-manifests for k8s:
- deployment-nginx.yml - main file (manifest) for deploy
- configmap-nginx.yml -  add config in container. This file storaging sepate for economy space in deployment-file
- service-nginx.yml - load balancer for nginx's pods (Internal network only) 
- ingress-nginx.yml - Port forwarding from internet to our pods

We can use only one file - deployment-nginx.yml. But we need copy content other files in deployment-nginx.yml


**System requirenmets:**
- Linux machine or VM with nested virtualization (Minimum: 4GB RAM, 2CPUs)
- Minikube (verison v1.25 or higher) with enables ingress addons
`minikube addons enable ingress`


**How to provision deployment**
1. Clone this repository `git clone https://github.com/tv1n94/k8s-examples`
2. Go to ansible directory `cd k8s-examples`
3. Starting minikube: `minikube start`
4. Add ingress minikube addons: `minikube addons enable ingress`
5. Add/Apply configmap nginx: `kubectl apply -f configmap-nginx.yml`
6. Add/Apply deployment nginx: `kubectl apply -f deployment-nginx.yml`
7. Add/Apply load balancer for nginx: `kubectl apply -f service-nginx.yml`
8. Add/Apply ingress from Internet to nginx: `kubectl apply -f ingress-nginx.yml`
9. Check ip address ingress controller: `kubectl get ingress`
```bash
➜  k8s-examples kubectl get ingress
NAME           CLASS   HOSTS                 ADDRESS        PORTS   AGE
ingres-nginx   nginx   nginx.cluster.local   192.168.49.2   80      112s
➜  k8s-examples 
```
10. Add dns record to /etc/hosts: `echo -e "192.168.49.2 nginx.cluster.local" >> /etc/hosts`
11. Check pods status: `kubectl get pods`
```bash
➜  k8s-examples kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
deployment-nginx-85c58b6db9-wfbxh   1/1     Running   0          4m28s
deployment-nginx-85c58b6db9-wxmpp   1/1     Running   0          4m28s
➜  k8s-examples 
```
12. Use curl to check lb and ingress: `curl nginx.cluster.local`
```bash
➜  k8s-examples curl nginx.cluster.local
deployment-nginx-85c58b6db9-wxmpp
➜  k8s-examples curl nginx.cluster.local
deployment-nginx-85c58b6db9-wfbxh
➜  k8s-examples
```


**How to delete this deployment**
1. Delete deployment: `kubectl delete deployment deployment-nginx `
2. Delete load balancer: `kubectl delete service service-nginx`
3. Delete ingress: `kubectl delete ingress ingress-nginx`
4: Delete configmap: `kubectl delete configmap configmap-nginx`
