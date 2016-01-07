## Kubernetes on Amazon EC2 Container Service

Running Kubernetes on ECS might seeem odd at first, however it provides the use with a very simple way of ensuring that master components are running all the time. If either `kube-scheduler` or `kube-controller-manager` go down, ECS will make sure they are running soon and the user doesn't have to care about this and no need to run extra parts, i.e. the so-called [`podmaster` and another etcd cluster it brings with it](http://kubernetes.io/v1.1/docs/proposals/high-availability.html).

### Setup ECS cluster
```
cd examples/aws-ecs/
./create-cluster.sh
./ecs-deploy-services.sh
./ecs-docker-ps.sh
```

### Login to an instance

```
> ssh_cloud -i weave-ecs-demo-key.pem ec2-user@XXX.compute-1.amazonaws.com
```

Setup Weave environment:
```
$ eval $(weave env)
```
View DNS records for Kubernetes cluster components
```
$ weave status dns
```

### Deploy the Kubernetes app

```
$ docker run -ti weaveworks/kubernetes-anywhere:tools bash -l

# kubectl get nodes
# kubectl create -f /skydns-addon/
# kubectl get pods,rc,services --all-namespaces
# kubectl scale --namespace=kube-system --replicas=3 rc kube-dns-v8
# kubectl get pods --all-namespaces --watch

# kubectl create -f /guestbook-example/
# kubectl get pods --watch
```

If you want to deploy something else, you can just pass a URL to your manifest like this:

```
# kubectl create -f https://example.com/guestbook.yaml
```

### Tear-down the ECS cluster

```
./ecs-remove-services.sh
./delete-cluster.sh
```