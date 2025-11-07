# hello-world-helm

This repository packages a static "Hello from the world of Helm!" web page inside a Docker image and ships a Helm chart for deploying it to Kubernetes. It is designed to be a lightweight smoke test for ingress and cluster connectivity.

## Container image

The container image is published to the GitHub Container Registry (GHCR) whenever changes are merged into the `main` branch. The GitHub Actions workflow at [`.github/workflows/publish.yaml`](.github/workflows/publish.yaml) builds the image with Docker Buildx and pushes it to `ghcr.io/<owner>/<repo>` with tags for the branch name and commit SHA.

To build the image locally:

```bash
docker build -t hello-world-helm:local .
```

## Helm chart

The chart lives under [`charts/hello-world`](charts/hello-world). Key configurable values include:

- `image.repository` – container registry and repository (defaults to `ghcr.io/OWNER/REPOSITORY`).
- `image.tag` – tag to deploy (e.g. `main` or a specific SHA).
- `ingress.*` – enable/disable ingress, provide hosts, class name, TLS configuration, and annotations.
- `service.*` – service type and port.

Install the chart (after adjusting values as needed) with:

```bash
helm upgrade --install hello-world ./charts/hello-world \
  --namespace hello-world --create-namespace \
  --set image.repository=ghcr.io/<owner>/<repo> \
  --set image.tag=<tag-to-deploy>
```

## Development

- Edit the static page in [`app/index.html`](app/index.html).
- The [`Dockerfile`](Dockerfile) copies the page into an `nginx:alpine` image and defines a simple health check.
- The Helm templates provide a Deployment, Service, optional ServiceAccount, and optional Ingress with flexible overrides via `values.yaml`.
