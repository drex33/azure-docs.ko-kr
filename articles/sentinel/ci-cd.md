---
title: 리포지토리에서 사용자 지정 콘텐츠 배포 | Microsoft Docs
description: 이 문서에서는 사용자 지정 콘텐츠를 저장 하 고 Azure 센티널에 배포할 수 있는 GitHub 또는 Azure DevOps 리포지토리를 사용 하 여 연결을 만드는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/20/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b31e2e67058e6762e590f99e49eb1e4658b6c2e
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990080"
---
# <a name="deploy-custom-content-from-your-repository-public-preview"></a>리포지토리에서 사용자 지정 콘텐츠 배포 (공개 미리 보기)

> [!IMPORTANT]
>
> Azure 센티널 **리포지토리** 페이지는 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 센티널 *콘텐츠* 는 azure 센티널에서 수집, 모니터링, 경고, 구하기 등의 고객을 지원 하는 Siem (보안 정보 및 이벤트 관리)입니다. 예를 들어 Azure 센티널 콘텐츠는 데이터 커넥터, 파서, 통합 문서 및 분석 규칙을 포함 합니다. 자세한 내용은 [Azure 센티널 콘텐츠 및 솔루션 정보](sentinel-solutions.md)를 참조 하세요.

Azure 센티널 콘텐츠 허브에 제공 된 기본 제공 (기본 제공) 콘텐츠를 사용 하 여 사용자의 요구에 맞게 사용자 지정 하거나 처음부터 직접 사용자 지정 콘텐츠를 만들 수 있습니다.

사용자 지정 콘텐츠를 만들 때 사용자 고유의 Azure 센티널 작업 영역 또는 GitHub 및 Azure DevOps 리포지토리를 비롯 한 외부 소스 제어 리포지토리에 저장 하 고 관리할 수 있습니다. 이 문서에서는 Azure 센티널과 외부 소스 제어 리포지토리 간의 연결을 만들고 관리 하는 방법을 설명 합니다. 외부 리포지토리에서 콘텐츠를 관리 하면 Azure 센티널 외부에서 해당 콘텐츠를 업데이트 하 고 작업 영역에 자동으로 배포할 수 있습니다.

