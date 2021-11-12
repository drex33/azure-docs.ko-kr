---
title: 리소스 이름 및 형식 세그먼트가 잘못되었습니다.
description: 리소스 이름과 형식의 세그먼트 수가 동일하지 않은 경우 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: c4b04c78f84dada1e41289d1523b9ee2c49bd4e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404691"
---
# <a name="resolve-errors-for-resource-name-and-type-mismatch"></a>리소스 이름 및 형식 불일치 오류 해결

이 문서에서는 리소스 이름의 형식이 리소스 형식의 형식과 일치하지 않는 경우 오류를 해결하는 방법을 설명합니다.

## <a name="symptom"></a>증상

템플릿을 배포할 때 **InvalidTemplate** 오류 코드와 함께 오류가 발생합니다. 메시지는 리소스 종류와 이름이 일치하지 않는다는 것을 나타냅니다. 이름에 있는 세그먼트 수를 수정하는 것이 좋습니다.

## <a name="cause"></a>원인

리소스 종류에는 리소스 공급자 네임스페이스와 형식에 대한 하나 이상의 세그먼트가 포함됩니다. 각 세그먼트는 리소스 계층의 수준을 나타내며 슬래시로 구분됩니다.

```
{resource-provider-namespace}/{type-segment-1}/{type-segment-2}
```

리소스 이름에는 슬래시로 구분된 하나 이상의 세그먼트가 포함됩니다. 세그먼트 수는 리소스 유형의 숫자와 일치해야 합니다.

```
{name-segment-1}/{name-segment-2}
```

리소스 종류와 이름에 다른 수의 세그먼트가 포함된 경우 이 오류가 발생합니다.

## <a name="solution"></a>솔루션

리소스 유형의 수준을 이해해야 합니다. 예를 들어 키 자격 증명 모음 리소스의 정규화된 리소스 유형은 `Microsoft.KeyVault/vaults` 입니다. 리소스 공급자 네임스페이스(**Microsoft.KeyVault)를** 무시하고 형식( 자격 증명 모음)에 집중할 수 **있습니다.** 하나의 세그먼트가 있습니다.

키 자격 증명 모음 비밀은 자격 증명 모음의 자식 리소스입니다. 정규화된 리소스 종류가 `Microsoft.KeyVault/vaults/secrets` 입니다. 이 리소스 유형에는 두 개의 **세그먼트(자격 증명 모음/비밀)가 있습니다.**

키 자격 증명 모음의 이름을 지정하려면 와 같은 세그먼트를 하나만 `examplevault123` 제공합니다. 비밀의 이름을 지정하려면 와 같은 두 세그먼트를 `examplevault123/examplesecret` 제공합니다. 첫 번째 세그먼트는 이 비밀이 저장되는 키 자격 증명 모음을 나타냅니다.

다음 예제에서는 리소스 이름에 대한 유효한 형식을 보여줍니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
}
```

---

세그먼트가 두 개 이상인 이름을 제공한 경우 **오류가 표시됩니다.**

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'contoso/examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "contoso/examplevault123",
  ...
}
```

---

부모 리소스 내에 자식 리소스를 중첩하는 경우 추가 세그먼트만 제공합니다. 전체 리소스 종류와 이름에는 여전히 부모 리소스의 값이 포함되지만 생성됩니다. 다음 예제에서 형식은 `secrets` 이고 이름은 `examplesecret` 입니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
  resource kvsecret 'secrets' = {
    name: 'examplesecret'
    properties: {
     value: secretValue
    }
  }
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
  ...
  "resources": [
    {
      "type": "secrets",
      "apiVersion": "2019-09-01",
      "name": "examplesecret",
      "properties": {
        "value": "[parameters('secretValue')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
      ]
    }
  ]
}
```

---

부모 외부에서 자식 리소스를 정의하는 경우 전체 리소스 유형을 제공합니다. JSON의 경우 전체 리소스 이름을 제공합니다.

Bicep의 경우 속성을 사용하고 `parent` 부모 리소스의 기호 이름을 제공합니다. 부모 속성을 사용하면 전체 이름이 생성되므로 자식 리소스 이름을 단일 세그먼트로 제공합니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource kvsecret 'Microsoft.KeyVault/vaults/secrets@2021-06-01-preview' = {
  name: 'examplesecret'
  parent: kv
  properties: {
     value: secretValue
  }
}

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'examplevault123'
  ...
}
```

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults/secrets",
  "apiVersion": "2019-09-01",
  "name": "examplevault123/examplesecret",
  "properties": {
    "value": "[parameters('secretValue')]"
  },
  "dependsOn": [
    "[resourceId('Microsoft.KeyVault/vaults', 'examplevault123')]"
  ]
},
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "examplevault123",
```

---

자세한 내용은 [Bicep에서 자식 리소스에 대한 이름 및 형식 설정](../bicep/child-resource-name-type.md) 또는 ARM [템플릿에서 자식 리소스에 대한 이름 및 형식 설정을 참조하세요.](../templates/child-resource-name-type.md)