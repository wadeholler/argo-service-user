# ARGO SERVICE USER

The following creates a Kubernetes Service Account for Argo 2.2
and higher workflow submissions, deletions, listing, etc. 

## Dependencies

* Argo 2.2 running in the cluster
* cluster-admin kubeconfig

## Steps

This process involves the following steps:

* create the service account and bind to the argo 
cluster role binding for edit - argo-aggregate-to-edit
* create the kubeconfig for the service account


### Create the Service Account and Binding

the following step presumes that your current KUBECONFIG
has cluster-admin

    kubectl create -f argoSVCUser.yaml

### Customize the kubeconfig.example

    cadata=$(kubectl get secret $(kubectl get sa argosvcuser -o jsonpath={.secrets[0].name}) -o "jsonpath={.data['ca\.crt']}")
    token=$(kubectl get secret $(kubectl get sa argosvcuser -o jsonpath={.secrets[0].name}) -o "jsonpath={.data.token}"|base64 -d -w 0)
    sed -s "s/CADATA/${cadata}/g" kubeconfig.example > temp
    sed -s "s/TOKEN/${token}/g" temp > temp2
    mv temp2 kubeconfig
    rm temp 

### Set the server parameter

Set the server paramter in the `kubeconfig` file to the API endpoint
of your kubernetes cluster

example:


```yaml
clusters:
- cluster:
    certificate-authority-data: CADATA
    # You'll need the API endpoint of your Cluster here:
    server: https://k8s-api-server-dns-name.com:6443
  name: cluster
```

    export KUBECONFIG=kubeconfig
    argo list
