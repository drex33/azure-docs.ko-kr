---
title: Azure Pipelines를 사용하여 Azure VM에 CI/CD 설정
description: YAML 기반 Azure 파이프라인을 사용하여 Node.js 앱의 CI(연속 통합) 및 CD(지속적인 배포)를 Azure VM에 설정하는 방법을 알아봅니다.
ms.service: virtual-machines
author: ushan
tags: azure-devops-pipelines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 1118fb9d41ace11adb55adedc4b3700c3c34e50a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003450"
---
# <a name="deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Azure에서 Azure DevOps Services 및 Azure Pipelines를 사용하여 Linux 가상 머신에 앱 배포

**적용 대상:** :heavy_check_mark: Linux VM 

CI(지속적인 통합) 및 CD(지속적인 배포)는 모든 코드를 커밋한 후에 코드를 빌드, 릴리스 및 배포할 수 있는 파이프라인을 구성합니다. 이 문서에는 Azure Pipelines를 사용하여 다중 머신 배포를 수행하기 위한 CI/CD 파이프라인 설정과 관련된 단계가 포함되어 있습니다.

Azure Pipelines는 온-프레미스 또는 클라우드 모두에서 가상 머신에 배포할 수 있는 완전한 기능을 갖춘 CI/CD 자동화 도구 세트를 제공합니다.

이 자습서에서는 YAML 기반 CI/CD 파이프라인을 설정하여 앱을 Azure Pipelines [환경](/azure/devops/pipelines/process/environments)에 배포합니다. 이 경우 Linux 가상 머신을 리소스로 사용하여 각각 앱을 실행하는 웹 서버 역할을 합니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 샘플 앱을 가져옵니다.
> * 샘플 앱을 빌드하기 위한 YAML 기반 Azure Pipelines CI 파이프라인을 만듭니다.
> * Azure 가상 머신에 대한 Azure Pipelines 환경을 만듭니다.
> * Azure Pipelines CD 파이프라인을 만듭니다.
> * 수동 및 CI 트리거 배포 실행

## <a name="before-you-begin"></a>시작하기 전에

