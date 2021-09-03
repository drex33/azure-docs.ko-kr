---
title: 로컬 Git 리포지토리에서 배포
description: Azure App Service에 로컬 Git 배포를 사용하는 방법에 대해 알아봅니다. 로컬 머신에서 코드를 배포하는 가장 간단한 방법 중 하나입니다.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 90acf43471e0213b801e4d147fe4e8a8abbd0394
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528131"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service에 로컬 Git 배포

이 방법 가이드에서는 로컬 컴퓨터의 Git 리포지토리에서 [Azure App Service](overview.md)에 앱을 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git를 설치](https://www.git-scm.com/downloads)합니다.

- 배포할 코드가 있는 로컬 Git 리포지토리가 있어야 합니다. 샘플 리포지토리를 다운로드하려면 로컬 터미널 창에서 다음 명령을 실행합니다.
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>배포 사용자 구성

[Azure App Service의 배포 자격 증명 구성](deploy-configure-credentials.md)을 참조하세요. 사용자 범위 자격 증명 또는 애플리케이션 범위 자격 증명을 사용할 수 있습니다.

## <a name="create-a-git-enabled-app"></a>Git 사용 앱 만들기

App Service 앱이 이미 있고 해당 앱에 맞게 로컬 Git 배포를 구성하려면, 대신 [기존 앱 구성](#configure-an-existing-app)을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

`--deployment-local-git` 옵션과 함께 [`az webapp create`](/cli/azure/webapp#az_webapp_create)를 실행합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

출력에는 `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`와 같은 URL이 포함되어 있습니다. 다음 단계에서 이 URL을 사용하여 앱을 배포합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Git 리포지토리의 루트에서 [New-AzWebApp](/powershell/module/az.websites/new-azwebapp)을 실행합니다. 예를 들면 다음과 같습니다.

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Git 리포지토리인 디렉터리에서 이 cmdlet을 실행하면 `azure`라는 App Service 앱의 Git 원격을 자동으로 만듭니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

포털에서 먼저 앱을 만든 다음, 해당 앱에 맞게 배포를 구성해야 합니다. [기존 앱 구성](#configure-an-existing-app)을 참조하세요.

-----

## <a name="configure-an-existing-app"></a>기존 앱 구성

앱을 아직 만들지 않은 경우, 대신 [Git 사용 앱 만들기](#create-a-git-enabled-app)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) 를 실행합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

출력에는 `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`와 같은 URL이 포함되어 있습니다. 다음 단계에서 이 URL을 사용하여 앱을 배포합니다.

> [!TIP]
> 이 URL에는 사용자 범위 배포 사용자 이름이 포함됩니다. 원하는 경우 대신 [애플리케이션 범위 자격 증명을 사용](deploy-configure-credentials.md#appscope)할 수 있습니다. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Set-AzResource](/powershell/module/az.resources/set-azresource) cmdlet을 실행하여 앱의 `scmType`을 설정합니다.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 앱의 관리 페이지로 이동합니다.

1. 왼쪽 메뉴에서 **배포 센터** > **설정** 을 선택합니다. **원본** 에서 **로컬 Git** 를 선택한 다음, **저장** 을 클릭합니다.

    ![Azure Portal에서 App Service의 로컬 Git 배포를 사용하도록 설정하는 방법을 보여 줍니다.](./media/deploy-local-git/enable-portal.png)

1. 로컬 Git 섹션에서 나중에 사용하도록 **Git Clone Uri** 를 복사합니다. 이 Uri는 자격 증명을 포함하지 않습니다.

-----

## <a name="deploy-the-web-app"></a>웹앱 배포

1. 로컬 터미널 창에서 디렉터리를 Git 리포지토리의 루트로 변경하고 앱에서 가져온 URL을 사용하여 Git 원격을 추가합니다. 선택한 방법에서 URL을 제공하지 않으면 `<app-name>`의 앱 이름과 함께 `https://<app-name>.scm.azurewebsites.net/<app-name>.git`을 사용합니다.
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > [New-AzWebApp을 사용하여 PowerShell에서 Git 사용 앱을 만든](#create-a-git-enabled-app) 경우 원격이 이미 생성되어 있습니다.
   
1. `git push azure master`을 사용하여 Azure 원격으로 푸시합니다([배포 분기 변경](#change-deployment-branch) 참조). 
   
1. **Git 자격 증명 관리자** 창에서 Azure 로그인 자격 증명이 아닌 [사용자 범위 또는 애플리케이션 범위 자격 증명](#configure-a-deployment-user)을 입력합니다.

    Git 원격 URL에 이미 사용자 이름과 암호가 포함되어 있으면 메시지가 표시되지 않습니다. 
   
1. 출력을 검토합니다. ASP.NET용 MSBuild, Node.js용 `npm install` 및 Python용 `pip install`과 같은 런타임 관련 자동화가 표시될 수 있습니다. 
   
1. Azure Portal의 앱으로 이동하여 콘텐츠가 배포되었는지 확인합니다.

## <a name="change-deployment-branch"></a>배포 분기 변경

App Service 리포지토리에 커밋을 푸시하면 App Service는 기본적으로 `master` 분기에 파일을 배포합니다. 많은 Git 리포지토리가 `master`에서 `main`으로 이동하고 있으므로 다음 두 가지 방법 중 하나로 App Service 리포지토리의 올바른 분기로 푸시해야 합니다.

- 다음과 같은 명령을 사용하여 명시적으로 `master`에 배포합니다.

    ```bash
    git push azure main:master
    ```

- `DEPLOYMENT_BRANCH` 앱 설정을 지정하여 배포 분기를 변경한 다음 사용자 지정 분기에 커밋을 푸시합니다. Azure CLI를 사용하여 수행하려면 다음을 수행합니다.

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <group-name> --settings DEPLOYMENT_BRANCH='main'
    git push azure main
    ```

## <a name="troubleshoot-deployment"></a>배포 문제 해결

Git을 사용하여 Azure에서 App Service 앱에 게시할 때 다음과 같은 일반적인 오류 메시지가 표시될 수 있습니다.

|메시지|원인|해결 방법
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|앱이 실행 중이지 않습니다.|Azure Portal에서 앱을 시작합니다. 웹앱이 중지되면 Git 배포를 사용할 수 없습니다.|
|`Couldn't resolve host 'hostname'`|‘azure’ 원격의 주소 정보가 잘못되었습니다.|`git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|`git push` 중에 분기를 지정하지 않았거나 `.gitconfig`에 `push.default` 값을 설정하지 않았습니다.|`git push`를 다시 실행하고 기본 분기를 지정합니다(`git push azure main`).|
|`Error - Changes committed to remote repository but deployment to website failed.`|‘azure’의 앱 배포 분기와 일치하지 않는 로컬 분기를 푸시했습니다.|현재 분기가 `master`인지 확인합니다. 기본 분기를 변경하려면 `DEPLOYMENT_BRANCH` 애플리케이션 설정을 사용합니다([배포 분기 변경](#change-deployment-branch) 참조). |
|`src refspec [branchname] does not match any.`|‘azure’ 원격에서 기본 이외의 분기로 푸시하려고 했습니다.|`git push`를 다시 실행하고 기본 분기를 지정합니다(`git push azure main`).|
|`RPC failed; result=22, HTTP code = 5xx.`|이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.|`postBuffer`를 확장하도록 로컬 머신에서 git 구성을 변경합니다. 예: `git config --global http.postBuffer 524288000`|
|`Error - Changes committed to remote repository but your web app not updated.`|추가 필수 모듈을 지정하는 _package.json_ 파일이 있는 Node.js 앱을 배포했습니다.|실패에 관한 추가 컨텍스트는 이 오류 전의 `npm ERR!` 오류 메시지를 검토하세요. 다음은 이 오류 및 해당 `npm ERR!` 메시지의 알려진 원인입니다.<br /><br />**잘못된 형식의 package.json 파일**: `npm ERR! Couldn't read dependencies.`<br /><br />**Windows용 이진 배포가 없는 네이티브 모듈**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />또는 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>추가 리소스

- [App Service 빌드 서버(Project Kudu 설명서)](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service 연속 배포](deploy-continuous-deployment.md)
- [샘플: 로컬 Git 리포지토리에서 웹앱 만들기 및 코드 배포(Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [샘플: 로컬 Git 리포지토리에서 웹앱 만들기 및 코드 배포(PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
