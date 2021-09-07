---
title: 지속적 배포 구성
description: GitHub, Bitbucket, Azure Repos 또는 기타 리포지토리에서 Azure App Service에 CI/CD를 사용하도록 설정하는 방법에 대해 알아봅니다. 요구 사항에 맞는 빌드 파이프라인을 선택합니다.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bff93e0dcfb49871cefa68d16d7a7db85c0f69ce
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122642999"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service에 지속적인 배포

[Azure App Service](overview.md)는 최신 업데이트를 끌어옴으로써 [GitHub](https://help.github.com/articles/create-a-repo), [Bitbucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) 및 [Azure Repos](/azure/devops/repos/git/creatingrepo) 리포지토리에서 지속적인 배포를 가능하게 합니다.

> [!NOTE]
> 배포 기능의 이전 버전인 Azure Portal의 **개발 센터(클래식)** 페이지는 2021년 3월부터 더 이상 사용되지 않습니다. 이 변경 내용은 앱의 기존 배포 설정에 영향을 주지 않으며, 포털에서 **배포 센터** 페이지에서 계속해서 앱 배포를 관리할 수 있습니다.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-the-deployment-source"></a>배포 원본 구성

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱의 관리 페이지로 이동합니다.

1. 왼쪽 창에서 **배포 센터** 를 선택합니다. 그런 다음, **설정** 을 선택합니다. 

1. **원본** 상자에서 CI/CD 옵션 중 하나를 선택합니다.

    ![배포 원본 선택 방법을 보여 주는 스크린샷](media/app-service-continuous-deployment/choose-source.png)

계속하려면 빌드 공급자에 해당하는 탭을 선택합니다.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works)는 기본 빌드 공급자입니다. 공급자를 변경하려면 **공급자 변경** > **App Service 빌드 서비스**(Kudu) > **확인** 을 선택합니다.

    > [!NOTE]
    > App Service 앱에 대한 빌드 공급자로 Azure Pipelines를 사용하려면 Azure Pipelines에서 직접 CI/CD를 구성합니다. App Service에서 구성하지 마세요. **Azure Pipelines** 옵션에서 바로 해당 작업을 수행할 수 있습니다.

1. 처음으로 GitHub에서 배포하는 경우 **권한 부여** 를 선택하고 권한 부여 프롬프트를 따릅니다. 다른 사용자의 리포지토리에서 배포하려면 **계정 변경** 을 선택합니다.

