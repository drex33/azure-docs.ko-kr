---
title: Event Grid 원본으로 사용되는 Azure Key Vault
description: Azure Event Grid를 사용한 Azure Key Vault 이벤트에 대해 제공되는 속성과 스키마를 설명합니다.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: fccd93b97455a673da5a3b92dcd54cf494f0cecb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612779"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Event Grid 원본으로 사용되는 Azure Key Vault

이 문서에서는 [Azure Key Vault](../key-vault/index.yml)의 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.


## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Key Vault 계정은 다음과 같은 이벤트 유형을 생성합니다.

| 이벤트 전체 이름 | 이벤트 표시 이름 | 설명 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | 인증서 새 버전이 생성됨 | 새 인증서 또는 새 인증서 버전이 만들어질 때 트리거됩니다. |
| Microsoft.KeyVault.CertificateNearExpiry | 인증서 만료 임박 | 현재 버전의 인증서가 만료될 때 트리거됩니다. (이 이벤트는 만료일 30일 전에 트리거됩니다.) |
| Microsoft.KeyVault.CertificateExpired | 인증서 만료됨 | 현재 버전의 인증서가 만료되면 트리거됩니다. |
| Microsoft.KeyVault.KeyNewVersionCreated | 새로 만든 주요 버전 | 새 키 또는 새 키 버전이 만들어질 때 트리거됩니다. |
| Microsoft.KeyVault.KeyNearExpiry | 만료 임박 키 | 현재 버전의 키가 만료될 때 트리거됩니다. (이 이벤트는 만료일 30일 전에 트리거됩니다.) |
| Microsoft.KeyVault.KeyExpired | 키가 만료됨 | 현재 버전의 키가 만료되면 트리거됩니다. |
| Microsoft.KeyVault.SecretNewVersionCreated | 새 비밀 버전이 생성됨 | 새 비밀 또는 새 비밀 버전이 만들어질 때 트리거됩니다. |
| Microsoft.KeyVault.SecretNearExpiry | 비밀 만료 임박 | 현재 버전의 비밀이 만료될 때 트리거됩니다. (이 이벤트는 만료일 30일 전에 트리거됩니다.) |
| Microsoft.KeyVault.SecretExpired | 비밀 만료됨 | 현재 버전의 비밀이 만료되면 트리거됩니다. |
| Microsoft.KeyVault.VaultAccessPolicyChanged | Vault 액세스 정책이 변경됨 | Key Vault에 대한 액세스 정책이 변경될 때 트리거됩니다. 여기에는 Key Vault 권한 모델이 Azure 역할 기반 액세스 제어로/에서 변경되는 시나리오가 포함됩니다.   |

## <a name="event-examples"></a>이벤트 예제

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

다음 예제에서는 **Microsoft.KeyVault.SecretNewVersionCreated** 에 대한 스키마를 보여줍니다.

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "VaultName":"sample-kv",
         "ObjectType":"Secret",
         "ObjectName":"newsecret",
         "Version":"ee059b2bb5bc48398a53b168c6cdcb10",
         "NBF":"1559081980",
         "EXP":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제에서는 **Microsoft.KeyVault.SecretNewVersionCreated** 에 대한 스키마를 보여줍니다.

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "VaultName":"sample-kv",
         "ObjectType":"Secret",
         "ObjectName":"newsecret",
         "Version":"ee059b2bb5bc48398a53b168c6cdcb10",
         "NBF":"1559081980",
         "EXP":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | App Configuration 이벤트 데이터. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |


# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | App Configuration 이벤트 데이터. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---
 

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| ---------- | ----------- |---|
| `id` | 문자열 | 이 이벤트를 트리거한 개체의 ID |
| `VaultName` | 문자열 | 이 이벤트를 트리거한 개체의 키 자격 증명 모음 이름 |
| `ObjectType` | 문자열 | 이 이벤트를 트리거한 개체 유형 |
| `ObjectName` | 문자열 | 이 이벤트를 트리거한 개체의 이름 |
| `Version` | 문자열 | 이 이벤트를 트리거한 개체의 버전 |
| `NBF` | number | 이 이벤트를 트리거한 개체의 1970-01-01T00:00:00Z 이후 경과되지 않은 날짜(초) |
| `EXP` | number | 이 이벤트를 트리거한 개체의 1970-01-01T00:00:00Z 이후 만료 날짜(초) |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |설명  |
|---------|---------|
| [Azure Event Grid를 사용하여 Key Vault 이벤트 모니터링](../key-vault/general/event-grid-overview.md) | Event Grid와 Key Vault 통합의 개요입니다. |
| [자습서: Event Grid를 사용하여 Key Vault 이벤트 만들기 및 모니터링](../key-vault/general/event-grid-logicapps.md) | Key Vault에 대한 Event Grid 알림을 설정하는 방법을 알아봅니다. |


## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)을 참조하세요.

