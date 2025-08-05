---
layout: post
title: "GitHub Actions로 CI/CD 구축하기"
date: 2025-03-18 18:3:00 +0900
categories: github
description: >
  GitHub Actions를 사용하여 CI/CD를 구축하는 방법에 대해 알아보겠습니다.
image: https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-actions.png
---

1. TOC
{:toc}

## 지금까지의 이야기

이전 포스팅에서 AWS EC2에 Nginx와 Certbot을 설치하여 Let's Encrypt SSL 인증서를 발급 및 자동 갱신까지 하는 방법에 대해 알아보았습니다. 서버를 구축하고 어느정도 사용해보시면 알겠지만, 서버 코드가 수정될 때 마다 수동으로 EC2에 접속하여 컨테이너를 내리고 수정된 코드를 이미지로 만들어 다시 컨테이너를 실행시키는 작업이 매우 비효율적이라는 것을 깨닫게 됩니다.
<br>
<br>
몇번 이 작업을 반복하다보면, '이 작업을 자동화 하면 좋겠다' 하는 생각이 드실겁니다.\
보통 이러한 과정을 CI/CD라고 부르고, GitHub에서는 Actions라는 서비스를 통해 개발자들이 편하게 CI/CD를 구축할 수 있도록 환경을 제공하고 있습니다. 이번 포스팅에서는 GitHub Actions를 사용하여 CI/CD를 구축하는 방법에 대해 알아보겠습니다.

## CI/CD란?

CI/CD는 'Continuous Integration/Continuous Delivery'의 약자로, 지속적인 통합과 지속적인 배포를 의미합니다. 즉, 코드 변경 사항을 자동으로 빌드하고 테스트하여 배포하는 과정을 자동화한다 라고 생각하시면 됩니다.

- <b>CI (Continuous Integration) - 지속적인 통합</b>
  - 개발자들이 작성한 코드를 정기적으로 통합하여 빌드하고 테스트하는 과정입니다. 이를 통해 코드의 품질을 높이고, 버그를 조기에 발견할 수 있습니다.
- <b>CD (Continuous Delivery) - 지속적인 배포</b>
  - CI 과정에서 빌드된 코드를 자동으로 배포하는 과정입니다. 이를 통해 코드 변경 사항을 빠르게 사용자에게 전달할 수 있습니다.

<br>
GitHub Actions는 이러한 CI/CD를 구현하기 위한 도구로, GitHub 저장소에서 직접 워크플로우를 정의하고 실행할 수 있습니다. GitHub는 개발자가 가장 많이 사용하는 서비스 중 하나이기 때문에 GitHub Actions에 대해 배우는 것은 크게 어렵지 않으실 겁니다.

## Actions Secrets 설정하기

GitHub Actions를 사용하기 전에 먼저 GitHub 저장소에 `Secrets`를 설정해보려 합니다. GitHub Actions는 GitHub repository에 올린 `workflow/` 폴더 내의 파일을 통해 CI/CD를 실행하게 되는데 여기서 Docker ID/PW나 AWS EC2에 SSH로 접속하기 위한 비밀번호 등과 같이 민감한 정보가 필요합니다.
<br>
<br>
GitHub에서는 이러한 민감한 정보를 `Secrets`라는 기능을 통해 안전하게 저장하고 사용할 수 있도록 지원하고 있습니다.
<br>
<br>
GitHub Secrets는 GitHub repository의 `Settings` > `Secrets and variables` > `Actions`에서 설정할 수 있습니다.
<br>
<br>
![GitHub Secrets 접속](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets.png)
<br>
<br>

`Secrets` 탭에서 시크릿 환경변수를 관리할 수 있는데요. 여기서 `Environment secrets`, `Repository secrets`, `Organization secrets`라는 세가지 종류의 시크릿 환경변수를 보실 수 있습니다.

- <b>Environment secrets</b>
  - 특정 환경에 대한 시크릿을 설정합니다.
  - 예) `development`, `staging`, `production` 등에 대한 시크릿.
- <b>Repository secrets</b>
  - 특정 repository에 대한 시크릿을 설정합니다. 해당 repository에서 공통으로 사용되는 시크릿을 설정할 수 있습니다.
  - 예) `Docker ID/PW`, `AWS ID/PW`에 대한 시크릿.
- <b>Organization secrets</b>
  - 조직에 대한 시크릿을 설정합니다. 조직 내의 모든 repository에서 공통으로 사용되는 시크릿을 설정할 수 있습니다.
  - 예) `AWS ID/PW`에 대한 시크릿.
