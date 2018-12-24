# Kubernetes on Raspberry PI

[Kubernetes on (vanilla) Raspbian Lite](https://gist.github.com/alexellis/fdbc90de7691a1b9edb545c17da2d975)


```sh

echo '
127.0.0.1       localhost
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters

192.168.1.110         rp0
192.168.1.111         rp1
192.168.1.112         rp2
192.168.1.141         rpb01
192.168.1.142         rpb02
192.168.1.143         rpb03
192.168.1.144         rpb04
' | sudo tee /etc/hosts


```


# Kubectl

```sh

kubectl --namespace=kube-system get pods

```

# Kubernetes Dashboard

[How do I remove the Kubernetes dashboard pod from my deployment on Google Cloud Platform?](https://stackoverflow.com/questions/46173307/how-do-i-remove-the-kubernetes-dashboard-pod-from-my-deployment-on-google-cloud)

> To have a clean removal you have to delete a lot of objects, just try to execute this to see how many they are:

```sh
kubectl get secret,sa,role,rolebinding,services,deployments --namespace=kube-system | grep dashboard
```
At time of writing to remove everything, I did this:

```sh
kubectl delete deployment kubernetes-dashboard --namespace=kube-system
kubectl delete service kubernetes-dashboard  --namespace=kube-system
kubectl delete role kubernetes-dashboard-minimal --namespace=kube-system
kubectl delete rolebinding kubernetes-dashboard-minimal --namespace=kube-system
kubectl delete sa kubernetes-dashboard --namespace=kube-system
kubectl delete secret kubernetes-dashboard-certs --namespace=kube-system
kubectl delete secret kubernetes-dashboard-key-holder --namespace=kube-system
```

(freedev)



From: https://gist.github.com/alexellis/fdbc90de7691a1b9edb545c17da2d975#gistcomment-2793682

```sh
# Standard:
# Dashboard URL: http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
# kubectl apply -f  https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard-arm.yaml

# TLS Disabled:
#
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/alternative/kubernetes-dashboard-arm.yaml
# run on master:
# kubectl proxy --address 0.0.0.0 --accept-hosts '.*'
# Dashboard url: http://rpb01:8001/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/


```

