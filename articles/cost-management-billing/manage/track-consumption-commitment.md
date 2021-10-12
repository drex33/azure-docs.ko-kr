---
title: Microsoft Azure 사용량 약정(MACC) 추적
description: Microsoft 고객 계약에 대한 Microsoft Azure 사용량 약정(MACC)을 추적하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/11/2021
ms.author: banders
ms.openlocfilehash: 741efbce1f8578ca425059b2b04b64d9892112cb
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857615"
---
# <a name="track-your-microsoft-azure-consumption-commitment-macc"></a>Microsoft Azure 사용량 약정(MACC) 추적

Microsoft Azure 사용량 약정(MACC)은 조직에서 시간 경과에 따라 Microsoft Azure에 대해 체결할 수 있는 약정입니다. 조직에 Microsoft MCA (고객 계약) 청구 계정 또는 EA (기업계약) 청구 계정에 대 한 macc가 있는 경우 시작 및 종료 날짜, 남은 약정 금액, Azure Portal 또는 REST api를 통해 적합 한 비용을 포함 하 여 약정에 대 한 중요 한 측면을 확인할 수 있습니다.

## <a name="track-your-macc-commitment"></a>MACC 약정 추적

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Cost Management + 청구** 를 검색합니다.  
    :::image type="content" source="./media/track-consumption-commitment/billing-search-cost-management-billing.png" alt-text="포털 내 Cost Management + Billing 검색을 보여주는 스크린샷." lightbox="./media/track-consumption-commitment/billing-search-cost-management-billing.png" :::
