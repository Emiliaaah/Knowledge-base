# Gitlab Runners

GitLab Runner is an application that works with GitLab CI/CD to run jobs in a pipeline.

Project Homepage: [Gitlab Runner](https://gitlab.com/gitlab-org/gitlab-runner) 
Documentation: [Gitlab Runner Docs](https://docs.gitlab.com/runner/)

---

## Anchors

You can make use of [[YAML]] anchors to make templates which you can use again, so you don't have to have duplicate code.

More info: [Gitlab Docs](https://docs.gitlab.com/ee/ci/yaml/yaml_optimization.html#yaml-anchors-for-scripts)

```yaml
image: docker:20

stages:
  - build

.registry-login: &registry-login
  - echo -n $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY

Build:
  stage: build
  script:
    - *registry-login
    - >
      docker build
      --pull
      --build-arg http_proxy=$http_proxy
      --build-arg https_proxy=$https_proxy
      --build-arg no_proxy=$no_proxy
      --cache-from $CI_REGISTRY_IMAGE:latest
      --label "org.opencontainers.image.title=$CI_PROJECT_TITLE"
      --label "org.opencontainers.image.url=$CI_PROJECT_URL"
      --label "org.opencontainers.image.created=$CI_JOB_STARTED_AT"
      --label "org.opencontainers.image.revision=$CI_COMMIT_SHA"
      --label "org.opencontainers.image.version=$CI_COMMIT_REF_NAME"
      --tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
      .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

---

## Extends

Just like anchors you can use extends to reuse the same config in multiple places. Below you can find an example of this with `before_script`.

More info: [Gitlab Docs](https://docs.gitlab.com/ee/ci/yaml/#extends)

```yml
image: docker:20

stages:
  - build

.before_script_template:
  before_script:
    - echo -n $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY

Build:
  extends: .before_script_template
  stage: build
  script:
    - >
      docker build
      --pull
      --build-arg http_proxy=$http_proxy
      --build-arg https_proxy=$https_proxy
      --build-arg no_proxy=$no_proxy
      --cache-from $CI_REGISTRY_IMAGE:latest
      --label "org.opencontainers.image.title=$CI_PROJECT_TITLE"
      --label "org.opencontainers.image.url=$CI_PROJECT_URL"
      --label "org.opencontainers.image.created=$CI_JOB_STARTED_AT"
      --label "org.opencontainers.image.revision=$CI_COMMIT_SHA"
      --label "org.opencontainers.image.version=$CI_COMMIT_REF_NAME"
      --tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
      .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

---

## SSH

### SSH key

For this you'll want to add some CI/CD variables to you project (or group if you want to use them in multiple projects). You can find these in your project under: Settings > CI/CD > Variables

We'll start by adding a variable for the SSH key. If you haven't made one do so now. Make sure the variable settings are the same as the following.

> [!WARNING] When pasting in your SSH key make sure you add a new line at the end

```yml
Key: ID_RSA
Type: File
Environment Scope: All (default)
Protect variable: ✔
Mask variable: ✖
```

### Server IP

For us to connect via ssh we'll also need the server ip address. Make a new Variable with the following settings.

```yml
Key: SERVER_IP
Value: <your server ip>
Type: Variable
Environment scope: All (default)
Protect variable: ✔
Mask variable: ✔
```

### User

The last thing we'll need is the username of the user on the remote server. You probably know the drill by now, just make sure your settings look like the following.

```yml
Key: SERVER_USER
Value: <username of the remote user>
Type: Variable
Environment scope: All (default)
Protect variable: ✔
Mask variable: ✔
```

### Example

Now the only thing left is to actually use these variables in our `.gitlab-ci.yml` file. Below you can find an example you can adapt to your own use case, it's only an example after all.

```yml
stages:
  - distro

Get distro:
  image: alpine:latest
  stage: distro
  script:
    - chmod og= $ID_RSA
    - apk update && apk add openssh-client
    - ssh -i $ID_RSA -o StrictHostKeyChecking=no $SERVER_USER@$SERVER_IP "lsb_release -a"
```

---