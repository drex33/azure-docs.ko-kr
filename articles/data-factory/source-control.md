---
title: 원본 제어
description: Azure Data Factory에서 소스 제어를 구성하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/23/2021
ms.openlocfilehash: 84f01cfef31995034d31eb8e3173217915584b7d
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956094"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory의 소스 제어
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

기본적으로는 Azure Data Factory 사용자 인터페이스 환경(UX) 작성자는 데이터 팩터리 서비스에서 직접 작성합니다. 이 환경에는 다음과 같은 제한 사항이 있습니다.

- Data Factory 서비스는 사용자의 변경 사항을 위한 JSON 엔터티를 저장하는 리포지토리를 포함하지 않습니다. 변경 내용을 저장하는 유일한 방법은 **모두 게시** 단추를 사용하는 것이며 모든 변경 내용은 데이터 팩터리 서비스에 직접 게시됩니다.
- Data Factory 서비스는 협업 및 버전 제어에 최적화되어 있지 않습니다.
- Data Factory 자체를 배포하는 데 필요한 Azure Resource Manager 템플릿이 포함되어 있지 않습니다.

작성 환경을 개선하기 위해 Azure Data Factory를 사용하면 Azure Repos 또는 GitHub를 사용하여 Git 리포지토리를 구성할 수 있습니다. Git는 변경 내용 추적과 협업 환경을 개선할 수 있는 버전 제어 시스템입니다. 본 문서에서는 git 리포지토리에서 구성하고 작업하는 방법과 함께 모범 사례 및 문제 해결 가이드를 간략하게 설명합니다.

> [!NOTE]
> azure .gov, azure 중국에서 공개 지원을 GitHub 추가 했습니다. [알림 블로그](https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918)를 참조 하세요.

Azure Data Factory와 Git를 통합하는 방법에 대한 자세한 내용은 아래의 15분 자습서 비디오를 참조하세요.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Git 통합의 장점

Git 통합이 작성 환경에 제공하는 일부 장점이 아래에 나열되어 있습니다.

-   **소스 제어:** 데이터 팩터리 워크로드가 중요해지면서 팩터리를 Git에 통합하여 다음과 같은 여러 소스 제어 이점을 활용할 수 있습니다.
    -   변경 내용을 추적/감사할 수 있습니다.
    -   버그를 발생한 변경 내용을 되돌릴 수 있습니다.
-   **부분 저장:** 데이터 팩터리 서비스에서 직접 작성하면 변경 내용을 초안으로 저장할 수 없으며 모든 게시는 데이터 팩터리 유효성 검사를 통과해야 합니다. 파이프라인이 완료되지 않았거나 컴퓨터 작동 중단 시 변경 내용을 잃지 않으려는 경우 git 통합을 사용하면 상태와 관계없이 데이터 팩터리 리소스를 점진적으로 변경할 수 있습니다. git 리포지토리를 구성하면 변경 사항을 저장하고 변경 사항을 만족스럽게 테스트한 경우에만 게시할 수 있습니다.
-   **협업 및 제어:** 동일한 팩터리에 기여하는 팀 멤버가 여러 명인 경우에는 코드 검토 프로세스를 통해 팀원이 서로 협업하도록 할 수 있습니다. 일부 기여자는 다른 권한을 갖도록 팩터리를 설정할 수도 있습니다. 일부 팀 멤버는 Git를 통해서만 변경할 수 있고, 팀에서 특정 사용자만 팩터리에 변경 내용을 게시하도록 허용할 수 있습니다.
-   **CI/CD 향상:**  [지속적인 업데이트 프로세스](continuous-integration-delivery.md)를 통해 여러 환경에 배포하는 경우 GIT 통합을 통해 특정 작업을 더 쉽게 수행할 수 있습니다. 이러한 작업 중 일부는 다음을 포함합니다.
    -   'dev' 팩터리에 변경 사항이 생기는 즉시 자동으로 트리거되도록 릴리스 파이프라인을 구성합니다.
    -   Resource Manager 템플릿에서 매개 변수로 사용할 수 있는 속성을 팩터리에서 사용자 지정합니다. 필수 속성 세트만 매개 변수로 유지하고 다른 모든 항목은 하드 코딩하면 유용할 수 있습니다.
