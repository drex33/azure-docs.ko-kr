---
title: Azure Spring Cloud 애플리케이션 배포 자동화
description: Azure Pipelines에 대한 Azure Spring Cloud 작업을 사용하는 방법을 설명합니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2ac965e54a66eb2489ae7a4bfa9cac363f494b4f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538006"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Azure Spring Cloud 애플리케이션 배포 자동화

이 문서에서는 [Azure Pipelines에 대한 Azure Spring Cloud 작업](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud)을 사용하여 애플리케이션을 배포하는 방법을 보여 줍니다.

연속 통합 및 지속적인 업데이트 도구를 사용하면 최소한의 노력과 위험으로 기존 애플리케이션에 업데이트를 신속하게 배포할 수 있습니다. Azure DevOps는 이러한 핵심 작업을 구성하고 제어하는 데 도움이 됩니다. 

다음 비디오에서는 Azure Pipelines를 포함하여 선택한 도구를 사용한 엔드투엔드 자동화를 설명합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager 서비스 연결 만들기

Azure DevOps 프로젝트에 대한 Azure Resource Manager 서비스 연결을 만드는 방법에 대해 알아보려면 [이 문서](/azure/devops/pipelines/library/connect-to-azure)를 참조하세요. Azure 스프링 클라우드 서비스 인스턴스에 사용하는 것과 동일한 구독을 선택해야 합니다.

## <a name="build-and-deploy-apps"></a>앱 빌드 및 배포

::: zone pivot="programming-language-csharp"

### <a name="deploy-artifacts"></a>아티팩트 배포

일련의 작업을 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 이 코드 조각은 변수, 애플리케이션을 빌드하는 .NET Core 작업 및 애플리케이션을 배포하는 Azure Spring Cloud 작업을 정의합니다.

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

### <a name="deploy-artifacts"></a>아티팩트 배포

#### <a name="to-production"></a>프로덕션으로

일련의 작업을 사용하여 프로젝트를 빌드하고 배포할 수 있습니다. 이 코드 조각은 먼저 애플리케이션을 빌드하는 Maven 작업을 정의한 다음, Azure Pipelines에 대한 Azure Spring Cloud 작업을 사용하여 JAR 파일을 배포하는 두 번째 작업을 정의합니다.

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
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: ./target/your-result-jar.jar
```

#### <a name="blue-green-deployments"></a>파란색-녹색 배포

이전 섹션에 표시된 배포는 배포 시 애플리케이션 트래픽을 즉시 수신합니다. 경우에 따라 개발자는 애플리케이션이 고객 트래픽을 수신하기 전에 프로덕션 환경에서 애플리케이션을 테스트하려고 합니다.

다음 코드 조각은 위와 동일한 방식으로 애플리케이션을 빌드한 다음 스테이징 배포에 배포합니다. 이 예에서는 스테이징 배포가 이미 있어야 합니다. 대체 접근 방식은 [파란색-녹색 배포 전략](concepts-blue-green-deployment-strategies.md)을 참조하세요.

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

### <a name="deploy-from-source"></a>소스에서 배포

별도의 빌드 단계 없이 Azure에 직접 배포할 수 있습니다.

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
