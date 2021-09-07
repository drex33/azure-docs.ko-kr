---
title: Portal에서 Azure Purview 계정 만들기 FAQ(질문과 대답)
description: 이 문서에서는 Portal을 통해 Purview 계정을 만드는 것에 대한 질문과 대답을 제공합니다.
author: nayenama
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: bf148408d733dffad862eecf51cc06455846b19a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122530830"
---
# <a name="faq-about-creating-purview-accounts-via-portal"></a>Portal을 통해 Purview 계정 만들기에 대한 FAQ

이 문서에는 고객 및 현장 팀이 Azure Portal [Purview 계정 만들기](create-catalog-portal.md)를 사용하여 Purview 계정을 만드는 것과 관련해서 자주 묻는 일반적인 질문에 대한 답변이 있습니다.

## <a name="common-questions"></a>일반적인 질문

다음과 같은 일반적인 질문에 대한 답변을 확인하세요.

### <a name="azure-policy-blocking-from-creating-storage-and-event-hub-namespace"></a>스토리지 및 이벤트 허브 네임스페이스를 만들지 못하도록 차단하는 Azure Policy 

* 모든 애플리케이션에서 **Storage 계정** 및 **EventHub 네임스페이스** 를 만들지 못하도록 차단하는 **Azure Policy** 가 있는 경우 태그를 사용하여 정책 예외를 만들어야 합니다. 이 예외는 Purview 계정을 만드는 과정에서 입력할 수 있습니다. 가장 중요한 이유는 만든 각 Purview 계정에 대해 관리되는 리소스 그룹을 만들고 이 리소스 그룹 내에 Storage 계정 및 EventHub 네임스페이스를 만들어야 하기 때문입니다.

   >[!IMPORTANT]
   >Azure Policy가 없거나 기존 Azure Policy가 **스토리지 계정** 및 **EventHub 네임스페이스** 생성을 차단하지 않는 경우 이 단계를 수행하지 않아도 됩니다.

    1. Azure Portal로 이동하여 **정책** 을 검색합니다.
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
