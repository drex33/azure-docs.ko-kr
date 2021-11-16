---
title: Azure DevOps를 사용하여 컨테이너화된 애플리케이션에 대한 지속적인 배포
description: '자습서: Azure DevOps를 사용하여 컨테이너화된 애플리케이션에 대한 지속적인 배포'
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: rahugup
ms.openlocfilehash: 7d23b7c84cc7a7053bfa80b3f6e1b24c3bd4470c
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000653"
---
# <a name="continuous-deployment-for-containerized-applications-with-azure-devops"></a>Azure DevOps를 사용하여 컨테이너화된 애플리케이션에 대한 지속적인 배포

이 단계별 가이드에서는 Azure DevOps를 사용하여 2일차 작업을 위해 컨테이너화된 앱을 지속적으로 빌드하고 배포하는 파이프라인을 만드는 방법에 대해 알아봅니다. Dockerfile이 포함된 리포지토리에서 코드를 변경할 때마다 이미지가 Azure Container Registry로 푸시되고 매니페스트가 Azure Kubernetes Service 또는 Azure App Service에 배포됩니다.

Azure DevOps를 사용하면 무료 워크플로를 통해 코드를 호스트, 빌드, 계획 및 테스트할 수 있습니다. Azure Pipelines를 이러한 워크플로 중 하나로 사용하면 모든 플랫폼 및 클라우드에서 작동하는 CI/CD를 사용하여 애플리케이션을 배포할 수 있습니다. 파이프라인은 리포지토리의 루트 디렉터리에서 YAML 파일로 정의됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 다음을 수행해야 합니다.

