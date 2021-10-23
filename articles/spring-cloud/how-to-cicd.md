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
ms.openlocfilehash: 4f1075a690c806be76b0102c2954a4f5127dabee
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258338"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Azure Spring Cloud 애플리케이션 배포 자동화

이 문서에서는 [Azure Pipelines에 대한 Azure Spring Cloud 작업](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)을 사용하여 애플리케이션을 배포하는 방법을 보여 줍니다.

연속 통합 및 지속적인 업데이트 도구를 사용하면 최소한의 노력과 위험으로 기존 애플리케이션에 업데이트를 신속하게 배포할 수 있습니다. Azure DevOps는 이러한 핵심 작업을 구성하고 제어하는 데 도움이 됩니다. 

다음 비디오에서는 Azure Pipelines를 포함하여 선택한 도구를 사용한 엔드투엔드 자동화를 설명합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager 서비스 연결 만들기

먼저 Azure DevOps 프로젝트에 대한 Azure Resource Manager 서비스 연결을 만듭니다. 자세한 내용은 [Microsoft Azure 커넥트](/azure/devops/pipelines/library/connect-to-azure)참조하세요. Azure Spring Cloud 서비스 인스턴스에 사용하려는 것과 동일한 구독을 선택해야 합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

이제 일련의 작업을 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 다음 Azure Pipelines 템플릿은 변수, 애플리케이션을 빌드하는 .NET Core 작업 및 애플리케이션을 배포하는 Azure Spring Cloud 작업을 정의합니다.

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

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>Azure Spring Cloud 인스턴스 및 Azure DevOps 프로젝트 설정

먼저 다음 단계를 사용하여 Azure DevOps 사용할 기존 Azure Spring Cloud 인스턴스를 설정합니다.

