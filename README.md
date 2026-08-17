# Stupid Git: A simple Gitweb and git-http-backend Helm Chart

This project contains a minimal Helm chart for running a lightweight Gitweb UI and a Git smart HTTP backend in Kubernetes. The Gitweb interface is served under `/ui`, while Git repositories are exposed over HTTP at the root of the webserver for normal Git client operations such as `git clone`, `git fetch`, and `git push`.

## Chart location

- [helm/git-instaweb/Chart.yaml](helm/git-instaweb/Chart.yaml)
- [helm/git-instaweb/values.yaml](helm/git-instaweb/values.yaml)
- [helm/git-instaweb/templates/deployment.yaml](helm/git-instaweb/templates/deployment.yaml)
- [helm/git-instaweb/templates/service.yaml](helm/git-instaweb/templates/service.yaml)

## What it does

- Runs a custom image that includes Git, `lighttpd`, `gitweb`, and the `git-http-backend` CGI service
- Serves the Gitweb browser UI at `/ui` and exposes repositories over smart HTTP at the root URL
- Exposes a simple `ClusterIP` service on port `8080`
- Stores repository data under a configurable hostPath mount by default
- Creates a bare repo at `/srv/git/repo.git` if one does not already exist

## Custom image requirement

`gitweb` is served through `lighttpd` as a CGI application and the Git HTTP backend is exposed through the same webserver. The stock `alpine/git` image does not reliably include the `gitweb` CGI runtime, so this chart uses a Debian-based custom image defined in [docker/git-instaweb/Dockerfile](docker/git-instaweb/Dockerfile) to ensure both the web interface and the HTTP backend are available.

Build it locally before installing the chart:

```bash
docker build -t git-instaweb:latest ./docker/git-instaweb
```

The chart defaults to the published GHCR image built by GitHub Actions:

```yaml
image:
  repository: ghcr.io/ed-ad/git-instaweb
  tag: latest
```

If your cluster is using a different registry or a private image, override those values during install, for example:

```bash
helm install git-instaweb ./helm/git-instaweb \
  --set image.repository=ghcr.io/your-org/git-instaweb \
  --set image.tag=latest
```

## Default configuration

The default hostPath target is:

```yaml
repo:
  hostPath:
    enabled: true
    path: /var/lib/stupid-git/repos
    type: DirectoryOrCreate
```

This path is mounted into the container at `/srv/git`.

## Install

```bash
helm install git-instaweb ./helm/git-instaweb \
  --set repo.hostPath.path=/var/lib/stupid-git/repos
```

## Port-forward

```bash
kubectl port-forward svc/git-instaweb 8080:8080
```

Then open: http://localhost:8080

## Override the repo hostPath

```bash
helm install git-instaweb ./helm/git-instaweb \
  --set repo.hostPath.enabled=true \
  --set repo.hostPath.path=/data/git \
  --set repo.repoPath=myrepo.git
```