> [!TIP]
> 이 문서에서는 특정 형식의 콘텐츠를 처음부터 만드는 방법을 설명 *하지* 는 않습니다. 자세한 내용은 각 콘텐츠 형식에 대 한 관련 [Azure 센티널 GitHub wiki](https://github.com/Azure/Azure-Sentinel/wiki#get-started) 를 참조 하세요.
>

## <a name="prerequisites-and-scope"></a>필수 조건 및 범위

외부 소스 제어 리포지토리에 Azure 센티널 작업 영역을 연결 하기 전에 다음이 있는지 확인 합니다.

- 작업 영역에 배포 하려는 사용자 지정 콘텐츠 파일을 포함 하는 GitHub 또는 Azure DevOps 리포지토리에 대 한 액세스는 [ARM (관련 Azure Resource Manager) 템플릿에](/azure/azure-resource-manager/templates/)액세스 합니다.

    Azure 센티널은 현재 GitHub 및 Azure DevOps 리포지토리를 통해서만 연결을 지원 합니다.

- Azure 센티널 작업 영역을 포함 하는 리소스 그룹의 **소유자** 역할입니다. **소유자** 역할은 Azure 센티널과 소스 제어 리포지토리 간의 연결을 만드는 데 필요 합니다.

### <a name="maximum-connections-and-deployments"></a>최대 연결 및 배포

- 각 Azure 센티널 작업 영역은 현재 **5 개의 연결** 로 제한 됩니다.

- 각 Azure 리소스 그룹은 배포 기록에서 **800 배포** 로 제한 됩니다. 리소스 그룹에 ARM 템플릿 배포의 볼륨이 많은 경우 오류가 표시 될 수 있습니다 `Deployment QuotaExceeded` . 자세한 내용은 Azure Resource Manager 템플릿 설명서에서 [DeploymentQuotaExceeded](/azure/azure-resource-manager/templates/deployment-quota-exceeded) 를 참조 하세요.

### <a name="validate-your-content"></a>콘텐츠 유효성 검사

리포지토리 연결을 통해 Azure 센티널에 콘텐츠를 배포 하는 경우 데이터가 올바른 ARM 템플릿 형식 인지 확인 하는 것 외에는 해당 콘텐츠의 유효성을 검사 하지 않습니다.

일반 유효성 검사 프로세스를 사용 하 여 콘텐츠 템플릿의 유효성을 검사 하는 것이 좋습니다. [Azure 센티널 GitHub 유효성 검사 프로세스](https://github.com/Azure/Azure-Sentinel/wiki#test-your-contribution) 및 도구를 활용 하 여 사용자 고유의 유효성 검사 프로세스를 설정할 수 있습니다.

## <a name="connect-a-repository"></a>리포지토리 커넥트

이 절차에서는 azure 센티널 작업 영역에 GitHub 또는 Azure DevOps 리포지토리를 연결 하는 방법에 대해 설명 합니다. 여기서 azure 센티널 대신 사용자 지정 콘텐츠를 저장 하 고 관리할 수 있습니다.

각 연결은 분석 규칙, 자동화 규칙, 구하기 쿼리, 파서, 플레이 북 및 통합 문서를 포함 하 여 여러 유형의 사용자 지정 콘텐츠를 지원할 수 있습니다. 자세한 내용은 [Azure 센티널 콘텐츠 및 솔루션 정보](sentinel-solutions.md)를 참조 하세요.


**연결을 만들려면 다음을 수행 합니다**.

1. 연결에 사용 하려는 자격 증명을 사용 하 여 소스 제어 앱에 로그인 했는지 확인 합니다.  현재 다른 자격 증명을 사용 하 여 로그인 한 경우 먼저 로그 아웃 합니다.

1. Azure 센티널의 **콘텐츠 관리** 왼쪽에서 **리포지토리** 를 선택 합니다.

1. **새로 추가** 를 선택 하 고 **새 연결 만들기** 페이지에서 연결에 대 한 의미 있는 이름 및 설명을 입력 합니다.

1. **원본 제어** 드롭다운에서 연결 하려는 리포지토리의 유형을 선택한 다음 **권한 부여** 를 선택 합니다.

1. 연결 유형에 따라 다음 탭 중 하나를 선택 합니다.
    # <a name="github"></a>[GitHub](#tab/github)

    1. 메시지가 표시 되 면 GitHub 자격 증명을 입력 합니다.

        처음으로 연결을 추가 하면 새 브라우저 창이 나 탭이 표시 되어 Azure 센티널에 대 한 연결 권한을 부여 하 라는 메시지가 표시 됩니다. 동일한 브라우저에서 GitHub 계정에 이미 로그인 한 경우에는 GitHub 자격 증명이 자동으로 채워집니다.

    1. 이제 **리포지토리** 영역이 **새 연결 만들기** 페이지에 표시 되며, 여기서 연결할 기존 리포지토리를 선택할 수 있습니다. 목록에서 리포지토리를 선택 하 고 **리포지토리 추가** 를 선택 합니다.

        처음으로 특정 리포지토리에 연결 하면 새 브라우저 창이 나 탭이 표시 되어 리포지토리에 **Azure 센티널** 앱을 설치 하 라는 메시지가 표시 됩니다. 리포지토리가 여러 개 있는 경우 **Azure 센티널** 앱을 설치 하려는 저장소를 선택 하 고 설치 합니다.

        앱 설치를 계속 하려면 GitHub로 이동 합니다.

    1. 리포지토리에 **Azure 센티널** 앱을 설치 하면 **새 연결 만들기** 페이지의 **분기** 드롭다운이 분기로 채워집니다. Azure 센티널 작업 영역에 연결 하려는 분기를 선택 합니다.

    1. **콘텐츠 형식** 드롭다운에서 배포할 콘텐츠 형식을 선택 합니다.

        - 파서와 검색 쿼리는 모두 **저장 된 검색** API를 사용 하 여 Azure 센티널에 콘텐츠를 배포 합니다. 이러한 콘텐츠 형식 중 하나를 선택 하 고 분기에 다른 형식의 콘텐츠가 있는 경우 두 콘텐츠 형식이 모두 배포 됩니다.

        - 다른 모든 콘텐츠 형식의 경우 **새 연결 만들기** 창에서 콘텐츠 형식을 선택 하면 해당 콘텐츠만 Azure 센티널에 배포 됩니다. 다른 형식의 콘텐츠는 배포 되지 않습니다.

    1. **만들기** 를 선택하여 연결을 만듭니다. 다음은 그 예입니다.

        :::image type="content" source="media/ci-cd/create-new-connection-github.png" alt-text="새 GitHub 리포지토리 연결의 스크린샷":::


    # <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

    현재 Azure 자격 증명을 사용 하 여 Azure DevOps에 자동으로 로그인 됩니다. azure 센티널에서 사용 하는 것과 동일한 자격 증명을 사용 하 여 Azure DevOps에 로그인 되어 있지 않은 경우 Azure DevOps에서 계정을 전환 하 여 azure 센티널의 계정과 일치 시킵니다.

    1.  표시 되는 드롭다운 목록에서 Azure 센티널의 **조직**, **Project**, **리포지토리**, **분기** 및 **콘텐츠 형식을** 선택 합니다.

        - 파서와 검색 쿼리는 모두 **저장 된 검색** API를 사용 하 여 Azure 센티널에 콘텐츠를 배포 합니다. 이러한 콘텐츠 형식 중 하나를 선택 하 고 분기에 다른 형식의 콘텐츠가 있는 경우 두 콘텐츠 형식이 모두 배포 됩니다.

        - 다른 모든 콘텐츠 형식의 경우 **새 연결 만들기** 창에서 콘텐츠 형식을 선택 하면 해당 콘텐츠만 Azure 센티널에 배포 됩니다. 다른 형식의 콘텐츠는 배포 되지 않습니다.

    1. **만들기** 를 선택하여 연결을 만듭니다. 다음은 그 예입니다.

        :::image type="content" source="media/ci-cd/create-new-connection-devops.png" alt-text="새 GitHub 리포지토리 연결의 스크린샷":::

    ---

    > [!NOTE]
    > 단일 Azure 센티널 작업 영역에서 동일한 리포지토리 및 분기를 사용 하 여 중복 된 연결을 만들 수 없습니다.
    >

연결이 만들어지면 리포지토리에 새 워크플로 또는 파이프라인이 생성 되 고 리포지토리에 저장 된 콘텐츠가 Azure 센티널 작업 영역에 배포 됩니다.

배포 시간은 배포 하는 콘텐츠의 양에 따라 다를 수 있습니다. 

### <a name="view-the-deployment-status"></a>배포 상태를 확인 합니다.

- **GitHub**: 리포지토리의 **작업** 탭에서 자세한 배포 로그 및 특정 오류 메시지 (관련 된 경우)에 액세스할 수 있는 워크플로 **. yaml** 파일을 선택 합니다.
- **Azure DevOps**: 리포지토리의 **Pipelines** 탭에 있습니다.

배포가 완료 되 면 다음을 수행 합니다.

- 리포지토리에 저장 된 콘텐츠는 Azure 센티널 작업 영역에서 관련 Azure 센티널 페이지에 표시 됩니다.

- **저장소** 페이지의 연결 세부 정보는 연결의 배포 로그에 대 한 링크로 업데이트 됩니다. 다음은 그 예입니다.

    :::image type="content" source="media/ci-cd/deployment-logs-link.png" alt-text="GitHub 리포지토리 연결의 배포 로그 스크린샷":::

### <a name="customize-the-deployment-workflow"></a>배포 워크플로 사용자 지정

기본 콘텐츠 배포는 해당 분기의 모든 항목이 푸시 될 때마다 연결 된 리포지토리 분기에서 관련 된 모든 사용자 지정 콘텐츠를 배포 합니다.

GitHub 또는 Azure DevOps에서 콘텐츠 배포에 대 한 기본 구성이 모든 요구 사항을 충족 하지 않는 경우 요구 사항에 맞게 환경을 수정할 수 있습니다.

예를 들어 다른 배포 트리거를 구성 하거나 특정 루트 폴더의 콘텐츠만 배포할 수 있습니다.

연결 유형에 따라 다음 탭 중 하나를 선택 합니다.

# <a name="github"></a>[GitHub](#tab/github)

**GitHub 배포 워크플로를 사용자 지정 하려면**:

1. GitHub에서 리포지토리로 이동 하 여 디렉터리에서 워크플로를 찾습니다 `.github/workflows` .

    워크플로 이름은 워크플로 파일의 첫 번째 줄에 표시 되며 다음과 같은 기본 명명 규칙이 `Deploy Content to <workspace-name> [<deployment-id>]` 있습니다.

    예: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. 페이지의 오른쪽 위에서 연필 단추를 선택 하 여 편집할 파일을 열고 다음과 같이 배포를 수정 합니다.

    - **배포 트리거를 수정 하려면** `on` 워크플로 실행을 트리거하는 이벤트를 설명 하는 코드에서 섹션을 업데이트 합니다.

        기본적으로이 구성은로 설정 됩니다 `on: push` . 즉, 기존 콘텐츠를 수정 하 고 리포지토리에 새 콘텐츠를 추가 하는 작업을 포함 하 여 연결 된 분기에 대 한 모든 푸시에서 워크플로가 트리거됩니다. 다음은 그 예입니다.

        ```yml
        on:
            push:
                branches: [ main ]
                paths:
                - `**`
                - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
                - `.github/workflows/sentinel-deploy-<deployment-id>.yml`
        ```

        예를 들어 워크플로를 정기적으로 실행 하도록 예약 하거나 다른 워크플로 이벤트를 결합 하기 위해 이러한 설정을 변경할 수 있습니다.

        자세한 내용은 워크플로 이벤트 구성에 대 한 [GitHub 설명서](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#configuring-workflow-events) 를 참조 하세요.

    - **배포 경로를 수정 하려면**:

        섹션에서 위에 표시 된 기본 구성에서 `on` `**` 섹션의 첫 번째 줄에 있는 와일드 카드 ()는 `paths` 전체 분기가 배포 트리거의 경로에 있음을 의미 합니다.

        이 기본 구성은 콘텐츠를 분기의 모든 부분에 푸시할 때마다 배포 워크플로가 트리거되는 것을 의미 합니다.

        파일에서 나중에 섹션에는 `jobs` 다음과 같은 기본 구성이 포함 됩니다 `directory: '${{ github.workspace }}'` . 이 줄은 전체 GitHub 분기가 폴더 경로를 필터링 하지 않고 콘텐츠 배포에 대 한 경로에 있음을 나타냅니다.

        특정 폴더 경로 에서만 콘텐츠를 배포 하려면 및 구성 모두에 콘텐츠를 추가 `paths` `directory` 합니다. 예를 들어 이라는 루트 폴더의 콘텐츠만 배포 하려면 다음과 `SentinelContent` 같이 코드를 업데이트 합니다.

        ```yml
        paths:
        - `SentinelContent/**`
        - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
        - `.github/workflows/sentinel-deploy-<deployment-id>.yml`

        ...
            directory: '${{ github.workspace }}/SentinelContent'
        ```

자세한 내용은 GitHub 작업 및 GitHub 워크플로 편집에 대 한 [GitHub 설명서](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onpushpull_requestpaths) 를 참조 하세요.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

**Azure DevOps 배포 파이프라인을 사용자 지정 하려면**:

1. Azure DevOps에서 리포지토리로 이동 하 여 디렉터리에서 파이프라인 정의 파일을 찾습니다 `.sentinel` .

    파이프라인 이름이 파이프라인 파일의 첫 번째 줄에 표시 되 고 기본 명명 규칙 인가 `Deploy Content to <workspace-name> [<deployment-id>]` 있습니다.

    예: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. 페이지의 오른쪽 위에서 연필 단추를 선택 하 여 편집할 파일을 열고 다음과 같이 배포를 수정 합니다.

    - **배포 트리거를 수정 하려면** `trigger` 워크플로 실행을 트리거하는 이벤트를 설명 하는 코드에서 섹션을 업데이트 합니다.

        기본적으로이 구성은 기존 콘텐츠를 수정 하 고 리포지토리에 새 콘텐츠를 추가 하는 작업을 포함 하 여 연결 된 분기로의 푸시를 검색 하도록 설정 됩니다.

        예약 또는 끌어오기 요청 트리거와 같은 사용 가능한 모든 Azure DevOps 트리거로이 트리거를 수정 합니다. 자세한 내용은 [Azure DevOps 트리거 설명서](/azure/devops/pipelines/yaml-schema)를 참조 하세요.


    - **배포 경로를 수정 하려면**:

        섹션에 대 한 기본 구성에는 `trigger` `main` 분기가 배포 트리거의 경로에 있음을 나타내는 다음 코드가 있습니다.

        ```yml
        trigger:
            branches:
                include:
                - main
        ```

        이 기본 구성은 콘텐츠가 분기의 모든 부분으로 푸시되는 경우 언제 든 지 배포 파이프라인이 트리거됩니다 `main` .

        특정 폴더 경로에만 콘텐츠를 배포 하려면 섹션에 폴더 이름을 추가 하 `include` 고 필요에 따라 `steps` 아래의 배포 경로에 대해 섹션을 추가 합니다.

        예를 들어 분기에 이름이 지정 된 루트 폴더 에서만 콘텐츠를 배포 하려면 다음과 `SentinelContent` `main` `include` 같이 및 `workingDirectory` 설정을 코드에 추가 합니다.

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

자세한 내용은 Azure DevOps yaml 스키마에 대 한 [Azure DevOps 설명서](/azure/devops/pipelines/yaml-schema) 를 참조 하세요.

---

> [!IMPORTANT]
> GitHub 및 Azure DevOps 둘 다에서 트리거 경로와 배포 경로 디렉터리를 일관 되 게 유지 해야 합니다.
>
## <a name="edit-or-delete-content-in-your-repository"></a>리포지토리의 콘텐츠 편집 또는 삭제

원본 제어 리포지토리에 대 한 연결을 성공적으로 만든 후에는 해당 리포지토리의 콘텐츠를 수정 하거나 추가할 때마다 배포 워크플로가 다시 실행 되 고 모든 연결 된 Azure 센티널 작업 영역에 리포지토리의 모든 콘텐츠가 배포 됩니다.

연결 된 리포지토리에 저장 된 콘텐츠는 Azure 센티널 뿐만 아니라 리포지토리에 *만* 편집 하는 것이 좋습니다. 예를 들어 분석 규칙을 변경 하려면 GitHub 또는 Azure DevOps에서 직접 수행 해야 합니다.

Azure 센티널에서 콘텐츠를 편집한 경우 원본 제어 리포지토리로 내보내 해당 콘텐츠를 다음에 작업 영역에 배포할 때 변경 내용을 덮어쓰지 않도록 합니다.

콘텐츠를 삭제 하는 경우 리포지토리와 Azure Portal 모두에서 삭제 해야 합니다. 리포지토리에서 콘텐츠를 삭제 해도 Azure 센티널 작업 영역에서 삭제 되지 않습니다.

## <a name="remove-a-repository-connection"></a>리포지토리 연결 제거

이 절차에서는 Azure 센티널에서 원본 제어 리포지토리에 대 한 연결을 제거 하는 방법을 설명 합니다.

**연결을 제거 하려면**:

1. Azure 센티널의 **콘텐츠 관리** 왼쪽에서 **리포지토리** 를 선택 합니다.
1. 표에서 제거 하려는 연결을 선택 하 고 **삭제** 를 선택 합니다.
1. **예** 를 선택하여 삭제를 확인합니다.

연결을 제거 하면 이전에 연결을 통해 배포 된 콘텐츠가 Azure 센티널 작업 영역에 남아 있습니다. 연결을 제거한 후 리포지토리에 추가 된 콘텐츠는 배포 되지 않습니다.

> [!TIP]
> 연결을 삭제할 때 문제가 발생 하거나 오류 메시지가 표시 되는 경우 소스 제어를 확인 하 여 연결과 관련 된 GitHub 워크플로 또는 Azure DevOps 파이프라인이 삭제 되었는지 확인 하는 것이 좋습니다.
>

### <a name="removing-the-azure-sentinel-app-from-your-github-repository"></a>GitHub 리포지토리에서 Azure 센티널 앱 제거

GitHub 리포지토리에서 azure 센티널 앱을 삭제 하려는 경우 *먼저* azure 센티널 **리포지토리** 페이지에서 연결 된 모든 연결을 제거 하는 것이 좋습니다.

각 Azure 센티널 앱 설치에는 연결을 추가 하 고 제거할 때 사용 되는 고유 ID가 있습니다. ID가 누락 되거나 변경 된 경우 Azure 센티널 **리포지토리** 페이지에서 연결을 제거 하 고 나중에 콘텐츠를 배포 하지 않도록 GitHub 리포지토리에서 워크플로를 수동으로 제거 해야 합니다.

## <a name="next-steps"></a>다음 단계

기본 콘텐츠를 사용 하는 것과 동일한 방식으로 Azure 센티널에서 사용자 지정 콘텐츠를 사용 합니다.

자세한 내용은 다음을 참조하세요.

- [Azure 센티널 솔루션 검색 및 배포 (공개 미리 보기)](sentinel-solutions-deploy.md)
- [Azure Sentinel 데이터 커넥터](connect-data-sources.md)
- [ASIM(Azure Sentinel 정보 모델) 파서(퍼블릭 미리 보기)](normalization-about-parsers.md)
- [수집된 데이터 시각화](get-visibility.md)
- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
- [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)
- [Azure Sentinel 관심 목록 사용](watchlists.md)
- [Azure Sentinel의 플레이북을 사용하여 위협 대응 자동화](automate-responses-with-playbooks.md)
