---
title: Enterprise Reporting에서 Azure Resource Manager API로 마이그레이션
description: 이 문서는 Reporting API와 Azure Resource Manager API의 차이점, Azure Resource Manager API로 마이그레이션할 때 기대할 수 있는 이점 그리고 새 Azure Resource Manager API에서 제공하는 새 기능을 이해하는 데 도움이 됩니다.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 3c3ee4673f196adc94684fe80ca0237f7b7a32bd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260306"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Enterprise Reporting에서 Azure Resource Manager API로 마이그레이션

이 문서는 [엔터프라이즈 고객용 Azure Reporting API](../manage/enterprise-api.md)를 사용하여 사용자 지정 솔루션을 만든 개발자가 Cost Management용 Azure Resource Manager API로 마이그레이션하는 데 도움이 됩니다. 최신 Azure Resource Manager API에 대한 서비스 주체 지원이 이제 일반 공급됩니다. Azure Resource Manager API는 현재 적극적으로 개발 중입니다. 이전 버전의 엔터프라이즈 고객용 Azure Reporting API 대신 최신 버전으로 마이그레이션하는 것을 고려해 보세요. 이전 API는 곧 사용이 중단됩니다. 이 문서는 Reporting API와 Azure Resource Manager API의 차이점, Azure Resource Manager API로 마이그레이션할 때 기대할 수 있는 이점 그리고 새 Azure Resource Manager API에서 제공하는 새 기능을 이해하는 데 도움이 됩니다.

## <a name="api-differences"></a>API 차이점

다음은 이전 버전의 엔터프라이즈 고객용 보고 API와 최신 Azure Resource Manager API의 차이점을 설명하는 정보입니다.

| **사용** | **기업계약 API** | **Azure Resource Manager API** |
| --- | --- | --- |
| 인증 | EA(기업계약) 포털에서 프로비저닝된 API 키 | 사용자 토큰 또는 서비스 주체를 사용하는 Azure AD(Azure Active Directory) 인증. 서비스 주체가 API 키를 대신합니다. |
| 범위 및 권한 | 모든 요청은 등록 범위에 있습니다. API 키 권한 할당에 따라 전체 등록, 부서 또는 특정 계정에 대한 데이터가 반환됩니다. 사용자 인증 없음 | 사용자 또는 서비스 주체에게 등록, 부서 또는 계정 범위에 대한 액세스 권한이 할당됩니다. |
| URI 엔드포인트 | https://consumption.azure.com | https://management.azure.com |
| 개발 상태 | 유지 관리 모드. 곧 사용 중단됩니다. | 적극적으로 개발 중 |
| 사용 가능한 API | 현재 제공되는 API로 제한 | 동일한 API를 사용하여 각 EA API를 대체할 수 있습니다. <p> 다음을 비롯한 추가 [Cost Management API](/rest/api/cost-management/)도 사용할 수 있습니다. <p> <ul><li>예산</li><li>경고<li>내보내기</li></ul> |

## <a name="migration-checklist"></a>마이그레이션 검사 목록

