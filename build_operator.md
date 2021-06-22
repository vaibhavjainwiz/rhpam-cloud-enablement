
### Create RHPAM Kogito Operator Build Locally

**Step 1**: Clone RHPAM Kogito Operator Github Repo
```
git clone https://github.com/kiegroup/rhpam-kogito-operator.git
```

**Step 2**: Create Docker Build
```
cd rhpam-kogito-operator
make container-build BUILDER=docker IMG=quay.io/vajain/rhpam-kogito-operator:1.5.x 
make container-push BUILDER=docker IMG=quay.io/vajain/rhpam-kogito-operator:1.5.x
```
Default {BUILDER} is `podman`, as above you could define another builder such as `BUILDER=docker`. Similarly, default RHPAM Kogito Operator image name is `registry.stage.redhat.io/rhpam-7/rhpam-kogito-rhel8-operator:7.11.0` but you could define your custom image name as `IMG=quay.io/vajain/rhpam-kogito-operator:1.5.x`.

**Step 3**: Create Operator Bundle
```
make bundle IMG=quay.io/vajain/rhpam-kogito-operator:1.5.x
make bundle-build BUILDER=docker BUNDLE_IMG=quay.io/vajain/rhpam-kogito-operator-bundle:1.0
make bundle-push BUILDER=docker BUNDLE_IMG=quay.io/vajain/rhpam-kogito-operator-bundle:1.0
```

**Step 4**: Create Operator Catalog
```
make catalog-build BUILDER=docker BUNDLE_IMG=quay.io/vajain/rhpam-kogito-operator-bundle:1.0 CATALOG_IMG=quay.io/vajain/rhpam-kogito-operator-catalog:1.0
make catalog-push BUILDER=docker CATALOG_IMG=quay.io/vajain/rhpam-kogito-operator-catalog:1.0
```

**Step 5**: Create Catalog Source
```
apiVersion: operators.coreos.com/v1alpha1
kind: CatalogSource
metadata:
    name: rhpam-operators-catalog-source
    namespace: openshift-marketplace
spec:
    sourceType: grpc
    image: quay.io/vajain/rhpam-kogito-operator-catalog:1.0
    displayName: RHPAM Kogito Operator
```

### Install RHPAM Kogito Operator

Then navigate to the Operators->OperatorHub in the OpenShift console and in the Provider Type select the RHPAM Kogito Operator.

![RHPAM Kogito Operator in Operator hub](./kogito_operator.png?raw=true)

and install a 7.x version.
This makes the RHPAM Kogito Operator custom resource available, that the application developer will use later.

or you could create below Subscription.
```
API Version:  operators.coreos.com/v1alpha1
Kind:         Subscription
Name:         rhpam-kogito-operator
Namespace:    namespace-placeholder
Spec:
  Channel:                7.x
  Name:                   rhpam-kogito-operator
  Source:                 rhpam-operators-catalog-source
  Source Namespace:       openshift-marketplace
  Starting CSV:           rhpam-kogito-operator.v7.11.0
```