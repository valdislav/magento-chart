# Magento Kubernetes components

Install minikube. 

Start minikube. And use docker env.

```bash
minikube start
eval $(minikube docker-env)
```

Install helm (cluster should be run to set tiller). [Install guide](https://docs.helm.sh/using_helm/#installing-helm)


## NFS server (optional)

Configure your sources directory as export (/etc/exports) in the NFS server that runs on your host machine. This way containers can mount source code. This is a lot faster than a default VirtualBox shared folder mount. You only have to do this once, the NFS service will load /etc/exports at (re)boot.

NOTE: The Minikube IP can be different after a minikube delete and minikube start command. Make sure that your NFS export contains the correct Minikube IP again.

[More info](http://pietervogelaar.nl/minikube-nfs-mounts)

### Mac OS X
If you do not have python, install it using brew:
```bash
brew install python
```
echo "$(python -c 'import os,sys;print(os.path.realpath(".")')/sources -alldirs -mapall="$(id -u)":"$(id -g))" $(minikube ip)" | sudo tee -a /etc/exports && sudo nfsd restart
Check if the entry is active by executing on your host machine:

```bash
showmount -e 127.0.0.1
```

This should output something like:

```bash
Exports list on 127.0.0.1:
/Absolute/path/to/magento 192.168.99.100
```

There is also variable in values.yml which should be enabled

## Run deployments

Configuration values located in values.yaml

To run all charts execute next command:

```bash
helm install --values values.yaml .
```

(temporary) Magento sources should be cloned to source path in current chart and in checkout subchart as well.

DB password autogenerated in container, to check it list pods:
```bash
kubectl get pods
```

choose one of mysql pods end run bash in it:

```bash
kubectl exec -it release-1-some-mysql-pod bash
```

Observe env variables with prefix MYSQL_

## Clean

To delete releases run:
```
helm delete $(helm list -q)
```