<br>
<br>

본인의 상황에 맞게 시크릿을 설정하시면 되는데, 보통 `production`, `development`과 같이 환경을 나누어 관리하는 경우가 많기 때문에 `Environment secrets`를 사용하여 시크릿을 설정하는 것을 추천드립니다.
<br>
<br>
`development` 환경에서 사용할 시크릿을 설정해보겠습니다.\
`Manage environment secrets` 버튼을 클릭합니다.
<br>
<br>
![GitHub Secrets 설정](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-setting.png)
<br>
<br>
<br>
`New environment` 를 클릭하여 새 Environments를 생성합니다.
<br>
<br>
![GitHub Secrets New Environment](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment.png)
<br>
<br>
<br>
Environments에 대한 이름을 설정할 수 있는데, 저는 develop 브랜치의 CI/CD를 구현할 예정이므로 `development` 로 입력하겠습니다.\
입력 후 `Configure environment` 버튼을 클릭합니다.
<br>
<br>
![GitHub Secrets New Environment Name](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment-name.png)
<br>
<br>
<br>
Environments가 생성되면 아래와 같은 화면이 보이게 됩니다.\
여기서 배포 보호 규칙을 설정할 수도 있지만 이 부분은 스킵하겠습니다. `Deployment branches and tags`에서 `Select branches and tags`로 바꿔 배포할 브랜치를 설정해보겠습니다.
<br>
<br>
![GitHub Secrets New Environment Setting](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment-setting.png)
<br>
<br>
<br>
그런 다음 Environments를 사용할 브랜치를 추가하면 됩니다.\
저는 `develop` 브랜치를 추가하겠습니다.
<br>
<br>
![GitHub Secrets New Environment Setting2](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment-setting2.png)
<br>
<br>
![GitHub Secrets New Environment Setting3](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment-setting3.png)
<br>
<br>
<br>
이제 `development` 환경에 대한 설정이 완료되었으므로 시크릿 변수를 추가해봅시다.\
바로 아래 `Environment secrets` 탭에서 `New environment secret` 버튼을 클릭합니다.
<br>
<br>
![GitHub Secrets New Environment Secret](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment-secret.png)
<br>
<br>
<br>
이름과 값을 입력할 수 있는 화면이 보이는데 여기에 CI/CD에 필요한 시크릿 변수를 추가하면 됩니다.\
참고로 한번 설정된 시크릿 변수는 수정만 가능하고 볼 수 없으므로 주의하시기 바랍니다.\
저는 `DOCKER_USERNAME`, `DOCKER_PASSWORD`, `EC2_HOST`, `EC2_USER`, `SSH_PRIVATE_KEY`를 추가하겠습니다.
<br>
<br>
![GitHub Secrets New Environment Secret Setting](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-secrets-new-environment-secret-setting.png)
<br>
<br>
<br>
`EC2_HOST`는 EC2의 `퍼블릭 IPv4 DNS`를, `EC2_USER`는 EC2에 접속하기 위한 사용자 이름을 입력하시면 됩니다.\
`SSH_PRIVATE_KEY`는 EC2에 접속하기 위한 개인키를 말하는데요. `.pem` 파일이나 `.ppk` 파일을 열어 개인키를 복사하여 붙여넣으면 됩니다.
<br>
<br>
macOS에서는 `cat` 명령어를 사용하여 개인키를 확인할 수 있습니다.

```bash
# cat {파일위치}
$ cat ~/.ssh/your-key.pem
```

![ec2 ssh private key](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/ec2-ssh-private-key.png)
<br>
📍 <b>ssh 키를 복사할 때는 반드시 Header와 Footer를 포함해서 복사해야 합니다!</b>

```
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAu06IpZklZ5W9L/AS56LAE95FvmmPhmO9MeyUItiw2jDfwaiR
...
-----END RSA PRIVATE KEY-----
```

## GitHub Actions Workflow 생성하기

