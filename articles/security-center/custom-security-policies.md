---
title: Azure Security Center에서 사용자 지정 보안 정책 만들기 | Microsoft Docs
description: Azure Security Center에서 모니터링되는 Azure 사용자 지정 정책 정의를 다룹니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: 828dd2cc5b83737a313156876c54c290a2a67d73
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112238846"
---
# <a name="create-custom-security-initiatives-and-policies"></a>사용자 지정 보안 이니셔티브 및 정책 만들기

시스템 및 환경 보호를 위해 Azure Security Center는 보안 권장 사항을 제공합니다. 이러한 권장 사항은 모든 고객에게 제공되는 일반적인 기본 보안 정책에 통합되는 업계 모범 사례를 기반으로 합니다. 또한 업계 및 규제 표준에 대한 Azure Security Center의 정보를 활용합니다.

이 기능을 사용하여 사용자 고유의 *사용자 지정* 이니셔티브를 추가할 수 있습니다. 그러면 환경이 사용자가 만든 정책을 따르지 않을 경우 권장 사항이 제공됩니다. [규정 준수 개선](security-center-compliance-dashboard.md) 자습서에 설명된 대로 사용자가 만든 사용자 지정 이니셔티브는 규정 준수 대시보드에 기본 제공 이니셔티브와 함께 나타납니다.

