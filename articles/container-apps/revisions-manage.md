---
title: Azure Container Apps 미리 보기에서 수정 버전 관리
description: Azure Container Apps에서 수정 버전 및 트래픽 분할을 관리합니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5f90deaa5e3b8e2aaf067f218638390a089142b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103231"
---
# <a name="manage-revisions-azure-container-apps-preview"></a>수정 버전 관리 Azure Container Apps 미리 보기

Azure Container Apps에서 여러 수정 버전을 지원하면 각 수정 버전으로 [전송되는 트래픽의](#traffic-splitting)버전 관리 및 양을 관리할 수 있습니다. 다음 명령을 사용하여 컨테이너 앱이 수정 버전을 관리하는 방법을 제어합니다.

## <a name="list"></a>목록

를 통해 컨테이너 앱과 연결된 모든 수정 버전을 `az containerapp revision list` 나열합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision list `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  -o table
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="show"></a>표시

를 사용하여 특정 수정 버전에 대한 세부 정보를 `az containerapp revision show` 표시합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision show \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision show `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="update"></a>업데이트

컨테이너 앱을 업데이트하려면 를 `az containerapp update` 사용합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp update `
  --name <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME> `
  --image mcr.microsoft.com/azuredocs/containerapps-helloworld
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="activate"></a>활성화

를 사용하여 수정 버전을 `az containerapp revision activate` 활성화합니다.

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

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="deactivate"></a>비활성화

에서 더 이상 사용되지 않는 수정 버전을 `az container app revision deactivate` 비활성화합니다. 비활성화는 수정 버전에서 실행 중인 모든 복제본을 중지합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision deactivate \
  --name <REVISION_NAME> \
  --app <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision deactivate `
  --name <REVISION_NAME> `
  --app <CONTAINER_APP_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="restart"></a>재시작

모든 기존 컨테이너 앱 수정 버전은 다시 시작될 때까지 이 비밀에 액세스할 수 없습니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp revision restart \
  --name <REVISION_NAME> \
  --app <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp revision restart `
  --name <REVISION_NAME> `
  --app <APPLICATION_NAME> `
  --resource-group <RESOURCE_GROUP_NAME>
```

---

이 예제와 상호 작용할 때 으로 둘러싸는 자리 표시자를 값으로 바꿉 `<>` 있습니다.

## <a name="set-active-revision-mode"></a>활성 수정 모드 설정

컨테이너 앱이 여러 활성 수정 버전을 지원하는지 여부를 구성합니다.

`activeRevisionsMode`속성은 다음 두 값을 허용합니다.

- `multiple`: 두 개 이상의 활성 수정 버전을 허용하도록 컨테이너 앱을 구성합니다.

- `single`: 수정 버전이 활성화되면 다른 모든 수정 버전을 자동으로 비활성화합니다. 모드를 사용하도록 설정하면 수정 범위 변경을 만들고 새 수정 버전을 `single` 만들 때 다른 수정 버전이 자동으로 비활성화됩니다.

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

다음 구성 조각에서는 속성을 설정하는 방법을 보여 `activeRevisionsMode` 줍니다. 이 속성을 변경하려면 컨테이너 앱의 전체 ARM 템플릿 컨텍스트가 필요합니다.

## <a name="traffic-splitting"></a>트래픽 분할

백분율 값을 할당하여 적용하면 여러 수정 버전 간에 트래픽을 분산하는 방법을 결정할 수 있습니다. 트래픽 분할 규칙은 가중치를 다른 수정 버전으로 설정하여 할당됩니다.

다음 예제에서는 세 개의 수정 버전 간에 트래픽을 분할하는 방법을 보여줍니다.

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

- 요청의 50%가 REVISION1로 이동
- 요청의 30%가 REVISION2로 이동
- 요청의 20%가 최신 수정 버전으로 이동

모든 수정 가중치의 합계는 100과 같아야 합니다.

이 예제에서는 `<REVISION*_NAME>` 자리 표시자를 컨테이너 앱의 수정 이름으로 대체합니다. [list](#list) 명령을 통해 수정 이름에 액세스합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [시작](get-started.md)
