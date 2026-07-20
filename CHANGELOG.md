# Changelog

## 0.1.0 - initial fixture

- Generic container + k8s + IaC linting fixture for the hyperi-ci fleet.
- Path A: a multi-stage, non-root Dockerfile (hadolint gate + droast advisory).
- Path B: a `web` Helm chart, a plain ConfigMap manifest and a small `.tf`
  (kubeconform gate + kube-linter / checkov advisories via
  `hyperi-ci lint-manifests`).
- Stdlib-only Python app, unlinked from scalo / any HyperI library.
