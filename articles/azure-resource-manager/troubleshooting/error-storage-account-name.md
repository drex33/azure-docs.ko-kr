---
title: 스토리지 계정 이름 오류
description: Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일에서 저장소 계정 이름을 지정할 때 발생할 수 있는 오류에 대해 설명 합니다.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: 8f26efffac8768abb2279722fb1b5dbf174072f3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487746"
---
# <a name="resolve-errors-for-storage-account-names"></a>스토리지 계정 이름 오류 해결

이 문서에서는 Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 저장소 계정을 배포할 때 발생할 수 있는 이름 지정 오류에 대해 설명 합니다.

## <a name="symptom"></a>증상

저장소 계정 이름에 대문자 또는 느낌표와 같은 금지 된 문자가 포함 된 경우 다음과 같은 오류가 표시 됩니다.

```Output
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

저장소 계정의 경우 Azure에서 고유한 리소스 이름을 제공 해야 합니다. 고유 이름을 제공 하지 않으면 다음과 같은 오류가 표시 됩니다.

```Output
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

구독에서 기존 저장소 계정과 이름이 같은 저장소 계정을 배포 하지만 다른 위치에 배포 하는 경우 오류가 표시 됩니다. 이 오류는 저장소 계정이 다른 위치에 이미 있음을 나타냅니다. 기존 저장소 계정을 삭제 하거나 기존 저장소 계정과 동일한 위치를 사용 합니다.

## <a name="cause"></a>원인

Storage 계정 이름은 3 자에서 24 자 사이 여야 하 고 숫자 및 소문자만 사용 해야 합니다. 대문자 또는 특수 문자는 없습니다. 이름은 고유해야 합니다.

## <a name="solution"></a>솔루션

명명 규칙을 함수의 결과와 연결 하 여 고유한 이름을 만들 수 있습니다 `uniqueString` .

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep는 [uniqueString](../bicep/bicep-functions-string.md#uniquestring)에 [문자열 보간을](../bicep/bicep-functions-string.md#concat) 사용 합니다.

```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

ARM 템플릿은 [uniqueString](../templates/template-functions-string.md#uniquestring)와 함께 [concat](../templates/template-functions-string.md#concat) 를 사용 합니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

---

저장소 계정 이름이 24 자를 초과 하지 않는지 확인 합니다. `uniqueString`함수는 13 자를 반환 합니다. 접두사 또는 접미사를 연결 하려는 경우 11 자 이하의 값을 제공 합니다.

다음 예제에서는 최대 11 자의 접두사를 만드는 매개 변수를 사용 합니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('The prefix value for the storage account name.')
@maxLength(11)
param storageNamePrefix string = 'storage'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The prefix value for the storage account name."
    }
  }
}
```

---

그런 다음 매개 변수 값을 값과 연결 `uniqueString` 하 여 저장소 계정 이름을 만듭니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: '${storageNamePrefix}${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat(parameters('storageNamePrefix'), uniquestring(resourceGroup().id))]"
```

---