-  Azure Migrate 앱 컨테이너화를 사용하여 ASP.NET 또는 Java 웹앱을 컨테이너화하고 배포합니다.
-  리포지토리를 만들 수 있는 GitHub 계정. Microsoft 계정이 없는 경우에는 [무료로 만들 수 있습니다](https://github.com/).
-  Azure DevOps 조직. Microsoft 계정이 없는 경우에는 [무료로 만들 수 있습니다](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). (Azure DevOps 조직은 GitHub 조직과 다릅니다. DevOps 조직과 GitHub 조직 간에 일치시키려는 경우 동일한 이름을 지정할 수 있습니다.) <br/> 팀에 이미 있는 경우 사용하려는 Azure DevOps 프로젝트의 관리자인지 확인합니다.
-  Microsoft 호스팅 에이전트에서 파이프라인을 실행하는 기능. [병렬 작업](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops)을 구매하거나 무료 계층을 요청할 수 있습니다. 무료 계층을 요청하려면 [이 문서](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops)의 지침을 따릅니다. 무료 계층을 부여하는 데 영업일 기준 2~3일이 걸릴 수 있습니다.


## <a name="locate-the-artifacts"></a>아티팩트 찾기

Azure Migrate 앱 컨테이너화 도구는 Azure Pipelines를 사용하여 애플리케이션에 대한 CI/CD 워크플로를 구성하는 데 사용할 수 있는 아티팩트를 자동으로 생성합니다. 도구를 통해 애플리케이션 배포가 완료되면 아티팩트가 생성됩니다. 다음과 같이 아티팩트를 찾을 수 있습니다. 

1. Azure Migrate 앱 컨테이너화 도구를 실행하는 컴퓨터로 이동합니다. 
2. **C:\ProgramData\Microsoft Azure Migrate App Containerization** 디렉터리로 이동합니다. C:\ProgramData로 이동할 수 없는 경우 파일 탐색기의 *보기* 아래에서 *숨겨진 항목* 을 표시하는 옵션을 선택해야 합니다. 
3. **원본 컴퓨터 IP/FQDN** 에 해당하는 디렉터리를 선택합니다. 원본 컴퓨터는 컨테이너화된 애플리케이션을 실행하는 앱 컨테이너화 도구에 지정된 컴퓨터입니다.
4. Java 애플리케이션의 경우 
    - **JavaTomcatWebApp\Artifacts** 로 이동합니다.    
    - **Catalina\localhost** 디렉터리로 이동합니다. 이 디렉터리를 찾을 수 없으면 Tomcat 엔진 이름 및 호스트 이름에 해당하는 디렉터리로 이동합니다.
    - 이 디렉터리에서 애플리케이션 폴더를 찾습니다. 
5. ASP.NET 애플리케이션의 경우
    - **IISAspNetWebApp** 으로 이동합니다.
    - 이 디렉터리에서 애플리케이션 폴더를 찾습니다.


## <a name="upload-artifacts-to-github"></a>GitHub에 아티팩트 업로드

아티팩트는 Azure DevOps에서 사용할 원본 리포지토리에 업로드해야 합니다. 

1. GitHub 계정에 로그인합니다. 
2. [이 문서](https://docs.github.com/get-started/quickstart/create-a-repo)의 단계에 따라 새 리포지토리를 만듭니다. 
3. 다음 단계는 다음 아티팩트를 이 리포지토리에 업로드하는 것입니다.
   -  Java 앱의 경우 앱 컨테이너화 도구를 실행하는 컴퓨터의 애플리케이션 폴더에서 다음 폴더 및 파일을 선택합니다.
        - MandatoryArtifacts 폴더
        - manifests 폴더
        - OptionalArtifacts 폴더
        - Dockerfile 
        - Entryscript.sh 파일
        - azure-pipelines.yml 파일
    - ASP.NET 앱의 경우 앱 컨테이너화 도구를 실행하는 컴퓨터의 애플리케이션 폴더에서 다음 폴더 및 파일을 선택합니다.
        - manifests 폴더
        - 빌드 폴더
        - azure-pipelines.yml 파일

## <a name="sign-in-to-azure-pipelines"></a>Azure Pipelines 로그인

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines)에 로그인합니다. 로그인하면 브라우저가 https://dev.azure.com/my-organization-name 으로 이동하여 Azure DevOps 대시보드를 표시합니다.

선택한 조직 내에서 *프로젝트* 를 만듭니다. 조직에 프로젝트가 없으면 **프로젝트를 만들어 시작** 화면이 표시됩니다. 그렇지 않으면 대시보드의 오른쪽 위 모서리에서 **프로젝트 만들기** 단추를 선택합니다.

## <a name="add-service-connections"></a>서비스 연결 추가

파이프라인을 만들기 전에 템플릿에서 연결을 선택하고 확인하라는 메시지가 표시되므로 먼저 서비스 연결을 만들어야 합니다. 서비스 연결을 사용하면 작업 템플릿을 사용할 때 Azure Container Registry에 연결하고 애플리케이션을 배포하려는 Azure 구독에 연결할 수 있습니다. 

1. 왼쪽 아래 모서리에서 **프로젝트 설정 > 서비스 연결** 을 차례로 선택합니다.
2. **새 서비스 연결** 을 선택하고, 필요한 서비스 연결 형식에 대해 **Docker 레지스트리 > Azure Container Registry** 옵션을 차례로 선택하고, [다음]을 선택합니다.
3. 인증 방법을 선택하고, [다음]을 선택합니다.
4. 서비스 연결에 대한 매개 변수를 입력합니다. 매개 변수 목록은 서비스 연결의 각 형식에 따라 다릅니다. 자세한 내용은 [서비스 연결 형식 및 관련 매개 변수 목록](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#common-service-connection-types)을 참조하세요.
5. **저장** 을 선택하여 연결을 만듭니다.
6. 연결이 만들어지고 매개 변수가 입력되면 연결의 **유효성을 검사** 합니다. 유효성 검사 링크는 입력한 정보와 함께 외부 서비스에 대한 REST 호출을 사용하고 호출이 성공했는지 여부를 나타냅니다.
7. **새 서비스 연결 > Azure Resource Manager** 를 차례로 선택하여 Azure 구독에 대한 서비스 연결을 만드는 동일한 단계를 반복합니다. 
8. 두 서비스 연결에 대한 리소스 ID를 적어 둡니다. 

## <a name="create-the-pipeline"></a>파이프라인 만들기

이제 두 서비스 연결을 모두 만들었으므로 파이프라인을 구성할 수 있습니다. 파이프라인 YAML은 앱 컨테이너화 도구에서 자동으로 만들어지며 다음과 같이 구성할 수 있습니다.  

1. Pipelines로 이동한 다음, **새 파이프라인** 을 선택합니다.
2. 먼저 소스 코드의 위치로 **GitHub** 를 선택하여 마법사의 단계를 진행합니다.
3. 로그인할 GitHub로 리디렉션될 수 있습니다. 그렇다면 GitHub 자격 증명을 입력합니다.
4. 리포지토리 목록이 표시되면 리포지토리를 선택합니다.
5. Azure Pipelines 앱을 설치하도록 GitHub로 리디렉션될 수 있습니다. 그렇다면 [승인 및 설치]를 선택합니다.
6. 새 파이프라인이 표시되면 YAML을 검토하여 수행하는 작업을 확인합니다.
7. YAML에서 Azure Container Registry 서비스 연결에 대한 리소스 ID를 **dockerRegistryServiceConnection** 변수 값으로 제공합니다.
8. Azure Resource Manager 서비스 연결에 대한 리소스 ID를 **dockerRegistryServiceConnection** 변수 값으로 제공합니다.
9. 준비가 되면 **저장** 하여 새 파이프라인을 리포지토리에 커밋합니다. 

파이프라인은 2일차 작업을 위해 컨테이너화된 앱을 빌드하고 배포하기 위한 모든 설정입니다. 조직의 요구 사항에 맞게 [파이프라인을 사용자 지정](/azure/devops/pipelines/customize-pipeline?view=azure-devops#prerequisite)할 수 있습니다. 