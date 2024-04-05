---
layout: post
title: "Docker 설치하기 for macOS"
date: 2024-04-03 15:50:00 +0900
categories: docker
description: >
  Docker 컨테이너와 이미지에 대해 간단히 살펴보고 macOS환경에서 Docker를 설치해봅시다.
image: https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-logo.png
---

1. TOC
{:toc}

## Docker란?

도커(Docker)는 리눅스 컨테이너를 사용하여 애플리케이션을 더 쉽게 만들고 배포할 수 있도록 해주는 플랫폼입니다. 도커는 컨테이너 이미지를 사용하여 애플리케이션을 빌드하고 배포할 수 있습니다.
<br>
<br>
여기서 <b>컨테이너</b>는 격리된 환경에서 애플리케이션을 실행할 수 있도록 하는 기술입니다.\
컨테이너는 필요한 모든 라이브러리, 종속성 및 설정을 포함하는 격리된 패키지로서, 개발자는 개발 환경과 운영 환경 간의 일관성을 유지할 수 있습니다. 또한, 컨테이너는 빠르게 시작되고 쉽게 이동할 수 있어서 애플리케이션을 더 쉽게 배포하고 확장할 수 있습니다.
<br>
<br>
컨테이너 기술의 주요 장점은 다음과 같습니다.
  1. <b>경량화</b>\
   가상화된 운영 체제를 사용하는 가상 머신에 비해 더 적은 자원을 사용합니다.

  2. <b>이식성</b>\
   컨테이너는 호스트 시스템과 상관없이 동일한 방식으로 동작하므로 이식성이 뛰어납니다.

  3. <b>일관성</b>\
   컨테이너는 필요한 라이브러리 및 종속성을 포함하므로 환경이 일관되며, 개발 및 배포 과정이 표준화됩니다.

  4. <b>확장성</b>\
   컨테이너는 쉽게 배포되고 확장될 수 있어서 애플리케이션의 요구 사항에 따라 빠르게 조정할 수 있습니다.

<br>
<br>
도커 로고를 보시면 고래가 그려져 있고 그 위에 컨테이너가 올려져 있는 모습을 볼 수 있습니다.\
<b>"바다 위에 배가 떠다니면서 그 위에 컨테이너를 올려 놓고 필요할 때마다 컨테이너에 접근하여 사용한다."</b>라고 생각하시면 좋을 것 같습니다! 😊
<br>
<br>
<br>
<b>이미지</b>는 우리가 흔히 알고 있는 사진이나 그림 파일을 의미하는 것이 아니라, 컨테이너 실행에 필요한 파일과 설정 등을 포함하는 템플릿 입니다.
<br>
<br>
이미지는 값이 변하지 않는 정적인 파일이며, 컨테이너는 이미지를 실행한 상태입니다.\
때문에 컨테이너를 삭제하더라도 이미지는 영향을 받지 않고 그대로 남아있습니다.

또, 이미지에는 컨테이너를 실행하기 위해 필요한 모든 파일을 가지고 있기 때문에 복잡하게 이것저것 설정해주지 않아도 간편하게 컨테이너를 실행할 수 있으며, 하나의 이미지로 여러 개의 컨테이너를 실행할 수도 있습니다.

## macOS에 Docker 설치하기

도커에 대한 설명은 간략하게 이 정도로 마치고, 이제 macOS에 도커를 설치해봅시다!

[Docker 다운로드](https://www.docker.com/products/docker-desktop/) 페이지에 접속하여 본인의 컴퓨터 환경에 맞춰 도커를 다운로드 받습니다.

![Docker 다운로드 페이지](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-download-page.png)
<br>
<br>
<br>
다운로드가 완료되었다면 다운로드 받은 `Docker.dmg`파일을 실행하여 설치를 진행합니다.

![Docker 설치 화면](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-install.png)
<br>
<br>
<br>
설치가 완료되면 '응용프로그램'에서 도커 앱이 추가된 것을 확인할 수 있습니다.

도커 앱을 실행해봅시다.

![Docker 앱 실행](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-start.png)
<br>
`Accept`를 눌러 도커 약관에 동의하고 넘어갑니다.
<br>
<br>
![Docker 초기 설정](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-setting.png)
<br>
초기 설정은 기본값(추천)으로 설정하고 `Finish`를 눌러 다음 단계로 넘어갑니다.
<br>
<br>
![Docker 로그인](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-signin.png)

도커 허브 계정이 있다면 로그인을 하고, 없다면 스킵하셔도 됩니다.\
어짜피 도커는 계속 사용할테니 저는 계정을 새로 만들어서 로그인했습니다.
<br>
<br>
<br>
![Docker 메인화면](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-main.png)
<br>
이제 도커 설치가 완료되었습니다!
<br>
<br>
<br>
터미널을 열어 아래 명령어를 입력하여 도커가 정상적으로 설치되었는지 확인해봅시다.

```zsh
docker --version
```

![Docker 버전 확인](https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/2024-04-03-Docker-설치하기-for-macOS/docker-version-check.png)
<br>
도커 버전이 출력된다면 정상적으로 설치완료 된 것입니다! 👍

## 마치며

오늘은 도커에 대해 알아보고 macOS에 도커를 설치해봤습니다.\
다음 포스트에서는 도커 이미지를 다운받은 후 컨테이너를 실행하고 관리하는 방법에 대해 알아보겠습니다.

그럼 다음 포스트에서 뵙겠습니다! 뾰로롱~
<br>
<br>
<img src="https://cdn.jsdelivr.net/gh/NicoDora/nicodora.github.io/assets/img/frieren1.gif" width="300" height="300" alt="프리렌 움짤1">