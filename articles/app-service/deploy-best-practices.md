---
title: 배포 모범 사례
description: Azure App Service에 배포하는 주요 메커니즘에 대해 알아봅니다. 언어별 권장 사항 및 기타 주의 사항을 찾습니다.
keywords: azure app service, 웹앱, 배포, 배포, 파이프라인, 빌드
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 10fe758bf11f0452ef07d30329d7d48914fbb4bf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566822"
---
# <a name="deployment-best-practices"></a>배포 모범 사례

개발 팀마다 요구 사항이 다르기 때문에 클라우드 서비스에 대한 효율적인 배포 파이프라인 구현에 어려움을 겪을 수 있습니다. 이 문서에서는 App Service에 배포하는 세 가지 주요 구성 요소인 배포 원본, 빌드 파이프라인 및 배포 메커니즘을 소개합니다. 이 문서에는 특정 언어 스택에 대한 몇 가지 모범 사례 및 팁이 포함되어 있습니다.

## <a name="deployment-components"></a>배포 구성 요소

### <a name="deployment-source"></a>배포 원본

배포 원본은 애플리케이션 코드의 위치입니다. 프로덕션 앱의 경우 배포 원본은 일반적으로 [GitHub, BitBucket 또는 Azure Repos](deploy-continuous-deployment.md)와 같은 버전 제어 소프트웨어에서 호스팅되는 리포지토리입니다. 개발 및 테스트 시나리오의 경우 배포 원본은 [로컬 컴퓨터의 프로젝트](deploy-local-git.md)일 수 있습니다. App Service는 배포 원본으로 [OneDrive 및 Dropbox 폴더](deploy-content-sync.md)도 지원합니다. 클라우드 폴더를 사용하면 쉽게 App Service를 시작할 수 있지만, 일반적으로 엔터프라이즈 수준의 프로덕션 애플리케이션에는 이 소스를 사용하지 않는 것이 좋습니다. 

### <a name="build-pipeline"></a>빌드 파이프라인

배포 원본을 결정한 후 다음 단계는 빌드 파이프라인을 선택하는 것입니다. 빌드 파이프라인은 배포 원본에서 소스 코드를 읽고 코드 컴파일, HTML 및 JavaScript 축소 테스트 실행, 구성 요소 패키징 등의 일련의 단계를 실행하여 애플리케이션을 실행 가능한 상태로 만듭니다. 빌드 파이프라인에 의해 실행되는 특정 명령은 언어 스택에 따라 달라집니다. 이러한 작업은 Azure Pipelines 같은 빌드 서버에서 실행하거나 로컬로 실행할 수 있습니다.

### <a name="deployment-mechanism"></a>배포 메커니즘

배포 메커니즘은 웹앱의 */home/site/wwwroot* 디렉터리에 빌드된 애플리케이션을 배치하는 데 사용되는 작업입니다. */wwwroot* 디렉터리는 웹앱의 모든 인스턴스에서 공유되는 탑재된 스토리지 위치입니다. 배포 메커니즘이 애플리케이션을 이 디렉터리에 배치하면 인스턴스는 새 파일을 동기화하기 위한 알림을 받게 됩니다. App Service는 다음과 같은 배포 메커니즘을 지원합니다.