시크릿 변수를 모두 추가하셨다면 CI/CD를 실행할 파일을 생성해봅시다.\
GitHub Actions는 `.github/workflows` 폴더 내에 `yaml` 파일을 통해 CI/CD를 실행합니다.
<br>
<br>
프로젝트 repository 내에 `Actions` 탭에서 `New workflow` 버튼을 클릭하여 미리 정의된 템플릿을 사용할 수 있는데 대부분 템플릿을 사용하지 않고 직접 작성하시더라구요? 사실 템플릿을 사용할 만큼 파일을 작성하는게 어렵지 않아서 그런거 같습니다.\
그래서 저도 직접 작성해보려 합니다. 별로 어렵지 않으니... ㅎㅎ
<br>
<br>
일단 `.github/workflows` 폴더를 생성하고 그 안에 `.yml` 파일을 생성합니다.\
참고로 `.yml`, `.yaml` 둘다 같은 확장자이니 편하신걸로 사용하시면 됩니다. 만들어져 있는 production 환경 CI/CD 파일이 `.yml` 확장자라서 일관성을 위해 저도 `.yml`로 작성하겠습니다.\
파일 이름도 원하시는대로 작성하시면 되고, 저는 production 환경과 구분하기 위해 파일 이름은 `app-dev.yml`로 작성하겠습니다.
<br>
<br>

``` yaml
# file: ".github/workflows/app-dev.yml"
name: app-dev

on:
  push:
    branches: [develop]

jobs:
  build:
    runs-on: ubuntu-latest
    environment: development
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${ { secrets.DOCKER_USERNAME } }
          password: ${ { secrets.DOCKER_PASSWORD } }

      - name: Build Docker image (development)
        run: |
          docker build --platform linux/amd64 -f Dockerfile.dev -t nicodora/honey-moa-dev:latest .

      - name: Push Docker image
        run: |
          docker push nicodora/honey-moa-dev:latest

      # 배포 단계: EC2에 SSH로 접속하여 컨테이너 업데이트
      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.8
        with:
          host: ${ { secrets.EC2_HOST } }
          username: ${ { secrets.EC2_USER } }
          key: ${ { secrets.SSH_PRIVATE_KEY } }
          script: |
            # 기존 컨테이너 중지 및 삭제
            sudo docker-compose -f docker-compose.dev.yaml down

            # 오래된 이미지 정리
            sudo docker rmi -f nicodora/honey-moa-dev:latest || true

            # 최신 이미지 pull
            sudo docker pull nicodora/honey-moa-dev:latest

            # 최신 이미지로 Docker Compose 실행
            sudo docker-compose -f docker-compose.dev.yaml up -d
```

<details>
<summary><b>코드 흐름</b></summary>
<div markdown="1">

- `develop` 브랜치에 push가 발생하면 `app-dev.yml` 파일이 실행됩니다.
- `ubuntu-latest` 환경에서 실행되며, `development` 환경을 사용합니다.
- 도커에 로그인 하고 이미지를 빌드하여 `Docker Hub`에 푸시합니다.
- `appleboy/ssh-action`을 사용하여 EC2에 SSH로 접속하여 컨테이너를 업데이트합니다.
</div>
</details>
<br>
<br>
이제 `app-dev.yml` 파일을 커밋하고 `develop` 브랜치에 푸시하면 CI/CD가 실행됩니다.

## GitHub Actions 실행 확인하기

Actions 탭에서 작성한 workflow 파일을 클릭해 보면 CI/CD가 실행되는 것을 확인할 수 있습니다.
<br>
<br>
![GitHub Actions 실행 확인](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-actions-run.png)
<br>
<br>
<br>
build 탭에 들어가보면 각 단계별로 자세한 로그를 확인할 수 있습니다.\
아래와 같이 ✅ 표시가 뜨면 성공적으로 실행된 것입니다!
<br>
<br>
![GitHub Actions 실행 확인2](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-actions-run2.png)
<br>
<br>
<br>
이전에 수동으로 배포할 때는 약 5분정도 소요되었지만, GitHub Actions를 사용하여 CI/CD를 구축하니 평균 2분 10초 정도에 배포가 완료되는 모습입니다. 결과적으로 이전 수동 배포에 비해 53% 정도의 시간을 단축할 수 있었습니다.
<br>
<br>
![GitHub Actions 실행 확인3](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/github/2025-03-18-GitHub-Actions로-CICD-구축하기/github-actions-run3.png)

## 마치며

이번 포스팅에서는 GitHub Actions를 사용하여 CI/CD를 구축하는 방법에 대해 알아보았습니다.\
큰 노력 없이 CI/CD를 구축하여 배포 자동화를 할 수 있기에 지속적으로 코드를 배포한다고 하면 거의 필수 작업이라고 생각합니다.

그럼 다음 포스트에서 뵙겠습니다! 뾰로롱~
<br>
<br>
<img src="https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/frieren1.gif" width="300" height="300" alt="프리렌 움짤1">