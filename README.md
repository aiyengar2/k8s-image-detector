#### k8-image-detector

Includes a simple Helm file under `templates/validate-image-hashes.yaml` that takes in a list of `imageConflicts` under the field `global.cattle.imageConflicts`, where each `imageConflict` is a key containing the container hash and a value containing a human-readable identifier for that container hash.

This chart isn't actually meant to be used itself; instead, it's meant to store the `templates/validate-image-hashes.yaml` which can be placed into other charts that want to deny deploying themselves if certain image hashes exist in the cluster (e.g. avoid deploying Prometheus Operator if one of Prometheus Operator's images already exists within the cluster at the time of installation).

#### Example

I place the following in my values.yaml

```
global:
  cattle:
    imageConflicts:
      67da37a9a360e600e74464da48437257b00a754c77c40f60c65e4cb327c34bd5: coredns
```


On a helm install / upgrade, it uses the Helm `lookup` function to run through all the pods and containers within the cluster and outputs:
```
$ helm upgrade --install test .
WARNING: "kubernetes-charts.storage.googleapis.com" is deprecated for "stable" and will be deleted Nov. 13, 2020.
WARNING: You should switch to "https://charts.helm.sh/stable" via:
WARNING: helm repo add "stable" "https://charts.helm.sh/stable" --force-update
Release "test" does not exist. Installing it now.
Error: execution error at (k8s-image-detector/templates/validate-image-hashes.yaml:8:5): Cannot deploy Helm chart since pod coredns-66bff467f8-2xcwg has container coredns with imageHash 67da37a9a360e600e74464da48437257b00a754c77c40f60c65e4cb327c34bd5 (identifed as coredns) that conflicts with the installation of this chart.
```