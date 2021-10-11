---
title: Azure Synapse Analytics 지속적인 통합 & 제공
description: CI/CD(연속 통합 및 지속적인 업데이트)를 사용하여 Azure Synapse Analytics 작업 영역의 환경 간에 변경 내용을 배포하는 방법을 알아봅니다.
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3269bfed98264574d5b44759cceae6e1613eb5f8
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729250"
---
# <a name="continuous-integration-and-delivery-for-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics 작업 영역에 대한 지속적인 통합 및 업데이트

CI(연속 통합)는 팀 멤버가 버전 제어에 대한 변경 작업을 커밋할 때마다 코드 빌드 및 테스트를 자동화하는 프로세스입니다. CD(지속적인 업데이트)는 여러 테스트 또는 스테이징 환경에서 프로덕션 환경으로 빌드, 테스트, 구성 및 배포하는 프로세스입니다.

Azure Synapse Analytics 작업 영역에서 CI/CD는 모든 엔터티를 한 환경(개발, 테스트, 프로덕션)에서 다른 환경으로 이동합니다. 작업 영역을 다른 작업 영역으로 승격하는 프로세스는 두 부분으로 구성된 프로세스입니다. 먼저 [ARM 템플릿(Azure Resource Manager 템플릿)](../../azure-resource-manager/templates/overview.md)을 사용하여 작업 영역 리소스(풀 및 작업 영역)를 만들거나 업데이트합니다. 그런 다음, Azure DevOps 또는 GitHub Azure Synapse CI/CD 도구를 사용하여 SQL 스크립트 및 Notebook, Spark 작업 정의, 파이프라인, 데이터 세트 및 데이터 흐름과 같은 아티팩트를 마이그레이션합니다. 

이 문서에서는 Azure DevOps 릴리스 파이프라인 및 GitHub 작업을 사용하여 여러 환경에 Azure Synapse 작업 영역의 배포를 자동화하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

여러 환경에 Azure Synapse 작업 영역 배포를 자동화하려면 다음 필수 구성 요소 및 구성이 있어야 합니다.

### <a name="azure-devops"></a>Azure DevOps

- 릴리스 파이프라인을 실행하기 위한 Azure DevOps 프로젝트를 준비합니다.
- [리포지토리를](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true)볼 수 있도록 조직 수준에서 코드 기본 액세스를 체크 인할 모든 사용자에게 권한을 부여합니다.
- Azure Synapse 리포지토리에 소유자 권한을 부여합니다.
- 자체 호스팅 Azure DevOps VM 에이전트를 만들거나 Azure DevOps 호스팅된 에이전트를 사용했는지 확인합니다.
- 리소스 그룹에 [대한 Azure Resource Manager 서비스 연결을 만들](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true)수 있는 권한을 부여합니다.
- Azure AD(Azure Active Directory) 관리자는 [Azure DevOps 조직에 Azure DevOps Synapse 작업 영역 Deployment Agent 확장을 설치](/azure/devops/marketplace/install-extension)해야 합니다.
- 파이프라인을 실행할 기존 서비스 계정을 만들거나 지정합니다. 서비스 계정 대신 개인 액세스 토큰을 사용할 수 있지만 사용자 계정이 삭제된 후에는 파이프라인이 작동하지 않습니다.

### <a name="github"></a>GitHub

- Azure Synapse 작업 영역 아티팩트 및 작업 영역 템플릿이 포함된 GitHub 리포지토리를 만듭니다. 
- 자체 호스팅된 Runner를 만들었는지 또는 GitHub 호스팅된 Runner를 사용했는지 확인합니다.

### <a name="azure-active-directory"></a>Azure Active Directory

- 서비스 주체를 사용하는 경우 Azure AD에서 배포에 사용할 서비스 주체를 만듭니다. 
- 관리 ID를 사용하는 경우 Azure의 VM에서 시스템 할당 관리 ID를 에이전트 또는 Runner로 사용하도록 설정한 다음, Synapse 관리자로 Azure Synapse Studio에 추가합니다.
- Azure AD 관리자 역할을 사용하여 이러한 작업을 완료합니다.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> 동일한 파이프라인, ARM 템플릿 또는 Azure CLI 사용하여 이러한 필수 구성을 자동화하고 배포할 수 있지만 이러한 프로세스는 이 문서에서 설명하지 않습니다.

