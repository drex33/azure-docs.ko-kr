---
title: 리포지토리에서 사용자 지정 콘텐츠 배포
description: 이 문서에서는 사용자 지정 콘텐츠를 저장하고 Microsoft Sentinel에 배포할 수 있는 GitHub 또는 Azure DevOps 리포지토리와의 연결을 만드는 방법을 설명합니다.
author: batamig
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4bdde4a6167df822842e0b8a5dcfa5e305fbcc38
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964551"
---
# <a name="deploy-custom-content-from-your-repository-public-preview"></a>리포지토리에서 사용자 지정 콘텐츠 배포(공개 미리 보기)

> [!IMPORTANT]
>
> Microsoft Sentinel **리포지토리** 페이지는 현재 **미리 보기로** 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft Sentinel *콘텐츠는* 고객이 Microsoft Sentinel에서 을(를) 수강, 모니터링, 경고, 헌팅 등을 지원하는 SIEM(보안 정보 및 이벤트 관리)입니다. 예를 들어 Microsoft Sentinel 콘텐츠에는 데이터 커넥터, 파서, 통합 문서 및 분석 규칙이 포함됩니다. 자세한 내용은 [Microsoft Sentinel 콘텐츠 및 솔루션 정보를 참조하세요.](sentinel-solutions.md)

Microsoft Sentinel 콘텐츠 허브에 제공된 기본 제공(기본 제공) 콘텐츠를 사용하고 사용자 고유의 요구에 맞게 사용자 지정하거나 처음부터 사용자 지정 콘텐츠를 만들 수 있습니다.

사용자 지정 콘텐츠를 만들 때 사용자 고유의 Microsoft Sentinel 작업 영역 또는 외부 소스 제어 리포지토리(GitHub 및 Azure DevOps 리포지토리 포함)에 저장하고 관리할 수 있습니다. 이 문서에서는 Microsoft Sentinel과 외부 소스 제어 리포지토리 간의 연결을 만들고 관리하는 방법을 설명합니다. 외부 리포지토리에서 콘텐츠를 관리하면 Microsoft Sentinel 외부에서 해당 콘텐츠를 업데이트하고 작업 영역에 자동으로 배포할 수 있습니다.

