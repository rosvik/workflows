# Workflows

## Push Container

Build and push amd64 and arm64 containers to a registry. It is made for easy integration with [Container Cubby](https://github.com/rosvik/container-cubby), which is why it only supports basic auth for now.

> [!IMPORTANT]
> This workflow uses the `ubuntu-24.04-arm` runner, which currently is only available by default for public repositories.

### Inputs

- `containerfile`: Path to the Containerfile to build. Defaults to `Containerfile`.
- `image`: The name of the image to build. E.g. `rosvik/hello`
- `tag`: The tag to build the image with. Can be set to `${{ github.ref_name }}` to use the branch name or git tag. Based on this, three tags are pushed: `{tag}`, `{tag}-amd64`, and `{tag}-arm64`, where `{tag}` will be a multi-arch manifest.
- `registry_url`: The URL of the registry to push the image to.
- `registry_username`: The username to use to push the image to the registry.

### Secrets

- `registry_password`: The password to use when authenticating with the registry.
