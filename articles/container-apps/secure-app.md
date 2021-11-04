---
title: Azure 컨테이너 앱 미리 보기에서 앱 보호
description: Azure 컨테이너 앱에서 응용 프로그램을 보호 하는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: how-to
ms.date: 10/25/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: bbd18b4647eb12add43fec0223755c41518360ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103227"
---
# <a name="secure-an-app-in-azure-container-apps-preview"></a>Azure 컨테이너 앱 미리 보기에서 앱 보호

Azure Container Apps를 사용 하면 응용 프로그램에서 중요 한 구성 값을 안전 하 게 저장할 수 있습니다. 응용 프로그램 수준에서 정의 되 면 보안 값은 컨테이너, 크기 조정 규칙 및 d 4를 통해 사용할 수 있습니다.

- 비밀은 응용 프로그램의 특정 수정 버전 외부의 응용 프로그램으로 범위가 지정 됩니다.
- 비밀을 추가, 제거 또는 변경 해도 새 수정 버전이 생성 되지 않습니다.
- 각 응용 프로그램 수정 버전은 하나 이상의 암호를 참조할 수 있습니다.
- 여러 수정 버전은 동일한 암호를 참조할 수 있습니다.

비밀을 업데이트 하거나 삭제 하면 다음 두 가지 방법 중 하나로 변경 내용에 응답할 수 있습니다.

 1. 새 수정 버전을 배포 합니다.
 2. 기존 수정 버전을 다시 시작 합니다.

업데이트 또는 제거 된 암호는 수정 버전을 자동으로 다시 시작 하지 않습니다.

- 비밀을 삭제 하기 전에 이전 비밀을 더 이상 참조 하지 않는 새 수정 버전을 배포 합니다.
- 비밀 값을 변경 하는 경우 수정 버전을 다시 시작 하 여 새 값을 사용 해야 합니다.

## <a name="defining-secrets"></a>비밀 정의

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

암호는 섹션의 응용 프로그램 수준에서 정의 됩니다 `resources.properties.configuration.secrets` .

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

여기서 큐 저장소 계정에 대 한 연결 문자열은 배열에서 선언 됩니다 `secrets` . 이 구성을 사용 하려면를 `<MY-CONNECTION-STRING-VALUE>` 연결 문자열의 값으로 바꿉니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

비밀은 매개 변수를 사용 하 여 정의 됩니다 `--secrets` .

- 매개 변수는 쉼표로 구분 된 이름/값 쌍 집합을 허용 합니다.
- 각 쌍은 등호 ()로 구분 됩니다 `=` .

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING" \
```

여기서 큐 저장소 계정에 대 한 연결 문자열은 매개 변수에 선언 됩니다 `--secrets` . 의 값은 `queue-connection-string` 라는 환경 변수에서 가져옵니다 `$CONNECTION_STRING` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

비밀은 매개 변수를 사용 하 여 정의 됩니다 `--secrets` .

- 매개 변수는 쉼표로 구분 된 이름/값 쌍 집합을 허용 합니다.
- 각 쌍은 등호 ()로 구분 됩니다 `=` .

```powershell
az containerapp create `
  --resource-group "my-resource-group" `
  --name queuereader `
  --environment "my-environment-name" `
  --image demos/queuereader:v1 `
  --secrets "queue-connection-string=$CONNECTION_STRING" `
```

여기서 큐 저장소 계정에 대 한 연결 문자열은 매개 변수에 선언 됩니다 `--secrets` . 의 값은 `queue-connection-string` 라는 환경 변수에서 가져옵니다 `$CONNECTION_STRING` .

---

## <a name="using-secrets"></a>비밀 사용

응용 프로그램 암호는 속성을 통해 참조 됩니다 `secretref` . 비밀 값은 응용 프로그램 수준 암호에 매핑됩니다. 여기서 `secretref` 값은 응용 프로그램 수준에서 선언 된 비밀 이름과 일치 합니다.

## <a name="example"></a>예제

다음 예제에서는 응용 프로그램 수준에서 연결 문자열을 선언 하 고를 통해 구성 전체에서 사용 되는 응용 프로그램을 보여 줍니다 `secretref` .

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

이 예제에서 응용 프로그램 연결 문자열은으로 선언 되 `queue-connection-string` 고 구성 섹션의 다른 곳에서 사용할 수 있게 됩니다.

:::code language="json" source="code/secure-app-arm-template.json" highlight="11,12,13,27,28,29,30,31,44,45,61,62":::

여기서 라는 환경 변수는 `connection-string` 응용 프로그램 수준 암호에서 해당 값을 가져옵니다 `queue-connection-string` . 또한 Azure Queue Storage 크기 조정 규칙의 권한 부여 구성은 `queue-connection-string` 연결이 설정 될 때를 사용 합니다.

ARM 템플릿을 사용 하 여 비밀 값을 소스 제어로 커밋하지 않으려면 암호 값을 ARM 템플릿 매개 변수로 전달 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 예제에서는 Azure CLI를 사용 하 여 환경 변수에서 참조 되는 암호를 사용 하 여 응용 프로그램을 만듭니다.

```bash
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

여기서 라는 환경 변수는를 `connection-string` 사용 하 여 응용 프로그램 수준 암호에서 해당 값을 가져옵니다 `queue-connection-string` `secretref` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

이 예제에서는 Azure CLI를 사용 하 여 환경 변수에서 참조 되는 암호를 사용 하 여 응용 프로그램을 만듭니다.

```powershell
az containerapp create `
  --resource-group "my-resource-group" `
  --name myQueueApp `
  --environment "my-environment-name" `
  --image demos/myQueueApp:v1 `
  --secrets "queue-connection-string=$CONNECTIONSTRING" `
  --environment-variables "QueueName=myqueue,ConnectionString=secretref:queue-connection-string"
```

여기서 라는 환경 변수는를 `connection-string` 사용 하 여 응용 프로그램 수준 암호에서 해당 값을 가져옵니다 `queue-connection-string` `secretref` .

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [컨테이너](containers.md)
