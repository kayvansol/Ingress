![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/kubernetes.gif?raw=true)

Setting up Ingress controller NGINX along with HAproxy for Microservice deployed inside Kubernetes cluster is in progress :

General Design :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/Plan.jpg?raw=true)


Kubernetes cluster nodes :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/nodes.png?raw=true)


Install NGINX Ingress Controller from :
```
https://docs.k0sproject.io/v1.23.6+k0s.2/examples/nginx-ingress
```

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/baremetal/deploy.yaml

kubectl get all --namespace=ingress-nginx
```
Ingress-Nginx workloads (ingress port is 30798) :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/Nginx%20Ingress.png?raw=true)


On the node, where the POD will be located (node1 and node2 in our case) :
```
DIRNAME="vol1"
mkdir -p /mnt/disk/$DIRNAME 
chcon -Rt svirt_sandbox_file_t /mnt/disk/$DIRNAME
chmod 777 /mnt/disk/$DIRNAME
```

Deploy the Storage Class & PV & PVC :
```
kubectl apply -f storageClass.yaml

kubectl apply -f persistentVolume.yaml
kubectl apply -f persistentVolume1.yaml

kubectl apply -f persistentVolumeClaim.yaml
kubectl apply -f persistentVolumeClaim1.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/pvc.png?raw=true)


Deploy the web apps :
```
kubectl apply -f http-pod1.yaml
kubectl apply -f http-pod.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/deploymentds.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/pods.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/PodDesciption.png?raw=true)


Get pod ip & curl the related web app :
```
POD_IP=$(kubectl get pod www2-c5644ff98-trk4d  -o yaml | grep podIP | awk '{print $2}'); echo $POD_IP

curl $POD_IP
```

Deploy the Services :
```
kubectl apply -f IngressService.yaml
kubectl apply -f IngressService1.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/svc.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/svcDesc.png?raw=true)


Deploy the Ingress resource :
```
kubectl apply -f Ingress.yaml
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/ingress.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/ingressDesc.png?raw=true)


HAProxy config as a Load Balancer (On 192.168.56.118) :
```
sudo nano /etc/haproxy/haproxy.cfg
```
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/haproxy.png?raw=true)


DNS Record (On DNS Server) :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/dns.png?raw=true)

The final result is 🍹 :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/web.png?raw=true)
![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/app.png?raw=true)
