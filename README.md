# kubectl-freebsd - How to build and run kubectl on FreeBSD

# TLDR - The easy way

```bash
$ fetch https://github.com/dcasati/kubectl-freebsd/releases/download/release-1.9/kubectl
$ chmod +x kubectl
$ sudo mv kubectl /usr/local/sbin
```

# Manual build process

## Prerequisites
* FreeBSD 11.1 (confirmed to work)
* The Kubernetes source code
* golang 1.9.2

## Building Kubectl
Install the required packages

```bash
$ sudo pkg install go
```

Fetch the Kubernetes source code

```bash
$ go get -v -d k8s.io/kubernetes
```

Next, `cd` to the kubernetes directory and install kubectl
```bash
$ cd go/src/k8s.io/kubernetes/cmd/kubectl
```

Finally build and install kubectl

```bash
$ go install .
```

`kubectl` is now availabe at `$GOPATH/bin`.

Move that to `/usr/local/sbin`

```bash
sudo mv $GOPATH/bin/kubectl /usr/local/sbin
```
## Testing

To test, run some basic `kubectl` commands.

Checking the current version:

```bash
[dcasati@diego /usr/home/dcasati]$ kubectl version
Client Version: version.Info{Major:"", Minor:"", GitVersion:"v0.0.0-master+$Format:%h$", GitCommit:"$Format:%H$", GitTreeState:"", BuildDate:"1970-01-01T00:00:00Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"freebsd/amd64"}
Server Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.2", GitCommit:"bdaeafa71f6c7c04636251031f93464384d54963", GitTreeState:"clean", BuildDate:"2017-10-24T19:38:10Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
[dcasati@diego /usr/home/dcasati]$
```

Running commands against a pod:

```bash
[dcasati@diego /usr/home/dcasati]$ kubectl get pods
NAME                                     READY     STATUS             RESTARTS   AGE
alpine-875084182-h7jz9                   1/1       Running            0          19d
api-server-deployment-56cfb7d46c-hrfd2   0/1       ImagePullBackOff   0          5d
azure-visualizer-1640327816-8njmd        1/1       Running            0          19d
freebsd-sidecar-556972417-stnds          1/1       Running            0          19d
[dcasati@diego /usr/home/dcasati]$
```
Getting information about the cluster:

```bash
[dcasati@diego /usr/home/dcasati]$ kubectl get node
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-97650676-0   Ready     agent     19d       v1.8.2
aks-nodepool1-97650676-1   Ready     agent     19d       v1.8.2
aks-nodepool1-97650676-3   Ready     agent     19d       v1.8.2
```

## Installing Azure CLI [Optional for testing]

Install python

```bash
$ sudo pkg install python
```

Then create a link so python can work:
```bash
$ sudo ln -s /usr/local/bin/python2 /usr/bin/python
```
Finally install Azure CLI

```bash
$ curl -L https://aka.ms/InstallAzureCli | bash
```

## FAQ

1. Question: Editing a resource fails with `Error: /var/tmp/vi.recover/: Permission denied.`
> Answer: This seems to be known and easy to fix. Change your KUBE_EDITOR variable to `vim` or another 
editor of choice.

```bash
export KUBE_EDITOR="vim"
```
