# Kubernetes Guestbook (Simple Variant)

A version of the [Kubernetes Guestbook](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)
application using Pulumi.

This is a straight port of the original YAML, and doesn't highlight advantages of using real languages. For an example
using abstraction to cut down on boilerplate, please see the [variant using components](../components),
also in this repo. It provisions the same set of resources.

## Running the App

Follow the steps in [Pulumi Installation](https://pulumi.io/install/) and [Kubernetes Setup](
https://pulumi.io/quickstart/kubernetes/setup.html) to get Pulumi working with Kubernetes.

Install dependencies:

```sh
npm install
```

Create a new stack:

```sh
$ pulumi stack init
Enter a stack name: testbook
```

This example will attempt to expose the Guestbook application to the Internet with a `Service` of
type `LoadBalancer`. Since minikube does not support `LoadBalancer`, the Guestbook application
already knows to use type `ClusterIP` instead; all you need to do is to tell it whether you're
deploying to minikube:

```sh
pulumi config set guestbook:isMinikube <value>
```

Perform the deployment:

```sh
$ pulumi up
Updating stack 'testbook'
Performing changes:

     Type                           Name                Status      Info
 +   pulumi:pulumi:Stack            guestbook-testbook  created
 +   ├─ kubernetes:apps:Deployment  redis-master        created
 +   ├─ kubernetes:apps:Deployment  frontend            created
 +   ├─ kubernetes:apps:Deployment  redis-slave         created
 +   ├─ kubernetes:core:Service     redis-master        created     1 info message
 +   ├─ kubernetes:core:Service     redis-slave         created     1 info message
 +   └─ kubernetes:core:Service     frontend            created     2 info messages

---outputs:---
frontendIp: "35.232.147.18"

info: 7 changes performed:
    + 7 resources created
Update duration: 40.829381902s

Permalink: https://app.pulumi.com/hausdorff/testbook/updates/1
```

And finally - open the application in your browser to see the running application. If you're running
macOS you can simply run:

```sh
open $(pulumi stack output frontendIp)
```

> *Note*: minikube does not support type `LoadBalancer`; if you are deploying to minikube, make sure
> to run `kubectl port-forward svc/frontend 8080:80` to forward the cluster port to the local
> machine and access the service via `localhost:8080`.

![Guestbook in browser](./imgs/guestbook.png)