- [Azure Resource Manager REST API](/rest/api/azure)를 숙지합니다.
- 사용하는 EA API를 확인하고, [새 Azure Resource Manager API로 EA API 매핑](#ea-api-mapping-to-new-azure-resource-manager-apis)에서 이동할 Azure Resource Manager API를 확인합니다.
- Azure Resource Manager API에 대한 서비스 권한 부여 및 인증 구성
  - 아직 Azure Resource Manager API를 사용하지 않는 경우 [클라이언트 앱을 Azure AD에 등록](/rest/api/azure/#register-your-client-application-with-azure-ad)합니다. 등록하면 API 호출에 사용되는 서비스 주체가 만들어집니다.
  - 아래에 설명된 대로 필요한 범위에 대한 액세스 권한을 서비스 주체에 할당합니다.
  - 서비스 주체에 [Azure AD 인증](/rest/api/azure/#create-the-request)을 사용하도록 프로그래밍 코드를 업데이트합니다.
- API를 테스트한 다음, EA API 호출을 Azure Resource Manager API 호출로 바꾸도록 프로그래밍 코드를 업데이트합니다.
- 새 오류 코드를 사용하도록 오류 처리를 업데이트합니다. 그 외에도 다음과 같은 사항을 고려해야 합니다.
  - Azure Resource Manager API의 제한 시간은 60초입니다.
  - Azure Resource Manager API는 현재 속도 제한이 있습니다. 이 속도 제한을 초과하면 429 제한 오류가 발생합니다. 짧은 시간 내에 API 호출이 너무 많이 발생하지 않도록 솔루션을 빌드하세요.
- 나중에 사용할 수 있도록 Azure Resource Manager를 통해 제공되는 다른 Cost Management API를 검토하고 평가합니다. 자세한 내용은 [추가 Cost Management API 사용](#use-additional-cost-management-apis)을 참조하세요.

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Azure Resource Manager API에 대한 액세스 권한을 서비스 주체에 할당

Azure Resource Manager API를 프로그래밍 방식으로 호출하는 서비스 주체를 만든 후에는 Azure Resource Manager에서 요청에 대한 권한을 부여하고 요청을 실행할 수 있는 적절한 권한을 서비스 주체에 할당해야 합니다. 여러 시나리오에 사용되는 두 가지 권한 프레임워크가 있습니다.

### <a name="azure-billing-hierarchy-access"></a>Azure 청구 계층 구조 액세스

기업 청구 계정, 부서 또는 등록 계정 범위에 서비스 사용자 권한을 할당하려면 [Azure 기업계약 서비스 사용자 이름에 역할 할당](../manage/assign-roles-azure-service-principals.md)을 참조하세요.

### <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

새 서비스 주체 지원은 관리 그룹, 구독 및 리소스 그룹과 같은 Azure 관련 범위로 확장됩니다. [Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)에서 또는 [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role)을 사용하여 이러한 범위에 대한 권한을 서비스 주체에 직접 할당할 수 있습니다.

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>새 Azure Resource Manager API로 EA API 매핑

아래 표를 사용하여 현재 사용하는 EA API 및 대신 사용할 대체 Azure Resource Manager API를 확인하세요.

| **시나리오** | **EA API** | **Azure Resource Manager API** |
| --- | --- | --- |
| 잔액 요약 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| 가격표 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – 협상된 가격에 사용 <p> [소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices) – 소매 가격에 사용 |
| 예약 인스턴스 세부 정보 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/azure/cost-management-billing/reservations/reservation-utilization) |
| 예약 인스턴스 요약 | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| 예약 인스턴스 권장 사항 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 예약 인스턴스 요금 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>API별 마이그레이션 세부 정보

다음 섹션에서는 이전 API 요청 예제와 새로운 대체 API 예제를 보여줍니다.

### <a name="balance-summary-api"></a>잔액 요약 API

새 잔액 요약 API를 호출할 때 다음 요청 URI를 사용합니다. 등록 번호를 billingAccountId로 사용해야 합니다.

#### <a name="supported-requests"></a>지원되는 요청

[등록 가져오기](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>응답 본문 변경

_이전 응답 본문_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_새 응답 본문_:

이제 새 API 응답의 `properties` 필드에서 동일한 데이터를 사용할 수 있습니다. 일부 필드 이름의 철자가 변경될 수 있습니다.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>가격표

새 가격표 API를 호출할 때 다음 요청 URI를 사용합니다.

#### <a name="supported-requests"></a>지원되는 요청

 다음 범위를 사용하여 API를 호출할 수 있습니다.

- 등록: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- 구독: `subscriptions/{subscriptionId}`

[_현재 청구 기간 가져오기_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_지정된 청구 기간 가져오기_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>응답 본문 변경

_이전 응답_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_새 응답_:

이제 새 API 응답의 `pricesheets` 필드에서 이전 데이터를 사용할 수 있습니다. 미터 세부 정보도 제공됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>예약 인스턴스 사용량 세부 정보

Microsoft는 현재 동기식 예약 세부 정보 API를 적극적으로 개발하고 있지 않습니다. 마이그레이션할 때 최신 SPN 지원 비동기 API 호출 패턴으로 이동하는 것이 좋습니다. 비동기 요청은 대량의 데이터를 보다 효율적으로 처리하며 시간 제한 오류를 줄입니다.

#### <a name="supported-requests"></a>지원되는 요청

새 비동기 예약 세부 정보 API를 호출할 때 다음 요청 URI를 사용합니다. 등록 번호를 `billingAccountId`로 사용해야 합니다. 다음 범위를 사용하여 API를 호출할 수 있습니다.

- 등록: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>예약 세부 정보 보고서를 생성하는 요청 샘플

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>보고서 생성 상태를 폴링하는 요청 샘플

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>폴링 응답 샘플

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>응답 본문 변경

이전 동기식 예약 세부 정보 API의 응답은 아래와 같습니다.

_이전 응답_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_새 응답_:

새 API는 자동으로 CSV 파일을 만듭니다. 다음 파일 필드를 참조하세요.

| **이전 속성** | **새 속성** | **참고** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | 인스턴스 유연성을 위한 새 속성입니다. |
|  | InstanceFlexibilityRatio | 인스턴스 유연성을 위한 새 속성입니다. |
| instanceId | InstanceName |  |
|  | Kind | 새 속성입니다. 값은 `None`, `Reservation` 또는 `IncludedQuantity`입니다. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>예약 인스턴스 사용량 요약 정보

다음 요청 URI를 사용하여 새 예약 요약 API를 호출합니다.

#### <a name="supported-requests"></a>지원되는 요청

 다음 범위를 사용하여 API를 호출합니다.

- 등록: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_매일 예약 요약 가져오기_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_매달 예약 요약 가져오기_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>응답 본문 변경

_이전 응답_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_새 응답_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>예약 인스턴스 권장 사항

다음 요청 URI를 사용하여 새 예약 권장 사항 API를 호출합니다.

#### <a name="supported-requests"></a>지원되는 요청

 다음 범위를 사용하여 API를 호출합니다.

- 등록: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- 구독: `subscriptions/{subscriptionId}`
- 리소스 그룹: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_권장 사항 가져오기_](/rest/api/consumption/reservationrecommendations/list)

공유 범위와 단일 범위의 권장 사항은 모두 이 API를 통해 사용할 수 있습니다. 범위를 선택적 API 매개 변수로 필터링할 수도 있습니다.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>응답 본문 변경

공유 범위와 단일 범위의 권장 사항은 하나의 API에 결합됩니다.

_이전 응답_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_새 응답_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>예약 인스턴스 요금

다음 요청 URI를 사용하여 새 예약 인스턴스 요금 API를 호출합니다.

#### <a name="supported-requests"></a>지원되는 요청

[_날짜 범위로 예약 요금 가져오기_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>응답 본문 변경

_이전 응답_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_새 응답_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>추가 Cost Management API 사용

기존 보고 시나리오의 Azure Resource Manager API로 마이그레이션한 후에는 다른 여러 API를 사용할 수 있습니다. API는 Azure Resource Manager를 통해서도 사용할 수 있으며, 서비스 주체 기반 인증을 사용하여 자동화할 수 있습니다. 다음은 사용할 수 있는 새 기능에 대한 간략한 요약 정보입니다.

- [예산](/rest/api/consumption/budgets/createorupdate) - 사전에 비용을 모니터링하고, 관련자에게 경고하고, 임계값 위반에 대응하는 조치를 자동화하도록 임계값을 설정하는 데 사용합니다.

- [경고](/rest/api/cost-management/alerts) - 예산 경고, 청구서 경고, 신용 경고 및 할당량 경고를 비롯한(이에 국한되지 않음) 경고 정보를 보는 데 사용합니다.

- [내보내기](/rest/api/cost-management/exports) - 사용자가 선택한 Azure Storage 계정으로 요금 데이터 내보내기 되풀이를 예약하는 데 사용합니다. 데이터를 분석하여 자체 내부 시스템에서 사용하고자 하는 대규모 Azure 시스템을 보유한 고객에게 권장하는 솔루션입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager REST API](/rest/api/azure)를 숙지합니다.
- 필요한 경우 사용하는 EA API를 확인하고, [새 Azure Resource Manager API로 EA API 매핑](#ea-api-mapping-to-new-azure-resource-manager-apis)에서 이동할 Azure Resource Manager API를 확인합니다.
- 아직 Azure Resource Manager API를 사용하지 않는 경우 [클라이언트 앱을 Azure AD에 등록](/rest/api/azure/#register-your-client-application-with-azure-ad)합니다.
- 필요한 경우 서비스 주체에 [Azure AD 인증](/rest/api/azure/#create-the-request)을 사용하도록 프로그래밍 코드를 업데이트합니다.
