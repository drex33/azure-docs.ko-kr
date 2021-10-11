---
title: Key Vault Azure 정책 구현과 관련된 문제 해결
description: Key Vault Azure 정책 구현과 관련된 문제 해결
author: sebansal
ms.author: sebansal
ms.date: 08/17/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 23a9a47bcce81530890c466e19c6c5651104adeb
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740802"
---
# <a name="troubleshooting-issues-with-implementing-azure-policy-on-key-vault"></a>Key Vault Azure 정책 구현과 관련된 문제 해결

이 문서에서는 Key Vault [대한 Azure Policy](./azure-policy.md)설정할 때 발생할 수 있는 일반적인 오류를 해결하는 방법을 안내하고 이를 해결하는 방법을 제안합니다.

## <a name="about-azure-policy-for-key-vault"></a>azure policy for Key Vault 정보

[Azure Policy](../../governance/policy/index.yml)는 사용자에게 대규모로 Azure 환경을 감사 및 관리하는 기능을 제공하는 거버넌스 도구입니다. Azure Policy는 할당된 정책 규칙을 준수하도록 Azure 리소스에 가드 레일을 적용하는 기능을 제공합니다. 이를 통해 사용자는 Azure 환경의 감사, 실시간 적용 및 수정을 수행할 수 있습니다. 정책에 의해 수행되는 감사 결과는 규정 준수 대시보드의 사용자가 사용할 수 있으며, 여기에서 어떤 리소스와 구성 요소가 규정을 준수하는지 드릴다운할 수 있습니다.

### <a name="logging"></a>로깅

정책 평가가 수행되는 방식을 모니터링하기 위해 Key Vault 로그를 검토할 수 있습니다. 이 작업은 제공하는 정보를 Azure 스토리지 계정에 저장하는 Azure Key Vault에서 로깅을 사용하도록 설정하여 수행할 수 있습니다. 이를 설정하는 방법에 대한 단계별 지침은 [키 자격 증명 모음 로깅을 사용하는 방법](howto-logging.md)을 참조하세요.

로깅을 사용하도록 설정하면 **AzurePolicyEvaluationDetails라는** 새 컨테이너가 자동으로 만들어지고 지정된 스토리지 계정에서 정책 관련 로깅 정보를 수집합니다. 

