---
title: Synapse 작업 영역에 대한 연속 통합 및 배달
description: 연속 통합 및 배달을 사용하여 환경(개발, 테스트, 프로덕션) 간에 작업 영역의 변경 내용을 배포하는 방법을 알아봅니다.
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: b21bc818d83c3a48973b1121fb2f24f87638890b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220410"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse 작업 영역에 대한 연속 통합 및 배달

## <a name="overview"></a>개요

CI(연속 통합)는 팀 멤버가 커밋할 때마다 코드 작성 및 테스트를 자동화하는 프로세스가 버전 제어로 변경됩니다. CD(지속적인 배포)는 다중 테스트 또는 스테이징 환경에서 프로덕션 환경으로 빌드, 테스트, 구성 및 배포하는 프로세스입니다.

Azure Synapse Analytics 작업 영역에서 CI/CD(연속 통합 및 배달)는 모든 엔터티를 환경(개발, 테스트, 프로덕션) 간에 이동합니다. 작업 영역을 다른 작업 영역으로 승격하기 위한 두 부분이 있습니다. 먼저 [ARM 템플릿(Azure Resource Manager 템플릿)](../../azure-resource-manager/templates/overview.md)을 사용하여 작업 영역 리소스(풀 및 작업 영역)를 만들거나 업데이트합니다. 그런 다음, Azure DevOps 또는 GitHub의 Azure Synapse Analytics CI/CD 도구를 사용하여 아티팩트(SQL 스크립트, Notebook, Spark 작업 정의, 파이프라인, 데이터 세트, 데이터 흐름 등)를 마이그레이션합니다. 

이 문서에서는 Azure DevOps 릴리스 파이프라인 및 GitHub 작업을 사용하여 Azure Synapse 작업 영역을 여러 환경에 배포하는 작업을 자동화하는 방법을 간략하게 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Synapse 작업 영역을 여러 환경에 배포하는 작업을 자동화하려면 이러한 필수 구성 요소 및 구성이 준비되어 있어야 합니다.

### <a name="azure-devops"></a>Azure DevOps

- Azure DevOps 프로젝트가 릴리스 파이프라인을 실행할 준비가 되었습니다.
- [코드를 체크 인할 모든 사용자에게 조직 수준에서 "기본" 액세스 권한을 부여](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true)하여 리포지토리를 볼 수 있도록 합니다.
- Azure Synapse 리포지토리에 소유자 권한을 부여합니다.
- 자체 호스팅 Azure DevOps VM 에이전트를 만들었는지 또는 Azure DevOps 호스트된 에이전트를 사용했는지 확인합니다.
- [리소스 그룹에 대한 Azure Resource Manager 서비스 연결을 만들 수 있는](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true) 권한
- Azure AD(Azure Active Directory) 관리자는 [Azure DevOps 조직에 Azure DevOps Synapse 작업 영역 Deployment Agent 확장을 설치](/azure/devops/marketplace/install-extension)해야 합니다.
- 파이프라인을 실행할 기존 서비스 계정을 만들거나 지정합니다. 서비스 계정 대신 개인 액세스 토큰을 사용할 수 있지만 사용자 계정이 삭제된 후에는 파이프라인이 작동하지 않습니다.

### <a name="github"></a>GitHub

- Synapse 작업 영역 아티팩트 및 작업 영역 템플릿이 있는 GitHub 리포지토리 
- 자체 호스팅된 Runner를 만들었는지 또는 GitHub 호스팅된 Runner를 사용했는지 확인합니다.

### <a name="azure-active-directory"></a>Azure Active Directory

- Azure AD에서 서비스 주체를 사용하는 경우, 배포에 사용할 서비스 주체를 생성합니다. 
- 관리 ID를 사용하려면, Azure의 VM에서 시스템이 할당한 관리 ID를 에이전트 또는 Runner로 사용하도록 설정하고 Synapse Studio에 Synapse 관리자로 추가해야 합니다.
- 이 작업을 수행하려면 Azure AD 관리자 권한이 필요 합니다.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> 이러한 필수 구성 요소는 동일한 파이프라인, ARM 템플릿 또는 Azure CLI를 사용하여 자동화하고 배포할 수 있지만 이 문서에는 프로세스가 설명되어 있지 않습니다.