1. Azure Spring Cloud 인스턴스로 이동한 다음, 새 앱을 만듭니다. 
1. Azure DevOps 포털로 이동한 다음 선택한 조직에서 새 프로젝트를 만듭니다. Azure DevOps 조직이 없는 경우 무료로 만들 수 있습니다.
1. **Repos** 선택한 [다음, Spring Boot 데모 코드를](https://github.com/spring-guides/gs-spring-boot) 리포지토리로 가져옵니다.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager 서비스 연결 만들기

다음으로 Azure DevOps 프로젝트에 대한 Azure Resource Manager 서비스 연결을 만듭니다. 자세한 내용은 [Microsoft Azure 커넥트](/azure/devops/pipelines/library/connect-to-azure)참조하세요. Azure Spring Cloud 서비스 인스턴스에 사용하려는 것과 동일한 구독을 선택해야 합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

이제 일련의 작업을 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 다음 섹션에서는 Azure DevOps 사용하여 앱을 배포하는 다양한 옵션을 보여 줍니다.

### <a name="deploy-using-a-pipeline"></a>파이프라인을 사용하여 배포

파이프라인을 사용하여 배포하려면 다음 단계를 수행합니다.

1. **Pipelines** 선택한 다음, Maven 템플릿을 사용하여 새 파이프라인을 만듭니다.
1. *azure-pipelines.yml* 파일을 편집하여 `mavenPomFile` 필드를 *'complete/pom.xml'로* 설정합니다.
1. 오른쪽에서 **도우미 표시를** 선택한 다음, **Azure Spring Cloud** 템플릿을 선택합니다.
1. Azure 구독에 대해 만든 서비스 연결을 선택한 다음, Spring Cloud 인스턴스 및 앱 인스턴스를 선택합니다. 
1. **준비 배포 사용을** 사용하지 않도록 설정합니다.
1. **패키지 또는 폴더를** *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar 로* 설정합니다.
1. **추가를** 선택하여 이 작업을 파이프라인에 추가합니다.
  
   파이프라인 설정은 다음 이미지와 일치해야 합니다.

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="파이프라인 설정의 스크린샷." lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   다음 파이프라인 템플릿을 사용하여 프로젝트를 빌드하고 배포할 수도 있습니다. 이 예제에서는 먼저 애플리케이션을 빌드하는 Maven 작업을 정의한 다음, Azure Pipelines Azure Spring Cloud 작업을 사용하여 JAR 파일을 배포하는 두 번째 작업을 정의합니다.

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

3. **저장 후 실행을** 선택한 다음, 작업이 완료되기를 기다립니다.

### <a name="blue-green-deployments"></a>파란색-녹색 배포

이전 섹션에 표시된 배포는 배포 즉시 애플리케이션 트래픽을 수신합니다. 이렇게 하면 고객 트래픽을 받기 전에 프로덕션 환경에서 애플리케이션을 테스트할 수 있습니다.

#### <a name="edit-the-pipeline-file"></a>파이프라인 파일 편집

이전에 표시된 것과 동일한 방식으로 애플리케이션을 빌드하고 스테이징 배포에 배포하려면 다음 템플릿을 사용합니다. 이 예에서는 스테이징 배포가 이미 있어야 합니다. 대체 접근 방식은 [파란색-녹색 배포 전략](concepts-blue-green-deployment-strategies.md)을 참조하세요.

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

다음 단계에서는 릴리스 섹션에서 파란색-녹색 배포를 사용하도록 설정하는 방법을 보여 **줍니다.**

1. **Pipelines** 선택하고 Maven 빌드 및 게시 아티팩트용 새 파이프라인을 만듭니다.
   1. 코드 위치에 **Azure Repos Git을** 선택합니다.
   1. 코드가 있는 리포지토리를 선택합니다.
   1. **Maven** 템플릿을 선택하고 파일을 수정하여 `mavenPomFile` 필드를 로 *`complete/pom.xml`* 설정합니다.
   1. 오른쪽에서 **도우미 표시를** 선택하고 **빌드 아티팩트 게시 템플릿을** 선택합니다.
   1. **게시할 경로를** *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar 로* 설정합니다.
   1. **저장 및 실행** 을 선택합니다.

1. **릴리스를** 선택한 다음, **릴리스 만들기를** 선택합니다.
1. 새 파이프라인을 추가하고 **빈 작업을** 선택하여 작업을 만듭니다.
1. **단계에서** **줄 1 작업, 0 태스크를** 선택합니다.

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="작업에 태스크를 추가할 위치를 선택하는 스크린샷.":::

   1. 를 선택하여 **+** 작업에 태스크를 추가합니다.
   1. **Azure Spring Cloud** 템플릿을 검색한 다음, **추가를** 선택하여 태스크를 작업에 추가합니다.
   1. **Azure Spring Cloud 배포:** 를 선택하여 작업을 편집합니다.
   1. 이 작업을 앱의 정보로 채운 다음 **스테이징 배포 사용을** 사용하지 않도록 설정합니다.
   1. 새 **스테이징 배포 만들기(없는 경우)를** 사용하도록 설정한 다음, **배포** 에 이름을 입력합니다.
   1. **저장을** 선택하여 이 작업을 저장합니다.
   1. **확인** 을 선택합니다.
1. **파이프라인을** 선택한 다음, **아티팩트 추가를** 선택합니다.
   1. **원본(빌드 파이프라인)** 아래에서 이전에 만든 파이프라인을 선택합니다.
   1. **추가** 를 선택한 다음, **저장** 을 선택합니다.
1. **단계** 아래에서 **작업 1개, 태스크 1개** 를 선택합니다.
1. **1단계에서** **Azure Spring Cloud 배포** 작업으로 이동한 다음 패키지 또는 **폴더** 옆에 있는 말줄임표를 선택합니다.
1. 대화 *상자에서 spring-boot-complete-0.0.1-SNAPSHOT.jar을* 선택한 다음, **확인을** 선택합니다.

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="'파일 또는 폴더 선택' 대화 상자의 스크린샷.":::

1. 를 선택하여 **+** 작업에 다른 **Azure Spring Cloud** 태스크를 추가합니다.
2. 작업을 프로덕션 **배포 설정으로 변경합니다.**
3. **저장,** **릴리스 만들기를** 선택하여 배포를 자동으로 시작합니다. 

앱의 현재 릴리스 상태를 확인하려면 **릴리스 보기를** 선택합니다. 이 작업이 완료되면 Azure Portal 방문하여 앱 상태를 확인합니다.

### <a name="deploy-from-source"></a>소스에서 배포

별도의 빌드 단계 없이 Azure에 직접 배포하려면 다음 파이프라인 템플릿을 사용합니다.

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

* [빠른 시작: Azure Spring Cloud 첫 번째 Spring Boot 앱 배포](./quickstart.md)