> [!NOTE]
> 중요한 정보를 포함할 수 있기 때문에 모니터링 데이터, 특히 로그 파일에 대한 액세스를 엄격하게 규제해야 합니다. 기본 제공 [모니터링 Azure 역할](https://docs.microsoft.com/azure/azure-monitor/roles-permissions-security) 적용 및 액세스 제한에 대해 알아봅니다.
> 
> 

개별 Blob은 JSON Blob 형식으로 텍스트로 저장됩니다. 키 정책에 대한 로그 항목 예제를 살펴보겠습니다. [키는 만료 날짜가 로 설정되어 있어야 합니다.](https://docs.microsoft.com/azure/key-vault/general/azure-policy?tabs=keys#secrets-should-have-expiration-date-set-preview) 이 정책은 키 자격 증명 모음의 모든 키를 평가하고 만료 날짜가 비준수로 설정되지 않은 키에 플래그를 지정합니다.

```json
{
  "ObjectName": "example",
  "ObjectType": "Key",
  "IsComplianceCheck": false,
  "EvaluationDetails": [
    {
      "AssignmentId": "<subscription ID>",
      "AssignmentDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "DefinitionId": "<definition ID>",
      "DefinitionDisplayName": "[Preview]: Key Vault keys should have an expiration date",
      "Outcome": "NonCompliant",
      "ExpressionEvaluationDetails": [
        {
          "Result": "True",
          "Expression": "type",
          "ExpressionKind": "Field",
          "ExpressionValue": "Microsoft.KeyVault.Data/vaults/keys",
          "TargetValue": "Microsoft.KeyVault.Data/vaults/keys",
          "Operator&quot;: &quot;Equals"
        },
        {
          "Result": "True",
          "Expression": "Microsoft.KeyVault.Data/vaults/keys/attributes.expiresOn",
          "ExpressionKind": "Field",
          "ExpressionValue": "******",
          "TargetValue": "False",
          "Operator&quot;: &quot;Exists"
        }
      ]
    }
  ]
}
```

다음 표에는 필드 이름 및 설명이 나와 있습니다.

| 필드 이름 | Description |
| --- | --- |
| **ObjectName** |개체의 이름 |
| **ObjectType** |키 자격 증명 모음 개체의 유형입니다. 인증서, 비밀 또는 키 |
| **IsComplianceCheck** |야간 감사 중에 평가가 발생한 경우 true, 리소스를 만들거나 업데이트하는 동안 평가가 발생한 경우 false입니다. |
| **결과** | 정책 평가를 반환합니다. |
| **ExpressionEvaluationDetails** | 평가 필드, 식 값에 대한 세부 정보 |


### <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="key-vault-recovery-blocked-by-azure-policy"></a>Azure 정책에 의해 차단된 Key Vault 복구

그 이유 중 하나는 구독(또는 관리 그룹)에 복구를 차단하는 정책이 있기 때문입니다. 수정은 자격 증명 모음이 복구될 때 적용되지 않도록 정책을 조정하는 것입니다.

기본 제공 정책으로 인해 복구에 대한 오류 유형이 표시되는 경우 ```RequestDisallowedByPolicy``` **가장 업데이트된 버전** 를 사용하고 있는지 확인합니다.  

사용자 고유의 논리를 사용하여 **사용자 지정 정책을** 만든 경우 일시 삭제를 요구하는 데 사용할 수 있는 정책 부분의 예는 다음과 같습니다. 일시 삭제된 자격 증명 모음의 복구는 자격 증명 모음을 만들거나 업데이트하는 것과 동일한 API를 사용합니다. 그러나 자격 증명 모음의 속성을 지정하는 대신 값이 "recover"인 단일 "createMode" 속성이 있습니다. 자격 증명 모음은 삭제될 때의 속성과 함께 복원됩니다. 특정 속성이 구성되지 않은 경우 요청을 차단하는 정책은 일시 삭제된 자격 증명 모음의 복구도 차단합니다. 수정은 정책이 "createMode"가 "복구"인 요청을 무시하도록 하는 절을 포함하는 것입니다.

"createMode"가 "recover"와 같지 않은 경우에만 정책이 적용되도록 하는 절이 있습니다.

```

    "policyRule": { 
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.KeyVault/vaults"
          }, 
          {
            "not": {
              "field": "Microsoft.Keyvault/vaults/createMode",
              "equals": "recover"
            }
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "exists": "false"
              },
              {
                "field": "Microsoft.KeyVault/vaults/enableSoftDelete",
                "equals": "false"
              }
            ]
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
```

#### <a name="latency-on-azure-policy-assignment-delete-on-key-vault"></a>Key Vault Azure 정책 할당 삭제의 대기 시간 

Microsoft.KeyVault.Data: 삭제된 정책 할당은 적용을 중지하는 데 최대 24시간이 걸릴 수 있습니다. 

완화: 정책 할당의 효과를 '사용 안 함'으로 업데이트합니다.


#### <a name="secret-creation-via-arm-template-missing-out-policy-evaluation"></a>ARM 템플릿을 통한 비밀 만들기 정책 평가 누락

비밀 만들기를 평가하는 데이터 평면 정책은 비밀을 만들 때 [ARM 템플릿을 통해 만든 비밀에](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template?tabs=CLI) 적용되지 않습니다. 24시간이 지나면 자동화된 준수 검사가 발생하고 규정 준수 결과를 검토할 수 있습니다.


## <a name="next-steps"></a>다음 단계

* [Azure Policy 사용하여 오류 문제를 해결하는](https://docs.microsoft.com/azure/governance/policy/troubleshoot/general) 방법을 알아봅니다.
* Azure Policy [알려진 문제에](https://github.com/azure/azure-policy#known-issues) 대해 알아보기
