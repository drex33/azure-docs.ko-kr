---
title: Azure Purview에 대한 Azure Policy 예외 만들기
description: 이 문서에서는 보안을 유지하기 위해 기존 정책을 그대로 유지하면서 Purview에 대한 Azure Policy 예외를 만드는 방법을 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 10ac52f62da8f8c20ca79ad4d5bb3073eb2bfe69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111918"
---
# <a name="create-an-azure-policy-exception-for-purview"></a>Purview에 대한 Azure Policy 예외 만들기

많은 구독에는 일부 리소스 만들기를 제한하는 [Azure 정책](../governance/policy/overview.md)이 있습니다. 이는 구독 보안 및 정리를 유지하기 위한 것입니다. 그러나 Purview 계정은 만들어질 때 Azure Storage 계정과 Event Hub 네임스페이스라는 두 가지 다른 Azure 리소스를 배포합니다. [Purview 계정을 만들면](create-catalog-portal.md) 이러한 리소스가 배포됩니다. Azure에서 관리하므로 유지 관리할 필요는 없지만 배포해야 합니다.

구독에서 정책을 유지 관리하면서 이러한 관리되는 리소스 만들기를 계속 허용하려면 정책 예외를 만들 수 있습니다.

## <a name="create-a-policy-exception-for-purview"></a>Purview에 대한 정책 예외 만들기

1. [Azure Portal](https://portal.azure.com)로 이동하여 **정책** 을 검색합니다.

    :::image type="content" source="media/create-purview-portal-faq/search-for-policy.png" alt-text="Policy 키워드를 검색하는 Azure Portal 검색 표시줄을 보여 주는 스크린샷":::

1. [사용자 지정 정책 정의 만들기](../governance/policy/tutorials/create-custom-policy-definition.md)를 수행하거나, 기존 정책을 수정하여 `not` 연산자와 `resourceBypass` 태그를 사용하는 두 가지 예외를 추가합니다.

    ```json
    {
    "mode": "All",
      "policyRule": {
        "if": {
          "anyOf": [
          {
            "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "tags['<resourceBypass>']",
                "exists": true
              }
            }]
          },
          {
            "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.EventHub/namespaces"
            },
            {
              "not": {
                "field": "tags['<resourceBypass>']",
                "exists": true
              }
            }]
          }]
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    ```
  
    > [!Note]
    > 태그는 `resourceBypass` 옆에 있을 수 있으며, 정책에서 태그를 검색할 수 있는 한 나중 단계에서 Purview를 만들 때 사용자가 값을 정의할 수 있습니다.

    :::image type="content" source="media/create-catalog-portal/policy-definition.png" alt-text="정책 정의를 만드는 방법을 보여 주는 스크린샷":::

1. 만든 사용자 지정 정책을 사용하여 [정책 할당을 만듭니다](../governance/policy/assign-policy-portal.md).

    :::image type="content" source="media/create-catalog-portal/policy-assignment.png" alt-text="정책 할당을 만드는 방법을 보여 주는 스크린샷" lightbox="./media/create-catalog-portal/policy-assignment.png":::

> [!Note] 
> **Azure Policy** 가 있고 **사전 요구 사항** 에서와 같이 예외를 추가해야 하는 경우 올바른 태그를 추가해야 합니다. 예를 들어 `resourceBypass` 태그를 추가할 수 있습니다. :::image type="content" source="media/create-catalog-portal/add-purview-tag.png" alt-text="Purview 계정에 태그 추가":::

## <a name="next-steps"></a>다음 단계

프라이빗 링크를 사용하여 Azure Purview를 설정하려면 [Azure Purview 계정에 프라이빗 엔드포인트 사용](./catalog-private-link.md)을 참조하세요.
