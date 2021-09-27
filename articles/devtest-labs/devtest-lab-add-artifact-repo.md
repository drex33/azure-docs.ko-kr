---
title: 랩에 Git 리포지토리 추가
description: Azure DevTest Labs에서 사용자 지정 아티팩트 원본용 GitHub 또는 Azure DevOps Services Git 리포지토리를 추가하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 20fa9f872b4e1ef4650dcdb114f92616a2f1bbe0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668719"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>사용자 지정 아티팩트 및 Resource Manager 템플릿을 저장할 Git 리포지토리 추가

랩에서 VM에 대한 [사용자 지정 아티팩트를 만들](devtest-lab-artifact-author.md)거나 [Azure Resource Manager 템플릿을 사용하여 사용자 지정 테스트 환경을 만들](devtest-lab-create-environment-from-arm.md) 수 있습니다. 팀에서 만드는 아티팩트 또는 Resource Manager 템플릿에 대한 프라이빗 Git 리포지토리를 추가해야 합니다. 리포지토리는 [GitHub](https://github.com) 또는 [Azure DevOps Services](https://visualstudio.com)에서 호스팅될 수 있습니다.

Microsoft는 그대로 배포하거나 랩에 맞게 사용자 지정할 수 있는 [아티팩트의 GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)를 제공합니다. 아티팩트를 사용자 지정하거나 만들 때 공용 리포지토리에 아티팩트를 저장할 수 없습니다. 사용자 지정 아티팩트 및 사용자가 만드는 아티팩트에 대한 사용자 고유의 프라이빗 리포지토리를 만들어야 합니다. 

VM을 만들 경우 Resource Manager 템플릿을 저장하고 원할 경우 사용자 지정한 다음 나중에 추가 VM을 만들 수 있습니다. 사용자 지정 Resource Manager 템플릿을 저장할 프라이빗 리포지토리를 만들어야 합니다.  

* GitHub 리포지토리를 만드는 방법을 알아보려면 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)를 참조하세요.
* Git 리포지토리가 있는 Azure DevOps Services 프로젝트를 만드는 방법을 알아보려면 [Azure DevOps Services에 연결](https://azure.microsoft.com/services/devops/)을 참조하세요.

다음 그림은 아티팩트가 잇는 리포지토리가 GitHub에서 어떻게 표시되는지 보여 주는 예입니다.  

![GitHub 아티팩트 리포지토리 샘플](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>리포지토리 정보 및 자격 증명 가져오기
랩에 리포지토리를 추가하려면 먼저 리포지토리에서 특정 정보를 가져옵니다. 다음 섹션에서는 GitHub 또는 Azure DevOps Services에서 호스트되는 리포지토리에 대한 필요한 정보를 가져오는 방법을 설명합니다.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub 리포지토리 복제 URL 및 개인 액세스 토큰 가져오기

1. 아티팩트 또는 Resource Manager 템플릿 정의를 포함하는 GitHub 리포지토리의 홈페이지로 이동합니다.
2. **복제 또는 다운로드** 를 선택합니다.
3. URL을 클립보드에 복사하려면 **HTTPS 복제 url** 단추를 선택합니다. 나중에 사용할 수 있도록 URL을 저장합니다.
4. GitHub의 오른쪽 위에서 프로필 이미지를 선택한 다음 **설정** 을 선택합니다.
5. 왼쪽의 **개인 설정** 메뉴에서 **개인 액세스 토큰** 을 선택합니다.
6. **Generate new token**(새 토큰 생성)을 탭합니다.
7. **새 개인 액세스 토큰** 페이지의 **토큰 설명** 아래에 설명을 입력합니다. **범위 선택** 아래의 기본 항목에 동의한 다음 **토큰 생성** 을 선택합니다.
8. 생성된 토큰을 저장합니다. 토큰은 나중에 사용합니다.
9. GitHub를 닫습니다.   
10. [리포지토리에 랩 연결](#connect-your-lab-to-the-repository) 섹션을 계속 진행합니다.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos 복제 URL 및 개인 액세스 토큰 가져오기

1. 팀 컬렉션의 홈 페이지(예: `https://contoso-web-team.visualstudio.com`)로 이동한 다음, 프로젝트를 선택합니다.
2. 프로젝트 홈 페이지에서 선택 **코드** 합니다.
3. 복제 URL을 보려면 프로젝트 **코드** 페이지에서 **복제** 를 선택합니다.
4. URL을 저장합니다. URL은 나중에 사용합니다.
5. 개인 액세스 토큰을 만들려면 사용자 계정 드롭다운 메뉴에서 **내 프로필** 을 선택합니다.
6. 프로필 정보 페이지에서 **보안** 을 탭합니다.
7. **보안** 탭에서 **추가** 를 선택합니다.
8. **개인 액세스 토큰 만들기** 페이지에서:
   1. 토큰에 대한 **설명** 을 입력합니다.
   2. **다음 기간 내에 만료** 목록에서 **180일** 을 선택합니다.
   3. **계정** 목록에서 **액세스 가능한 모든 계정** 을 선택합니다.
   4. **읽기 전용** 옵션을 선택합니다.
   5. **토큰 만들기** 를 선택합니다.
9. 새 토큰이 **개인 액세스 토큰** 목록에 표시됩니다. **토큰 복사** 를 선택하고 나중에 사용할 수 있게 토큰 값을 저장합니다.
10. [리포지토리에 랩 연결](#connect-your-lab-to-the-repository) 섹션을 계속 진행합니다.

## <a name="connect-your-lab-to-the-repository"></a>랩을 리포지토리에 연결
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
2. **추가 서비스** 를 선택한 후 서비스 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 랩을 선택합니다. 
4. **구성 및 정책** > **리포지토리** >  **+ 추가** 를 선택합니다.

    ![리포지토리 추가 버튼](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 두 번째 **리포지토리** 페이지에서 다음 정보를 지정합니다.
   1. **이름**. 리포지토리의 이름을 입력합니다.
   2. **Git Clone Url**. 이전에 GitHub 또는 Azure DevOps Services에서 복사한 Git HTTPS 복제 URL을 입력합니다.
   3. **분기**. 정의를 가져오려면 분기를 입력합니다.
   4. **개인 액세스 토큰**. 이전에 GitHub 또는 Azure DevOps Services에서 가져온 개인 액세스 토큰을 입력합니다.
   5. **폴더 경로**. 아티팩트 또는 Resource Manager 템플릿 정의가 포함된 복제 URL에 상대적인 폴더 경로를 하나 이상 입력합니다. 하위 디렉터리를 지정하는 경우 폴더 경로에 슬래시를 포함해야 합니다.

      ![리포지토리 영역](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **저장** 을 선택합니다.

### <a name="related-blog-posts"></a>관련 블로그 게시물
* [DevTest Labs에서 실패한 아티팩트 문제 해결](devtest-lab-troubleshoot-artifact-failure.md)
* [DevTest Labs에서 Resource Manager 템플릿을 사용하여 기존 Active Directory 도메인에 VM 가입](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
프라이빗 Git 리포지토리를 만든 후 필요에 따라 다음 중 하나 또는 둘 다를 수행할 수 있습니다.
* [사용자 지정 아티팩트](devtest-lab-artifact-author.md)를 저장합니다. 나중에 새 VM을 만드는 데 사용할 수 있습니다.
* [Resource Manager 템플릿을 사용하여 다중 VM 환경 및 PaaS 리소스를 만듭니다](devtest-lab-create-environment-from-arm.md). 그런 다음, 프라이빗 리포지토리에 템플릿을 저장할 수 있습니다.

VM을 만드는 경우 Git 리포지토리에 아티팩트 또는 템플릿이 추가되었는지 확인할 수 있습니다. 아티팩트 또는 템플릿 목록에서 즉시 사용할 수 있습니다. 사용자의 프라이빗 리포지토리 이름은 원본을 지정하는 열에 표시됩니다. 
