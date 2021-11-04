---
title: Azure Container Apps 미리 보기에서 GitHub 작업을 사용 하 여 수정 버전 게시
description: Azure Container Apps 미리 보기에서 GitHub 작업을 사용 하 여 새 수정 버전을 자동으로 만드는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.openlocfilehash: d83c6904f721d429488bca50359c3b32877ad50b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483047"
---
# <a name="publish-revisions-with-github-actions-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기에서 GitHub 작업을 사용 하 여 수정 버전 게시

Azure container Apps를 사용 하면 GitHub 작업을 사용 하 여 컨테이너 앱에 [수정 버전](revisions.md) 을 게시할 수 있습니다. 커밋이 GitHub 리포지토리로 푸시되 면 컨테이너 레지스트리의 [컨테이너](containers.md) 이미지를 업데이트 하는 GitHub 동작이 트리거됩니다. 컨테이너를 레지스트리에서 업데이트 하면 Azure Container Apps는 업데이트 된 컨테이너 이미지를 기반으로 새 수정 버전을 만듭니다.

:::image type="content" source="media/github-actions/azure-container-apps-github-actions.png" alt-text="GitHub 리포지토리를 변경 하면 새 수정 버전을 만들기 위한 동작이 트리거됩니다.":::

GitHub 동작은 리포지토리의 특정 분기에 대 한 커밋에 의해 트리거됩니다. 통합 링크를 만들 때 작업을 트리거하는 분기를 결정 합니다.

## <a name="authentication"></a>인증

GitHub 작업 통합을 추가 하거나 제거 하는 경우 GitHub [개인용 액세스 토큰](https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)을 전달 하거나 대화형 GitHub 로그인 환경을 사용 하 여 인증할 수 있습니다. 대화형 환경에서는 웹 브라우저에서 양식을 열고 GitHub에 로그인 할 수 있는 기회를 제공 합니다. 성공적으로 인증 되 면 토큰은 현재 세션의 나머지 부분에 대 한 GitHub에서 사용 되는 CLI로 다시 전달 됩니다.

- 개인 액세스 토큰을 전달 하려면 매개 변수를 사용 하 `--token` 고 토큰 값을 제공 합니다.
- 대화형 로그인을 사용 하도록 선택 하는 경우에는 값을 사용 `--login-with-github` 하지 않고 매개 변수를 사용 합니다.

## <a name="add"></a>추가

이 `containerapp github-action add` 명령은 컨테이너 앱과 GitHub 작업 통합을 만듭니다.

컨테이너 앱에 GitHub 작업을 처음으로 연결 하는 경우 서비스 주체 컨텍스트를 제공 해야 합니다. 다음 명령은 서비스 사용자를 만드는 방법을 보여 줍니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az ad sp create-for-rbac \
  --name <SERVICE_PRINCIPAL_NAME> \
  --role "contributor" \
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> \
  --sdk-auth
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az ad sp create-for-rbac `
  --name <SERVICE_PRINCIPAL_NAME> `
  --role "contributor" `
  --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME> /subscriptions/<SUBSCRIPTION_ID> `
  --sdk-auth
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

이 명령의 반환 값은 서비스 사용자의, 및를 포함 하는 JSON 페이로드입니다 `tenantId` `cliendId` `clientSecret` .

다음 예제에서는 개인용 액세스 토큰을 사용 하는 동안 통합을 추가 하는 방법을 보여 줍니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action add \
  --repo-url "https://github.com/<OWNER>/<REPOSITORY_NAME>" \
  --docker-file-path "./dockerfile" \
  --branch <BRANCH_NAME> \
  --registry-url <URL_TO_CONTAINER_REGISTRY> \
  --registry-user-name <REGISTRY_USER_NAME> \
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
  --registry-url <URL_TO_CONTAINER_REGISTRY> `
  --registry-user-name <REGISTRY_USER_NAME> `
  --registry-password <REGISTRY_PASSWORD> `
  --service-principal-client-id <CLIENT_ID> `
  --service-principal-client-secret <CLIENT_SECRET> `
  --service-principal-tenant-id <TENANT_ID> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="show"></a>표시

이 `containerapp github-action show` 명령은 컨테이너 앱에 대 한 GitHub 작업 구성 설정을 반환 합니다.

이 예제에서는 개인용 액세스 토큰을 사용 하는 동안 통합을 추가 하는 방법을 보여 줍니다.

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

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

이 명령은 GitHub 작업 통합 구성 설정을 사용 하 여 JSON 페이로드를 반환 합니다.

## <a name="delete"></a>삭제

`containerapp github-action remove`명령은 컨테이너 앱에서 GitHub 작업을 제거 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp github-action remove \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp github-action remove `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.