1. Azure 계정에 GitHub에 대한 권한을 부여한 후에는 **조직**, **리포지토리** 및 **분기** 를 선택하여 관련 CI/CD를 구성합니다. 조직 또는 리포지토리를 찾을 수 없는 경우 GitHub에 대한 추가 권한을 사용하도록 설정해야 할 수 있습니다. 자세한 내용은 [조직의 리포지토리에 대한 액세스 관리](https://docs.github.com/organizations/managing-access-to-your-organizations-repositories)를 참조하세요.

1. 빌드 공급자로 GitHub Actions를 선택한 경우 **런타임 스택** 및 **버전** 드롭다운 목록을 사용하여 원하는 워크플로 파일을 선택할 수 있습니다. Azure는 선택한 GitHub 리포지토리에 이 워크플로 파일을 커밋하여 빌드 및 배포 작업을 처리합니다. 변경 내용을 저장하기 전에 파일을 보려면 **파일 미리 보기** 를 선택합니다.

    > [!NOTE]
    > App Service는 앱의 [언어 스택 설정](configure-common.md#configure-language-stack-settings)을 검색하고 가장 적합한 워크플로 템플릿을 선택합니다. 다른 템플릿을 선택하는 경우 제대로 실행되지 않는 앱을 배포할 수도 있습니다. 자세한 내용은 [GitHub Actions 빌드 공급자의 작동 방식](#how-the-github-actions-build-provider-works)을 참조하세요.

1. **저장** 을 선택합니다.
   
    선택한 리포지토리 및 분기의 새 커밋이 이제 App Service 앱에 지속적으로 배포됩니다. **로그** 탭에서 커밋 및 배포를 추적할 수 있습니다.

# <a name="bitbucket"></a>[Bitbucket](#tab/bitbucket)

Bitbucket 통합 시에는 빌드 자동화를 위해 App Service 빌드 서비스(Kudu)를 사용합니다.

4. Bitbucket에서 처음으로 배포하는 경우 **권한 부여** 를 선택하고 권한 부여 프롬프트를 따릅니다. 다른 사용자의 리포지토리에서 배포하려면 **계정 변경** 을 선택합니다.

1. BitBucket의 경우 지속적으로 배포하려는 BitBucket **팀**, **리포지토리** 및 **분기** 를 선택합니다.

1. **저장** 을 선택합니다.
   
    선택한 리포지토리 및 분기의 새 커밋이 이제 App Service 앱에 지속적으로 배포됩니다. **로그** 탭에서 커밋 및 배포를 추적할 수 있습니다.
   
# <a name="local-git"></a>[로컬 Git](#tab/local)

[Azure App Service에 로컬 Git 배포](deploy-local-git.md)를 참조합니다.

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)
   
   > [!NOTE]
   > Azure Repos를 배포 원본으로 사용하는 경우 Windows 앱에서 이를 지원합니다.
   >

4. App Service 빌드 서비스(Kudu)는 기본 빌드 공급자입니다.

    > [!NOTE]
    > App Service 앱에 대한 빌드 공급자로 Azure Pipelines를 사용하려면 Azure Pipelines에서 직접 CI/CD를 구성합니다. App Service에서 구성하지 마세요. **Azure Pipelines** 옵션에서 바로 해당 작업을 수행할 수 있습니다.

1. 지속적으로 배포하려는 **Azure DevOps 조직**, **프로젝트**, **리포지토리** 및 **분기** 를 선택합니다. 

    Azure DevOps 조직이 나열되지 않는 경우 해당 조직이 아직 Azure 구독에 연결되어 있지 않은 것입니다. 자세한 내용은 [Azure 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure)를 참조하세요.

-----

## <a name="disable-continuous-deployment"></a>지속적 배포 사용 안 함

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱의 관리 페이지로 이동합니다.

1. 왼쪽 창에서 **배포 센터** 를 선택합니다. 그런 후 **설정** > **연결 끊기** 를 선택합니다.

    ![Azure Portal에서 App Service 앱과 클라우드 폴더의 동기화를 해제하는 방법을 보여 주는 스크린샷](media/app-service-continuous-deployment/disable.png)

1. 기본적으로 GitHub Actions 워크플로 파일은 리포지토리에 유지되지만 앱에 대한 배포는 계속 트리거됩니다. 리포지토리에서 파일을 삭제하려면 **워크플로 파일 삭제** 를 선택합니다.

1. **확인** 을 선택합니다.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>GitHub Actions 빌드 공급자의 작동 방식

GitHub Actions 빌드 공급자는 [GitHub의 CI/CD](#configure-the-deployment-source) 관련 옵션입니다. CI/CD를 설정하기 위해 다음 작업을 완료합니다.

- App Service에 대한 빌드 및 배포 작업을 처리하기 위해 GitHub Actions 워크플로 파일을 GitHub 리포지토리에 저장합니다.
- 앱의 게시 프로필을 GitHub 비밀로 추가합니다. 워크플로 파일은 이 비밀을 사용하여 App Service에서 인증합니다.
- [워크플로 실행 로그](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)에서 정보를 캡처하여 앱 배포 센터의 **로그** 탭에 표시합니다.

다음과 같은 방법으로 GitHub Actions 빌드 공급자를 사용자 지정할 수 있습니다.

- 워크플로 파일이 GitHub 리포지토리에서 생성된 후 이를 사용자 지정합니다. 자세한 내용은 [GitHub Actions의 워크플로 구문](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)을 참조하세요. [azure/webapps-deploy](https://github.com/Azure/webapps-deploy) 작업을 사용하여 워크플로가 App Service에 배포되도록 합니다.
- 선택한 분기가 보호되는 경우에도 구성을 저장하지 않고 워크플로 파일을 미리 본 다음, 리포지토리에 수동으로 추가할 수 있습니다. 이 방법은 Azure Portal과의 로그 통합을 제공하지 않습니다.
- 게시 프로필을 사용하는 대신 Azure Active Directory에서 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 사용하여 배포합니다.

#### <a name="authenticate-by-using-a-service-principal"></a>서비스 주체를 사용하여 인증

이 선택적 구성은 생성된 워크플로 파일의 게시 프로필로 기본 인증을 대체합니다.

1. [Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 서비스 주체를 생성합니다. 다음 예제에서는 \<subscription-id> , \<group-name> 및 \<app-name> 값을 사용자 고유의 값으로 바꿉니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > 보안을 위해 서비스 주체에 필요한 최소한의 액세스 권한을 부여합니다. 이전 예제의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한되어 있습니다.
    
1. 다음 단계에 대한 전체 JSON 출력을 최상위 수준(`{}`)을 포함하여 저장합니다.

1. [GitHub](https://github.com/)의 리포지토리에서 **설정** > **비밀** > **새 비밀 추가** 를 선택합니다.

1. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 지정합니다(예: `AZURE_CREDENTIALS`).

1. 배포 센터에 의해 생성된 워크플로 파일에서 다음 예제(Node.js 워크플로 파일에서 수정됨)와 같이 `azure/webapps-deploy` 단계를 수정합니다.

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added.
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile.
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure.
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>다른 리포지토리에서 배포

Windows 앱의 경우 [GitLab](https://gitlab.com/)과 같이 Portal이 직접 지원하지 않는 클라우드 Git 또는 Mercurial 리포지토리에서 지속적인 배포를 수동으로 구성할 수 있습니다. **원본** 드롭다운 목록에서 **외부 Git** 을 선택하여 이 작업을 수행합니다. 자세한 내용은 [수동 단계를 사용하여 지속적인 배포 설정](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)을 참조하세요.

## <a name="more-resources"></a>추가 리소스

* [Azure Pipelines에서 Azure App Service로 배포](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [지속적인 배포와 관련된 일반적인 문제 조사](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell 사용](/powershell/azure/)
* [프로젝트 Kudu](https://github.com/projectkudu/kudu/wiki)
