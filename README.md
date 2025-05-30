# Workflows

## Push Container

Build and push amd64 and arm64 containers to a registry. It is made for easy integration with [Container Cubby](https://github.com/rosvik/container-cubby), which is why it only supports basic auth for now.

> [!IMPORTANT]
> This workflow uses the [`ubuntu-24.04-arm`](https://docs.github.com/en/actions/using-github-hosted-runners/using-github-hosted-runners/about-github-hosted-runners#standard-github-hosted-runners-for-public-repositories) runner, which is only available for public repositories by default. If you don't have access to it, you can use (the much slower) [`push-container-qemu.yml`](.github/workflows/push-container-qemu.yml) workflow instead — usage instructions are the same.

### Inputs

- `containerfile`: Path to the Containerfile to build. Defaults to `Containerfile`.
- `image`: The name of the image to build. E.g. `rosvik/hello`
- `tag`: The tag to build the image with. Defaults to the branch name or git tag that triggered the workflow. Based on this, three tags are pushed: `{tag}`, `{tag}-amd64`, and `{tag}-arm64`, where `{tag}` will be a multi-arch manifest.
- `build_args`: Optional build arguments to pass to the Containerfile. [Usage example](https://github.com/rosvik/248.no/blob/master/.github/workflows/push-container.yml).
- `registry_url`: The URL of the registry to push the image to.
- `registry_username`: The username to use to push the image to the registry.

### Secrets

- `registry_password`: The password to use when authenticating with the registry.

### Example

`.github/workflows/push-container.yml`

```yaml
name: Push Container
on:
  push:
    branches:
      - main
jobs:
  push-container:
    name: Push Container
    uses: rosvik/workflows/.github/workflows/push-container.yml@main
    with:
      image: rosvik/hello
      registry_url: cubby.no
      registry_username: rosvik
    secrets:
      registry_password: ${{ secrets.REGISTRY_PASSWORD }}
```

## Cubbyman Reload

Reload [Cubbyman](https://github.com/rosvik/cubbyman) after a new container has been pushed to the registry.

### Inputs

- `base_url`: The base URL of the Cubbyman instance. A POST request will be made to `{base_url}/cubbyman/v1/reload`.

### Secrets

- `credentials`: The credentials to use to authenticate with the Cubbyman instance. Format: `username:password`.

### Example

`.github/workflows/deploy.yml`

```yaml
name: Deploy
on:
  push:
    branches:
      - main
jobs:
  push-container:
    name: Push Container
    uses: rosvik/workflows/.github/workflows/push-container.yml@main
    with:
      image: rosvik/hello
      registry_url: cubby.no
      registry_username: rosvik
    secrets:
      registry_password: ${{ secrets.REGISTRY_PASSWORD }}
  cubbyman-reload:
    name: Cubbyman Reload
    needs: push-container
    uses: rosvik/workflows/.github/workflows/cubbyman-reload.yml@main
    with:
      base_url: https://cubby.no
    secrets:
      credentials: ${{ secrets.CUBBYMAN_CREDENTIALS }}
```