[Azure Policy 설명서](../governance/policy/concepts/definition-structure.md#definition-location)에 설명된 대로 관리 그룹 또는 구독을 사용자 지정 이니셔티브의 위치로 지정해야 합니다. 

> [!TIP]
> 이 페이지의 주요 개념에 대한 개요는 [보안 정책, 이니셔티브 및 권장 사항이란?](security-policy-concept.md)을 참조하세요.

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>구독에 사용자 지정 이니셔티브를 추가하려면 

1. Azure Security Center의 사이드바에서 **보안 정책** 페이지를 엽니다.

1. 사용자 지정 이니셔티브를 추가할 구독이나 관리 그룹을 선택합니다.

    [![사용자 지정 정책을 만들 구독 선택.](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Security Center에서 평가하고 표시하려면 구독 수준 이상에서 사용자 지정 이니셔티브를 추가해야 합니다. 사용 가능한 가장 넓은 범위를 선택하는 것이 좋습니다.

1. 보안 정책 페이지의 사용자 지정 이니셔티브에서 **사용자 지정 이니셔티브 추가** 를 클릭합니다.

    [![사용자 지정 이니셔티브 추가를 클릭합니다.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    다음 페이지가 나타납니다.

    ![정책을 만들거나 추가합니다.](media/custom-security-policies/create-or-add-custom-policy.png)

1. 사용자 지정 이니셔티브 추가 페이지에서 조직에 이미 생성된 사용자 지정 정책 목록을 검토합니다. 구독에 할당하려는 사용자 지정 이니셔티브가 있으면 **추가** 를 클릭합니다. 목록에 요구 사항에 맞는 이니셔티브가 없으면 이 단계를 건너뜁니다.

1. 새 사용자 지정 이니셔티브를 만들려면 다음을 수행합니다.

    1. **새로 만들기** 를 클릭합니다.
    1. 정의의 위치와 이름을 입력합니다.
    1. 포함할 정책을 선택하고 **추가** 를 클릭합니다.
    1. 원하는 매개 변수를 입력합니다.
    1. **저장** 을 클릭합니다.
    1. 사용자 지정 이니셔티브 추가 페이지에서 새로 고침을 클릭합니다. 새 이니셔티브가 사용 가능한 것으로 표시됩니다.
    1. **추가** 를 클릭하고 구독에 해당 이니셔티브를 할당합니다.

    > [!NOTE]
    > 새 이니셔티브를 만들려면 구독 소유자 자격 증명이 필요합니다. Azure 역할에 대한 자세한 내용은 [Azure Security Center의 사용 권한](security-center-permissions.md)을 참조하세요.

    새 이니셔티브가 적용되며 다음과 같은 두 가지 방법으로 영향을 확인할 수 있습니다.

    * Azure Security Center 사이드바의 정책 및 규정 준수 아래에서 **규정 준수** 를 선택합니다. 기본 제공 이니셔티브와 함께 새로운 사용자 지정 이니셔티브를 표시하는 규정 준수 대시보드가 열립니다.
    
    * 환경이 사용자가 정책을 따르지 않을 경우 권장 사항이 제공되기 시작합니다.

1. 정책에 대한 결과 권장 사항을 보려면 사이드바에서 **권장 사항** 을 클릭하여 권장 사항 페이지를 엽니다. 권장 사항이 "사용자 지정" 레이블과 함께 표시되고 약 1시간 내에 사용할 수 있습니다.

    [![사용자 지정 권장 사항.](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>REST API를 사용하여 Azure Policy에서 보안 정책 구성

Azure Policy와 네이티브 통합의 일환으로, Azure Security Center를 사용하면 Azure Policy의 REST API를 활용하여 정책 할당을 생성할 수 있습니다. 다음 지침은 기존 할당에 대한 사용자 지정은 물론 정책 할당 생성 과정을 안내합니다. 

Azure Policy의 중요 개념: 

- **정책 정의** 는 규칙입니다. 

- **이니셔티브** 는 정책 정의(규칙) 컬렉션입니다. 

- **할당** 은 특정 범위(관리 그룹, 구독 등)에 이니셔티브 또는 정책을 적용하는 것입니다. 

Security Center에는 모든 보안 정책을 포함하는 기본 제공 이니셔티브인 [Azure Security Benchmark](/security/benchmark/azure/introduction)가 있습니다. Azure 리소스에 대한 Security Center의 정책을 평가하려면 평가하려는 관리 그룹 또는 구독에 할당을 만들어야 합니다.

기본 제공 이니셔티브에는 모든 Security Center의 정책이 기본적으로 사용하도록 설정되어 있습니다. 기본 제공 이니셔티브에서 특정 정책을 사용하지 않도록 설정할 수 있습니다. 예를 들어 정책의 효과 매개 변수 값을 **사용 안 함** 로 변경하면, **웹 애플리케이션 방화벽** 을 제외한 모든 Security Center의 정책을 적용할 수 있습니다.

## <a name="api-examples"></a>API 예제

다음 예제에서 다음 변수를 바꿉니다.

- **{scope}** : 정책을 적용할 관리 그룹 또는 구독의 이름을 입력합니다.
- **{policyAssignmentName}** : 관련 정책 할당의 이름을 입력합니다.
- **{name}** : 사용자의 이름을 입력하거나 정책 변경을 승인한 관리자의 이름을 입력합니다.

이 예제는 구독 또는 관리 그룹에 기본 제공 Security Center 이니셔티브를 할당하는 방법을 보여줍니다.
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

이 예제는 다음 정책을 사용하지 않도록 설정하고, 기본 제공 Security Center 이니셔티브를 구독에 할당하는 방법을 보여줍니다. 

- 시스템 업데이트(“systemUpdatesMonitoringEffect”) 

- 보안 구성("systemConfigurationsMonitoringEffect") 

- 엔드포인트 보호("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
이 예제는 할당을 제거하는 방법을 보여줍니다.
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>세부 정보를 사용하여 사용자 지정 권장 사항 향상

Azure Security Center에서 제공하는 기본 제공 권장 사항에는 심각도 수준과 수정 지침 등의 세부 정보가 포함됩니다. Microsoft Azure Portal이나 권장 사항에 액세스하는 모든 위치에 표시되도록 이러한 유형의 정보를 사용자 지정 권장 사항에 추가하려면 REST API를 사용해야 합니다. 

다음과 같은 두 가지 유형의 정보를 추가할 수 있습니다.

- **RemediationDescription** – 문자열
- **Severity** – 열거형 [낮음, 보통, 높음]

사용자 지정 이니셔티브의 일부인 정책에 대한 정의에 메타데이터를 추가해야 합니다. 다음과 같이 'securityCenter' 속성에 있어야 합니다.

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

다음은 metadata/securityCenter 속성을 포함하는 사용자 지정 정책의 예입니다.

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

securityCenter 속성 사용의 다른 예는 [REST API 설명서의 이 섹션](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)을 참조하세요.


## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 보안 정책을 만드는 방법을 알아보았습니다. 

기타 관련 자료는 다음 문서를 참조하세요. 

- [보안 정책 개요](tutorial-security-policy.md)
- [기본 제공 보안 정책 목록](./policy-reference.md)