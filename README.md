# Multi-cluster Service APIs

This repository hosts the Multi-Cluster Service APIs. Providers can import packages in this repo to ensure their multi-cluster service controller implementations will be compatible with MCS data planes.

This repo contains the initial implementation according to [KEP-1645][kep].

[kep]: https://github.com/kubernetes/enhancements/tree/master/keps/sig-multicluster/1645-multi-cluster-services-api

## Try it out

To see the API in action, run `make demo` to build and run a local demo against
a pair of kind clusters. Alternatively, you can take a self guided tour. Use:

- `./scripts/up.sh` to create a pair of clusters with mutually connected networks
  and install the `mcs-api-controller`.

  _This will use a pre-existing controller image if available, it's recommended
  to run `make docker-build` first._
- `./demo/demo.sh` to run the same demo as above against your newly created
  clusters (must run `./scripts/up.sh` first).
- `./scripts/down.sh` to tear down your clusters.

## Inspect service publication destinations

If you operate an exporting cluster, use `ServiceExport.status.clusters` to see
which clusters your service information is made available to. The MCS
implementation populates this status. For example, inspect `payments/api` on
`cluster-a`:

```sh
kubectl --context cluster-a -n payments get serviceexport api -o yaml
```

An implementation that reports destinations might return the following fields:

```yaml
apiVersion: multicluster.x-k8s.io/v1beta1
kind: ServiceExport
metadata:
  name: api
  namespace: payments
status:
  clusters:
    - cluster: cluster-b
    - cluster: cluster-c
```

Here, `cluster-a` makes the service information available for `cluster-b` and
`cluster-c` to discover and import. `cluster-b` remains listed even if it has no
`payments` namespace or ignores or rejects the export. The exporting
implementation does not need to inspect remote namespaces or import state to
report these destinations.

Destination reporting is optional. An absent or empty list does not imply that
the service has no destinations. For the field definition, see
[`ServiceExportStatus`](pkg/apis/v1beta1/serviceexport.go). To inspect the source
clusters contributing to an imported service instead, use
[`ServiceImport.status.clusters`](pkg/apis/v1beta1/serviceimport.go).

## Community, discussion, contribution, and support

Learn how to engage with the Kubernetes community on the [community page](http://kubernetes.io/community/).

You can reach the maintainers of this project at:

- [Slack](https://kubernetes.slack.com/messages/sig-multicluster)
- [Mailing List](https://groups.google.com/forum/#!forum/kubernetes-sig-multicluster)

[Our meeting schedule is here]( https://github.com/kubernetes/community/tree/master/sig-multicluster#meetings)


## Technical Leads

- @pmorie
- @jeremyot

### Code of conduct

Participation in the Kubernetes community is governed by the [Kubernetes Code of Conduct](code-of-conduct.md).
