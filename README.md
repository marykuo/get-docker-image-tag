# get-docker-image-tag

Get docker image tag name from git tag or commit hash with optional prefix.

```
marykuo/get-docker-image-tag@v1
```

## Inputs

All inputs are optional.

| ID            | Description              | Default                  | Examples         |
| :------------ | :----------------------- | :----------------------- | :--------------- |
| `ref_type`    | Type of git reference    | `${{ github.ref_type }}` | `tag`, `branch`  |
| `ref_name`    | Git tag name             | `${{ github.ref_name }}` | `v1.0.0`         |
| `commit_hash` | SHA-1 hash of the commit | `${{ github.sha }}`      |                  |
| `prefix`      | Optional string          |                          | `commit`, `sha-` |
| `length`      | Length of commit hash    | `7`                      | `7`, `10`        |

## Outputs

| ID          | Description                                          | Examples                                                          |
| :---------- | :--------------------------------------------------- | :---------------------------------------------------------------- |
| `image_tag` | Docker image tag derived from git tag or commit hash | `v1.0.0` `sha-c9d08c2` `c9d08c22b24c163003ae60fc75edd232c09d4d61` |

## Examples

### Build image with commit tag

[docker/build-push-action] builds Docker images with tags, here we tag the image
we're building with the short commit hash.

```yaml
jobs:
  get-docker-image-tag:
    runs-on: ubuntu-latest
    steps:
      - name: Get Docker Image Tag
        id: get-docker-image-tag
        uses: marykuo/get-docker-image-tag@v1

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: user/app:${{ steps.get-docker-image-tag.outputs.image_tag }}
```

### Sample Outputs

| ref_type | ref_name | commit_hash                                | prefix | length | image_tag                                  |
| :------- | :------- | :----------------------------------------- | :----- | :----- | :----------------------------------------- |
| tag      | v1.0.0   | `c9d08c22b24c163003ae60fc75edd232c09d4d61` |        |        | `v1.0.0`                                   |
| branch   | main     | `c9d08c22b24c163003ae60fc75edd232c09d4d61` | commit | 10     | `commit-c9d08c22b2`                        |
| branch   | develop  | `c9d08c22b24c163003ae60fc75edd232c09d4d61` | sha    | 7      | `sha-c9d08c2`                              |
| branch   | feature  | `c9d08c22b24c163003ae60fc75edd232c09d4d61` |        | 40     | `c9d08c22b24c163003ae60fc75edd232c09d4d61` |
