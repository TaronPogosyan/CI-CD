# CI/CD. homeWork-02

## Задача

Переписать test stage для тестирования docker

## Решение

После создания контейнера docker

```yaml
docker build:
  image: docker:latest
  stage: build
  services:
    - docker:dind
  script:
    - docker login -u $GITLAB_CI_USER -p $GITLAB_CI_PASSWORD $CI_REGISTRY
    - echo $GITLAB_CI_USER $GITLAB_CI_PASSWORD $CI_REGISTRY $CI_REGISTRY_IMAGE:$IMAGE_TAG
    - docker build -t $CI_REGISTRY_IMAGE:$IMAGE_TAG .
    - docker push $CI_REGISTRY_IMAGE:$IMAGE_TAG
```

проверяем возможность его загрузки из нашего реестра:

```yaml
testdocker:
  stage: test
  script:
    - docker pull $CI_REGISTRY_IMAGE:$IMAGE_TAG
    - docker images
    - echo "----------MY DOCKER IMAGE TEST----------"
```

## Скриншоты

Добавляем блок тестирования (тянем образ контейнера).
![Alt text](images/dz-02-01.jpg)

Просматриваем список созданных контейнеров (ишь, расплодились!).
![Alt text](images/dz-02-02.jpg)

Pipeline passed
![Alt text](images/dz-02-03.jpg)

## Замечание

При создании образа (раздел `docker build:`) — появлялась проблема нехватки прав.

`Got permission denied while trying to connect to the Docker daemon socket at… `

Исправляется добавлением gitlab-runner в группу docker.

```
usermod -aG docker gitlab-runner
```
