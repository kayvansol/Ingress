![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/kubernetes.gif?raw=true)

Setting up Ingress controller NGINX along with HAproxy for Microservice deployed inside Kubernetes cluster (Bare-metal servers) is in progress :

General Design (big picture) :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/Plan.jpg?raw=true)

All files needed for deployments are available at Manifests folder.

The control-plane & worker nodes addresses are :
```
192.168.56.115
192.168.56.116
192.168.56.117
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/vmnet.png?raw=true)

and HAProxy as a Load Balancer :
```
192.168.56.118
```

Kubernetes cluster nodes :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/nodes.png?raw=true)


The Steps :

1) Install NGINX Ingress Controller from :
```
https://docs.k0sproject.io/main/examples/nginx-ingress
```

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.3/deploy/static/provider/baremetal/deploy.yaml

kubectl get all --namespace=ingress-nginx
```
Ingress-Nginx workloads (ingress port is 30798) :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/Nginx%20Ingress.png?raw=true)

***
2) On the node, where the POD will be located (node1 and node2 in our case) :
```
DIRNAME="vol1"
mkdir -p /mnt/disk/$DIRNAME 
chcon -Rt svirt_sandbox_file_t /mnt/disk/$DIRNAME
chmod 777 /mnt/disk/$DIRNAME
```

***
3) Deploy the Storage Class & PV & PVC :
```
kubectl apply -f storageClass.yaml

kubectl apply -f persistentVolume.yaml
kubectl apply -f persistentVolume1.yaml

kubectl apply -f persistentVolumeClaim.yaml
kubectl apply -f persistentVolumeClaim1.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/pvc.png?raw=true)

***
4) Deploy the web apps :
```
kubectl apply -f http-pod.yaml
kubectl apply -f http-pod1.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/deploymentds.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/pods.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/PodDesciption.png?raw=true)


Get pod ip & curl the related web app :
```
POD_IP=$(kubectl get pod www2-c5644ff98-trk4d  -o yaml | grep podIP | awk '{print $2}'); echo $POD_IP

curl $POD_IP
```

***
5) Deploy the Services :
```
kubectl apply -f IngressService.yaml
kubectl apply -f IngressService1.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/svc.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/svcDesc.png?raw=true)

***
6) Deploy the Ingress resource :
```
kubectl apply -f Ingress.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/ingress.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/ingressDesc.png?raw=true)

***
7) HAProxy config as a Load Balancer (On 192.168.56.118) :
```
sudo nano /etc/haproxy/haproxy.cfg
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/haproxy.png?raw=true)

***
8) DNS Record (On DNS Server) :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/dns.png?raw=true)

***
The final results are 🍹 :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/web.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/app.png?raw=true)
