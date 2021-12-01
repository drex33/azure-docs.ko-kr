---
title: Azure Container Apps 미리 보기에서 GitHub 작업을 통해 수정 버전 게시
description: Azure Container Apps 미리 보기에서 GitHub Actions를 사용하여 새 수정 버전을 자동으로 만드는 방법을 알아봅니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.openlocfilehash: c4020cc9c0942cecef3a1cece768eab20bb958b6
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133425489"
---
# <a name="publish-revisions-with-github-actions-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기에서 GitHub 작업을 통해 수정 버전 게시

Azure Container Apps를 사용하면 GitHub Actions를 사용하여 컨테이너 앱에 [수정 버전을](revisions.md) 게시할 수 있습니다. 커밋이 GitHub 리포지토리에 푸시되면 컨테이너 레지스트리의 [컨테이너](containers.md) 이미지를 업데이트하는 GitHub 작업이 트리거됩니다. 컨테이너가 레지스트리에서 업데이트되면 Azure Container Apps는 업데이트된 컨테이너 이미지를 기반으로 새 수정 버전을 만듭니다.

:::image type="content" source="media/github-actions/azure-container-apps-github-actions.png" alt-text="GitHub 리포지션을 변경하여 새 수정 버전을 만드는 작업을 트리거합니다.":::

GitHub 작업은 리포지토리의 특정 분기에 대한 커밋에 의해 트리거됩니다. 통합 링크를 만들 때 작업을 트리거하는 분기를 결정합니다.

## <a name="authentication"></a>인증

GitHub Actions 통합을 추가하거나 제거할 때 GitHub [개인용 액세스 토큰을](https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)전달하거나 대화형 GitHub 로그인 환경을 사용하여 인증할 수 있습니다. 대화형 환경은 웹 브라우저에서 양식을 열고 GitHub 로그인할 수 있는 기회를 제공합니다. 성공적으로 인증되면 토큰이 현재 세션의 나머지 부분에 대해 GitHub 사용되는 CLI로 다시 전달됩니다.

- 개인용 액세스 토큰을 전달하려면 매개 변수를 사용하고 `--token` 토큰 값을 제공합니다.
- 대화형 로그인을 사용하도록 선택하는 경우 `--login-with-github` 값 없이 매개 변수를 사용합니다.

## <a name="add"></a>추가

`containerapp github-action add`명령은 컨테이너 앱과 GitHub Actions 통합을 만듭니다.

GitHub Actions를 컨테이너 앱에 처음 연결할 때 서비스 주체 컨텍스트를 제공해야 합니다. 다음 명령은 서비스 주체를 만드는 방법을 보여줍니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> \
  --sdk-auth
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az ad sp create-for-rbac `
  --name <SERVICE_PRINCIPAL_NAME> `
  --role "contributor" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> `
  --sdk-auth
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

이 명령의 반환 값은 서비스 주체의 , 및 를 포함하는 JSON `tenantId` `cliendId` 페이로드입니다. `clientSecret`

다음 예제에서는 개인용 액세스 토큰을 사용하는 동안 통합을 추가하는 방법을 보여줍니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action add \
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" \
  --docker-file-path "./dockerfile" \
  --branch <BRANCH_NAME> \
  --name <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP> \
  --registry-url <URL_TO_CONTAINER_REGISTRY> \
  --registry-username <REGISTRY_USER_NAME> \
  --registry-password <REGISTRY_PASSWORD> \
  --service-principal-client-id <CLIENT_ID> \
  --service-principal-client-secret <CLIENT_SECRET> \
  --service-principal-tenant-id <TENANT_ID> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action add `
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" `
  --docker-file-path "./dockerfile" `
  --branch <BRANCH_NAME> `
  --name <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP> `
  --registry-url <URL_TO_CONTAINER_REGISTRY> `
  --registry-username <REGISTRY_USER_NAME> `
  --registry-password <REGISTRY_PASSWORD> `
  --service-principal-client-id <CLIENT_ID> `
  --service-principal-client-secret <CLIENT_SECRET> `
  --service-principal-tenant-id <TENANT_ID> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="show"></a>표시

명령은 `containerapp github-action show` 컨테이너 앱에 대한 GitHub 작업 구성 설정을 반환합니다.

이 예제에서는 개인용 액세스 토큰을 사용하는 동안 통합을 추가하는 방법을 보여줍니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME>
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

이 명령은 GitHub Actions 통합 구성 설정이 있는 JSON 페이로드를 반환합니다.

## <a name="delete"></a>DELETE

`containerapp github-action delete`명령은 컨테이너 앱에서 GitHub Actions를 제거합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action delete \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action delete `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.
