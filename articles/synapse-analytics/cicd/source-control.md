---
title: Synapse Studio에서 원본 제어
description: Azure Synapse Studio에서 원본 제어를 구성하는 방법을 알아봅니다.
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5b03a7c713203dd61eb95fd5422b3002939e9011
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208740"
---
# <a name="source-control-in-synapse-studio"></a>Synapse Studio에서 원본 제어

기본적으로 Synapse Studio는 Synapse 서비스에 대해 직접 작성합니다. 원본 제어에 Git을 사용하여 공동 작업해야 하는 경우 Synapse Studio를 사용하여 작업 영역을 Git 리포지토리, Azure DevOps 또는 GitHub와 연결할 수 있습니다. 

이 문서에서는 git 리포지토리를 사용하도록 설정한 상태에서 Synapse 작업 영역을 구성하고 작업하는 방법을 설명합니다. 또한 몇 가지 모범 사례 및 문제 해결 가이드가 강조되어 있습니다.

> [!NOTE]
> Azure Government 클라우드에서는 Synapse Studio git 통합을 사용할 수 없습니다.

## <a name="prerequisites"></a>필수 조건
사용자는 Synapse 작업 영역에서 Azure 기여자(Azure RBAC) 이상 역할이 있어야 Synapse를 사용하여 Git 리포지토리를 구성, 편집 및 연결을 끊을 수 있습니다. 

## <a name="configure-git-repository-in-synapse-studio"></a>Synapse Studio에서 Git 리포지토리 구성 

Synapse Studio를 시작한 후 작업 영역에서 git 리포지토리를 구성할 수 있습니다. Synapse Studio 작업 영역은 한 번에 하나의 git 리포지토리에만 연결될 수 있습니다. 

### <a name="configuration-method-1-global-bar"></a>구성 방법 1: 글로벌 모음

Synapse Studio 글로벌 모음에서 **Synapse Live** 드롭다운 메뉴를 선택한 다음 **코드 리포지토리 설정** 을 선택합니다.

