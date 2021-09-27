---
title: Azure Spring Cloud 애플리케이션 배포 자동화
description: Azure Pipelines에 대한 Azure Spring Cloud 작업을 사용하는 방법을 설명합니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: b3e5520f91301471bfc9df6bbd4a9cd4a8bc6a6a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666065"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Azure Spring Cloud 애플리케이션 배포 자동화

이 문서에서는 [Azure Pipelines에 대한 Azure Spring Cloud 작업](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)을 사용하여 애플리케이션을 배포하는 방법을 보여 줍니다.

연속 통합 및 지속적인 업데이트 도구를 사용하면 최소한의 노력과 위험으로 기존 애플리케이션에 업데이트를 신속하게 배포할 수 있습니다. Azure DevOps는 이러한 핵심 작업을 구성하고 제어하는 데 도움이 됩니다. 

다음 비디오에서는 Azure Pipelines를 포함하여 선택한 도구를 사용한 엔드투엔드 자동화를 설명합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager 서비스 연결 만들기

먼저 Azure DevOps 프로젝트에 대 한 Azure Resource Manager 서비스 연결을 만듭니다. 자세한 내용은 [커넥트 Microsoft Azure를](/azure/devops/pipelines/library/connect-to-azure)참조 하세요. Azure 스프링 클라우드 서비스 인스턴스에 사용 하는 것과 동일한 구독을 선택 해야 합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

이제 일련의 작업을 사용 하 여 프로젝트를 빌드하고 배포할 수 있습니다. 다음 Azure Pipelines 템플릿은 변수, 응용 프로그램을 빌드하는 .net Core 작업 및 응용 프로그램을 배포 하는 Azure 스프링 클라우드 작업을 정의 합니다.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>Azure 스프링 클라우드 인스턴스 및 Azure DevOps 프로젝트 설정

먼저 다음 단계를 사용 하 여 Azure DevOps와 함께 사용할 기존 Azure 스프링 클라우드 인스턴스를 설정 합니다.

