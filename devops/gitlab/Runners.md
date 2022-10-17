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