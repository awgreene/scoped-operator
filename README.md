# scoped-operator

# Build the catalog 

```
make docker-build docker-push IMG="quay.io/agreene/scoped-operator:v0.0.1"
make bundle-build bundle-push IMG="quay.io/agreene/scoped-operator:v0.0.1" BUNDLE_IMG=quay.io/agreene/scoped-operator-bundle:v0.0.1
make catalog-build catalog-push IMG="quay.io/agreene/scoped-operator:v0.0.1" BUNDLE_IMG=quay.io/agreene/scoped-operator-bundle:v0.0.1 CATALOG_IMG=quay.io/agreene/scoped-operator-catalog:v0.0.1 
```

## Create a namespace, operatorgroup, and subscription for the demo
```bash
kubectl apply -f-<<EOF
apiVersion: v1
kind: Namespace
metadata:
  name: demo
---
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  name: demo-operators
  namespace: demo
spec:
  targetNamespaces:
  - default
  - operators
---
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
  name: scoped-catalog
  namespace: demo
spec:
  displayName: Scoped Operators
  image: quay.io/agreene/scoped-operator-catalog:v0.0.1
  sourceType: grpc
EOF
kubectl config set-context --current --namespace=demo


kubectl apply -f-<<EOF
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: scoped-operator-subscription
  namespace: demo
spec:
  channel: "alpha"
  installPlanApproval: Automatic
  name: scoped-operator
  source: scoped-catalog
  sourceNamespace: demo
EOF
```

# Create a bar cr

```bash
kubectl create ns foo
kubectl create deployment bar --image=nginx:1.19 -n foo
kubectl create -f-<<EOF
apiVersion: foo.my.domain/v1alpha1
kind: Bar
metadata:
  name: bar-sample
  namespace: foo
EOF
```