1. Azure 스프링 클라우드 인스턴스로 이동한 다음 새 앱을 만듭니다. 
1. Azure DevOps 포털로 이동한 후 선택한 조직 아래에 새 프로젝트를 만듭니다. Azure DevOps 조직이 없는 경우 무료로 만들 수 있습니다.
1. **Repos** 를 선택 하 고 [스프링 부팅 데모 코드](https://github.com/spring-guides/gs-spring-boot) 를 리포지토리로 가져옵니다.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager 서비스 연결 만들기

다음으로 Azure DevOps 프로젝트에 대 한 Azure Resource Manager 서비스 연결을 만듭니다. 자세한 내용은 [커넥트 Microsoft Azure를](/azure/devops/pipelines/library/connect-to-azure)참조 하세요. Azure 스프링 클라우드 서비스 인스턴스에 사용 하는 것과 동일한 구독을 선택 해야 합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

이제 일련의 작업을 사용 하 여 프로젝트를 빌드하고 배포할 수 있습니다. 다음 섹션에서는 Azure DevOps를 사용 하 여 앱을 배포 하기 위한 다양 한 옵션을 보여 줍니다.

### <a name="deploy-using-a-pipeline"></a>파이프라인을 사용 하 여 배포

파이프라인을 사용 하 여 배포 하려면 다음 단계를 수행 합니다.

1. **Pipelines** 를 선택 하 고 Maven 템플릿을 사용 하 여 새 파이프라인을 만듭니다.
1. *Azure-pipelines* 파일을 편집 하 여 `mavenPomFile` 필드를 *' complete/pom.xml '* 로 설정 합니다.
1. 오른쪽에서 **보조자 표시** 를 선택한 다음, **Azure 스프링 클라우드** 템플릿을 선택 합니다.
1. Azure 구독에 대해 만든 서비스 연결을 선택한 다음, 스프링 클라우드 인스턴스 및 앱 인스턴스를 선택 합니다. 
1. **스테이징 배포 사용** 을 사용 하지 않도록 설정 합니다.
1. **Package 또는 folder** 를 *complete/target/spring-boot-complete-0.0.1-snapshot* 으로 설정 합니다.
1. **추가** 를 선택 하 여이 작업을 파이프라인에 추가 합니다.
  
   파이프라인 설정은 다음 이미지와 일치 해야 합니다.

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="파이프라인 설정의 스크린샷" lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   또한 다음 파이프라인 템플릿을 사용 하 여 프로젝트를 빌드하고 배포할 수 있습니다. 이 예에서는 먼저 Maven 작업을 정의 하 여 응용 프로그램을 빌드한 다음, Azure Pipelines에 대 한 Azure 스프링 클라우드 작업을 사용 하 여 JAR 파일을 배포 하는 두 번째 작업을 정의 합니다.

   ```yaml
   steps:
   - task: Maven@3
     inputs:
       mavenPomFile: 'complete/pom.xml'
   - task: AzureSpringCloud@0
     inputs:
       azureSubscription: '<your service connection name>'
       Action: 'Deploy'
       AzureSpringCloud: <your Azure Spring Cloud service>
       AppName: <app-name>
       UseStagingDeployment: false
       DeploymentName: 'default'
       Package: ./target/your-result-jar.jar
   ```

3. **저장 및 실행** 을 선택 하 고 작업이 완료 될 때까지 기다립니다.

### <a name="blue-green-deployments"></a>파란색-녹색 배포

이전 섹션에 표시 된 배포는 배포 시 즉시 응용 프로그램 트래픽을 받습니다. 이렇게 하면 고객 트래픽을 받기 전에 프로덕션 환경에서 응용 프로그램을 테스트할 수 있습니다.

#### <a name="edit-the-pipeline-file"></a>파이프라인 파일 편집

이전에 표시 된 것과 동일한 방식으로 응용 프로그램을 빌드하고 스테이징 배포에 배포 하려면 다음 템플릿을 사용 합니다. 이 예에서는 스테이징 배포가 이미 있어야 합니다. 대체 접근 방식은 [파란색-녹색 배포 전략](concepts-blue-green-deployment-strategies.md)을 참조하세요.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

#### <a name="use-the-releases-section"></a>릴리스 섹션 사용

다음 단계에서는 **릴리스** 섹션에서 파랑-녹색 배포를 사용 하도록 설정 하는 방법을 보여 줍니다.

1. **Pipelines** 를 선택 하 고 Maven 빌드 및 게시 아티팩트에 대 한 새 파이프라인을 만듭니다.
   1. 코드 위치에 **Azure Repos Git** 를 선택 합니다.
   1. 코드가 있는 리포지토리를 선택 합니다.
   1. **Maven** 템플릿을 선택 하 고 필드를로 설정 하는 파일을 수정 합니다 `mavenPomFile` *`complete/pom.xml`* .
   1. 오른쪽에서 **보조자 표시** 를 선택 하 고 **빌드 아티팩트 게시** 템플릿을 선택 합니다.
   1. *Complete/target/spring-boot-complete-0.0.1-SNAPSHOT* 에 **게시할 경로** 를 설정 합니다.
   1. **저장 및 실행** 을 선택합니다.

1. 릴리스 **를 선택한** 다음 **릴리스를 만듭니다**.
1. 새 파이프라인을 추가 하 고 **빈 작업** 을 선택 하 여 작업을 만듭니다.
1. **스테이지** 에서 **1 번째 작업, 0 개 작업을** 선택 합니다.

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="작업에 작업을 추가 하기 위해 선택할 수 있는 위치에 대 한 스크린샷":::

   1. 작업 **+** 에 작업을 추가 하려면를 선택 합니다.
   1. **Azure 스프링 클라우드** 템플릿을 검색 한 다음 **추가** 를 선택 하 여 작업에 작업을 추가 합니다.
   1. **Azure 스프링 클라우드 배포** 를 선택 하 여 작업을 편집 합니다.
   1. 앱 정보를 사용 하 여이 작업을 채운 후 **준비 배포 사용** 을 사용 하지 않도록 설정 합니다.
   1. **새 스테이징 배포 (없는 경우**)를 만든 다음 **배포** 에 이름을 입력 합니다.
   1. **저장** 을 선택 하 여이 작업을 저장 합니다.
   1. **확인** 을 선택합니다.
1. **파이프라인** 을 선택 하 고 **아티팩트 추가** 를 선택 합니다.
   1. **원본 (빌드 파이프라인)** 에서 이전에 만든 파이프라인을 선택 합니다.
   1. **추가** 를 선택한 다음, **저장** 을 선택합니다.
1. **스테이지** 에서 **1 개 작업, 1 개 작업을** 선택 합니다.
1. **1 단계** 에서 **Azure 스프링 클라우드 배포** 작업으로 이동한 다음 **패키지 또는 폴더** 옆에 있는 줄임표를 선택 합니다.
1. 대화 상자에서 *spring-boot-complete-0.0.1-SNAPSHOT* 을 선택 하 고 **확인** 을 선택 합니다.

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="' 파일 또는 폴더 선택 ' 대화 상자의 스크린샷":::

1. **+** 작업에 다른 **Azure 스프링 클라우드** 작업을 추가 하려면를 선택 합니다.
2. **프로덕션 배포를 설정** 하는 작업을 변경 합니다.
3. **저장** 을 선택 하 고 **릴리스 만들기** 를 선택 하 여 배포를 자동으로 시작 합니다. 

앱의 현재 릴리스 상태를 확인 하려면 **릴리스 보기** 를 선택 합니다. 이 작업이 완료 되 면 Azure Portal를 방문 하 여 앱 상태를 확인 합니다.

### <a name="deploy-from-source"></a>소스에서 배포

별도의 빌드 단계 없이 Azure에 직접 배포 하려면 다음 파이프라인 템플릿을 사용 합니다.

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```

::: zone-end

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 첫 번째 Azure Spring Cloud 애플리케이션 배포](./quickstart.md)