- 개발에 사용되는 "원본" 작업 영역은 Azure Synapse Studio의 Git 리포지토리를 사용하여 구성해야 합니다. 자세한 내용은 [Azure Synapse Studio의 소스 제어를 참조하세요.](source-control.md#configuration-method-2-manage-hub)

- 배포할 빈 작업 영역을 다음과 같이 설정합니다.

  1. 새 Azure Synapse 작업 영역을 만듭니다.
  1. 새 작업 영역이 호스트되는 리소스 그룹에 VM 에이전트 및 서비스 주체 기여자 권한을 부여합니다.
  1. 작업 영역에서 Git 리포지토리 연결을 구성하지 마세요.
  1. Azure Portal 새 Azure Synapse 작업 영역을 찾은 다음 Azure DevOps 파이프라인 Azure Synapse 작업 영역을 실행할 사용자에게 소유자 권한을 부여합니다. 
  1. Azure DevOps VM 에이전트 및 서비스 주체를 작업 영역의 기여자 역할에 추가합니다. (역할이 상속되어야 하지만 해당 역할을 확인합니다.)
  1. Azure Synapse 작업 영역에서 **Studio**  >  **관리**  >  **Access Control** 으로 이동합니다. Azure DevOps VM 에이전트 및 서비스 주체를 작업 영역 관리 그룹에 추가합니다.
  1. 작업 영역에 사용되는 스토리지 계정을 엽니다. ID **및 액세스 관리** 창에서 VM 에이전트 및 서비스 주체를 Storage Blob 데이터 기여자 역할에 추가합니다.
  1. 지원 구독에서 키 자격 증명 모음을 만들고 기존 작업 영역과 새 작업 영역 모두에 자격 증명 모음에 대한 GET 및 LIST 이상의 권한이 있는지 확인합니다.
  1. 자동화된 배포가 작동하려면 연결된 서비스에 지정된 연결 문자열이 주요 자격 증명 모음에 있는지 확인합니다.

### <a name="other-prerequisites"></a>기타 사전 요구 사항
 
- Spark 풀 및 자체 호스팅 통합 런타임은 작업 영역 배포 작업에서 생성되지 않습니다. 자체 호스팅 통합 런타임을 사용하는 연결된 서비스가 있는 경우 새 작업 영역에서 수동으로 런타임을 만듭니다.
- 개발 작업 영역의 항목이 특정 풀에 연결된 경우 매개 변수 파일의 대상 작업 영역에 있는 풀에 대해 동일한 이름을 만들거나 매개 변수화해야 합니다.  
- 배포를 시도할 때 프로비전된 SQL 풀이 일시 중지되면 배포가 실패할 수 있습니다.

자세한 내용은 [Azure Synapse Analytics 4부 - 릴리스 파이프라인 의 CI/CD를 참조하세요.](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434) 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>Azure DevOps에서 릴리스 파이프라인 설정

이 섹션에서는 Azure DevOps Azure Synapse 작업 영역을 배포하는 방법을 알아봅니다. 

1. [Azure DevOps](https://dev.azure.com/)릴리스용으로 만든 프로젝트를 엽니다.

1. 왼쪽 메뉴에서 릴리스 **Pipelines**  >  선택합니다.

    :::image type="content" source="media/create-release-pipeline.png" alt-text="Azure DevOps 메뉴에서 Pipelines 선택한 다음 릴리스를 선택하는 스크린샷.":::        
 
1. **새 파이프라인** 을 선택합니다. 기존 파이프라인이 있는 경우 **새 새** 릴리스 파이프라인 을  >  선택합니다.

1. **빈 작업** 템플릿을 선택합니다.

    :::image type="content" source="media/create-release-select-empty.png" alt-text="빈 작업 템플릿 선택을 보여 주는 스크린샷.":::

1. **스테이지 이름** 에 환경의 이름을 입력합니다.

1. **아티팩트 추가를** 선택한 다음, 개발 환경에서 Azure Synapse Studio로 구성된 Git 리포지토리를 선택합니다. 풀 및 작업 영역 ARM 템플릿을 관리하는 Git 리포지토리를 선택합니다. GitHub 원본으로 사용하는 경우 GitHub 계정에 대한 서비스 연결을 만들고 리포지토리를 끌어오세요. 자세한 내용은 [서비스 연결을 참조하세요.](/azure/devops/pipelines/library/service-endpoints)

    :::image type="content" source="media/release-creation-github.png" alt-text="새 아티팩트용 게시 분기를 추가할 GitHub 선택하는 방법을 보여 주는 스크린샷":::

1. 리소스 ARM 템플릿 분기를 선택합니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    :::image type="content" source="media/release-creation-arm-template-branch.png" lightbox="media/release-creation-arm-template-branch.png" alt-text="리소스 ARM 템플릿 분기 설정을 보여 주는 스크린샷":::

1. 아티팩트 **기본 분기** 의 경우 리포지토리 [게시 분기](source-control.md#configure-publishing-settings)를 선택합니다. 기본적으로 게시 분기는 `workspace_publish` 입니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    :::image type="content" source="media/release-creation-publish-branch.png" alt-text="아티팩트 분기 설정을 보여 주는 스크린샷":::

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-a-resource"></a>ARM 템플릿에 대한 단계 작업을 설정하여 리소스를 만들고 업데이트합니다. 

Azure Synapse 작업 영역, Spark 및 SQL 풀 또는 키 자격 증명 모음과 같은 리소스를 배포하는 ARM 템플릿이 있는 경우 Azure Resource Manager 배포 작업을 추가하여 해당 리소스를 만들거나 업데이트합니다.

1. 단계 보기에서 **단계 작업 보기** 를 선택합니다.

    :::image type="content" source="media/release-creation-stage-view.png" alt-text="스테이지 보기 설정을 보여 주는 스크린샷.":::

1. 새 작업을 만듭니다. **ARM 템플릿 배포** 를 검색한 후 **추가** 를 선택합니다.

1. 배포 **작업** 탭에서 작업 영역에 대한 구독, 리소스 그룹 및 위치를 선택합니다. 필요한 경우 자격 증명을 제공합니다.

1. **작업에서** **리소스 그룹 만들기 또는 업데이트를** 선택합니다.

1. **템플릿** 에 대해 말임표 단추(**...**)를 선택합니다. 작업 영역의 ARM 템플릿으로 이동합니다.

1. **템플릿 매개 변수** 의 경우 **...** 매개 변수 파일을 선택합니다.

    :::image type="content" source="media/pools-resource-deploy.png" lightbox="media/pools-resource-deploy.png" alt-text="작업 영역 및 풀 배포를 보여 주는 스크린샷.":::

1. **템플릿 매개 변수 재정의에서** **...** 를 선택한 다음, 작업 영역에 사용할 매개 변수 값을 입력합니다. 

1. **배포 모드에서** **증분 을** 선택합니다.

1. (선택 사항) 권한 부여에 대한 **Azure PowerShell** 추가하고 작업 영역 역할 할당을 업데이트합니다. 릴리스 파이프라인을 사용하여 Azure Synapse 작업 영역을 만드는 경우 파이프라인의 서비스 주체가 기본 작업 영역 관리자로 추가됩니다. PowerShell을 실행하여 다른 계정에 작업 영역에 대한 액세스 권한을 부여할 수 있습니다. 

    :::image type="content" source="media/release-creation-grant-permission.png" lightbox="media/release-creation-grant-permission.png" alt-text="권한을 부여하기 위해 PowerShell 스크립트를 실행하는 방법을 보여 주는 스크린샷":::

> [!WARNING]
> 전체 배포 모드에서는 새 ARM 템플릿에 지정되지 않은 리소스 그룹의 리소스가 *삭제됩니다.* 자세한 내용은 [Azure Resource Manager 배포 모드](../../azure-resource-manager/templates/deployment-modes.md)를 참조하세요.

### <a name="set-up-a-stage-task-for-azure-synapse-artifacts-deployment"></a>Azure Synapse 아티팩트 배포를 위한 단계 작업 설정 

[Synapse 작업 영역 배포](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) 확장을 사용하여 Azure Synapse 작업 영역에 다른 항목을 배포합니다. 배포할 수 있는 항목에는 데이터 세트, SQL 스크립트 및 Notebook, Spark 작업 정의, 데이터 흐름, 파이프라인, 연결된 서비스, 자격 증명 및 통합 런타임이 포함됩니다.  

1. [Visual Studio Marketplace에서](https://marketplace.visualstudio.com/azuredevops)확장을 검색하고 가져옵니다.

    :::image type="content" source="media/get-extension-marketplace.png" alt-text="Synapse 작업 영역 배포 확장이 Visual Studio Marketplace에 표시되는 스크린샷":::

1. 확장을 설치할 Azure DevOps 조직을 선택합니다. 

    :::image type="content" source="media/install-extension.png" alt-text="Synapse workspace 배포 확장을 설치할 조직을 선택 하는 스크린샷":::

1. Azure DevOps 파이프라인의 서비스 주체에 게 구독 권한이 부여 되 고 작업 영역에 대 한 Synapse 작업 영역 관리자로 할당 되었는지 확인 합니다. 

1. 새 작업을 만들려면 **Synapse 작업 영역 배포** 를 검색 한 다음 **추가** 를 선택 합니다.

    :::image type="content" source="media/add-extension-task.png" alt-text="작업을 만들기 위한 Synapse 작업 영역 배포 검색을 보여 주는 스크린샷":::

1. 작업의 **템플릿** 옆 **에서 ...를 선택 합니다** . 템플릿 파일을 선택 합니다.

1. **템플릿 매개 변수** 옆 **에서 ...를 선택 합니다** .  를 클릭 하 여 매개 변수 파일을 선택 합니다.

1. 작업 영역에 대 한 연결, 리소스 그룹 및 이름을 선택 합니다. 

1. **템플릿 매개 변수 재정의** 옆 **에서 ...를 선택 합니다** . . 연결 된 서비스에 사용 되는 연결 문자열 및 계정 키를 포함 하 여 작업 영역에 사용할 매개 변수 값을 입력 합니다. 자세한 정보는 [Azure Synapse Analytics의 CI/CD](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)를 참조하세요.

    :::image type="content" source="media/create-release-artifacts-deployment.png" lightbox="media/create-release-artifacts-deployment.png" alt-text="작업 영역에 대 한 Synapse 배포 작업을 설정 하는 방법을 보여 주는 스크린샷":::

> [!IMPORTANT]
> CI/CD 시나리오에서 서로 다른 환경의 통합 런타임 형식이 동일 해야 합니다. 예를 들어 개발 환경에 자체 호스팅 통합 런타임이 있는 경우 동일한 통합 런타임을 테스트 및 프로덕션 등의 다른 환경에서 자체 호스팅해야 합니다. 마찬가지로, 여러 단계에서 통합 런타임을 공유 하는 경우 개발, 테스트 및 프로덕션 등의 모든 환경에서 통합 런타임을 연결 하 고 자체 호스팅해야 합니다.

### <a name="create-a-release-for-deployment"></a>배포용 릴리스 만들기 

모든 변경 내용을 저장 한 후 **릴리스 만들기** 를 선택 하 여 수동으로 릴리스를 만들 수 있습니다. 릴리스 생성을 자동화 하는 방법에 대 한 자세한 내용은 [Azure DevOps 릴리스 트리거](/azure/devops/pipelines/release/triggers)를 참조 하세요.

:::image type="content" source="media/release-creation-manually.png" lightbox="media/release-creation-manually.png" alt-text="릴리스 만들기가 강조 표시 된 새 릴리스 파이프라인 창을 보여 주는 스크린샷":::

## <a name="set-up-a-release-in-github-actions"></a>GitHub 작업에서 릴리스 설정 

이 섹션에서는 Azure Synapse 작업 영역 배포에 대 한 GitHub 작업을 사용 하 여 GitHub 워크플로를 만드는 방법에 대해 알아봅니다.

[Azure Resource Manager 템플릿에 대 한 GitHub 작업](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) 을 사용 하 여 작업 영역 및 compute 풀에 대해 ARM 템플릿을 Azure에 자동으로 배포할 수 있습니다.

### <a name="workflow-file"></a>워크플로 파일

리포지토리의 */.github/workflows/* 경로에 있는 yaml (.yml) 파일의 GitHub 작업 워크플로를 정의 합니다. 정의는 워크플로를 구성 하는 다양 한 단계와 매개 변수를 포함 합니다.

.Yml 파일에는 두 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**배포** | 작업 영역 아티팩트를 배포 합니다. |

### <a name="configure-github-actions-secrets"></a>GitHub 작업 비밀 구성

GitHub 작업 비밀은 암호화 된 환경 변수입니다. 이 리포지토리에 대 한 협력자 권한이 있는 사용자는 이러한 암호를 사용 하 여 리포지토리의 작업과 상호 작용할 수 있습니다.

1. GitHub 리포지토리에서 **설정** 탭을 선택한 다음 **비밀**  >  **새 리포지토리 암호** 를 선택 합니다.

    :::image type="content" source="media/create-secret-new.png" lightbox="media/create-secret-new.png" alt-text="새 리포지토리 비밀을 만들기 위해 선택할 GitHub 요소를 보여 주는 스크린샷":::

1. 클라이언트 ID에 대 한 새 암호를 추가 하 고 배포를 위해 서비스 주체를 사용 하는 경우 새 클라이언트 암호를 추가 합니다. 구독 ID와 테 넌 트 ID를 암호로 저장 하도록 선택할 수도 있습니다. 

### <a name="add-your-workflow"></a>워크플로 추가

GitHub 리포지토리에서 **작업** 으로 이동 합니다. 

1. **워크플로 직접 설정** 을 선택합니다.
1. 워크플로 파일에서 섹션 뒤에 나오는 모든 항목을 삭제 `on:` 합니다. 예를 들어 나머지 워크플로는 다음 예와 같습니다. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 워크플로의 이름을 바꿉니다. **Marketplace** 탭에서 Synapse 작업 영역 배포 작업을 검색 한 다음 작업을 추가 합니다. 

    :::image type="content" source="media/search-action.png" lightbox="media/search-action.png" alt-text="Marketplace 탭에서 Synapse 작업 영역 배포 작업을 검색 하는 방법을 보여 주는 스크린샷":::

1. 필요한 값 및 작업 영역 템플릿을 설정 합니다.

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You also can use the self-hosted runners.
            runs-on: windows-latest
            steps:
            # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it.
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

1. 변경 내용을 커밋할 준비가 되었습니다. **커밋 시작** 을 선택 하 고 제목을 입력 한 다음 설명 (선택 사항)을 추가 합니다. 그런 다음 **새 파일 커밋** 을 선택 합니다.

    :::image type="content" source="media/commit-workflow.png" lightbox="media/commit-workflow.png" alt-text="GitHub에서 워크플로 커밋을 보여 주는 스크린샷":::

   파일은 리포지토리의 *github/워크플로* 폴더에 표시 됩니다.

   > [!NOTE]
   > 관리 id는 Azure에서 자체 호스트 된 Vm 에서만 지원 됩니다. Runner를 자체 호스팅으로 설정 해야 합니다. VM에 대 한 시스템 할당 관리 id를 사용 하도록 설정 하 고 Synapse admin으로 Azure Synapse Studio에 추가 합니다.

### <a name="review-your-deployment"></a>배포 검토

1. GitHub 리포지토리에서 **작업** 으로 이동 합니다.
1. 워크플로의 실행에 대 한 자세한 로그를 보려면 첫 번째 결과를 엽니다.

    :::image type="content" source="media/review-deploy-status.png" lightbox="media/review-deploy-status.png" alt-text="GitHub의 리포지토리 작업에서 작업 영역 배포 로그를 선택 하는 것을 보여 주는 스크린샷":::

## <a name="create-custom-parameters-in-the-workspace-template"></a>작업 영역 템플릿에서 사용자 지정 매개 변수 만들기 

자동화 된 CI/CD를 사용 하 고 배포 중에 일부 속성을 변경 하려고 하지만 속성은 기본적으로 매개 변수화 되지 않은 경우 기본 매개 변수 템플릿을 재정의할 수 있습니다.

기본 매개 변수 템플릿을 재정의 하려면 Git 공동 작업 분기의 루트 폴더에 *template-parameters-definition. json* 이라는 사용자 지정 매개 변수 템플릿을 만듭니다. 이 정확한 파일 이름을 사용 해야 합니다. Azure Synapse 작업 영역은 공동 작업 분기에서 게시할 때이 파일을 읽고 해당 구성을 사용 하 여 매개 변수를 생성 합니다. Azure Synapse 작업 영역이 해당 파일을 찾지 못하면는 기본 매개 변수 템플릿을 사용 합니다.

### <a name="custom-parameter-syntax"></a>사용자 지정 매개 변수 구문

다음 지침을 사용 하 여 사용자 지정 매개 변수 파일을 만들 수 있습니다.

* 관련 엔터티 형식 아래에 속성 경로를 입력합니다.
* 속성 이름을로 설정 `*` 하면 속성의 모든 속성 (재귀적이 아닌 첫 번째 수준 까지만)을 매개 변수화 합니다. 이 구성에 대 한 예외를 설정할 수 있습니다.
* 문자열로 속성의 값을 설정하면 속성을 매개 변수화하려고 함을 나타냅니다. `<action>:<name>:<stype>` 형식을 사용합니다.
   *  `<action>`은 다음 문자 중 하나일 수 있습니다.
      * `=`는 현재 값을 매개 변수의 기본값으로 유지함을 의미합니다.
      * `-`는 매개 변수의 기본값을 유지하지 않음을 의미합니다.
      * `|`는 연결 문자열 또는 키와 관련한 Azure Key Vault의 비밀에 대한 특수 사례입니다.
   * `<name>`은 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용합니다. 값이 `-` 문자로 시작하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString`에서 `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>`은 매개 변수의 형식입니다. `<stype>`이 비어 있는 경우 기본 형식은 `string`입니다. 지원되는 값은 `string`, `securestring`, `int`, `bool`, `object`, `secureobject` 및 `array`입니다.
* 파일에서 배열을 지정하면 템플릿의 일치하는 속성이 배열임을 나타냅니다. Azure Synapse는 지정 된 정의를 사용 하 여 배열의 모든 개체를 반복 합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스와 관련될 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용됩니다.
* 기본적으로 모든 보안 문자열 (예: Key Vault 비밀) 및 보안 문자열 (예: 연결 문자열, 키, 토큰)은 매개 변수화 됩니다.

### <a name="parameter-template-definition-example"></a>매개 변수 템플릿 정의 예제 

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

다음은 리소스 유형별로 위의 템플릿이 생성 되는 방법에 대 한 설명입니다.

**`notebooks`**

- 경로의 모든 속성 `properties/bigDataPool/referenceName` 은 해당 기본값을 사용 하 여 매개 변수화 됩니다. 각 노트북 파일에 대해 연결 된 Spark 풀을 매개 변수화 할 수 있습니다. 

**`sqlscripts`**

- 경로에서 `properties/content/currentConnection` 및 속성은 모두 `poolName` `databaseName` 템플릿의 기본값 없이 문자열로 매개 변수화 됩니다. 

**`pipelines`**

- 경로의 모든 속성 `activities/typeProperties/waitTimeInSeconds` 은 매개 변수화 됩니다. `waitTimeInSeconds`라는 코드 수준 속성(예: `Wait` 작업)이 있는 파이프라인의 모든 작업은 기본 이름을 가진 숫자로 매개 변수화됩니다. 속성은 Resource Manager 템플릿에 기본값이 없습니다. 대신 Resource Manager 배포하는 동안 속성이 필수 입력이 됩니다.
- `headers`속성(예: `Web` 활동)은 `object` (Object) 형식으로 매개 변수화됩니다. `headers`속성에는 원본 팩터리와 동일한 값의 기본값이 있습니다.

**`integrationRuntimes`**

- 경로의 모든 `typeProperties` 속성은 해당 기본값으로 매개 변수화됩니다. 예를 들어 형식 속성에는 및 의 두 `IntegrationRuntimes` 속성이 `computeProperties` `ssisProperties` 있습니다. 두 속성 유형 모두 해당 기본값과 유형(Object)으로 만들어집니다.

**`triggers`**

- `typeProperties`에서 두 가지 속성이 매개 변수화됩니다.
  - `maxConcurrency`속성에는 기본값이 있으며 `string` 형식입니다. 속성의 기본 매개 변수 이름은 `maxConcurrency`  `<entityName>_properties_typeProperties_maxConcurrency` 입니다.
  - `recurrence` 속성도 매개 변수화됩니다. 속성 아래의 모든 `recurrence` 속성은 기본값 및 매개 변수 이름을 사용하여 문자열로 매개 변수화하도록 설정됩니다. 예외는 `interval` 형식으로 매개 변수화되는 `int` 속성입니다. 매개 변수 이름에는 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`가 접미사로 붙습니다. 마찬가지로 `freq` 속성은 문자열이며 문자열로 매개 변수화됩니다. 그러나 `freq` 속성은 기본값 없이 매개 변수화됩니다. 이름은 짧아지고 접미사가 붙습니다(예: `<entityName>_freq` ).

**`linkedServices`**

- 연결된 서비스는 고유합니다. 연결된 서비스 및 데이터 세트에는 다양한 형식이 있어 형식별로 사용자 지정할 수 있습니다. 앞의 예제에서 형식의 모든 연결된 서비스에 대해 `AzureDataLakeStore` 특정 템플릿이 적용됩니다. 다른 모든 항목의 경우(문자를 사용하여 식별) `*` 다른 템플릿이 적용됩니다.
- `connectionString`속성은 값으로 매개 `securestring` 변수화됩니다. 기본값은 없습니다. 매개 변수 이름은 줄여지고 접미사가 `connectionString` 붙습니다.
- `secretAccessKey`속성은 값으로 매개 변수화됩니다(예: `AzureKeyVaultSecret` Amazon S3 연결된 서비스에서). 속성은 자동으로 Azure Key Vault 비밀로 매개 변수화되고 구성된 키 자격 증명 모음에서 페치됩니다. 키 자격 증명 모음 자체를 매개 변수화할 수도 있습니다.

**`datasets`**

- 데이터 세트의 형식을 사용자 지정할 수 있지만 명시적 \* 수준 구성은 필요하지 않습니다. 이전 예에서 `typeProperties` 아래의 모든 데이터 세트 속성은 매개 변수화됩니다.

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

Azure Synapse 작업 영역과 Git 통합을 사용하고 개발에서 테스트, 프로덕션으로 변경 내용을 이동하는 CI/CD 파이프라인이 있는 경우 다음 모범 사례를 사용하는 것이 좋습니다.

-   **개발 작업 영역만 Git 와 통합합니다.** Git 통합을 사용하는 경우 *개발* Azure Synapse 작업 영역만 Git과 통합합니다. 테스트 및 프로덕션 작업 영역에 대한 변경 내용은 CI/CD를 통해 배포되므로 Git 통합이 필요하지 않습니다.
-   **아티팩트 를 마이그레이션하기 전에 풀을 준비합니다.** 개발 작업 영역의 풀에 연결된 SQL 스크립트 또는 Notebook이 있는 경우 다른 환경의 풀에 동일한 이름을 사용합니다. 
-   **인프라의 버전 관리 를 코드 시나리오로 동기화합니다.** 설명적 모델에서 인프라(네트워크, 가상 머신, 부하 분산기 및 연결 토폴로지)를 관리하려면 DevOps 팀이 소스 코드에 사용하는 것과 동일한 버전 관리를 사용합니다. 
-   **Azure Data Factory 모범 사례를 검토합니다.** Data Factory 사용하는 경우 [Data Factory 아티팩트 에 대한 모범 사례를 참조하세요.](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshoot-artifacts-deployment"></a>아티팩트 배포 문제 해결 

### <a name="use-the-synapse-workspace-deployment-task"></a>Synapse 작업 영역 배포 작업 사용

Azure Synapse Data Factory 달리 일부 아티팩트에서는 리소스를 Resource Manager 않습니다. ARM 템플릿 배포 작업을 사용하여 Azure Synapse 아티팩트 배포를 수행할 수 없습니다. 대신 Synapse 작업 영역 배포 작업을 사용합니다.
 
### <a name="unexpected-token-error-in-release"></a>릴리스의 예기치 않은 토큰 오류

매개 변수 파일에 이스케이프되지 않은 매개 변수 값이 있는 경우 릴리스 파이프라인은 파일을 구문 분석하지 못하고 `unexpected token` 오류를 생성합니다. 매개 변수를 재정의하거나 Key Vault 사용하여 매개 변수 값을 검색하는 것이 좋습니다. 이중 이스케이프 문자를 사용하여 문제를 해결할 수도 있습니다.
