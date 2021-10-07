---
title: '자습서: GitHub Actions를 사용하여 App Service 사용자 지정 컨테이너에 배포 및 데이터베이스에 연결'
description: GitHub Actions를 사용하여 ASP.NET Core 앱을 Azure 및 Azure SQL Database에 배포하는 방법 알아보기
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 24001d71721f2e3ee243248705ed8d4b64571c83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675405"
---
# <a name="tutorial-use-github-actions-to-deploy-to-an-app-service-custom-container-and-connect-to-a-database"></a>자습서: GitHub Actions를 사용하여 App Service 사용자 지정 컨테이너에 배포 및 데이터베이스에 연결

이 자습서에서는 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) 백 엔드를 사용하여 컨테이너화된 ASP.NET Core 애플리케이션을 배포하는 GitHub Actions 워크플로를 설정하는 과정을 안내합니다. 완료되면 ASP.NET 앱이 Azure에서 실행되고 SQL Database에 연결됩니다. 먼저 [ARM 템플릿](../azure-resource-manager/templates/overview.md) GitHub Actions 워크플로를 사용하여 Azure 리소스를 만듭니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - GitHub Actions 워크플로를 사용하여 ARM 템플릿(Azure Resource Manager 템플릿)을 통해 Azure에 리소스 추가
> - GitHub Actions 워크플로를 사용하여 최신 웹앱 변경 내용으로 컨테이너 빌드

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.
  - Resource Manager 템플릿 및 워크플로 파일을 저장하는 GitHub 리포지토리. 리포지토리를 만들려면 [새 리포지토리 만들기](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)를 참조하세요.

## <a name="download-the-sample"></a>샘플 다운로드

Azure 샘플 리포지토리에서 [샘플 프로젝트를 포크](https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/)합니다.

```
https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/
```

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

https://shell.azure.com 에서 Azure Cloud Shell을 엽니다. Azure CLI를 로컬에 설치한 경우 대신 사용할 수 있습니다. (Cloud Shell에 대한 자세한 내용은 Cloud Shell 개요를 참조하세요.)

```azurecli-interactive
    az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

리소스 배포 스크립트가 작동하려면 서비스 사용자로 인증해야 합니다. [Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
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

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한되어 있습니다.

## <a name="configure-the-github-secret-for-authentication"></a>인증을 위한 GitHub 비밀 구성

[GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용하려면 Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

## <a name="add-a-sql-server-secret"></a>SQL Server 비밀 추가

리포지토리에 `SQL_SERVER_ADMIN_PASSWORD`에 대한 새 비밀을 만듭니다. 이 비밀은 암호 보안에 대한 Azure 표준을 충족하는 암호일 수 있습니다. 이 암호에 다시 액세스할 수 없으므로 별도로 저장해야 합니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

Azure 리소스 만들기 워크플로는 [ARM 템플릿](../azure-resource-manager/templates/overview.md)을 실행하여 Azure에 리소스를 배포합니다. 워크플로는 다음을 수행합니다.

- [체크 아웃 작업](https://github.com/marketplace/actions/checkout)을 사용하여 소스 코드를 체크 아웃합니다.
- [Azure 로그인 작업](https://github.com/marketplace/actions/azure-login)을 사용하여 Azure에 로그인하고 환경 및 Azure 리소스 정보를 수집합니다.
- [Azure Resource Manager 배포 작업](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)을 사용하여 리소스를 배포합니다.

Azure 리소스 만들기 워크플로를 실행하려면 다음을 수행합니다.

1. 리포지토리 내 `.github/workflows`에서 `azuredeploy.yaml` 파일을 엽니다.

1. `AZURE_RESOURCE_GROUP` 값을 리소스 그룹 이름으로 업데이트합니다.

1. **작업** 으로 이동하여 **워크플로 실행** 을 선택합니다.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="GitHub Actions 워크플로를 실행하여 리소스를 추가합니다.":::

1. **작업** 페이지에서 녹색 확인 표시를 확인하여 작업이 성공적으로 실행되었는지 확인합니다.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="리소스 만들기를 성공적으로 실행했습니다.":::

## <a name="add-container-registry-and-sql-secrets"></a>컨테이너 레지스트리 및 SQL 비밀 추가

1. Azure Portal에서 리소스 그룹에서 새로 만든 Azure Container Registry를 엽니다.

1. **액세스 키** 로 이동하여 사용자 이름 및 암호 값을 복사합니다.

1. 리포지토리에서 `ACR_USERNAME` 및 `ACR_PASSWORD` 암호에 대한 새 GitHub 비밀을 만듭니다.

1. Azure Portal에서 Azure SQL 데이터베이스를 엽니다. **연결 문자열** 을 열고 값을 복사합니다.

1. `SQL_CONNECTION_STRING`에 대한 새 비밀을 만듭니다. `{your_password}`를 `SQL_SERVER_ADMIN_PASSWORD`로 바꿉니다.

## <a name="build-push-and-deploy-your-image"></a>이미지 빌드, 푸시 및 배포

빌드, 푸시 및 배포 워크플로는 최신 앱 변경 내용으로 컨테이너를 작성하고, 컨테이너를 [Azure Container Registry](../container-registry/index.yml)로 푸시하고, 푸시된 최신 컨테이너를 가리키도록 웹 애플리케이션 스테이징 슬롯을 업데이트합니다. 워크플로 컨테이너 빌드 및 배포 작업:

- 빌드 작업은 [체크 아웃 작업](https://github.com/marketplace/actions/checkout)을 사용하여 소스 코드를 체크 아웃합니다. 그런 다음, [Docker 로그인 작업](https://github.com/marketplace/actions/docker-login)과 사용자 지정 스크립트를 사용하여 Azure Container Registry로 인증하고, 컨테이너 이미지를 빌드하고, Azure Container Registry에 배포합니다.
- 배포 작업은 [Azure 로그인 작업](https://github.com/marketplace/actions/azure-login)을 사용하여 Azure에 로그인하고 환경 및 Azure 리소스 정보를 수집합니다. 그런 다음, 작업은 [Azure App Service 설정 작업](https://github.com/marketplace/actions/azure-app-service-settings)을 사용하여 웹앱 설정을 업데이트하고 [Azure 웹 배포 작업](https://github.com/marketplace/actions/azure-webapp)을 사용하여 App Service 스테이징 슬롯에 배포합니다. 마지막으로 작업은 사용자 지정 스크립트를 실행하여 SQL 데이터베이스를 업데이트하고 스테이징 슬롯을 프로덕션으로 교환합니다.

빌드, 푸시 및 배포 워크플로를 실행하려면 다음을 수행합니다.

1. 리포지토리 내 `.github/workflows`에서 `build-deploy.yaml` 파일을 엽니다.

1. `AZURE_RESOURCE_GROUP` 및 `WEB_APP_NAME`에 대한 환경 변수가 `azuredeploy.yaml`의 환경 변수와 일치하는지 확인합니다.

1. Azure Container Registry 로그인 서버에 대한 `ACR_LOGIN_SERVER` 값을 업데이트합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 및 GitHub 통합에 대해 알아보기](/azure/developer/github/)