3. 청구 범위 페이지에서 약정을 추적할 청구 계정을 선택합니다. 청구 계정 유형은 microsoft 고객 계약 (MCA) 고객에 대 한 **microsoft 고객 계약** 이거나 EA 고객용 **기업계약** 이어야 합니다.   
    :::image type="content" source="./media/track-consumption-commitment/list-of-scopes.png" alt-text="청구 범위를 보여주는 스크린샷." lightbox="./media/track-consumption-commitment/list-of-scopes.png" :::
    > [!NOTE]
     > Azure Portal은 사용자가 액세스한 마지막 청구 범위를 기억했다가 다음에 Cost Management + 청구 페이지로 이동하면 해당 범위를 표시합니다. 이전에 Cost Management + 청구 페이지를 방문한 경우 청구 범위 페이지가 표시되지 않습니다. 이런 경우, [올바른 범위](#check-access-to-a-microsoft-customer-agreement)에 있는지 확인하십시오. 그렇지 않으면, [범위를 전환](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)하여 Microsoft 고객 계약에 대한 청구 계정을 선택하십시오.
4. 계약 유형에 따라 다음 중 하나를 수행 합니다.
    - MCA 고객의 경우 왼쪽에서 **속성** 을 선택한 다음, **macc (Microsoft Azure 소비 약정)** 를 선택 합니다.  
        :::image type="content" source="./media/track-consumption-commitment/select-macc-tab.png" alt-text="MCA의 MACC 탭을 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/track-consumption-commitment/select-macc-tab.png" :::
    - EA 고객의 경우 왼쪽 탐색 메뉴에서 **크레딧을 + 약정** 을 선택 하 고, **macc (Microsoft Azure 소비 약정)** 를 선택 합니다.  
        :::image type="content" source="./media/track-consumption-commitment/select-macc-tab-ea.png" alt-text="EA에 대 한 MACC 탭을 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/track-consumption-commitment/select-macc-tab-ea.png" :::
1. Microsoft Azure 사용량 약정(MACC) 탭에는 다음과 같은 섹션이 있습니다.

#### <a name="remaining-commitment"></a>남은 약정 

남은 약정 금액은 마지막 청구 이후 남은 약정 금액을 표시합니다.

:::image type="content" source="./media/track-consumption-commitment/macc-remaining-commitment.png" alt-text="MACC의 남은 약정 스크린샷." lightbox="./media/track-consumption-commitment/macc-remaining-commitment.png" :::

#### <a name="details"></a>세부 정보

세부 정보 섹션은 약정의 다른 중요한 측면을 표시합니다.

:::image type="content" source="./media/track-consumption-commitment/macc-details.png" alt-text="MACC 세부 정보 스크린샷." lightbox="./media/track-consumption-commitment/macc-details.png" :::

| 용어 | 정의 |
|---|---|
| ID | MACC를 고유하게 식별하는 식별자. 이 식별자는 REST API를 통해 MACC 정보를 가져오는 데 사용됩니다. |
| 구매 날짜 | 약정한 날짜입니다. |
| 시작 날짜 | 약정이 발효된 날짜입니다. |
| 종료 날짜 | 약정이 만료된 날짜입니다. |
| 약정 금액 | MACC 가능 제품/서비스에 소비하기 위해 커밋한 금액입니다. |
| 상태 | 약정 상태입니다. |

MACC는 다음 상태 중 하나일 수 있습니다.

- 활성: MACC가 활성 상태입니다. 모든 가능 소비는 MACC 약정에 반영됩니다.
- 완료됨: MACC 약정을 완료되었습니다. 
- 만료됨: MACC가 만료되었습니다. 자세한 정보는 Microsoft 계정 팀에 문의하세요. 
- 취소됨: MACC가 취소되었습니다. 새 Azure 지출은 MACC 약정에 영향을 주지 않습니다.

#### <a name="events"></a>이벤트

이벤트 섹션에는 MACC 약정을 감소시키는 이벤트(송장이 발부된 지출)가 표시됩니다.

:::image type="content" source="./media/track-consumption-commitment/macc-events.png" alt-text="MACC 이벤트 스크린샷." lightbox="./media/track-consumption-commitment/macc-events.png" :::

| 용어 | 정의 |
|---|---|
| Date | 이벤트가 발생한 날짜 |
| 설명 | 이벤트 설명 |
| 청구 프로필 | 이벤트가 발생 한 청구 프로필입니다. 청구 프로필은 Microsoft 고객 계약에만 적용 됩니다. EA 등록이 있는 경우 청구 프로필은 표시 되지 않습니다. |
| MACC 감소 | 이벤트로 인해 감소한 MACC 양 |
| 남은 약정 | 이벤트 후 남은 MACC 약정 |

### <a name="rest-api"></a>[REST API](#tab/rest)

[Azure 청구](/rest/api/billing/) 및 [사용량](/rest/api/consumption/) API를 사용하여 청구 계정의 Microsoft Azure 사용량 약정(MACC)을 프로그래밍 방식으로 가져올 수 있습니다.

아래에 표시된 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다. 예제 출력은 Microsoft 고객 계약에 대 한 것 이므로 Enterprise 계약에 대 한 출력은 다를 수 있습니다.

### <a name="find-billing-accounts-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2020-05-01
```
API 응답은 청구 계정 목록을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Contoso",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "hasReadAccess": true,
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Kayla Lewis",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Individual",
        "hasReadAccess": true,
      }
    }
  ]
}
```

청구 계정의 `displayName` 속성을 사용하여 MACC을 추적하려는 청구 계정을 식별합니다. 청구 계정 `name`을 복사합니다. 예를 들어, **Contoso** 청구 계정에 대한 MACC를 추적하려면 `9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`을 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="get-a-list-of-microsoft-azure-consumption-commitments"></a>Microsoft Azure 사용 약정 목록 가져오기

`<billingAccountName>`를 첫 번째 단계(`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/lots?api-version=2021-05-01&$filter=source%20eq%20%27ConsumptionCommitment%27
```
API 응답은 청구 계정에 대한 MACC 목록을 반환합니다.

```json
    {
    "value": [
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "name": "G2021032459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Active",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 9899.42
          },
          "source": "ConsumptionCommitment",
          "startDate": "2021-03-01T00:00:00.0000000Z",
          "expirationDate": "2024-02-28T00:00:00.0000000Z"
        }
      },
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G1011082459206000XXXX",
        "name": "G1011082459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Complete",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 0.00
          },
          "source": "ConsumptionCommitment",
          "startDate": "2020-03-01T00:00:00.0000000Z",
          "expirationDate": "2021-02-28T00:00:00.0000000Z"
        }
      }
    ]
  }
```

| 요소 이름  | 설명                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `purchasedDate`  | MACC를 구매한 날짜.   |
| `status`  | 약정 상태입니다. |
| `originalAmount` | 원래의 약정 금액. |
| `closedBalance`   | 마지막 청구 이후 남은 약정.    |
| `source`      | MACC의 경우, 원본은 항상 ConsumptionCommitment입니다. |
| `startDate`      |  MACC가 활성화된 날짜.  |
| `expirationDate`  | MACC가 만료되는 날짜.   |

MACC는 다음 상태 중 하나일 수 있습니다. 

- 활성: MACC가 활성 상태입니다. 모든 가능 소비는 MACC 약정에 반영됩니다.
- 완료됨: MACC 약정을 완료되었습니다. 
- 만료됨: MACC가 만료되었습니다. 자세한 정보는 Microsoft 계정 팀에 문의하세요. 
- 취소됨: MACC가 취소되었습니다. 새 Azure 지출은 MACC 약정에 영향을 주지 않습니다. 

### <a name="get-events-that-affected-macc-commitment"></a>MACC 약정에 영향을 주는 이벤트 가져오기

`<billingAccountName>`를 첫 번째 단계(`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`)에서 복사한 `name`로 바꿔서 다음 요청을 수행합니다. 필요한 기간의 이벤트를 가져오려면 **startDate** 및 **endDate** 를 전달해야 합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/events?api-version=2021-05-01&startDate=<startDate>&endDate=<endDate>&$filter=lotsource%20eq%20%27ConsumptionCommitment%27
```

API 응답은 MACC 약정에 영향을 준 모든 이벤트를 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Finance",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-05-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00075XXXX",
        "charges": {
          "currency": "USD",
          "value": -100.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9899.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00075XXXX"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Engineering",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-04-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00074XXXX",
        "charges": {
          "currency": "USD",
          "value": -0.29
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9999.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00074XXXX"
      }
    }
  ]
}

```
| 요소 이름  | 설명                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `billingProfileId` | 이벤트가 발생한 청구 프로필에 대한 고유 식별자. |
| `billingProfileDisplayName` | 이벤트가 발생한 청구 프로필에 대한 표시 이름. |
| `lotId`   | MACC에 대한 고유 식별자.    |
| `lotSource`      | MACC에 대해 ConsumptionCommitment됩니다. |
| `transactionDate`      |  이벤트가 발생한 날짜.  |
| `description`  | 이벤트에 대한 설명입니다.   |
| `charges`  | MACC 감소 양.   |
| `closedBalance`  | 이벤트 후 잔액.   |
| `eventType`  | SettledCharges 이벤트만 MACC를 지원합니다.   |
| `invoiceNumber`  | MACC를 감소시키는 송장의 고유 ID.   |

---

## <a name="azure-services-and-marketplace-offers-that-are-eligible-for-macc"></a>MACC에 적합한 Azure 서비스 및 Marketplace 제품

Azure Portal에서 MACC 감소에 적합한 Azure 서비스 및 Marketplace 제품을 확인할 수 있습니다. 자세한 정보는 [Azure 사용량 약정 사용 가능한 제품 확인(MACC/CtC)](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)을 참조하세요.

## <a name="azure-credits-and-macc"></a>Azure 크레딧 및 MACC

조직이 Microsoft에서 Azure 크레딧을 받은 경우, 크레딧이 적용된 사용량 또는 구매는 MACC 약정에 영향을 주지 않습니다.

조직에서 Azure 선불을 구매한 경우, 크레딧이 적용된 사용량 또는 구매는 MACC 약정에 영향을 주지 않습니다.  그러나 선불 구매 자체는 MACC 약정을 감소시킵니다.

예를 들어, Contoso는 5월에 $5만의 MACC 약정을 맺었습니다. 6월에는 Azure 선불 $1만을 구매했습니다. 구매는 MACC 약정 금액을 감소시키며, 남은 약정 금액은 $4만입니다. 6월에 Contoso는 Azure 선불 가능 서비스의 $1만을 사용했습니다. 서비스 요금은 Azure 선불에서 적용됩니다. 그러나 서비스 요금은 해당 MACC 약정을 감소시키지 않습니다. Azure 선불을 모두 사용하는 경우, 모든 Azure 서비스 사용량과 기타 가능한 구매는 MACC 약정을 감소시킵니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 사용량 약정 사용 가능한 제품 확인(MACC/CtC)](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)
- [Azure 크레딧 잔액 추적](mca-check-azure-credits-balance.md)