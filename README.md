# operator-pod-example

Simple operator that labels the pods with `claudiu.go/pod-name`, if they have a specific annotation `claudiu.go/add-pod-name-label=true`

## operator-sdk commands

Might work with other builds, but I only tested these:

```sh
$ operator-sdk version
operator-sdk version: "v1.8.0", commit: "d3bd87c6900f70b7df618340e1d63329c7cd651e", kubernetes version: "1.20.2", go version: "go1.16.4", GOOS: "linux", GOARCH: "amd64"
```

Generate project

```sh
operator-sdk init --domain=claudiu.go --repo=github.com/claudiumocanu/tag-operator
```

Generate the API and Controller(note that we do not need to create a CRD - this will require some changes in the upcoming build/deploy phase)

```sh
operator-sdk create api --group core --version v1 --kind Pod --controller
```

## Run locally

```sh
make run
```

## Spawn some pods and annotate them, to trigger the watcher

Create a Pod:

```sh
kubectl run --image=nginx my-nginx
kubectl get pod my-nginx --show-labels
kubectl annotate pod my-nginx --overwrite claudiu.go/add-pod-name-label=true
kubectl get pod my-nginx --show-labels
```

## Build and deploy in kube

Because this operator does not create a CRD, some changes are required in the normal build process:

- make sure to bump the version in the `Makefile`
- make sure to add the `IMAGE_TAG_BASE`
- generate the manifests and then comment the `- ../crd` base in the `config/default/kustomization.yaml`