> [!TIP]
> 이 *문서에서는* 특정 유형의 콘텐츠를 처음부터 만드는 방법을 설명하지 않습니다. 자세한 내용은 각 콘텐츠 형식에 대한 관련 [Microsoft Sentinel GitHub wiki를](https://github.com/Azure/Azure-Sentinel/wiki#get-started) 참조하세요.
>

## <a name="prerequisites-and-scope"></a>필수 조건 및 범위

Microsoft Sentinel 작업 영역을 외부 소스 제어 리포지토리에 연결하기 전에 다음이 있는지 확인합니다.

- [ARM(관련 Azure Resource Manager) 템플릿에서](../azure-resource-manager/templates/index.yml)작업 영역에 배포하려는 사용자 지정 콘텐츠 파일을 사용하여 GitHub 또는 Azure DevOps 리포지토리에 액세스합니다.

    Microsoft Sentinel은 현재 GitHub 및 Azure DevOps 리포지토리와의 연결만 지원합니다.

- Microsoft Sentinel 작업 영역을 포함하는 리소스 그룹의 **소유자** 역할입니다. **소유자** 역할은 Microsoft Sentinel과 소스 제어 리포지토리 간의 연결을 만드는 데 필요합니다.

### <a name="maximum-connections-and-deployments"></a>최대 연결 및 배포

- 각 Microsoft Sentinel 작업 영역은 현재 **5개의 연결로 제한됩니다.**

- 각 Azure 리소스 그룹은 배포 기록에서 **800개의 배포로 제한됩니다.** 리소스 그룹에 많은 양의 ARM 템플릿 배포가 있는 경우 오류가 표시 될 수 `Deployment QuotaExceeded` 있습니다. 자세한 내용은 Azure Resource Manager 템플릿 설명서의 [DeploymentQuotaExceeded를](/azure/azure-resource-manager/templates/deployment-quota-exceeded) 참조하세요.

### <a name="validate-your-content"></a>콘텐츠 유효성 검사

리포지토리 연결을 통해 Microsoft Sentinel에 콘텐츠를 배포해도 데이터가 올바른 ARM 템플릿 형식인지 확인하는 것 외에는 해당 콘텐츠의 유효성을 검사하지 않습니다.

일반 유효성 검사 프로세스를 사용하여 콘텐츠 템플릿의 유효성을 검사하는 것이 좋습니다. [Microsoft Sentinel GitHub 유효성 검사 프로세스](https://github.com/Azure/Azure-Sentinel/wiki#test-your-contribution) 및 도구를 활용하여 고유한 유효성 검사 프로세스를 설정할 수 있습니다.

## <a name="connect-a-repository"></a>리포지토리 커넥트

이 절차에서는 microsoft Sentinel 대신 사용자 지정 콘텐츠를 저장하고 관리할 수 있는 Microsoft Sentinel 작업 영역에 GitHub 또는 Azure DevOps 리포지토리를 연결하는 방법을 설명합니다.

각 연결은 분석 규칙, 자동화 규칙, 헌팅 쿼리, 파서, 플레이북 및 통합 문서를 포함하여 여러 유형의 사용자 지정 콘텐츠를 지원할 수 있습니다. 자세한 내용은 [Microsoft Sentinel 콘텐츠 및 솔루션 정보를 참조하세요.](sentinel-solutions.md)

**연결을 만들려면:**

1. 연결에 사용하려는 자격 증명을 사용하여 소스 제어 앱에 로그인했는지 확인합니다.  현재 다른 자격 증명을 사용하여 로그인한 경우 먼저 로그아웃합니다.

1. Microsoft Sentinel의 **왼쪽 콘텐츠 관리** 에서 **리포지토리** 를 선택합니다.

1. **새 추가를** 선택한 다음, 새 연결 만들기 페이지에서 **연결에** 대한 의미 있는 이름과 설명을 입력합니다.

1. 소스 **제어** 드롭다운에서 연결할 리포지토리의 유형을 선택한 다음 권한 **부여를** 선택합니다.

1. 연결 형식에 따라 다음 탭 중 하나를 선택합니다.

    # <a name="github"></a>[GitHub](#tab/github)

    1. 메시지가 표시되면 GitHub 자격 증명을 입력합니다.

        처음으로 연결을 추가하면 새 브라우저 창 또는 탭이 표시되어 Microsoft Sentinel에 대한 연결 권한을 부여하라는 메시지가 표시됩니다. 동일한 브라우저에서 GitHub 계정에 이미 로그인한 경우 GitHub 자격 증명이 자동으로 채워집니다.

    1. 이제 **리포지토리** 영역이 **새 연결 만들기** 페이지에 표시됩니다. 여기서 연결할 기존 리포지토리를 선택할 수 있습니다. 목록에서 리포지토리를 선택한 다음, **리포지토리 추가를** 선택합니다.

        특정 리포지토리에 처음 연결하면 새 브라우저 창 또는 탭이 표시되어 리포지토리에 **Azure-Sentinel** 앱을 설치하라는 메시지가 표시됩니다. 리포지토리가 여러 개인 경우 **Azure-Sentinel** 앱을 설치할 리포지토리를 선택하고 설치합니다.

        앱 설치를 계속하려면 GitHub.

    1. **Azure-Sentinel** 앱이 리포지토리에 설치되면 **새 연결 만들기** 페이지의 **분기** 드롭다운이 분기로 채워집니다. Microsoft Sentinel 작업 영역에 연결하려는 분기를 선택합니다.

    1. 콘텐츠 **형식** 드롭다운에서 배포할 콘텐츠 유형을 선택합니다.

        - 파서와 헌팅 쿼리는 모두 **저장된 검색** API를 사용하여 Microsoft Sentinel에 콘텐츠를 배포합니다. 이러한 콘텐츠 형식 중 하나를 선택하고 분기에 다른 형식의 콘텐츠도 있는 경우 두 콘텐츠 형식이 모두 배포됩니다.

        - 다른 모든 콘텐츠 형식의 경우 새 연결 만들기 창에서 콘텐츠 **형식을** 선택하면 해당 콘텐츠만 Microsoft Sentinel에 배포됩니다. 다른 형식의 콘텐츠는 배포되지 않습니다.

    1. **만들기** 를 선택하여 연결을 만듭니다. 예:

        :::image type="content" source="media/ci-cd/create-new-connection-github.png" alt-text="새 GitHub 리포지토리 연결의 스크린샷.":::

    # <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

    > [!NOTE]
    > 테넌트 간 제한으로 인해 작업 영역에서 [게스트 사용자로](../active-directory/external-identities/what-is-b2b.md) 연결을 만드는 경우 Azure DevOps URL이 드롭다운에 표시되지 않습니다. 대신 수동으로 입력합니다.
    >

    현재 Azure 자격 증명을 사용하여 Azure DevOps 수 있는 권한이 자동으로 부여됩니다. 올바른 연결을 보장하려면 Microsoft Sentinel에서 연결하는 [것과 동일한 Azure DevOps 계정에 권한을 부여했는지 확인하거나](https://aex.dev.azure.com/) InPrivate 브라우저 창을 사용하여 연결을 만듭니다.
    
    1.  Microsoft Sentinel의 표시되는 드롭다운 목록에서 **조직**, **Project,** **리포지토리,** **분기** 및 **콘텐츠 형식을** 선택합니다.

        - 파서와 헌팅 쿼리는 모두 **저장된 검색** API를 사용하여 Microsoft Sentinel에 콘텐츠를 배포합니다. 이러한 콘텐츠 형식 중 하나를 선택하고 분기에 다른 형식의 콘텐츠도 있는 경우 두 콘텐츠 형식이 모두 배포됩니다.

        - 다른 모든 콘텐츠 형식의 경우 새 연결 만들기 창에서 콘텐츠 **형식을** 선택하면 해당 콘텐츠만 Microsoft Sentinel에 배포됩니다. 다른 형식의 콘텐츠는 배포되지 않습니다.

    1. **만들기** 를 선택하여 연결을 만듭니다. 예:

        :::image type="content" source="media/ci-cd/create-new-connection-devops.png" alt-text="새 GitHub 리포지토리 연결의 스크린샷.":::

    ---

    > [!NOTE]
    > 단일 Microsoft Sentinel 작업 영역에서 동일한 리포지토리 및 분기를 사용하여 중복 연결을 만들 수 없습니다.
    >

연결이 만들어지고 나면 리포지토리에 새 워크플로 또는 파이프라인이 생성되고 리포지토리에 저장된 콘텐츠가 Microsoft Sentinel 작업 영역에 배포됩니다.

배포 시간은 배포하는 콘텐츠의 양에 따라 달라질 수 있습니다. 

### <a name="view-the-deployment-status"></a>배포 상태 보기

- **GitHub:** 리포지토리의 **작업** 탭에서 자세한 배포 로그 및 특정 오류 메시지에 액세스할 수 있는 워크플로 **.yaml** 파일을 선택합니다(해당하는 경우).
- **Azure DevOps:** 리포지토리의 **Pipelines** 탭에 있습니다.

배포가 완료된 후:

- 리포지토리에 저장된 콘텐츠는 관련 Microsoft Sentinel 페이지의 Microsoft Sentinel 작업 영역에 표시됩니다.

- **리포지토리** 페이지의 연결 세부 정보는 연결의 배포 로그에 대한 링크로 업데이트됩니다. 예:

    :::image type="content" source="media/ci-cd/deployment-logs-link.png" alt-text="GitHub 리포지토리 연결의 배포 로그 스크린샷.":::

### <a name="customize-the-deployment-workflow"></a>배포 워크플로 사용자 지정

기본 콘텐츠 배포는 해당 분기의 모든 콘텐츠에 푸시가 이루어질 때마다 연결된 리포지토리 분기의 모든 관련 사용자 지정 콘텐츠를 배포합니다.

GitHub 또는 Azure DevOps 콘텐츠 배포에 대한 기본 구성이 모든 요구 사항을 충족하지 않는 경우 필요에 맞게 환경을 수정할 수 있습니다.

예를 들어 다른 배포 트리거를 구성하거나 특정 루트 폴더의 콘텐츠만 배포할 수 있습니다.

연결 형식에 따라 다음 탭 중 하나를 선택합니다.

# <a name="github"></a>[GitHub](#tab/github)

**GitHub 배포 워크플로를 사용자 지정하려면:**

1. GitHub 리포지토리로 이동하여 디렉터리에서 워크플로를 `.github/workflows` 찾습니다.

    워크플로 이름은 워크플로 파일의 첫 번째 줄에 표시되며 기본 명명 규칙은 `Deploy Content to <workspace-name> [<deployment-id>]` 입니다.

    `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. 페이지 오른쪽 위에 있는 연필 단추를 선택하여 편집할 파일을 연 다음, 다음과 같이 배포를 수정합니다.

    - **배포 트리거를 수정하려면** `on` 실행할 워크플로를 트리거하는 이벤트를 설명하는 코드의 섹션을 업데이트합니다.

        기본적으로 이 구성은 로 설정됩니다. `on: push` 즉, 워크플로는 기존 콘텐츠에 대한 수정 및 리포지토리에 새 콘텐츠 추가를 포함하여 연결된 분기에 푸시할 때 트리거됩니다. 예:

        ```yml
        on:
            push:
                branches: [ main ]
                paths:
                - `**`
                - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
                - `.github/workflows/sentinel-deploy-<deployment-id>.yml`
        ```

        예를 들어 이러한 설정을 변경하여 워크플로가 주기적으로 실행되거나 서로 다른 워크플로 이벤트를 함께 결합하도록 예약할 수 있습니다.

        자세한 내용은 워크플로 이벤트 구성에 대한 [GitHub 설명서를](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#configuring-workflow-events) 참조하세요.

    - **배포 경로를 수정하려면:**

        섹션에 대해 위에 표시된 기본 구성에서 `on` 섹션의 첫 번째 줄에 있는 와일드카드( `**` `paths` )는 전체 분기가 배포 트리거의 경로에 있음을 나타냅니다.

        이 기본 구성은 콘텐츠가 분기의 모든 부분에 푸시될 때마다 배포 워크플로가 트리거됨을 의미합니다.

        파일의 나중에 `jobs` 섹션에는 다음과 같은 기본 구성이 포함됩니다. `directory: '${{ github.workspace }}'` 이 줄은 폴더 경로를 필터링하지 않고 전체 GitHub 분기가 콘텐츠 배포 경로에 있음을 나타냅니다.

        특정 폴더 경로에서만 콘텐츠를 배포하려면 및 `paths` 구성 모두에 `directory` 추가합니다. 예를 들어 라는 루트 폴더의 콘텐츠만 배포하려면 `SentinelContent` 다음과 같이 코드를 업데이트합니다.

        ```yml
        paths:
        - `SentinelContent/**`
        - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
        - `.github/workflows/sentinel-deploy-<deployment-id>.yml`

        ...
            directory: '${{ github.workspace }}/SentinelContent'
        ```

자세한 내용은 GitHub 작업 및 GitHub 워크플로 편집에 대한 GitHub [설명서를](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onpushpull_requestpaths) 참조하세요.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

**Azure DevOps 배포 파이프라인을 사용자 지정하려면:**

1. Azure DevOps 리포지토리로 이동하여 디렉터리에서 파이프라인 정의 파일을 `.sentinel` 찾습니다.

    파이프라인 이름은 파이프라인 파일의 첫 번째 줄에 표시되며 기본 명명 규칙은 `Deploy Content to <workspace-name> [<deployment-id>]` 입니다.

    `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. 페이지 오른쪽 위에 있는 연필 단추를 선택하여 편집할 파일을 연 다음, 다음과 같이 배포를 수정합니다.

    - **배포 트리거를 수정하려면** `trigger` 실행할 워크플로를 트리거하는 이벤트를 설명하는 코드의 섹션을 업데이트합니다.

        기본적으로 이 구성은 기존 콘텐츠에 대한 수정 및 리포지토리에 새 콘텐츠 추가를 포함하여 연결된 분기에 대한 푸시를 검색하도록 설정됩니다.

        이 트리거를 예약 또는 끌어오기 요청 트리거와 같은 사용 가능한 Azure DevOps 트리거로 수정합니다. 자세한 내용은 Azure DevOps [트리거 설명서를 참조하세요.](/azure/devops/pipelines/yaml-schema)

    - **배포 경로를 수정하려면:**

        섹션의 기본 `trigger` 구성에는 분기가 배포 트리거의 경로에 있음을 나타내는 다음 `main` 코드가 있습니다.

        ```yml
        trigger:
            branches:
                include:
                - main
        ```

        이 기본 구성은 콘텐츠가 분기의 모든 부분에 푸시될 때마다 배포 파이프라인이 트리거됨을 `main` 의미합니다.

        특정 폴더 경로의 콘텐츠만 배포하려면 `include` 필요에 따라 아래의 배포 경로에 대해 섹션, 트리거 및 섹션에 폴더 이름을 `steps` 추가합니다.

        예를 들어 분기에 라는 루트 폴더의 콘텐츠만 배포하려면 `SentinelContent` 다음과 같이 및 설정을 `main` `include` `workingDirectory` 코드에 추가합니다.

        ```yml
        paths:
            exclude:
            - .sentinel/*
            include:
            - .sentinel/sentinel-deploy-39d8ekc8-397-5963-49g8-5k63k5953829.yml
            - SentinelContent
        ....
        steps:
        - task: AzurePowerShell@5
          inputs:
            azureSubscription: `Sentinel_Deploy_ServiceConnection_0000000000000000`
            workingDirectory: `SentinelContent`
        ```

자세한 내용은 Azure DevOps YAML 스키마에 대한 Azure DevOps [설명서를](/azure/devops/pipelines/yaml-schema) 참조하세요.

---

> [!IMPORTANT]
> GitHub 및 Azure DevOps 모두 트리거 경로 및 배포 경로 디렉터리를 일관되게 유지해야 합니다.
>
## <a name="edit-or-delete-content-in-your-repository"></a>리포지토리에서 콘텐츠 편집 또는 삭제

소스 제어 리포지토리에 대한 연결을 성공적으로 만든 후에는 해당 리포지토리의 콘텐츠가 수정되거나 추가될 때마다 배포 워크플로가 다시 실행되어 리포지토리의 모든 콘텐츠를 연결된 모든 Microsoft Sentinel 작업 영역에 배포합니다.

연결된 리포지토리에 저장된 콘텐츠는 Microsoft Sentinel이 아닌 리포지토리에만 편집하는 것이 좋습니다.  예를 들어 분석 규칙을 변경하려면 GitHub 또는 Azure DevOps 직접 변경합니다.

Microsoft Sentinel에서 콘텐츠를 편집한 경우 다음에 리포지토리 콘텐츠가 작업 영역에 배포될 때 변경 내용을 덮어쓰지 않도록 원본 제어 리포지토리로 내보내야 합니다.

콘텐츠를 삭제하는 경우 리포지토리와 Azure Portal 모두 삭제해야 합니다. 리포지토리에서 콘텐츠를 삭제해도 Microsoft Sentinel 작업 영역에서 삭제되지는 않습니다.

## <a name="remove-a-repository-connection"></a>리포지토리 연결 제거

이 절차에서는 Microsoft Sentinel에서 소스 제어 리포지토리에 대한 연결을 제거하는 방법을 설명합니다.

**연결을 제거하려면:**

1. Microsoft Sentinel의 **왼쪽 콘텐츠 관리** 에서 **리포지토리** 를 선택합니다.
1. 표에서 제거할 연결을 선택한 다음, **삭제를** 선택합니다.
1. **예** 를 선택하여 삭제를 확인합니다.

연결을 제거한 후에는 연결을 통해 이전에 배포된 콘텐츠가 Microsoft Sentinel 작업 영역에 남아 있습니다. 연결을 제거한 후 리포지토리에 추가된 콘텐츠는 배포되지 않습니다.

> [!TIP]
> 연결을 삭제할 때 문제 또는 오류 메시지가 발생하는 경우 소스 제어를 확인하여 연결과 연결된 GitHub 워크플로 또는 Azure DevOps 파이프라인이 삭제되었는지 확인하는 것이 좋습니다.
>

### <a name="removing-the-microsoft-sentinel-app-from-your-github-repository"></a>GitHub 리포지토리에서 Microsoft Sentinel 앱 제거

GitHub 리포지토리에서 Microsoft Sentinel 앱을 삭제하려면 *먼저* Microsoft Sentinel **리포지토리** 페이지에서 연결된 모든 연결을 제거하는 것이 좋습니다.

각 Microsoft Sentinel 앱 설치에는 연결을 추가 및 제거할 때 사용되는 고유 ID가 있습니다. ID가 없거나 변경된 경우 Microsoft Sentinel **리포지토리** 페이지에서 연결을 제거하고 향후 콘텐츠 배포를 방지하기 위해 GitHub 리포지토리에서 워크플로를 수동으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

Microsoft Sentinel에서 사용자 지정 콘텐츠를 사용하는 것과 동일한 방식으로 바로 사용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel 솔루션 검색 및 배포(공개 미리 보기)](sentinel-solutions-deploy.md)
- [Microsoft Sentinel 데이터 커넥터](connect-data-sources.md)
- [ASIM(고급 SIEM 정보 모델) 파서(공개 미리 보기)](normalization-about-parsers.md)
- [수집된 데이터 시각화](get-visibility.md)
- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
- [Microsoft Sentinel을 사용하여 위협 헌츠](hunting.md)
- [Microsoft Sentinel 관심 목록 사용](watchlists.md)
- [Microsoft Sentinel에서 플레이북을 사용하여 위협 대응 자동화](automate-responses-with-playbooks.md)
