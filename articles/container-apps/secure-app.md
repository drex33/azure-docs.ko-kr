---
title: Azure Container Apps 미리 보기에서 앱 보호
description: Azure Container Apps에서 애플리케이션을 보호하는 방법을 알아봅니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: aca235582babedaa9bf6a7c44a835734ba4cb5dc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026998"
---
# <a name="secure-an-app-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기에서 앱 보호

Azure Container Apps를 사용하면 애플리케이션에서 중요한 구성 값을 안전하게 저장할 수 있습니다. 애플리케이션 수준에서 정의되면 컨테이너, 크기 조정 규칙 내부 및 Dapr을 통해 보안 값을 사용할 수 있습니다.

- 비밀은 애플리케이션의 특정 수정 버전 외부에서 애플리케이션으로 범위가 지정됩니다.
- 비밀을 추가, 제거 또는 변경해도 새 수정 버전이 생성되지 않습니다.
- 각 애플리케이션 수정 버전은 하나 이상의 비밀을 참조할 수 있습니다.
- 여러 수정 버전이 동일한 비밀을 참조할 수 있습니다.

비밀이 업데이트되거나 삭제되면 다음 두 가지 방법 중 하나로 변경 내용에 응답할 수 있습니다.

 1. 새 수정 버전을 배포합니다.
 2. 기존 수정 버전을 다시 시작합니다.

업데이트되거나 제거된 비밀은 수정 버전을 자동으로 다시 시작하지 않습니다.

- 비밀을 삭제하기 전에 이전 비밀을 더 이상 참조하지 않는 새 수정 버전을 배포합니다.
- 비밀 값을 변경하는 경우 수정 버전을 다시 시작하여 새 값을 사용해야 합니다.

## <a name="defining-secrets"></a>비밀 정의

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

비밀은 섹션의 애플리케이션 수준에서 `resources.properties.configuration.secrets` 정의됩니다.

```json
"resources": [
{
    ...
    "properties": {
        "configuration": {
            "secrets": [
            {
                "name": "queue-connection-string",
                "value": "<MY-CONNECTION-STRING-VALUE>"
            }],
        }
    }
}
```

여기서는 Queue Storage 계정에 대한 연결 문자열이 `secrets` 배열에 선언됩니다. 이 구성을 사용하려면 `<MY-CONNECTION-STRING-VALUE>` 을 연결 문자열의 값으로 대체합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

비밀은 매개 변수를 사용하여 `--secrets` 정의됩니다.

- 매개 변수는 쉼표로 구분된 이름/값 쌍 집합을 허용합니다.
- 각 쌍은 `=` 등호()로 구분됩니다.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING" \
```

여기서는 큐 스토리지 계정에 대한 연결 문자열이 `--secrets` 매개 변수에 선언됩니다. 의 값은 `queue-connection-string` 라는 환경 변수에서 `$CONNECTION_STRING` 제공됩니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

비밀은 매개 변수를 사용하여 `--secrets` 정의됩니다.

- 매개 변수는 쉼표로 구분된 이름/값 쌍 집합을 허용합니다.
- 각 쌍은 `=` 등호()로 구분됩니다.

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name queuereader `
  --environment "my-environment-name" `
  --image demos/queuereader:v1 `
  --secrets "queue-connection-string=$CONNECTION_STRING" `
```

여기서는 큐 스토리지 계정에 대한 연결 문자열이 `--secrets` 매개 변수에 선언됩니다. 의 값은 `queue-connection-string` 라는 환경 변수에서 `$CONNECTION_STRING` 제공됩니다.

---

## <a name="using-secrets"></a>비밀 사용

애플리케이션 비밀은 속성을 통해 `secretref` 참조됩니다. 비밀 값은 값이 애플리케이션 수준에서 `secretref` 선언된 비밀 이름과 일치하는 애플리케이션 수준 비밀에 매핑됩니다.

## <a name="example"></a>예

다음 예제에서는 애플리케이션 수준에서 연결 문자열을 선언하고 를 통해 구성 전체에서 사용되는 애플리케이션을 보여 `secretref` 줍니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

이 예제에서는 애플리케이션 연결 문자열이 로 `queue-connection-string` 선언되고 구성 섹션의 다른 곳에서 사용할 수 있게 됩니다.

:::code language="json" source="code/secure-app-arm-template.json" highlight="11,12,13,27,28,29,30,31,44,45,61,62":::

여기서 라는 환경 `connection-string` 변수는 애플리케이션 수준 비밀에서 해당 값을 `queue-connection-string` 가져옵니다. 또한 Azure Queue Storage 크기 조정 규칙의 권한 부여 구성은 `queue-connection-string` 연결이 설정될 때 를 사용합니다.

ARM 템플릿을 사용하여 비밀 값을 소스 제어에 커밋하지 않으려면 ARM 템플릿 매개 변수로 비밀 값을 전달합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 예제에서는 Azure CLI 사용하여 환경 변수에서 참조되는 비밀이 있는 애플리케이션을 만듭니다.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

여기서 라는 환경 `connection-string` 변수는 를 사용하여 애플리케이션 수준 비밀에서 해당 값을 `queue-connection-string` `secretref` 가져옵니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

이 예제에서는 Azure CLI 사용하여 환경 변수에서 참조되는 비밀이 있는 애플리케이션을 만듭니다.

```azurecli
az containerapp create `
  --resource-group "my-resource-group" `
  --name myQueueApp `
  --environment "my-environment-name" `
  --image demos/myQueueApp:v1 `
  --secrets "queue-connection-string=$CONNECTIONSTRING" `
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

여기서 라는 환경 `connection-string` 변수는 를 사용하여 애플리케이션 수준 비밀에서 해당 값을 `queue-connection-string` `secretref` 가져옵니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [컨테이너](containers.md)
