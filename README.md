# ClusterSecret
Introduce Kubernetes ClusterSecret 


Global inter-namespace cluster secrets - Secrets that work across namespaces 

```
apiVersion: v2
kind: ClusterSecret
metadata:
  name: default-wildcard-certifiate
matchNamespace:
  - prefix_ns-*
  - anothernamespace
avoidNamespaces:
  - supersecret-ns
data:
  tls.crt: BASE64
  tls.key: BASE64
```

ClusterSecret operator will make sure all the matching namespaces will have the secret available.
Any change on the secret will be replicated to all the matching namespaces

Inspired in:

 - https://github.com/kubernetes/kubernetes/issues/70147
 - https://github.com/kubernetes/kubernetes/issues/62153

Use it for certificates, registry pulling credentials and so on.

# installation

## tl;dr

```
kubectl apply -f ./yaml
```

## step by step

To instal ClusterSecret operator we need to create (in this order):

 - RBAC resources (avoid if you are not running RBAC) to allow the operator to create/update/patch secrets: yaml/00_
 - Custom resource definition for the ClusterSecret resource: yaml/01_crd.yaml
 - The ClusterSecret operator itself: yaml/02_deployment.yaml
 
 
# quick start:

create a ClusterSecret object yaml like the one above, or in the example in yaml/Object_example/obj.yaml and apply it in your cluster `kubectl apply -f yaml/Object_example/obj.yaml`

The ClusterSecret operator will pick it up and will create a copy in every matching namespace:  match `matchNamespace` but not matching  `avoidNamespaces` RegExp's.

You can specify multiple matching or non-matching RegExp. By default it will match all, same as defining matchNamespace = * 

## Minimal example

```
apiVersion: v2
kind: ClusterSecret
metadata:
  name: global-secret
data:
  username: MTIzNDU2Cg==
  password: Nzg5MTAxMTIxMgo=
```

 
 
# to-do:
 - allow to pass annotations.
 