- 개발에 사용되는 "원본" 작업 영역은 Synapse Studio에서 Git 리포지토리로 구성되어야 합니다. 자세한 내용은 [Synapse Studio의 소스 제어](source-control.md#configuration-method-2-manage-hub)를 참조하세요.

- 배포할 빈 작업 영역. 빈 작업 영역을 설정하려면:

  1. 새 Azure Synapse Analytics 작업 영역을 만듭니다.
  1. 새 작업 영역이 호스트되는 리소스 그룹에 VM 에이전트 및 서비스 주체 기여자 권한을 부여합니다.
  1. 대상 작업 영역에서 Git 리포지토리 연결을 구성하지 마십시오.
  1. Azure Portal에서 새 Azure Synapse Analytics 작업 영역을 찾고 자신 및 Azure DevOps 파이프라인을 실행할 사람에게 Azure Synapse Analytics 작업 영역 소유자 권한을 부여합니다. 
  1. Azure DevOps VM 에이전트 및 서비스 주체를 작업 영역의 기여자 역할에 추가합니다. (이 역할은 상속되지만 상속되었는지 확인해야 합니다.)
  1. Azure Synapse Analytics 작업 영역에서 **Studio** > **Manage** > **Access Control** 로 이동합니다. Azure DevOps VM 에이전트 및 서비스 주체를 작업 영역 관리자 그룹에 추가합니다.
  1. 작업 영역에 사용되는 스토리지 계정을 엽니다. IAM에서 VM 에이전트 및 서비스 주체를 Storage Blob 데이터 기여자 선택 역할에 추가합니다.
  1. 지원 구독에서 키 자격 증명 모음을 만들고 기존 작업 영역과 새 작업 영역 모두에, 자격 증명 모음에 대해 최소한 GET 및 LIST 권한이 있는지 확인합니다.
  1. 자동화된 배포가 작동하려면 연결된 서비스에 지정된 연결 문자열이 주요 자격 증명 모음에 있는지 확인합니다.

### <a name="additional-prerequisites"></a>추가 필수 조건
 
 - Spark 풀 및 자체 호스팅 통합 런타임은 작업 영역 배포 작업에서 생성되지 않습니다. 자체 호스팅 통합 런타임을 사용하는 연결된 서비스가 있는 경우 새 작업 영역에서 수동으로 만듭니다.
 - 개발 작업 영역의 항목이 특정 풀과 연결된 경우, 매개 변수 파일에서 풀을 만들거나 매개 변수화한 대상 작업 영역의 풀 이름이 같은지 확인합니다.  
 - 배포를 시도할 때 프로비전된 SQL 풀이 일시 중지되면 배포가 실패할 수 있습니다.

자세한 내용은 [Azure Synapse Analytics의 CI CD 4부 - 릴리스 파이프라인](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)를 참조 하세요. 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>Azure DevOps에서 릴리스 파이프라인 설정

이 부분에서는 Azure DevOps에서 synapse를 배포하는 방법을 알아봅니다. 

1.  [Azure DevOps](https://dev.azure.com/)에서 릴리스에 대해 만든 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인** 을 선택한 다음 **릴리스** 를 선택합니다.

    ![파이프라인, 릴리스 선택](media/create-release-1.png)

1.  **새 파이프라인** 을 선택하거나, 기존 파이프라인이 있는 경우에는 **새로 만들기** 를 선택한 다음 **새 릴리스 파이프라인** 을 선택합니다.

1.  **빈 작업** 템플릿을 선택합니다.

    ![빈 작업 선택](media/create-release-select-empty.png)

1.  **단계 이름** 상자에 사용자 환경의 이름을 입력합니다.

1.  **아티팩트 추가** 를 선택한 다음, 개발 Synapse Studio로 구성된 git 리포지토리를 선택합니다. 풀 및 작업 영역의 ARM 템플릿을 관리하는 데 사용한 git 리포지토리를 선택합니다. GitHub를 원본으로 사용하는 경우 GitHub 계정 및 풀 리포지토리에 대한 서비스 연결을 만들어야 합니다. [서비스 연결](/azure/devops/pipelines/library/service-endpoints)에 대한 자세한 내용은 

    ![게시 분기 추가](media/release-creation-github.png)

1.  리소스 업데이트를 위한 ARM 템플릿 분기를 선택합니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    ![ARM 템플릿 추가](media/release-creation-arm-branch.png)

1.  **기본 분기** 에 대한 리포지토리의 [게시 분기](source-control.md#configure-publishing-settings)를 선택합니다. 기본적으로 이 게시 분기는 `workspace_publish`입니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    ![아티팩트 추가](media/release-creation-publish-branch.png)

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>리소스 생성 및 업데이트를 위한 ARM 템플릿의 스테이지 작업 설정 

Azure Synapse 작업 영역, Spark 및 SQL 풀 또는 키 자격 증명 모음과 같은 리소스를 배포하기 위한 ARM 템플릿이 있는 경우 해당 리소스를 만들거나 업데이트하는 Azure Resource Manager 배포 작업을 추가합니다.

1. 단계 보기에서 **단계 작업 보기** 를 선택합니다.

    ![단계 보기](media/release-creation-stage-view.png)

1. 새 작업을 만듭니다. **ARM 템플릿 배포** 를 검색한 후 **추가** 를 선택합니다.

1. 배포 작업에서 대상 작업 영역에 대한 구독, 리소스 그룹 및 위치를 선택합니다. 필요한 경우 자격 증명을 제공합니다.

1. **작업** 목록에서 **리소스 그룹 만들기 또는 업데이트** 를 선택합니다.

1. **템플릿** 상자 옆에 있는 줄임표 단추( **...** )를 선택합니다. 대상 작업 영역의 Azure Resource Manager 템플릿 찾아보기

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다.

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에서 대상 작업 영역에 대해 원하는 매개 변수 값을 입력합니다. 

1. **배포 모드** 에 대해 **증분** 을 선택합니다.
    
    ![작업 영역 및 풀 배포](media/pools-resource-deploy.png)

1. (선택 사항) 권한 부여 및 업데이트 작업 영역 역할 할당에 대해 **Azure PowerShell** 을 추가합니다. 릴리스 파이프라인을 사용하여 Synapse 작업 영역을 만드는 경우 파이프라인의 서비스 주체가 기본 작업 영역 관리자로 추가됩니다. PowerShell을 실행하여 다른 계정에 작업 영역에 대한 액세스 권한을 부여할 수 있습니다. 
    
    ![사용 권한 부여](media/release-creation-grant-permission.png)

 > [!WARNING]
> 전체 배포 모드에서는 리소스 그룹에 있지만 새 Resource Manager 템플릿에 지정되지 않은 리소스가 **삭제** 됩니다. 자세한 내용은 [Azure Resource Manager 배포 모드](../../azure-resource-manager/templates/deployment-modes.md)를 참조하세요.

### <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Synapse 아티팩트 배포에 대한 스테이지 작업 설정 

[Synapse 작업 영역 배포](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) 확장을 사용하여 데이터 세트, SQL 스크립트, Notebook, spark 작업 정의, 데이터 흐름, 파이프라인, 연결된 서비스, 자격 증명 및 IR(Integration Runtime)과 같은 Synapse 작업 영역에 다른 항목을 배포합니다.  

1. **Azure DevOps 마켓플레이스** 에서 확장을 검색하고 가져옵니다(https://marketplace.visualstudio.com/azuredevops) 

     ![확장 가져오기](media/get-extension-from-market.png)

1. 확장을 설치할 조직을 선택합니다. 

     ![확장 설치](media/install-extension.png)

1. Azure DevOps 파이프라인의 서비스 주체에게 구독 권한이 부여되고 대상 작업 영역에 대한 작업 영역 관리자로 할당되었는지 확인합니다. 

1. 새 작업을 만듭니다. **Synapse 작업 영역 배포** 를 검색한 다음, **추가** 를 선택합니다.

     ![확장 추가](media/add-extension-task.png)

1.  작업에서 **...** 을 선택합니다. **템플릿** 상자 옆의 템플릿 파일을 선택합니다.

1. **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다.

1. 연결, 리소스 그룹 및 대상 작업 영역의 이름을 선택합니다. 

1. **템플릿 매개 변수 재정의** 상자 옆에 있는 **…** 를 선택하고 연결된 서비스에서 사용되는 연결 문자열 및 계정 키를 포함하여 대상 작업 영역에 대해 원하는 매개 변수 값을 입력합니다. 자세한 정보는 [Azure Synapse Analytics의 CI/CD](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)를 참조하세요.

    ![Synapse 작업 영역 배포](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD 시나리오에서는 서로 다른 환경에서의 IR(통합 런타임) 형식이 동일해야 합니다. 예를 들어 개발 환경에 자체 호스팅 IR이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서 동일한 IR이 자체 호스팅 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 연결된 자체 호스팅으로 구성해야 합니다.

### <a name="create-release-for-deployment"></a>배포용 릴리스 만들기 

모든 변경 내용을 저장한 후 **릴리스 만들기** 를 선택하여 수동으로 릴리스를 만들 수 있습니다. 릴리스 만들기를 자동화하려면 [Azure DevOps 릴리스 트리거](/azure/devops/pipelines/release/triggers)를 참조하세요.

   ![릴리스 만들기 선택](media/release-creation-manually.png)

## <a name="set-up-a-release-with-github-action"></a>GitHub 작업을 사용하여 릴리스 설정 

이 파트에서는 synapse 작업 영역 배포를 위한 GitHub Actions를 사용하여 GitHub 워크플로를 생성하는 방법에 대해 설명합니다.
[Deploy Azure Resource Manager Template Action](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)을 사용하여 Azure에 대한 ARM 템플릿(Azure Resource Manager 템플릿) 배포를 자동화할 수 있습니다.

### <a name="workflow-file-overview"></a>워크플로 파일 개요

GitHub Actions 워크플로는 리포지토리의 /.github/workflows/ 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

이 파일에는 다음 두 가지 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**배포** | 1. 작업 영역 아티팩트를 배포합니다. |

### <a name="configure-the-github-secrets"></a>GitHub 비밀 구성

암호는 암호화된 환경 변수입니다. 이 리포지토리에 대한 협력자 액세스 권한이 있는 모든 사용자는 이러한 암호를 사용하여 작업을 수행할 수 있습니다.

1. 리포지토리로 이동하여 **설정** 을 선택하고, 암호로 이동한 후 새 암호를 클릭합니다.

    ![새 비밀 만들기](media/create-secret-new.png)

1. 배포에 서비스 주체를 사용하는 경우, 클라이언트 ID, 클라이언트 암호에 대한 새 암호를 추가합니다. 구독 ID, 테넌트 ID를 암호로 저장하도록 선택할 수도 있습니다. 

### <a name="add-your-workflow"></a>워크플로 추가

GitHub 리포지토리의 **작업** 으로 이동합니다. 

1. **워크플로 직접 설정** 을 선택합니다. 
1. 워크플로 파일의 `on:` 섹션 뒤에 있는 모든 항목을 삭제합니다. 예를 들어 나머지 워크플로는 다음과 같습니다. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 워크플로의 이름을 바꾸고 마켓플레이스에서 Synapse 작업 영역 배포 작업을 검색한 다음, 작업을 추가합니다. 

     ![작업 검색](media/search-the-action.png)

1. 필요한 값과 작업 영역 템플릿을 지정합니다.

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You can also use the self-hosted runners
            runs-on: windows-latest
            steps:
            # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
            - uses: actions/checkout@v2
            - uses: azure/synapse-workspace-deployment@release-1.0
            with:
              TargetWorkspaceName: 'target workspace name'
              TemplateFile: './path of the TemplateForWorkspace.json'
              ParametersFile: './path of the TemplateParametersForWorkspace.json'
              OverrideArmParameters: './path of the parameters.yaml'
              environment: 'Azure Public'
              resourceGroup: 'target workspace resource group'
              clientId: ${{secrets.CLIENTID}}
              clientSecret:  ${{secrets.CLIENTSECRET}}
              subscriptionId: 'subscriptionId of the target workspace'
              tenantId: 'tenantId'
              DeleteArtifactsNotInTemplate: 'true'
              managedIdentity: 'False'
    ``` 

1. 이제 변경 내용을 커밋할 준비가 되었습니다. 커밋 시작을 선택하고 제목을 입력한 다음, 설명(선택 사항)을 추가합니다. 그런 다음, 새 파일 커밋을 클릭합니다.

    ![워크플로 커밋](media/commit-the-workflow.png)    


1. 파일이 리포지토리의 `.github/workflows` 폴더에 표시됩니다.

> [!NOTE]
> 관리 ID는 Azure에서 자체 호스팅된 VM에서만 지원됩니다. Runner를 자체 호스팅으로 설정하세요. VM에 시스템이 할당한 관리 ID를 사용하도록 설정하고, Synapse Studio에 Synapse 관리자로 추가합니다.

### <a name="review-your-deployment"></a>배포 검토

1. GitHub 리포지토리의 작업으로 이동합니다.
1. 첫 번째 결과를 열어 워크플로 실행에 대한 자세한 로그를 확인합니다

    ![배포 검토](media/review-deploy-status.png)    

## <a name="use-custom-parameters-of-the-workspace-template"></a>작업 영역 템플릿의 사용자 지정 매개 변수 사용 

자동화 CI/CD를 사용하고 배포 중에 일부 속성을 변경하려고 하지만 속성은 기본적으로 매개 변수화되지 않습니다. 이 경우 기본 매개 변수 템플릿을 재정의할 수 있습니다.

기본 매개 변수 템플릿을 재정의하려면 git 협업 분기의 루트 폴더에 **template-parameters-definition.json** 이라는 파일인 사용자 지정 매개 변수 템플릿을 만들어야 합니다. 정확한 파일 이름을 사용해야 합니다. 협업 분기에서 게시하는 경우 Synapse 작업 영역이 이 파일을 읽고 해당 구성을 사용하여 매개 변수를 생성합니다. 파일이 없는 경우 기본 매개 변수 템플릿이 사용됩니다.

### <a name="custom-parameter-syntax"></a>사용자 지정 매개 변수 구문

다음은 사용자 지정 매개 변수 파일을 만들기 위한 몇 가지 지침입니다.

* 관련 엔터티 형식 아래에 속성 경로를 입력합니다.
* 속성 이름을 `*`로 설정하면 해당 속성 아래(재귀적이 아닌 첫 번째 수준까지만)의 모든 속성을 매개 변수화하려고 함을 나타냅니다. 이 구성에 대한 예외를 제공할 수도 있습니다.
* 문자열로 속성의 값을 설정하면 속성을 매개 변수화하려고 함을 나타냅니다. `<action>:<name>:<stype>` 형식을 사용합니다.
   *  `<action>`은 다음 문자 중 하나일 수 있습니다.
      * `=`는 현재 값을 매개 변수의 기본값으로 유지함을 의미합니다.
      * `-`는 매개 변수의 기본값을 유지하지 않음을 의미합니다.
      * `|`는 연결 문자열 또는 키와 관련한 Azure Key Vault의 비밀에 대한 특수 사례입니다.
   * `<name>`은 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용합니다. 값이 `-` 문자로 시작하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString`에서 `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>`은 매개 변수의 형식입니다. `<stype>`이 비어 있는 경우 기본 형식은 `string`입니다. 지원되는 값은 `string`, `securestring`, `int`, `bool`, `object`, `secureobject` 및 `array`입니다.
* 파일에서 배열을 지정하면 템플릿의 일치하는 속성이 배열임을 나타냅니다. Synapse는 지정된 정의를 사용하여 배열에서 모든 개체를 반복합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스와 관련될 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용됩니다.
* 기본적으로 Key Vault 비밀과 같은 모든 보안 문자열과 연결 문자열, 키, 토큰 등의 보안 문자열은 매개 변수화됩니다.

### <a name="parameter-template-definition-samples"></a>매개 변수 템플릿 정의 샘플 

매개 변수 템플릿 정의의 예제는 다음과 같습니다.

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

다음은 리소스 종류별로 분할된 이전 템플릿의 구성 방법에 대한 설명입니다.

#### <a name="notebooks"></a>Notebooks 

* 경로 `properties/bigDataPool/referenceName`의 모든 속성은 해당 기본값을 사용하여 매개 변수화됩니다. 각 Notebook 파일에 대해 연결된 Spark 풀을 매개 변수화할 수 있습니다. 

#### <a name="sql-scripts"></a>SQL 스크립트 

* 경로 `properties/content/currentConnection`의 속성(poolName 및 databaseName)은 템플릿에 기본값이 없는 문자열로 매개 변수화됩니다. 

#### <a name="pipelines"></a>파이프라인

* `activities/typeProperties/waitTimeInSeconds` 경로에 있는 모든 속성은 매개 변수화됩니다. `waitTimeInSeconds`라는 코드 수준 속성(예: `Wait` 작업)이 있는 파이프라인의 모든 작업은 기본 이름을 가진 숫자로 매개 변수화됩니다. 그러나 Resource Manager 템플릿에는 기본값이 없습니다. Resource Manager 배포 중에 입력해야 할 필수 항목입니다.
* 마찬가지로 `headers`(예: `Web` 작업에서)라는 속성은 형식 `object`(개체)로 매개 변수화됩니다. 이 값은 원본 팩터리의 값과 동일한 기본값을 갖습니다.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* `typeProperties` 경로 아래에 있는 모든 속성은 해당 기본값을 사용하여 매개 변수화됩니다. 예를 들어 `IntegrationRuntimes` 형식 속성에는 `computeProperties` 및 `ssisProperties`의 두 가지 속성이 있습니다. 두 속성 유형 모두 해당 기본값과 유형(Object)으로 만들어집니다.

#### <a name="triggers"></a>트리거

* `typeProperties`에서 두 개의 속성은 매개 변수화됩니다. 첫 번째 값은 기본값을 갖도록 지정된 `maxConcurrency`이며 `string` 형식입니다. 이 값은 기본 매개 변수 이름 `<entityName>_properties_typeProperties_maxConcurrency`를 갖습니다.
* `recurrence` 속성도 매개 변수화됩니다. 이 속성 아래 수준의 모든 속성은 문자열로 매개 변수화되어 기본값 및 매개 변수 이름으로 지정됩니다. 단, `int` 형식으로 매개 변수화되는 `interval` 속성은 예외입니다. 매개 변수 이름에는 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`가 접미사로 붙습니다. 마찬가지로 `freq` 속성은 문자열이며 문자열로 매개 변수화됩니다. 그러나 `freq` 속성은 기본값 없이 매개 변수화됩니다. 이름이 단축되고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

#### <a name="linkedservices"></a>LinkedServices

* 연결된 서비스는 고유합니다. 연결된 서비스 및 데이터 세트에는 다양한 형식이 있어 형식별로 사용자 지정할 수 있습니다. 이 예에서는 `AzureDataLakeStore` 형식의 연결된 모든 서비스에 대해 특정 템플릿이 적용됩니다. `*`을 통한 다른 모든 서비스에 대해서는 다른 템플릿이 적용됩니다.
* `connectionString` 속성은 `securestring` 값으로 매개 변수화됩니다. 기본값이 없습니다. `connectionString`이 접미사로 사용되는 축약된 매개 변수 이름이 포함됩니다.
* 속성 `secretAccessKey`는 `AzureKeyVaultSecret`(예: Amazon S3에 연결된 서비스)가 됩니다. 자동으로 Azure Key Vault 비밀로 매개 변수화되고 구성된 키 자격 증명 모음에서 페치됩니다. 키 자격 증명 모음을 자체적으로 매개 변수화할 수도 있습니다.

#### <a name="datasets"></a>데이터 세트

* 데이터 세트에 대해 형식별 사용자 지정을 사용할 수 있지만, 명시적으로 \* 수준으로 구성하지 않고도 구성할 수 있습니다. 이전 예에서 `typeProperties` 아래의 모든 데이터 세트 속성은 매개 변수화됩니다.


## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

Azure Synapse 작업 영역을 통해 Git 통합을 사용하고 개발에서 테스트, 프로덕션 순서로 변경 내용을 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 적용하는 것이 좋습니다.

-   **Git 통합**. Git 통합으로 개발 Azure Synapse 작업 영역만 구성합니다. 테스트 및 프로덕션 작업 영역에 대한 변경 내용은 CI/CD를 통해 배포되므로 Git 통합이 필요하지 않습니다.
-   **아티팩트 마이그레이션 전에 풀을 준비합니다**. 개발 작업 영역의 풀에 연결된 SQL 스크립트 또는 Notebook이 있는 경우 다른 환경에서 동일한 풀 이름이 필요합니다. 
-   **IaC(코드 제공 인프라)** 기술 모델에서 인프라 관리(네트워크, 가상 머신, 부하 분산 장치 및 연결 토폴로지)는 DevOps 팀이 소스 코드에 사용하는 것과 동일한 버전 관리를 사용합니다. 
-   **기타** [ADF 아티팩트의 모범 사례](../../data-factory/continuous-integration-delivery.md#best-practices-for-cicd) 참조

## <a name="troubleshooting-artifacts-deployment"></a>아티팩트 배포 문제 해결 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Azure Synapse Analytics 작업 영역 배포 작업 사용

Azure Synapse Analytics에는 ARM 리소스가 아닌 여러 아티팩트가 있습니다. 이는 Azure Data Factory와 다릅니다. ARM 템플릿 배포 작업은 Azure Synapse Analytics 아티팩트를 배포하는 데 제대로 작동하지 않습니다.
 
### <a name="unexpected-token-error-in-release"></a>릴리스의 예기치 않은 토큰 오류

매개 변수 파일에 이스케이프되지 않은 매개 변수 값이 있는 경우 릴리스 파이프라인이 파일 구문 분석에 실패하고 "예기치 않은 토큰" 오류가 생성됩니다. 매개 변수를 재정의하거나 Azure Key Vault를 사용하여 매개 변수 값을 검색하는 것이 좋습니다. 또한 이중 이스케이프 문자를 해결 방법으로 사용할 수 있습니다.
