# App Toolkit on TCE

* Clone this repo and Download TCE v0.12.0 from the [release page](https://github.com/vmware-tanzu/community-edition/releases).

* Unpack the tar and cd into the directory, Install TCE:
```
./install.sh
```

* Create a TCE unmanaged cluster with 1 control plane and 1 worker
```
tanzu um create tce-test --provider minikube --worker-node-count 1 --control-plane-node-count 1
```
* Install `secretgen` controller
```
tanzu package install secretgen --package-name secretgen-controller.community.tanzu.vmware.com --version 0.7.1 --namespace tkg-system --wait=false
```
* Install `registry-credentials` secret using the Tanzu CLI `secret` plugin.
```
tanzu secret registry add registry-credentials --server REGISTRY_SERVER --username REGISTRY_USERNAME --password REGISTRY_PASSWORD --export-to-all-namespaces --yes
```

For e.g.
```
tanzu secret registry add registry-credentials --server gcr.io --username _json_key --password-file ~/projects/tanzu-install/tap-install/registry-sa.json --export-to-all-namespaces --yes
```

* Update `values.yaml` file and run the following commands to generate `app-toolkit-values.yaml` file
```
ytt --ignore-unknown-comments --data-values-file values.yaml -f template/app-toolkit.yaml > app-toolkit-values.yaml
```

* Install `app-toolkit` controller
```
tanzu package install app-toolkit --package-name app-toolkit.community.tanzu.vmware.com --version 0.2.0 --namespace tanzu-package-repo-global --values-file app-toolkit-values.yaml --wait=false
```

* Create a workload from a sample git repo using Tanzu CLI `apps` plugin.
```
tanzu apps workload create pet-clinic --git-repo https://github.com/sample-accelerators/spring-petclinic --git-tag tap-1.1 --type web --yes
```