-   **성능 향상:** git 통합을 사용하는 평균 팩터리는 데이터 팩터리 서비스에서 작성하는 경우보다 10배 더 빨리 로드됩니다. 이러한 성능 향상은 리소스가 Git를 통해 다운로드되기 때문입니다.

> [!NOTE]
> Git 리포지토리가 구성되면 Azure Data Factory UX에서 Data Factory 서비스를 사용하여 직접 작성할 수 없습니다. PowerShell 또는 SDK를 통해 변경된 내용은 Data Factory 서비스에 직접 게시되며 Git에 입력되지 않습니다.

## <a name="connect-to-a-git-repository"></a>Git 리포지토리에 연결

Azure Repos 및 GitHub 모두에 대한 데이터 팩터리에 Git 리포지토리를 연결하는 방법에는 네 가지가 있습니다. Git 리포지토리에 연결한 후 [관리 허브](author-management-hub.md)에서 **원본 제어** 섹션의 **Git 구성** 에서 구성을 보고 관리할 수 ​​있습니다.

### <a name="configuration-method-1-home-page"></a>구성 방법 1: 홈페이지

Azure Data Factory 홈페이지 맨 위에 있는 **코드 리포지토리 설정** 을 선택합니다.

:::image type="content" source="media/doc-common-process/set-up-code-repository.png" alt-text="홈페이지에서 코드 리포지토리 구성":::

### <a name="configuration-method-2-authoring-canvas"></a>구성 방법 2: 제작 캔버스

Azure Data Factory UX 제작 캔버스에서 **Data Factory** 드롭다운 메뉴를 선택한 다음, **코드 리포지토리 설정** 을 선택합니다.

:::image type="content" source="media/author-visually/configure-repo-2.png" alt-text="제작에서 코드 리포지토리 설정 구성":::

### <a name="configuration-method-3-management-hub"></a>구성 방법 3: 관리 허브

ADF UX의 관리 허브로 이동합니다. **소스 제어** 섹션에서 **Git 구성** 을 선택합니다. 연결된 리포지토리가 없는 경우 **구성** 을 클릭합니다.

:::image type="content" source="media/author-visually/configure-repo-3.png" alt-text="관리 허브에서 코드 리포지토리 설정 구성":::

### <a name="configuration-method-4-during-factory-creation"></a>구성 방법 4: 팩터리를 만드는 동안

Azure Portal에서 새 데이터 팩터리를 만들 때 **Git 구성** 탭에서 Git 리포지토리 정보를 구성할 수 있습니다.

> [!NOTE]
> Azure Portal에서 Git을 구성할 때 프로젝트 이름 및 리포지토리 이름과 같은 설정은 드롭다운에 포함되지 않고 수동으로 입력해야 합니다.

:::image type="content" source="media/author-visually/configure-repo-4.png" alt-text="Azure Portal에서 코드 리포지토리 설정 구성":::

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 통합을 통한 작성

Azure Repos Git 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 협업을 지원합니다. 사용자는 원본 제어, 협업, 버전 관리 등을 위해 Azure Repos Git 조직 리포지토리와 데이터 팩터리를 연결할 수 있습니다. 단일 Azure Repos Git 계정이 여러 리포지토리를 포함할 수는 있지만 각 Azure Repos Git 리포지토리는 데이터 팩터리 하나에만 연결할 수 있습니다. Azure Repos 조직 또는 리포지토리가 없는 경우 [이러한 지침](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)에 따라 리소스를 만듭니다.

> [!NOTE]
> Azure Repos Git 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. 데이터 팩터리 파이프라인은 Azure Repos Git 리포지토리에 저장된 스크립트 또는 데이터 파일을 Azure Storage에 자동으로 업로드하지 않습니다.

