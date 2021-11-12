---
title: GitHub Actions를 사용하여 컨테이너 이미지 검사
description: 컨테이너 검사 작업을 사용하여 컨테이너 이미지를 검사하는 방법 알아보기
author: v-abiss
ms.author: v-abiss
ms.topic: quickstart
ms.reviewer: jukullam
ms.service: azure
ms.date: 05/20/2021
ms.custom: github-actions-azure
ms.openlocfilehash: a16c858008ce2233d0382e8a8f2a8e6224bbb6b8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315808"
---
# <a name="scan-container-images-using-github-actions"></a>GitHub Actions를 사용하여 컨테이너 이미지 검사

컨테이너 이미지를 빌드하고 검사하는 워크플로를 만들어 [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions)를 시작합니다.

GitHub Actions를 사용하면 워크플로에서 이미지를 빌드하고 검사한 후 퍼블릭 또는 프라이빗 [Container Registry](https://azure.microsoft.com/services/container-registry/)에 푸시하여 CI/CD 프로세스를 가속화할 수 있습니다.

이 문서에서는 [GitHub Marketplace](https://github.com/marketplace)에서 [컨테이너 이미지 검사](https://github.com/marketplace/actions/container-image-scan)를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 빌드 및 푸시되는 모든 컨테이너 이미지를 저장할 Azure Container Registry. [Azure Portal에서 Azure Container Registry를 만들 수 있습니다](../container-registry/container-registry-get-started-portal.md).
- 활성 리포지토리가 있는 GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요. 
    - 이 예제에서는 [Java Spring PetClinic 애플리케이션 예제](https://github.com/spring-projects/spring-petclinic)를 사용합니다.

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

이 파일에는 다음과 같은 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. Azure에서 컨테이너 레지스트리를 만듭니다. <br /> 2. GitHub 비밀을 만듭니다. <br /> 3. GH 작업을 사용하여 레지스트리에 로그인합니다. |
|**빌드** | 1. 환경을 설정합니다. <br /> 2. 앱을 빌드합니다. |
|**이미지 빌드, 스캔 및 컨테이너 레지스트리에 푸시** | 1. 이미지를 빌드합니다. <br /> 2. 이미지를 스캔합니다. <br /> 3. 이미지를 푸시합니다.|

## <a name="create-github-secrets"></a>GitHub 비밀 만들기

[Azure Container Registry 로그인 작업](https://github.com/marketplace/actions/azure-container-registry-login)을 사용하려면 먼저 Container Registry 세부 정보를 GitHub 리포지토리에 비밀로 추가해야 합니다.

이 예제에서는 Azure에서 인증하는 데 사용할 수 있는 세 가지 비밀을 만듭니다.  

1. GitHub 리포지토리를 열고 **설정** 으로 이동합니다.

    :::image type="content" source="media/github-action-scan/github-repo-settings.png" alt-text="탐색 메뉴에서 설정 선택":::

1. **비밀** 과 **새 비밀** 을 차례로 선택합니다.

    :::image type="content" source="media/github-action-scan/azure-secret-add.png" alt-text="비밀 추가 선택":::

1. Container Registry에서 **액세스 키** 로 이동하여 Azure Portal에서 다음 값으로 만든 각 비밀에 대해 다음 값을 붙여넣습니다. 

    | GitHub 비밀 이름 | Azure Container Registry 값 |
    |---------|---------|
    |`ACR_LOGIN_SERVER` | **로그인 서버** |
    |`REGISTRY_USERNAME` | **사용자 이름** |
    |`REGISTRY_PASSWORD` | **password** |
    
1. **비밀 추가** 를 선택하여 저장합니다.

## <a name="add-a-dockerfile"></a>Dockerfile 추가

다음 조각을 사용하여 `Dockerfile`을 만들고 리포지토리에 커밋합니다.

```
FROM openjdk:11-jre-stretch
ADD target/spring-petclinic-2.4.2.jar spring-petclinic-2.4.2.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "spring-petclinic-2.4.2.jar" ]
```


## <a name="use-the-docker-login-action"></a>Docker 로그인 작업 사용

[Azure Container Registry 로그인 작업](https://github.com/marketplace/actions/azure-container-registry-login)에서 비밀을 사용하여 Azure에서 인증을 받습니다.

이 워크플로에서는 레지스트리의 **로그인 서버**, **사용자 이름 및 **암호** 세부 정보가 [docker-login action](https://github.com/Azure/docker-login)의 `secrets.ACR_LOGIN_SERVER`, `secrets.REGISTRY_USERNAME` 및 `secrets.REGISTRY_PASSWORD`에 각각 저장되어 있는 Azure Container Registry 로그인을 사용하여 인증을 받습니다. 워크플로 파일의 GitHub 비밀 참조에 대한 자세한 내용은 GitHub Docs의 [워크플로에서 암호화된 비밀 사용](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow)을 참조하세요.


```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
```

## <a name="configure-java"></a>Java 구성

[Java SDK 설정 작업](https://github.com/marketplace/actions/setup-java-jdk)을 사용하여 Java 환경을 설정합니다. 이 예제에서는 환경을 설정하고, Maven을 사용하여 빌드하고, 이미지를 빌드, 검사한 후 컨테이너 레지스트리에 푸시합니다.

[GitHub 아티팩트](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts)는 워크플로에서 작업 간에 파일을 공유하는 방법입니다. 

```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest    
    steps:
    - name: Checkout
      uses: actions/checkout@v2    

    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        java-version: '1.8.x'
        
    - name: Build with Maven
      run: mvn package --file pom.xml
```

## <a name="build-your-image"></a>이미지 빌드 

워크플로에서 다음 코드 조각을 사용하여 이미지를 빌드하고 태그를 지정합니다. 다음 코드 조각은 Docker CLI를 사용하여 셸 터미널 내에서 이미지를 빌드하고 태그를 지정합니다. 

```yaml
    - name: Build and Tag image
      run: |
        docker build -f ./Dockerfile -t ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }} .
        
```

## <a name="scan-the-image"></a>이미지 검사

컨테이너 레지스트리에 빌드된 이미지를 푸시하기 전에 [컨테이너 이미지 검사 작업](https://github.com/marketplace/actions/container-image-scan)을 사용하여 이미지를 검사하고 취약성을 확인해야 합니다.

다음 코드 조각을 워크플로에 추가합니다. 이 코드 조각은 푸시되는 이미지가 안전하고 표준을 준수하는지 파악하기 위해 이미지에서 ***중요 취약성*** 을 검사합니다.

이 작업을 사용하여 다른 입력을 추가하거나 리포지토리에 `allowedlist.yaml` 파일을 추가하여 취약성 및 모범 사례 검사를 무시할 수도 있습니다. 

```yaml
    - name: Scan image
      uses: Azure/container-scan@v0
      with:
        image-name: ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        severity-threshold: CRITICAL
        run-quality-checks: true        
```

예 `allowedlist.yaml`.

```yaml
general:
  vulnerabilities:
    - CVE-2003-1307
    - CVE-2011-3374
  bestPracticeViolations:
    - CIS-DI-0005
```
## <a name="push-the-image-to-a-private-registry"></a>이미지를 프라이빗 레지스트리로 푸시

이미지를 검사했으나 취약성이 발견되지 않을 경우 빌드된 이미지를 프라이빗 레지스트리에 푸시해도 안전합니다. 다음 코드 조각은 셸 터미널에서 Docker CLI를 사용하여 이미지를 푸시합니다.

```yaml
    - name: Push image
      run: |
        docker push ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        
```
## <a name="next-steps"></a>다음 단계
- [Azure Container Registry에서 Azure Container Instances에 배포](../container-instances/container-instances-using-azure-container-registry.md)하는 방법을 알아봅니다.