- Kudu 엔드포인트: [Kudu](https://github.com/projectkudu/kudu/wiki)는 Windows App Service에서 별도의 프로세스로 실행되는 오픈 소스 개발자 생산성 도구이며 Linux App Service의 두 번째 컨테이너로 실행됩니다. Kudu는 연속 배포를 처리하고 zipdeploy와 같은 배포에 대한 HTTP 엔드포인트를 제공합니다.
- FTP 및 WebDeploy: [사이트 또는 사용자 자격 증명](deploy-configure-credentials.md)을 사용하여 webdeploy 또는 [FTP를 통해](deploy-ftp.md) 파일을 업로드할 수 있습니다. 이러한 메커니즘은 Kudu를 거치지 않습니다.  

Azure Pipelines, Jenkins 및 편집기 플러그 인 같은 배포 도구는 이러한 배포 메커니즘 중 하나를 사용합니다.

## <a name="use-deployment-slots"></a>배포 슬롯 사용

가능하면 새 프로덕션 빌드를 배포할 때 [배포 슬롯](deploy-staging-slots.md)을 사용합니다. 표준 App Service 계획 계층 이상을 사용하는 경우 스테이징 환경에 앱을 배포하고, 변경 내용의 유효성을 검사하고, 스모크 테스트를 수행할 수 있습니다. 준비가 되면 스테이징 및 프로덕션 슬롯을 교환할 수 있습니다. 교환 작업을 수행하면 프로덕션 규모와 일치하도록 하는 데 필요한 작업자 인스턴스가 발생하므로 가동 중지 시간을 없앨 수 있습니다.

### <a name="continuously-deploy-code"></a>지속적으로 코드 배포

프로젝트에 테스트, QA 및 스테이징에 대한 분기가 지정된 경우 각 분기는 스테이징 슬롯에 지속적으로 배포되어야 합니다. 이를 [Gitflow 디자인](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)이라고 합니다. 이를 통해 관련자는 배포된 분기를 쉽게 평가하고 테스트할 수 있습니다. 

프로덕션 슬롯에 대해 연속 배포를 사용하도록 설정하면 안 됩니다. 대신 프로덕션 분기(주로 주 분기)가 비 프로덕션 슬롯에 배포되어야 합니다. 기본 분기를 릴리스할 준비가 되면 프로덕션 슬롯으로 교환합니다. 프로덕션에 배포하는 대신 프로덕션으로 교환하면 가동 중지 시간을 방지할 수 있으며, 다시 교환하여 변경 내용을 롤백할 수도 있습니다. 

![개발, 스테이징 및 주 분기와 배포되는 슬롯 간의 흐름을 보여 주는 다이어그램입니다.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>지속적으로 컨테이너 배포

Docker 또는 다른 컨테이너 레지스트리의 사용자 지정 컨테이너에 대해 이미지를 스테이징 슬롯에 배포하고 프로덕션으로 교환하여 가동 중지 시간을 방지합니다. 컨테이너 레지스트리에 이미지를 푸시하고 웹앱에서 이미지 태그를 업데이트해야 하기 때문에 자동화는 코드 배포보다 더 복잡합니다.

슬롯에 배포할 각 분기에 대해 자동화를 설정하여 분기에 대한 각 커밋에서 다음을 수행합니다.

1. **이미지 빌드 및 태그 지정**. 빌드 파이프라인의 일부로 git 커밋 ID, 타임스탬프 또는 기타 식별 가능한 정보를 사용하여 이미지에 태그를 지정합니다. 기본값인 "최신" 태그를 사용하지 않는 것이 좋습니다. 그러지 않으면 현재 배포된 코드를 추적하기가 까다롭기 때문에 디버깅이 훨씬 더 어려워집니다.
1. **태그가 지정된 이미지를 푸시** 합니다. 이미지를 빌드하고 태그를 지정하면 파이프라인에서 이미지를 컨테이너 레지스트리로 푸시합니다. 다음 단계에서 배포 슬롯은 컨테이너 레지스트리에서 태그가 지정된 이미지를 가져옵니다.
1. **새 이미지 태그를 사용하여 배포 슬롯을 업데이트** 합니다. 이 속성이 업데이트되면 사이트는 자동으로 다시 시작되며 새 컨테이너 이미지를 끌어옵니다.

![슬롯 사용 시각적 개체](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

일반적인 자동화 프레임워크에 대한 예는 다음과 같습니다.

### <a name="use-azure-devops"></a>Azure DevOps 사용

App Service에는 배포 센터를 통해 컨테이너에 대한 [기본 제공된 지속적인 업데이트](deploy-continuous-deployment.md)가 있습니다. [Azure Portal](https://portal.azure.com/)에서 앱으로 이동하고 **배포** 아래에서 **배포 센터** 를 선택합니다. 지침에 따라 리포지토리와 분기를 선택합니다. 그러면 새 커밋이 선택된 분기로 푸시될 때 컨테이너를 자동으로 빌드, 태그 지정 및 배포할 수 있도록 DevOps 빌드 및 릴리스 파이프라인이 구성됩니다.

### <a name="use-github-actions"></a>Github Actions 사용

[GitHub Actions를 사용하여](./deploy-ci-cd-custom-container.md) 컨테이너 배포를 자동화할 수도 있습니다.  아래 워크플로 파일은 커밋 ID를 사용하여 컨테이너를 빌드 및 태그 지정하고, 컨테이너 레지스트리에 푸시하고, 지정된 사이트 슬롯을 새 이미지 태그로 업데이트합니다.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@main

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>다른 자동화 공급자 사용

이전에 나열된 단계는 CircleCI 또는 Travis CI와 같은 다른 자동화 유틸리티에 적용됩니다. 그러나 마지막 단계에서 새 이미지 태그로 배포 슬롯을 업데이트하려면 Azure CLI를 사용해야 합니다. 자동화 스크립트에서 Azure CLI를 사용하려면 다음 명령을 사용하여 서비스 주체를 생성합니다.

```azurecli
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

스크립트에서 `az login --service-principal`를 사용하여 로그인하고 보안 주체의 정보를 제공합니다. 그런 다음 `az webapp config container set`를 사용하여 컨테이너 이름, 태그, 레지스트리 URL 및 레지스트리 암호를 설정할 수 있습니다. 컨테이너 CI 프로세스를 생성하는 데 도움이 되는 몇 가지 유용한 링크는 다음과 같습니다.

- [Circle CI에서 Azure CLI에 로그인하는 방법](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>언어별 고려 사항

### <a name="java"></a>Java

Kudu [zipdeploy/](deploy-zip.md) API를 사용하여 JAR 애플리케이션을 배포하고, [wardeploy/](deploy-zip.md#deploy-war-file)를 사용하여 WAR 앱을 배포합니다. Jenkins를 사용하는 경우 배포 단계에서 직접 해당 API를 사용할 수 있습니다. 자세한 내용은 [이 문서](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli)를 참조하세요.

### <a name="node"></a>노드

기본적으로 Kudu는 노드 애플리케이션(`npm install`)에 대한 빌드 단계를 실행합니다. Azure DevOps와 같은 빌드 서비스를 사용하는 경우 Kudu 빌드는 필요하지 않습니다. Kudu 빌드를 사용하지 않도록 설정하려면 값이 `false`인 앱 설정 `SCM_DO_BUILD_DURING_DEPLOYMENT`를 만듭니다.

### <a name="net"></a>.NET 

기본적으로 Kudu는 .NET 애플리케이션(`dotnet build`)에 대한 빌드 단계를 실행합니다. Azure DevOps와 같은 빌드 서비스를 사용하는 경우 Kudu 빌드는 필요하지 않습니다. Kudu 빌드를 사용하지 않도록 설정하려면 값이 `false`인 앱 설정 `SCM_DO_BUILD_DURING_DEPLOYMENT`를 만듭니다.

## <a name="other-deployment-considerations"></a>기타 배포 고려 사항

### <a name="local-cache"></a>로컬 캐시

Azure App Service의 콘텐츠는 Azure Storage에 저장되며 영구적 방식으로 콘텐츠 공유로 표시됩니다. 그러나 일부 앱은 고가용성으로 실행할 수 있는 고성능 읽기 전용 콘텐츠 스토리지를 필요로 합니다. 이러한 앱은 [로컬 캐시](overview-local-cache.md)를 사용할 수 있는 이점이 있습니다. WordPress 등의 콘텐츠 관리 사이트에서는 로컬 캐시 사용이 권장되지 않습니다.

가동 중지 시간을 방지하려면 항상 [배포 슬롯](deploy-staging-slots.md)과 함께 로컬 캐시를 사용합니다. 이러한 기능을 함께 사용하는 방법에 대한 자세한 내용은 [이 섹션](overview-local-cache.md#best-practices-for-using-app-service-local-cache)을 참조하세요.

### <a name="high-cpu-or-memory"></a>높은 CPU 또는 메모리

App Service 계획에서 사용 가능한 CPU 또는 메모리의 90%를 초과하여 사용하는 경우 기본 가상 머신에서 배포를 처리하는 데 문제가 있을 수 있습니다. 이 문제가 발생하면 배포를 수행하기 위해 인스턴스 수를 일시적으로 확장합니다. 배포가 완료되면 인스턴스 수를 이전 값으로 반환할 수 있습니다.

모범 사례에 대한 자세한 내용은 [App Service 진단](./overview-diagnostics.md)을 참조하세요. 리소스와 관련된 적용 가능한 모범 사례를 확인합니다.

- [Azure Portal](https://portal.azure.com)에서 해당 웹앱으로 이동합니다.
- 왼쪽 탐색 영역에서 **문제 진단 및 해결** 을 클릭하면 App Service 진단이 열립니다.
- **모범 사례** 홈페이지 타일을 선택합니다.
- **가용성 및 성능을 위한 모범 사례** 또는 **최적의 구성을 위한 모범 사례** 를 클릭하여 이러한 모범 사례에 관련된 앱의 현재 상태를 확인합니다.

이 링크(`https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`)를 사용하여 리소스에 대한 App Service 진단을 직접 열 수도 있습니다.

## <a name="more-resources"></a>추가 리소스

[환경 변수 및 앱 설정 참조](reference-app-settings.md)