### <a name="azure-repos-settings"></a>Azure Repos 설정

:::image type="content" source="media/author-visually/repo-settings.png" alt-text="코드 리포지토리 설정 구성":::

구성 창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| 설정 | Description | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다.<br/> | Azure DevOps Git 또는 GitHub |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | `<your tenant name>` |
| **Azure Repos 조직** | Azure Repos 조직 이름입니다. Azure Repos 조직 이름은 `https://{organization name}.visualstudio.com`에서 확인할 수 있습니다. [Azure Repos 조직에 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | `<your organization name>` |
| **ProjectName** | Azure Repos 프로젝트 이름입니다. Azure Repos 프로젝트 이름은 `https://{organization name}.visualstudio.com/{project name}`에서 확인할 수 있습니다. | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 코드 리포지토리 이름입니다. Azure Repos 프로젝트는 프로젝트가 확장됨에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your Azure Repos code repository name>` |
| **협업 분기** | 게시에 사용되는 Azure Repos 협업 분기입니다. 기본적으로 `main`입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch name>` |
| **분기 게시** | 게시 분기는 게시 관련 ARM 템플릿이 저장 및 업데이트 되는 리포지토리의 분기입니다. 기본적으로 `adf_publish`입니다. | `<your publish branch name>` |
| **루트 폴더** | Azure Repos 협업 분기의 루트 폴더입니다. | `<your root folder name>` |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX **제작 캔버스** 에서 기존 데이터 팩터리 리소스를 Azure Repos Git 리포지토리로 가져올 것인지를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기** | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 협업 b. 새로 만들기 c. 기존 리소스 사용 |  |

> [!NOTE]
> Microsoft Edge를 사용하는 경우 Azure DevOps 계정 드롭다운에 값이 표시되지 않으면 신뢰할 수 있는 사이트 목록에 https://*.visualstudio.com을 추가합니다.

### <a name="use-a-different-azure-active-directory-tenant"></a>다른 Azure Active Directory 테넌트 사용

Azure Repos Git 리포지토리는 다른 Azure Active Directory 테넌트에 있을 수 없습니다. 다른 Azure AD 테넌트를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다. 자세한 내용은 [구독 관리자 변경](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)을 참조하세요.

> [!IMPORTANT]
> 다른 Azure Active Directory에 연결하려면 로그인한 사용자가 해당 Active Directory에 있어야 합니다. 

### <a name="use-your-personal-microsoft-account"></a>개인 Microsoft 계정 사용

Git 통합에 개인 Microsoft 계정을 사용하려면 개인 Azure 리포지토리를 조직의 Active Directory에 연결하면 됩니다.

1. 개인 Microsoft 계정을 조직의 Active Directory에 게스트로 추가합니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../active-directory/external-identities/add-users-administrator.md)를 참조하세요.

2. 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다. 그런 다음, 조직의 Active Directory로 전환합니다.

3. Azure DevOps 섹션으로 이동하면 이제 개인 리포지토리가 보입니다. 리포지토리를 선택하고 Active Directory와 연결합니다.

이러한 구성 단계 후, Data Factory UI에서 Git 통합을 설정할 때 개인 리포지토리를 사용할 수 있습니다.

