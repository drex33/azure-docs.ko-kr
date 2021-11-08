---
title: Azure Container Apps 미리 보기에서 수정 버전 관리
description: Azure 컨테이너 앱에서 수정 및 트래픽 분할을 관리 합니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: a1818ba143e05656cb94f70f0c849fc2dd39ba96
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026371"
---
# <a name="manage-revisions-azure-container-apps-preview"></a>수정 버전 관리 Azure 컨테이너 앱 미리 보기

Azure Container Apps에서 여러 수정 버전을 지원 하면 [각 수정 버전으로 전송](#traffic-splitting)되는 트래픽 양과 버전 관리를 관리할 수 있습니다. 다음 명령을 사용 하 여 컨테이너 앱에서 수정 버전을 관리 하는 방법을 제어 합니다.

## <a name="list"></a>목록

을 사용 하 여 컨테이너 앱과 연결 된 모든 수정 버전을 나열 `az containerapp revision list` 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision list `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  -o table
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="show"></a>표시

을 사용 하 여 특정 수정 버전에 대 한 세부 정보를 표시 `az containerapp revision show` 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision show `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="update"></a>업데이트

컨테이너 앱을 업데이트 하려면를 사용 `az containerapp update` 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp update `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="activate"></a>활성화

을 사용 하 여 수정 버전을 활성화 `az containerapp revision activate` 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision activate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```poweshell
az containerapp revision activate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="deactivate"></a>비활성화

에서 더 이상 사용 되지 않는 수정 버전을 비활성화 `az container app revision deactivate` 합니다. 비활성화는 실행 중인 수정 버전의 모든 복제본을 중지 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision deactivate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision deactivate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="restart"></a>재시작

모든 기존 컨테이너 앱 수정 버전은 다시 시작 될 때까지이 암호에 액세스할 수 없습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurecli
az containerapp revision restart `
  --name <REVISION_NAME> `
  --app <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

## <a name="set-active-revision-mode"></a>활성 수정 모드 설정

컨테이너 앱이 여러 활성 수정 버전을 지원 하는지 여부를 구성 합니다.

`activeRevisionsMode`속성은 다음 두 값을 허용 합니다.

- `multiple`: 둘 이상의 활성 수정 버전을 허용 하도록 컨테이너 앱을 구성 합니다.

- `single`: 수정이 활성화 되 면 다른 모든 수정 버전을 자동으로 비활성화 합니다. 모드를 사용 하도록 설정 하면 `single` 수정 버전 범위 변경을 만들고 새 수정 버전이 만들어질 때 다른 모든 수정 버전이 자동으로 비활성화 됩니다.

```json
{
  ...
  "resources": [
  {
    ...
    "properties": {
        "configuration": {
          "activeRevisionsMode": "multiple"
      }
    }
  }]
}
```

다음 구성 조각에서는 속성을 설정 하는 방법을 보여 줍니다 `activeRevisionsMode` . 이 속성을 변경 하려면 컨테이너 앱 전체 ARM 템플릿의 컨텍스트가 필요 합니다.

## <a name="traffic-splitting"></a>트래픽 분할

백분율 값을 할당 하 여 적용 되는 경우 여러 수정 버전 간의 트래픽 균형을 조정 하는 방법을 결정할 수 있습니다. 트래픽 분할 규칙은 가중치를 다른 수정 버전으로 설정 하 여 할당 됩니다.

다음 예제에서는 세 개의 수정 버전 간에 트래픽을 분할 하는 방법을 보여 줍니다.

```json
{
  ...
  "configuration": {
    "ingress": {
      "traffic": [
        {
          "revisionName": <REVISION1_NAME>,
          "weight": 50
        },
        {
          "revisionName": <REVISION2_NAME>,
          "weight": 30
        },
        {
          "latestRevision": true,
          "weight": 20
        }
      ]
    }
  }
}
```

각 수정 버전은 다음 규칙에 따라 트래픽을 가져옵니다.

- 50%의 요청은 REVISION1로 이동 합니다.
- 요청의 30%가 REVISION2로 이동 합니다.
- 요청 중 20%가 최신 수정 버전으로 이동 합니다.

모든 수정 무게의 합계는 100과 같아야 합니다.

이 예제에서는 `<REVISION*_NAME>` 자리 표시자를 컨테이너 앱의 수정 이름으로 바꿉니다. [목록](#list) 명령을 통해 수정 이름에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [시작](get-started.md)
