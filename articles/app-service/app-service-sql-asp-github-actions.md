---
title: '자습서: GitHub Actions를 사용하여 App Service에 배포 및 데이터베이스에 연결'
description: GitHub Actions를 사용하여 Azure에 데이터베이스 지원 ASP.NET Core 앱 배포
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 01d4293b33eb0756dec8bc9ae870a972959ced89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701967"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-and-connect-to-a-database"></a>자습서: GitHub Actions를 사용하여 App Service에 배포 및 데이터베이스에 연결

[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) 백 엔드를 사용하여 ASP.NET Core 애플리케이션을 배포하는 GitHub Actions 워크플로를 설정하는 방법을 알아봅니다. 완료되면 ASP.NET 앱이 Azure에서 실행되고 SQL Database에 연결됩니다. 먼저 [ARM 템플릿](/azure/azure-resource-manager/templates/overview)을 사용하여 리소스를 만듭니다.

이 자습서에서는 컨테이너를 사용하지 않습니다. 컨테이너화된 ASP.NET Core 애플리케이션에 배포하려면 [GitHub Actions를 사용하여 컨테이너에 대한 App Service에 배포 및 데이터베이스에 연결](app-service-sql-github-actions.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - GitHub Actions 워크플로를 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)을 통해 Azure에 리소스 추가
> - GitHub Actions 워크플로를 사용하여 ASP.NET Core 애플리케이션 빌드

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.
  - Resource Manager 템플릿 및 워크플로 파일을 저장하는 GitHub 리포지토리. 리포지토리를 만들려면 [새 리포지토리 만들기](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)를 참조하세요.

## <a name="download-the-sample"></a>샘플 다운로드

Azure 샘플 리포지토리에서 [샘플 프로젝트를 포크](https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions)합니다.

```
https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions
```

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

https://shell.azure.com 에서 Azure Cloud Shell을 엽니다. Azure CLI를 로컬에 설치한 경우 대신 사용할 수 있습니다. (Cloud Shell에 대한 자세한 내용은 [Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.)

```azurecli-interactive
az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

리소스 배포 스크립트가 작동하려면 서비스 사용자로 인증해야 합니다. [Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
```

예에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 서비스 사용자 이름으로 바꿉니다. 출력은 App Service 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다. 도움말을 보려면 [배포 자격 증명 구성](https://github.com/Azure/login#configure-deployment-credentials)으로 이동하세요.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret-for-authentication"></a>인증을 위한 GitHub 비밀 구성

[GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용하려면 Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 `AZURE_CREDENTIALS`의 이름을 지정합니다.

## <a name="add-github-secrets-for-your-build"></a>빌드에 GitHub 비밀 추가

1. `SQLADMIN_PASS` 및 `SQLADMIN_LOGIN`에 대한 GitHub 리포지토리에 [두 개의 새 비밀](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)을 만듭니다. 복잡한 암호를 선택해야 합니다. 그렇지 않으면 SQL 데이터베이스 서버에 대한 만들기 단계가 실패합니다. 이 암호에 다시 액세스할 수 없으므로 별도로 저장해야 합니다.

2. Azure 구독 ID에 대한 `AZURE_SUBSCRIPTION_ID` 비밀을 만듭니다. 구독 ID를 모르는 경우 Azure Shell에서 이 명령을 사용하여 찾을 수 있습니다. `SubscriptionId` 열의 값을 복사합니다.
    ```azurecli
    az account list -o table
    ```
 
## <a name="create-azure-resources"></a>Azure 리소스 만들기

Azure 리소스 만들기 워크플로는 [ARM 템플릿](/azure/azure-resource-manager/templates/overview)을 실행하여 Azure에 리소스를 배포합니다. 워크플로는 다음을 수행합니다.

- [체크 아웃 작업](https://github.com/marketplace/actions/checkout)을 사용하여 소스 코드를 체크 아웃합니다.
- [Azure 로그인 작업](https://github.com/marketplace/actions/azure-login)을 사용하여 Azure에 로그인하고 환경 및 Azure 리소스 정보를 수집합니다.
- [Azure Resource Manager 배포 작업](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)을 사용하여 리소스를 배포합니다.

Azure 리소스 만들기 워크플로를 실행하려면 다음을 수행합니다.

1. 리포지토리 내 `.github/workflows`에서 `infraworkflow.yml` 파일을 엽니다.

1. `AZURE_RESOURCE_GROUP` 값을 리소스 그룹 이름으로 업데이트합니다.

1. ARM 배포 작업에서 `region`에 대한 입력을 해당 지역으로 설정합니다. 
    1. `templates/azuredeploy.resourcegroup.parameters.json`을 열고 해당 지역으로 `rgLocation` 속성을 업데이트합니다.
 
1. **작업** 으로 이동하여 **워크플로 실행** 을 선택합니다.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="GitHub Actions 워크플로를 실행하여 리소스를 추가합니다.":::

1. **작업** 페이지에서 녹색 확인 표시를 확인하여 작업이 성공적으로 실행되었는지 확인합니다.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="리소스 만들기를 성공적으로 실행했습니다.":::

1. 리소스를 만든 후 **Actions** 로 이동하여 Azure 리소스 만들기를 선택하고 워크플로를 사용하지 않도록 설정합니다. 
 
    :::image type="content" source="media/github-actions-workflows/disable-workflow-github-actions.png" alt-text="Azure 리소스 만들기 워크플로를 비활성화합니다.":::

## <a name="create-a-publish-profile-secret"></a>게시 프로필 비밀 만들기

1. Azure Portal에서 `Create Azure Resources` 워크플로를 사용하여 만든 새 스테이징 App Service(슬롯)를 엽니다.

1. **게시 프로필 가져오기** 를 선택합니다.

1. 텍스트 편집기에서 게시 프로필 파일을 열고 해당 내용을 복사합니다. 

1. `AZURE_WEBAPP_PUBLISH_PROFILE`에 대한 새 GitHub 비밀을 만듭니다. 

## <a name="build-and-deploy-your-app"></a>앱 빌드 및 배포

빌드 및 배포 워크플로를 실행하려면 다음을 수행합니다.

1. 리포지토리 내 `.github/workflows`에서 `workflow.yaml` 파일을 엽니다.

1. `AZURE_RESOURCE_GROUP`, `AZURE_WEBAPP_NAME`, `SQLSERVER_NAME` 및 `DATABASE_NAME`에 대한 환경 변수가 `infraworkflow.yml`의 환경 변수와 일치하는지 확인합니다.

1. 프로덕션 슬롯으로 교환 출력의 URL을 방문하여 앱이 배포되었는지 확인합니다. 샘플 앱인 내 TodoList 앱이 표시되어야 합니다. 
 
## <a name="clean-up-resources"></a>리소스 정리

샘플 프로젝트가 더 이상 필요하지 않은 경우 Azure Portal에서 리소스 그룹을 삭제하고 GitHub에서 리포지토리를 삭제합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 및 GitHub 통합에 대해 알아보기](/azure/developer/github/)
