# App Toolkit on TCE

* Clone this repo and Download TCE v0.12.0 from the [release page](https://github.com/vmware-tanzu/community-edition/releases).

* Unpack the tar and cd into the directory, Install TCE:
```
./install.sh
```

* Create a TCE unmanaged cluster
```
tanzu um create tce-test
```
* Install `secretgen` controller
```
tanzu package install secretgen --package-name secretgen-controller.community.tanzu.vmware.com --version 0.7.1 --namespace tkg-system
```
* Install `registry-credentials` secret
```
tanzu secret registry add registry-credentials --server gcr.io --username _json_key --password-file ~/projects/tanzu-install/tap-install/registry-sa.json --export-to-all-namespaces --yes
```

* Update `values.yaml` file and run the following commands to generate `app-toolkit-values.yaml` file
```
ytt --ignore-unknown-comments --data-values-file values.yaml -f template/app-toolkit.yaml > app-toolkit-values.yaml
```

* Install `app-toolkit` controller
```
tanzu package install app-toolkit --package-name app-toolkit.community.tanzu.vmware.com --version 0.2.0 --namespace tanzu-package-repo-global --values-file app-toolkit-values.yaml
```