Azure Repos를 조직의 Active Directory에 연결하는 방법에 대한 자세한 내용은 [Azure DevOps 조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

## <a name="author-with-github-integration"></a>GitHub 통합을 통한 작성

GitHub 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 협업을 지원합니다. 사용자는 원본 제어, 협업 및 버전 관리를 위해 GitHub 계정 리포지토리에 데이터 팩터리를 연결할 수 있습니다. 단일 GitHub 계정은 여러 리포지토리를 가질 수 있지만 GitHub 리포지토리는 단 하나의 데이터 팩터리와 연결할 수 있습니다. GitHub 계정 또는 리포지토리가 없는 경우 [이러한 지침](https://github.com/join)에 따라 리소스를 만듭니다.

Data Factory를 사용한 GitHub 통합은 공용 GitHub(즉, [https://github.com](https://github.com)) 및 GitHub Enterprise 모두를 지원합니다. GitHub의 리포지토리에 대한 읽기 및 쓰기 권한이 있다면 Data Factory를 사용하여 공용 및 프라이빗 GitHub 리포지토리를 모두 사용할 수 있습니다.

GitHub 리포지토리를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다.

### <a name="github-settings"></a>GitHub 설정

:::image type="content" source="media/author-visually/github-integration-image2.png" alt-text="GitHub 리포지토리 설정":::

구성 창에 다음 GitHub 리포지토리 설정이 표시됩니다.

| **설정** | **설명**  | **값**  |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다. | GitHub |
| **GitHub Enterprise 사용** | GitHub Enterprise를 선택하기 위한 확인란 | 선택하지 않음(기본값) |
| **GitHub Enterprise URL** | GitHub Enterprise 루트 URL(로컬 GitHub Enterprise 서버의 HTTPS여야 함) 예: `https://github.mydomain.com` **GitHub Enterprise 사용** 을 선택한 경우에만 필요 | `<your GitHub enterprise url>` |                                                           
| **GitHub 계정** | 사용자 GitHub 계정 이름. 이 이름은 https:\//github.com/{이름}/{리포지토리 이름}에서 찾을 수 있습니다. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다. | `<your GitHub account name>` |
| **리포지토리 이름**  | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your repository name>` |
| **협업 분기** | 게시에 사용되는 GitHub 협업 분기입니다. 이 분기가 기본 분기입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch>` |
| **루트 폴더** | GitHub 협업 분기의 루트 폴더입니다. |`<your root folder name>` |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX 제작 캔버스에서 기존 데이터 팩터리 리소스를 GitHub 리포지토리로 가져올지 여부를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기** | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 협업 b. 새로 만들기 c. 기존 리소스 사용 |  |

### <a name="github-organizations"></a>GitHub 조직

GitHub 조직에 연결하려면 조직이 Azure Data Factory에 권한을 부여해야 합니다. 조직에 대한 관리자 권한이 있는 사용자는 아래 단계를 수행하여 데이터 팩터리의 연결을 허용해야 합니다.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Azure Data Factory에서 처음으로 GitHub에 연결

Azure Data Factory에서 GitHub에 처음으로 연결하는 경우 다음 단계에 따라 GitHub 조직에 연결합니다.

1. Git 구성 창의 *GitHub 계정* 필드에 조직 이름을 입력합니다. GitHub에 로그인하라는 메시지가 표시됩니다. 
1. 사용자 자격 증명을 사용하여 로그인합니다.
1. *AzureDataFactory* 라는 애플리케이션으로 Azure Data Factory에 권한을 부여하라는 메시지가 표시됩니다. 이 화면에는 ADF에서 조직에 액세스할 수 있는 권한을 부여하는 옵션이 표시됩니다. 권한을 부여하는 옵션이 표시되지 않으면 관리자에게 GitHub를 통해 권한을 수동으로 부여하도록 요청합니다.

이러한 단계를 수행하면 팩터리는 조직 내에서 퍼블릭 및 프라이빗 리포지토리에 연결할 수 있습니다. 연결할 수 없는 경우 브라우저 캐시를 지우고 다시 시도하세요.

#### <a name="already-connected-to-github-using-a-personal-account"></a>개인 계정을 사용하여 GitHub에 이미 연결됨

이미 GitHub에 연결하고 개인 계정에 대한 액세스 권한만 부여한 경우 아래 단계에 따라 조직에 권한을 부여합니다. 

1. GitHub로 이동하여 **설정** 을 엽니다.

    :::image type="content" source="media/author-visually/github-settings.png" alt-text="GitHub 설정 열기":::

1. **애플리케이션** 을 선택합니다. **권한 있는 OAuth 앱** 탭에는 *AzureDataFactory* 가 표시됩니다.

    :::image type="content" source="media/author-visually/github-organization-select-application.png" alt-text="OAuth 앱 선택":::

1. 애플리케이션을 선택하고 조직에 대한 액세스 권한을 애플리케이션에 부여합니다.

    :::image type="content" source="media/author-visually/github-organization-grant.png" alt-text="액세스 권한 부여":::

이러한 단계를 수행하면 팩터리는 조직 내에서 퍼블릭 및 프라이빗 리포지토리에 연결할 수 있습니다. 

### <a name="known-github-limitations"></a>알려진 GitHub 제한 사항

- GitHub 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 GitHub 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

- 버전 2.14.0 이상의 GitHub Enterprise는 Microsoft Edge 브라우저에서 작동하지 않습니다.

- Data Factory 시각적 제작 도구와 GitHub 통합은 일반적으로 사용 가능한 Data Factory 버전에서만 작동합니다.

## <a name="version-control"></a>버전 제어

_원본 제어_ 라고도 하는 버전 제어 시스템을 통해 개발자는 코드에 대해 공동 작업을 수행하고, 코드 베이스의 변경 내용을 추적할 수 있습니다. 소스 제어는 개발자가 여러 명인 프로젝트에 반드시 필요한 도구입니다.

### <a name="creating-feature-branches"></a>기능 분기 만들기

데이터 팩터리와 연결된 각 Azure Repos Git 리포지토리에는 협업 분기가 생성됩니다. (`main`는 기본 협업 분기입니다). 분기 드롭다운에서 **+ 새 분기** 를 클릭하여 기능 분기를 만들 수도 있습니다. 

:::image type="content" source="media/author-visually/new-branch.png" alt-text="새 분기 만들기":::

새 분기 창이 나타나면 기능 분기의 이름을 입력하고 작업을 기반으로 할 분기를 선택합니다.

:::image type="content" source="media/author-visually/create-branch-from-private-branch.png" alt-text="프라이빗 분기를 기반으로 분기를 만드는 방법을 보여주는 스크린샷":::

기능 분기의 변경 내용을 협업 분기에 병합할 준비가 되면, 분기 드롭다운을 클릭하고 **끌어오기 요청 만들기** 를 선택합니다. 이 작업을 통해 끌어오기 요청을 수행하고, 코드 검토를 수행하고, 협업 분기에 변경 내용을 병합할 수 있는 Azure Repos Git로 이동합니다. (기본값은 `main`입니다.) 협업 분기에서 Data Factory 서비스에 게시할 수만 있습니다. 

:::image type="content" source="media/author-visually/create-pull-request.png" alt-text="새 끌어오기 요청 만들기":::

### <a name="configure-publishing-settings"></a>게시 설정 구성

기본적으로 데이터 팩터리는 게시된 팩터리의 Resource Manager 템플릿을 생성하여 `adf_publish`라는 분기에 저장합니다. 사용자 지정 게시 분기를 구성하려면 협업 분기의 루트 폴더에 `publish_config.json` 파일을 추가합니다. 게시하면 ADF는 이 파일을 읽고 `publishBranch` 필드를 찾아서 모든 Resource Manager 템플릿을 지정된 위치에 저장합니다. 분기가 없으면 데이터 팩터리가 자동으로 만듭니다. 이 파일의 모양은 다음과 같습니다.

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory는 게시 분기를 한 번에 하나만 포함할 수 있습니다. 새 게시 분기를 지정할 때 Data Factory는 이전 게시 분기를 삭제하지 않습니다. 이전 게시 분기를 제거하려는 경우에는 수동으로 해당 분기를 삭제합니다.

> [!NOTE]
> Data Factory는 팩터리를 로드할 때만 `publish_config.json` 파일을 읽습니다. 포털에 팩터리가 이미 로드되어 있으면 브라우저를 새로 고쳐서 변경 내용을 적용합니다.

### <a name="publish-code-changes"></a>코드 변경 내용 게시

협업 분기에 변경 내용을 병합한 후에(기본값은 `main`) **게시** 를 클릭하여 기본 분기의 코드 변경 내용을 Data Factory 서비스에 수동으로 게시합니다.

:::image type="content" source="media/author-visually/publish-changes.png" alt-text="Data Factory 서비스에 변경 내용 게시":::

사이드 창이 열리면 게시 분기 및 보류 중인 변경 사항이 올바른지 확인합니다. 변경 내용을 확인한 후 **확인** 을 클릭하여 게시를 확인합니다.

:::image type="content" source="media/author-visually/configure-publish-branch.png" alt-text="올바른 게시 분기 확인":::

> [!IMPORTANT]
> 기본 분기는 Data Factory 서비스에 배포된 내용을 반영하지 않습니다. 따라서 기본 분기를 *반드시* Data Factory 서비스에 수동으로 게시해야 합니다.



## <a name="best-practices-for-git-integration"></a>Git 통합에 대한 모범 사례

### <a name="permissions"></a>사용 권한

일반적으로 Data Factory를 업데이트할 권한이 모든 팀 멤버에게 필요하지는 않습니다. 다음과 같은 권한 설정을 사용하는 것이 좋습니다.

*   모든 팀 멤버는 Data Factory에 대한 읽기 권한이 있어야 합니다.
*   선택된 사용자 세트만 Data Factory에 게시할 수 있어야 합니다. 이렇게 하려면 해당 사용자에게 Data Factory를 포함하는 **리소스 그룹** 에 대한 **Data Factory 기여자** 역할이 있어야 합니다. 권한에 대한 자세한 내용은 [Azure Data Factory의 역할 및 권한](concepts-roles-permissions.md)을 참조하세요.

협업 분기에 대한 직접 체크 인은 허용하지 않는 것이 좋습니다. 이렇게 제한하면 [기능 분기 만들기](source-control.md#creating-feature-branches)의 설명대로 모든 체크 인이 끌어오기 요청 검토 프로세스를 거치기 때문에 버그를 방지하는 데 유용할 수 있습니다

### <a name="using-passwords-from-azure-key-vault"></a>Azure Key Vault의 암호 사용

Azure Key Vault를 사용하여 Data Factory 연결된 서비스에 대한 연결 문자열이나 암호 또는 관리 ID 인증을 저장하는 것이 좋습니다. 보안상의 이유로 데이터 팩터리는 Git에 비밀을 저장하지 않습니다. 암호와 같은 비밀을 포함하는 연결된 서비스에 대한 변경 내용은 Azure Data Factory 서비스에 즉시 게시됩니다.

Key Vault 또는 MSI 인증을 사용해도 연속 통합과 배포가 쉬워집니다. Resource Manager 템플릿 배포 중에 비밀을 제공할 필요가 없기 때문입니다.

## <a name="troubleshooting-git-integration"></a>Git 통합 문제 해결

### <a name="stale-publish-branch"></a>부실한 게시 분기

다음은 부실한 게시 분기를 유발할 수 있는 상황의 몇 가지 예입니다.

- 사용자에게 분기가 여러 개 있습니다. 하나의 기능 분기에서 AKV와 관련이 없는 연결된 서비스를 삭제한 후(AKV 이외의 연결된 서비스는 Git에 있는지 여부에 관계없이 즉시 게시됨) 기능 분기를 협업 분기에 병합하지 않았습니다.
- 사용자가 SDK 또는 PowerShell을 사용하여 데이터 팩터리를 수정했습니다.
- 사용자가 모든 리소스를 새 분기로 이동기고 처음으로 게시하려고 했습니다. 연결된 서비스는 리소스를 가져올 때 수동으로 만들어야 합니다.
- 사용자가 AKV 이외의 연결된 서비스 또는 Integration Runtime JSON을 수동으로 업로드합니다. 데이터 세트, 연결된 서비스 또는 파이프라인과 같은 다른 리소스에서 해당 리소스를 참조합니다. UX를 통해 만든 AKV 이외의 연결된 서비스는 자격 증명을 암호화해야 하기 때문에 즉시 게시됩니다. 연결된 서비스를 참조하는 데이터 세트를 업로드하고 게시하려고 하면 UX에서 허용됩니다. Git 환경에 존재하기 때문입니다. 하지만 데이터 팩터리 서비스에 존재하지 않기 때문에 게시할 때 거부됩니다.

게시 분기가 기본 분기와 동기화되지 않고 최근 게시에도 불구하고 오래된 리소스가 포함된 경우 아래 솔루션 중 하나를 사용할 수 있습니다.

#### <a name="option-1-use-overwrite-live-mode-functionality"></a>옵션 1: **라이브 모드 덮어쓰기** 기능 사용

협업 분기의 코드를 라이브 모드로 게시하거나 덮어씁습니다. 리포지토리의 코드를 진리 소스로 간주합니다. 

<u>*코드 흐름:*</u>***협업 분기 -> 라이브 모드***

:::image type="content" source="media/author-visually/force-publish-changes-from-collaboration-branch.png" alt-text="협업 분기에서 코드 강제 게시":::

#### <a name="option-2-disconnect-and-reconnect-git-repository"></a>옵션 2: Git 리포지토리 연결 끊기 및 다시 연결

라이브 모드에서 협업 분기로 코드를 가져옵니다. 라이브 모드의 코드를 진리의 소스로 간주합니다. 

<u>*코드 흐름:*</u>***라이브 모드 -> 협업 분기***  

1. 현재 Git 리포지토리 제거
1. 동일한 설정으로 Git를 다시 구성하지만 **리포지토리로 기존 Data Factory 리소스 가져오기** 가 선택되어 있는지 확인하고 **새 분기** 를 선택합니다.
1. 변경 내용을 협업 분기에 병합하는 끌어오기 요청을 만듭니다. 

필요에 따라 적절한 방법을 선택합니다. 

### <a name="all-resources-showing-as-new-on-publish"></a>게시할 때 새 리소스로 표시되는 모든 리소스

게시하는 동안 모든 리소스는 이전에 게시된 경우에도 새 리소스로 표시될 수 있습니다. 이 문제는 팩터리 ARM 템플릿을 다시 배포하거나 PowerShell 또는 REST API 통해 팩터리 *리포지토리* 리포지토리 속성을 업데이트하여 Factory의 *repoConfiguration* 속성에서 *lastCommitId* 속성이 다시 설정되는 경우에 발생할 수 있습니다. 리소스를 계속 게시하면 문제가 해결되지만 다시 발생하지 않도록 하려면 *팩터리 repoConfiguration* 속성을 업데이트하지 마십시오. 



## <a name="switch-to-a-different-git-repository"></a>다른 Git 리포지토리로 전환

다른 Git 리포지토리로 전환하려면 **소스 제어** 의 관리 허브에서 Git 구성 페이지로 이동합니다. **연결 끊기** 를 선택합니다. 

:::image type="content" source="media/author-visually/remove-repository.png" alt-text="GIT 아이콘":::

데이터 팩터리 이름을 입력하고 **확인** 을 클릭하여 데이터 팩터리와 연결된 Git 리포지토리를 제거합니다.

:::image type="content" source="media/author-visually/remove-repository-2.png" alt-text="현재 Git 리포지토리와 연결 제거":::

현재 리포지토리와 연결을 제거한 후에 다른 리포지토리를 사용하도록 Git 설정을 구성한 다음, 기존 Data Factory 리소스를 새 리포지토리로 가져올 수 있습니다.

> [!IMPORTANT]
> 데이터 팩터리에서 Git 구성을 제거해도 리포지토리에서 아무것도 삭제되지 않습니다. 팩터리에 게시된 모든 리소스가 포함됩니다. 서비스에 대해 직접 팩터리를 계속 편집할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
* 연속 통합 및 배포를 구현하려면 [Azure Data Factory의 CI/CD(연속 통합 및 지속적인 업데이트)](continuous-integration-delivery.md)를 참조하세요.