* Azure DevOps Services 조직( **https://dev.azure.com/** )에 로그인합니다. 
  [무료 Azure DevOps Services 조직](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)을 얻을 수 있습니다.

  > [!NOTE]
  > 자세한 내용은 [Azure DevOps Services에 연결](/azure/devops/organizations/projects/connect-to-projects)을 참조하세요.

*  배포 대상으로 Linux 가상 머신이 필요합니다.  자세한 내용은 [Azure CLI로 Linux VM 만들기 및 관리](./tutorial-manage-vm.md)를 참조하세요.

*  가상 컴퓨터의 인바운드 포트 80을 엽니다. 자세한 내용은 [Azure Portal을 사용하여 네트워크 보안 그룹 만들기](../../virtual-network/tutorial-filter-network-traffic.md)를 참조하세요.

## <a name="get-your-sample-app-code"></a>샘플 앱 코드 가져오기

배포하려는 앱이 GitHub에 이미 있는 경우 해당 코드에 대한 파이프라인을 만들어 볼 수 있습니다.

그러나 새 사용자인 경우 샘플 코드를 사용하여 더 효율적인 시작을 수행할 수 있습니다. 이 경우 GitHub에서 이 리포지토리를 포크합니다.

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic은 [Maven](https://spring.io/guides/gs/maven/)을 사용하여 빌드된 [Java Spring Boot](https://spring.io/guides/gs/spring-boot) 애플리케이션입니다.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> 이 Node.js 앱은 [Yeoman](https://yeoman.io/learning/index.html)을 통해 빌드되었습니다. Express, bower 및 grunt를 사용합니다. 그리고 일부 npm 패키지가 종속성으로 포함되어 있습니다.
> 샘플에는 Nginx를 설정하고 앱을 배포하는 스크립트도 포함되어 있습니다. 이 스크립트는 가상 머신에서 실행됩니다. 특히 이 스크립트는 다음을 수행합니다.
> 1. Node, Nginx 및 PM2를 설치합니다.
> 2. Nginx 및 PM2를 구성합니다.
> 3. 노드 앱을 시작합니다.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Linux VM의 필수 조건

위에서 언급한 샘플 앱은 Ubuntu 16.04에서 테스트되었으며, 이 빠른 시작에서는 동일한 버전의 Linux VM을 사용하는 것이 좋습니다.
앱에 사용된 런타임 스택에 따라 아래에 설명된 추가 단계를 수행합니다.

#### <a name="java"></a>[Java](#tab/java)

- Java Spring Boot 및 Spring Cloud 기반 앱을 배포하려면 완전히 지원되는 OpenJDK 기반 런타임을 제공하는 [이 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004)을 사용하여 Azure에서 Linux VM을 만듭니다.
- Tomcat 서버에서 Java 서블릿을 배포하려면 [이 Azure 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004)을 사용하여 Java 8이 포함된 Linux VM을 만들고 [Tomcat 9.x를 서비스로 구성](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)합니다.
- Java EE 기반 앱을 배포하려면 Azure 템플릿을 사용하여 [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90), [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) 또는 [Linux VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14를 만듭니다. 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

JavaScript 앱 또는 Node.js 앱을 설치하려면 앱을 배포하는 Nginx 웹 서버가 포함된 Linux VM이 필요합니다.
Nginx가 포함된 Linux VM이 아직 없는 경우 지금 Azure에서 [이 예제](./quick-create-cli.md)의 단계를 사용하여 만듭니다.

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Azure 가상 머신을 사용하여 Azure Pipelines 환경 만들기

가상 머신은 [환경](/azure/devops/pipelines/process/environments) 내에서 리소스로 추가하고, 다중 머신 배포의 대상으로 지정할 수 있습니다. 환경 내의 배포 기록 보기는 VM, 파이프라인, 커밋의 순서로 추적할 수 있는 기능을 제공합니다.

"**파이프라인**" 섹션 내의 "**환경**" 허브에서 환경을 만들 수 있습니다.
1.  Azure DevOps 조직에 로그인하고, 프로젝트로 이동합니다.
2.  프로젝트에서 **파이프라인** 페이지로 이동합니다. 그런 다음, **환경** 을 선택하고, **환경 만들기** 를 클릭합니다. 환경에 대한 **이름**(필수) 및 **설명** 을 지정합니다.
3.  환경에 추가할 **리소스** 로 **Virtual Machines** 를 선택하고, **다음** 을 클릭합니다.
4.  운영 체제(Windows/Linux)를 선택하고, **PS 등록 스크립트를 복사** 합니다. 
5.  이제 관리자 PowerShell 명령 프롬프트에서 복사한 스크립트를 이 환경에 등록할 각 대상 VM에서 실행합니다.
    > [!NOTE]
    > - 로그인한 사용자의 개인 액세스 토큰은 스크립트에 미리 삽입되어 당일에 만료되므로 복사한 스크립트는 사용할 수 없게 됩니다.
    > - VM에 이미 실행 중인 에이전트가 있는 경우 환경에 등록할 "에이전트"에 고유한 이름을 제공합니다.
6.  VM이 등록되면 환경의 "리소스" 탭 아래에 환경 리소스로 표시되기 시작합니다.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  더 많은 VM을 추가하려면 "리소스 추가"를 클릭하고 리소스로 "Virtual Machines"를 선택하여 스크립트를 다시 보고 복사할 수 있습니다. 이 스크립트는 이 환경에 추가할 모든 VM에 대해 동일하게 유지됩니다. 
8.  각 머신은 Azure Pipelines와 상호 작용하여 앱의 배포를 조정합니다.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. 태그를 대화형 PowerShell 등록 스크립트의 일부로 VM에 추가할 수 있습니다. 또는 리소스 보기에서 각 VM 리소스의 끝에 있는 줄임표를 클릭하여 리소스 보기에서 동일한 태그를 추가/제거할 수도 있습니다.

   할당한 태그를 사용하면 배포 작업에서 환경을 사용할 때 특정 가상 머신으로 배포하도록 제한할 수 있습니다. 태그는 각각 256자로 제한되지만, 사용할 수 있는 태그의 수는 제한되지 않습니다.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>CI 빌드 파이프라인 정의

웹 애플리케이션을 게시하는 CI(지속적인 통합) 빌드 파이프라인과 Ubuntu 서버에서 로컬로 실행할 수 있는 배포 스크립트가 필요합니다. 사용하려는 런타임에 따라 CI 빌드 파이프라인을 설정합니다. 

1. Azure DevOps 조직에 로그인하고, 프로젝트로 이동합니다.

1. 프로젝트에서 **파이프라인** 페이지로 이동합니다. 그런 다음, 작업을 선택하여 새 파이프라인을 만듭니다.

1. 먼저 소스 코드의 위치로 **GitHub** 를 선택하여 마법사의 단계를 진행합니다.

1. 로그인할 GitHub로 리디렉션될 수 있습니다. 그렇다면 GitHub 자격 증명을 입력합니다.

1. 리포지토리 목록이 표시되면 원하는 샘플 앱 리포지토리를 선택합니다.

1. Azure Pipelines에서 리포지토리를 분석하고 적절한 파이프라인 템플릿을 추천합니다.

#### <a name="java"></a>[Java](#tab/java)

**스타터** 템플릿을 선택하고, Apache Maven을 사용하여 Java 프로젝트를 빌드하고 테스트를 실행하는 아래 YAML 코드 조각을 복사합니다.

```yaml
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

자세한 지침은 [Maven을 사용하여 Java 앱 빌드](/azure/devops/pipelines/ecosystems/java)에 설명된 단계를 따르세요.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

**스타터** 템플릿을 선택하고 npm을 사용하여 일반 Node.js 프로젝트를 빌드하는 아래 YAML 코드 조각을 복사합니다.

```yaml
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

자세한 지침은 [gulp를 사용하여 Node.js 앱 빌드](/azure/devops/pipelines/ecosystems/javascript)의 단계를 따르세요.

- 파이프라인을 살펴보고 수행하는 작업을 확인합니다. 모든 기본 입력이 코드에 적합한지 확인합니다.

- **저장 후 실행** 을 선택하고 **기본 분기에 직접 커밋** 을 선택한 다음, **저장 후 실행** 을 다시 선택합니다.

- 새 실행이 시작됩니다. 실행이 완료될 때까지 기다립니다.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Linux VM에 배포할 CD 단계 정의

1. 아래 YAML 구문을 사용하여 이전에 사용했던 VM 리소스와 환경을 참조하여 [배포 작업](/azure/devops/pipelines/process/deployment-jobs)을 포함하도록 위의 파이프라인에 대한 YAML 파일을 변경합니다.

   ```yaml
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. 환경에서 각 가상 머신에 대해 정의한 **tags** 를 지정하여 배포를 받는 환경에서 특정 가상 머신 세트를 선택할 수 있습니다.
[여기](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job)에는 배포 작업에 대한 전체 YAML 스키마가 있습니다.

3. 배포 전략으로 `runOnce` 또는 `rolling`을 지정할 수 있습니다. 

   `runOnce`는 모든 수명 주기 후크(즉, `preDeploy` `deploy`, `routeTraffic` 및 `postRouteTraffic`)가 한 번 실행되는 가장 간단한 배포 전략입니다. 그런 다음, `on:` `success` 또는 `on:` `failure`가 실행됩니다.

   `runOnce`의 YAML 코드 조각 예제는 다음과 같습니다.
   ```yaml
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. 다음은 각 반복에서 최대 5개의 대상에 대한 가상 머신 업데이트의 롤링 전략을 정의하는 데 사용할 수 있는 YAML 코드 조각의 예제입니다. `maxParallel`은 동시에 배포할 수 있는 대상의 수를 결정합니다. 이 선택 항목은 배포되는 대상을 제외하고 언제든지 사용할 수 있어야 하는 대상의 절대 수 또는 백분율을 계산합니다. 또한 배포 중에 성공 및 실패 조건을 확인하는 데도 사용됩니다.

   ```yaml
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   이 작업을 실행할 때마다 VM을 만들고 등록한 `<environment name>` 환경에 대한 배포 기록이 기록됩니다.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>파이프라인 실행 및 환경에서 추적 가능성 보기 가져오기
환경의 배포 보기는 커밋 및 작업 항목에 대한 완전한 추적 가능성과 환경/리소스당 파이프라인 배포 기록을 제공합니다.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>다음 단계
- 방금 만든 [파이프라인 사용자 지정](/azure/devops/pipelines/customize-pipeline)으로 진행할 수 있습니다.
- YAML 파이프라인에서 수행할 수 있는 다른 작업에 대한 자세한 내용은 [YAML 스키마 참조](/azure/devops/pipelines/yaml-schema)를 참조하세요.
- LAMP(Linux, Apache, MySQL 및 PHP) 스택을 배포하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [LAMP 스택 배포](tutorial-lamp-stack.md)