![제작에서 코드 리포지토리 설정 구성](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>구성 방법 2: 허브 관리

Synapse Studio의 허브 관리로 이동합니다. **소스 제어** 섹션에서 **Git 구성** 을 선택합니다. 연결된 리포지토리가 없는 경우 **구성** 을 클릭합니다.

![관리 허브에서 코드 리포지토리 설정 구성](media/configure-repo-2.png)

작업 영역에서 Azure DevOps 또는 GitHub git 리포지토리를 연결할 수 있습니다.

## <a name="connect-with-azure-devops-git"></a>Azure DevOps Git을 사용하여 연결 

원본 제어, 공동 작업, 버전 관리 등을 위해 Synapse 작업 영역을 Azure DevOps 리포지토리와 연결할 수 있습니다. Azure DevOps 리포지토리가 없는 경우 [이러한 지침](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)에 따라 리포지토리 리소스를 만듭니다.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git 리포지토리 설정

Git 리포지토리에 연결하는 경우 먼저 리포지토리 유형을 Azure DevOps Git으로 선택한 후 드롭다운 목록에서 Azure AD 테넌트 하나를 선택하고 **계속** 을 클릭합니다.

![코드 리포지토리 설정 구성](media/connect-with-azuredevops-repo-selected.png)

구성 창에 다음 Azure DevOps Git 설정이 표시됩니다.

| 설정 | Description | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다.<br/> | Azure DevOps Git 또는 GitHub |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | `<your tenant name>` |
| **Azure DevOps 계정** | Azure Repos 조직 이름입니다. Azure Repos 조직 이름은 `https://{organization name}.visualstudio.com`에서 확인할 수 있습니다. [Azure Repos 조직에 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | `<your organization name>` |
| **ProjectName** | Azure Repos 프로젝트 이름입니다. Azure Repos 프로젝트 이름은 `https://{organization name}.visualstudio.com/{project name}`에서 확인할 수 있습니다. | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 코드 리포지토리 이름입니다. Azure Repos 프로젝트는 프로젝트가 확장됨에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your Azure Repos code repository name>` |
| **협업 분기** | 게시에 사용되는 Azure Repos 협업 분기입니다. 기본적으로 `master`입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. 기존 분기를 선택하거나 새로 만들 수 있습니다. | `<your collaboration branch name>` |
| **루트 폴더** | Azure Repos 협업 분기의 루트 폴더입니다. | `<your root folder name>` |
| **기존 리소스를 리포지토리로 가져오기** | Synapse Studio의 기존 리소스를 Azure Repos Git 리포지토리로 가져올지 여부를 지정합니다. 확인란을 선택하여 작업 영역 리소스(풀 제외)를 JSON 형식으로 연결된 Git 리포지토리로 가져옵니다. 이 작업은 각 리소스를 개별적으로 내보냅니다. 이 확인란을 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **이 분기로 리소스 가져오기** | 리소스(SQL 스크립트, Notebook, Spark 작업 정의, 데이터 세트, 데이터 흐름 등)를 가져올 분기를 선택합니다. 

Git 리포지토리에 빠르게 연결하기 위해 가리키는 리포지토리 링크를 사용할 수도 있습니다. 

### <a name="use-a-different-azure-active-directory-tenant"></a>다른 Azure Active Directory 테넌트 사용

Azure Repos Git 리포지토리는 다른 Azure Active Directory 테넌트에 있을 수 없습니다. 다른 Azure AD 테넌트를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다. 자세한 내용은 [구독 관리자 변경](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)을 참조하세요.

> [!IMPORTANT]
> 다른 Azure Active Directory에 연결하려면 로그인한 사용자가 해당 Active Directory에 있어야 합니다. 

### <a name="use-your-personal-microsoft-account"></a>개인 Microsoft 계정 사용

Git 통합에 개인 Microsoft 계정을 사용하려면 개인 Azure 리포지토리를 조직의 Active Directory에 연결하면 됩니다.

1. 개인 Microsoft 계정을 조직의 Active Directory에 게스트로 추가합니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../../active-directory/external-identities/add-users-administrator.md)를 참조하세요.

2. 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다. 그런 다음, 조직의 Active Directory로 전환합니다.

3. Azure DevOps 섹션으로 이동하면 이제 개인 리포지토리가 보입니다. 리포지토리를 선택하고 Active Directory와 연결합니다.

이러한 구성 단계 후에는 Synapse Studio에서 Git 통합을 설정할 때 개인 리포지토리를 사용할 수 있습니다.

Azure Repos를 조직의 Active Directory에 연결하는 방법에 대한 자세한 내용은 [조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

## <a name="connect-with-github"></a>GitHub로 연결 

 원본 제어, 공동 작업, 버전 관리를 위해 작업 영역을 GitHub 리포지토리와 연결할 수 있습니다. GitHub 계정 또는 리포지토리가 없는 경우 [이러한 지침](https://github.com/join)에 따라 리소스를 만듭니다.

Synapse Studio를 사용한 GitHub 통합은 공용 GitHub(즉, [https://github.com](https://github.com)) 및 GitHub Enterprise 모두를 지원합니다. GitHub의 리포지토리에 대한 읽기 및 쓰기 권한이 있는 한 공용 및 프라이빗 GitHub 리포지토리를 모두 사용할 수 있습니다.

### <a name="github-settings"></a>GitHub 설정

git 리포지토리에 연결할 때 먼저 GitHub로 리포지토리 유형을 선택한 다음, GitHub 계정, GitHub Enterprise Server를 사용하는 경우 GitHub Enterprise Server URL을 제공하고, GitHub Enterprise Cloud를 사용하는 경우 GitHub Enterprise 조직 이름을 제공합니다. **계속** 을 선택합니다.

> [!NOTE]
> GitHub Enterprise Cloud를 사용하는 경우 **GitHub Enterprise Server 사용** 확인란을 선택 취소한 상태로 둡니다. 

![GitHub 리포지토리 설정](media/connect-with-github-repo-1.png)

구성 창에 다음 GitHub 리포지토리 설정이 표시됩니다.

| **설정** | **설명**  | **값**  |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다. | GitHub |
| **GitHub Enterprise 사용** | GitHub Enterprise를 선택하기 위한 확인란 | 선택하지 않음(기본값) |
| **GitHub Enterprise URL** | GitHub Enterprise 루트 URL(로컬 GitHub Enterprise 서버의 HTTPS여야 함) 예: `https://github.mydomain.com` **GitHub Enterprise 사용** 을 선택한 경우에만 필요 | `<your GitHub enterprise url>` |                                                           
| **GitHub 계정** | 사용자 GitHub 계정 이름. 이 이름은 https:\//github.com/{이름}/{리포지토리 이름}에서 찾을 수 있습니다. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다. | `<your GitHub account name>` |
| **리포지토리 이름**  | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your repository name>` |
| **협업 분기** | 게시에 사용되는 GitHub 협업 분기입니다. 기본값은 마스터입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch>` |
| **루트 폴더** | GitHub 협업 분기의 루트 폴더입니다. |`<your root folder name>` |
| **기존 리소스를 리포지토리로 가져오기** | Synapse Studio에서 Git 리포지토리로 기존 리소스를 가져올지 여부를 지정합니다. 확인란을 선택하여 작업 영역 리소스(풀 제외)를 JSON 형식으로 연결된 Git 리포지토리로 가져옵니다. 이 작업은 각 리소스를 개별적으로 내보냅니다. 이 확인란을 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **이 분기로 리소스 가져오기** | 리소스(SQL 스크립트, Notebook, Spark 작업 정의, 데이터 세트, 데이터 흐름 등)를 가져올 분기를 선택합니다. 

### <a name="github-organizations"></a>GitHub 조직

GitHub 조직에 연결하려면 조직이 Synapse Studio에 권한을 부여해야 합니다. 조직에 대한 관리자 권한이 있는 사용자는 아래 단계를 수행해야 합니다.

#### <a name="connecting-to-github-for-the-first-time"></a>처음으로 GitHub에 연결

처음으로 Synapse Studio에서 GitHub에 연결하는 경우 다음 단계에 따라 GitHub 조직에 연결합니다.

1. Git 구성 창의 *GitHub 계정* 필드에 조직 이름을 입력합니다. GitHub에 로그인하라는 메시지가 표시됩니다. 

1. 사용자 자격 증명을 사용하여 로그인합니다. 

1. *Azure Synapse* 라는 애플리케이션으로 Synapse에 권한을 부여하라는 메시지가 표시됩니다. 이 화면에 Synapse에서 조직에 액세스할 수 있는 권한을 부여하는 옵션이 표시됩니다. 권한을 부여하는 옵션이 표시되지 않으면 관리자에게 GitHub를 통해 권한을 수동으로 부여하도록 요청합니다.

이러한 단계에 따라 작업 영역에서 조직 내 공용 및 프라이빗 리포지토리에 연결할 수 있습니다. 연결할 수 없는 경우 브라우저 캐시를 지우고 다시 시도하세요.

#### <a name="already-connected-to-github-using-a-personal-account"></a>개인 계정을 사용하여 GitHub에 이미 연결됨

이미 GitHub에 연결하고 개인 계정에 대한 액세스 권한만 부여한 경우 아래 단계에 따라 조직에 권한을 부여합니다.

1. GitHub로 이동하여 **설정** 을 엽니다.

    ![GitHub 설정 열기](media/github-settings.png)

1. **애플리케이션** 을 선택합니다. **권한 있는 OAuth 앱** 탭에는 *Azure Synapse* 가 표시됩니다.

    ![OAuth 앱 권한 부여](media/authorize-app.png)

1. *Azure Synapse* 를 선택하고 조직에 액세스 권한을 부여합니다.

    ![조직 권한 부여](media/grant-organization-permission.png)

이러한 단계를 완료하면 작업 영역에서 조직 내 공용 및 프라이빗 리포지토리에 연결할 수 있습니다.

## <a name="version-control"></a>버전 제어

버전 제어 시스템(_원본 제어_ 라고도 함)을 사용하면 개발자가 코드를 공동으로 작업하고 변경 사항을 추적할 수 있습니다. 원본 제어는 다중 개발자 프로젝트를 위한 필수 도구입니다.

### <a name="creating-feature-branches"></a>기능 분기 만들기

Synapse Studio와 연결된 각 Git 리포지토리에는 공동 작업 분기가 있습니다. (`main` 또는 `master`는 기본 협업 분기입니다). 분기 드롭다운에서 **+ 새 분기** 를 클릭하여 기능 분기를 만들 수도 있습니다. 

![새 분기 만들기](media/create-new-branch.png)

새 분기 창이 나타나면 기능 분기의 이름을 입력하고 작업을 기반으로 할 분기를 선택합니다.

![프라이빗 분기를 기반으로 분기 만들기 ](media/create-branch-from-private-branch.png)

기능 분기의 변경 내용을 협업 분기에 병합할 준비가 되면, 분기 드롭다운을 클릭하고 **끌어오기 요청 만들기** 를 선택합니다. 이 작업을 통해 끌어오기 요청을 수행하고, 코드 검토를 수행하고, 협업 분기에 변경 내용을 병합할 수 있는 Git 공급자로 이동합니다. 협업 분기에서 Synapse 서비스에 게시할 수만 있습니다. 

![새 끌어오기 요청 만들기](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>게시 설정 구성

기본적으로 Synapse Studio는 작업 영역 템플릿을 생성하고 `workspace_publish`라는 분기에 저장합니다. 사용자 지정 게시 분기를 구성하려면 협업 분기의 루트 폴더에 `publish_config.json` 파일을 추가합니다. 게시할 때 Synapse Studio는 이 파일을 읽고 `publishBranch` 필드를 찾은 다음 지정된 위치에 작업 영역 템플릿 파일을 저장합니다. 분기가 존재하지 않으면 Synapse Studio에서 자동으로 만듭니다. 이 파일의 모양은 다음과 같습니다.

```json
{
    "publishBranch": "workspace_publish"
}
```

Synapse Studio에는 한 번에 하나의 게시 분기만 있을 수 있습니다. 새 게시 분기를 지정하면 이전 게시 분기가 삭제되지 않습니다. 이전 게시 분기를 제거하려는 경우에는 수동으로 해당 분기를 삭제합니다.


### <a name="publish-code-changes"></a>코드 변경 내용 게시

협업 분기에 대한 변경 내용을 병합한 후 **게시** 를 클릭하여 협업 분기의 코드 변경 내용을 Synapse 서비스에 수동으로 게시합니다.

![변경 내용 게시](media/gitmode-publish.png)

사이드 창이 열리면 게시 분기 및 보류 중인 변경 사항이 올바른지 확인합니다. 변경 내용을 확인한 후 **확인** 을 클릭하여 게시를 확인합니다.

![올바른 게시 분기 확인](media/publish-change.png)

> [!IMPORTANT]
> 협업 분기는 서비스에 무엇이 배포되어 있는지 나타내지 않습니다. 협업 분기의 변경 사항은 수동으로 *게시해야* 합니다.

## <a name="switch-to-a-different-git-repository"></a>다른 Git 리포지토리로 전환

다른 Git 리포지토리로 전환하려면 **소스 제어** 의 관리 허브에서 Git 구성 페이지로 이동합니다. **연결 끊기** 를 선택합니다. 

![GIT 아이콘](media/remove-repository.png)

작업 영역 이름을 입력하고 **연결 해제** 를 클릭하여 작업 영역과 연결된 Git 리포지토리를 제거합니다.

현재 리포지토리와 연결을 제거한 후에 다른 리포지토리를 사용하도록 Git 설정을 구성한 다음, 기존 리소스를 새 리포지토리로 가져올 수 있습니다.

> [!IMPORTANT]
> 작업 영역에서 Git 구성을 제거해도 리포지토리에서 아무것도 삭제되지 않습니다. Synapse 작업 영역에는 게시된 모든 리소스가 포함됩니다. 서비스에 대해 직접 작업 영역을 계속 편집할 수 있습니다.

## <a name="best-practices-for-git-integration"></a>Git 통합에 대한 모범 사례

-   **사용 권한**. 작업 영역에 Git 리포지토리가 연결되면 작업 영역에서 모든 역할로 Git 리포지토리에 액세스할 수 있는 사람은 누구나 Git 모드에서 SQL 스크립트, Notebook, 스파크 작업 정의, 데이터 세트, 데이터 흐름 및 파이프 라인과 같은 아티팩트를 업데이트할 수 있습니다. 일반적으로 모든 팀 구성원이 작업 영역을 업데이트할 수 있는 권한을 갖기를 원하지 않습니다. Synapse 작업 영역 아티팩트 작성자에게만 Git 리포지토리 권한을 부여합니다. 
-   **협업**. 협업 분기에 대한 직접 체크 인은 허용하지 않는 것이 좋습니다. 이렇게 제한하면 [기능 분기 만들기](source-control.md#creating-feature-branches)의 설명대로 모든 체크 인이 끌어오기 요청 검토 프로세스를 거치기 때문에 버그를 방지하는 데 유용할 수 있습니다
-   **Synapse 라이브 모드**. Git 모드로 게시한 후에는 모든 변경 내용이 Synapse live 모드에 반영됩니다. Synapse live 모드에서는 게시를 사용할 수 없습니다. 적절한 권한이 부여된 경우 라이브 모드에서 아티팩트를 보고 실행할 수 있습니다. 
-   **Studio에서 아티팩트를 편집합니다**. Synapse Studio는 작업 영역 원본 제어를 사용하도록 설정하고 변경 내용을 Git에 자동으로 동기화할 수 있는 유일한 장소입니다. SDK, PowerShell을 통한 모든 변경 내용은 Git과 동기화되지 않습니다. Git을 사용하는 경우 항상 Studio에서 아티팩트를 편집하는 것이 좋습니다.

## <a name="troubleshooting-git-integration"></a>Git 통합 문제 해결

### <a name="access-to-git-mode"></a>Git 모드에 대한 액세스 

작업 영역과 연결된 GitHub Git 리포지토리에 대한 권한이 부여되었지만 Git 모드에 액세스할 수 없는 경우: 

1. 캐시를 지우고 페이지를 새로 고칩니다. 

1. GitHub 계정으로 로그인합니다.

### <a name="stale-publish-branch"></a>부실한 게시 분기

게시 분기가 협업 분기와 동기화되지 않고 최근 게시에도 불구하고 오래된 리소스가 포함된 경우에는 다음 단계를 수행합니다.

1. 현재 Git 리포지토리 제거

1. 동일한 설정으로 Git를 다시 구성하지만 **리포지토리로 기존 리소스 가져오기** 가 선택되어 있는지 확인하고 동일한 분기를 선택합니다.  

1. 변경 내용을 협업 분기에 병합하는 끌어오기 요청을 만듭니다. 

## <a name="unsupported-features"></a>지원되지 않는 기능

- Synapse Studio는 커밋의 cherry-pick 또는 리소스의 선택적 게시를 허용할 수 없습니다. 
- Synapse Studio는 커밋 메시지 사용자 지정을 지원하지 않습니다.
- 설계상 Studio에서의 삭제 작업은 직접 Git에 커밋됩니다.

## <a name="next-steps"></a>다음 단계

* 연속 통합 및 배포를 구현하려면 [CI/CD(연속 통합 및 지속적인 업데이트)](continuous-integration-delivery.md)를 참조하세요.
