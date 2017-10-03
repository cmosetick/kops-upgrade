Kubernetes Custom AMI + Upgrade Version
=======================================

### Optional: Set drain and validate variable in your shell

`export KOPS_FEATURE_FLAGS="+DrainAndValidateRollingUpdate"`

For more info see:  
- https://github.com/kubernetes/kops/blob/master/docs/instance_groups.md
- https://github.com/kubernetes/kops/issues/37
- https://github.com/kubernetes/kops/issues/1718


### Make sure the aws-cli profile you are managing the cluster with is active
and set to `[default]`.

### Set your kubectl context to the cluster you want to manage

e.g.  
`kubectl config use-context <cluster-name>`


### Check the version of your current masters and nodes

```
kubectl get nodes -l kubernetes.io/role=master
kubectl get nodes -l kubernetes.io/role=node
```


### Get a list of all the instance groups

`kops get instancegroups --state=s3://<state-bucket>`


### First apply the new AMI to nodes and/or masters

`kops edit ig nodes --state=s3://<state-bucket>`

- Change the yaml in `image` to say the AMI ID of the image you want to use.

e.g.

```
spec:
  image: ami-bd679cc5
```

- Do the same for all master hosts if you want a custom AMI there too.

e.g.
```
kops edit ig master-us-west-2a --state=s3://<state-bucket>
kops edit ig master-us-west-2b --state=s3://<state-bucket>
kops edit ig master-us-west-2c --state=s3://<state-bucket>
```

### Upgrade to newer Kubernetes Release

This step is not required if you just want to run a custom AMI.  
But is important for when you do actually want to upgrade your clusters.

After you have everything running the AMI you want to run, and confirmed all good,  
it is safe to upgrade to a newer version of Kubernetes if desired.

e.g. 1.7.2 to 1.8

`kops edit cluster <cluster-name> --state=s3://<state-bucket>`

- Change the yaml in `kubernetesVersion` line to say the name of the release you want to upgrade to.
- Be sure to select a release number based on the official releases here:  
https://github.com/kubernetes/kubernetes/releases

e.g. `kubernetesVersion: 1.8.0`

### Reference:
- https://github.com/kubernetes/kops/blob/master/docs/instance_groups.md
