---
title: Azure Monitor 로그의 사용량 및 비용 관리
description: Azure Monitor에서 Log Analytics 작업 영역에 대한 가격 책정 요금제를 변경하고 데이터 볼륨 및 보존 정책을 관리하는 방법을 알아봅니다.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2021
ms.author: bwren
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87aeb36a8bdd843f4ad1c0e228299b201321db44
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461915"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 사용량 및 비용 관리    

> [!NOTE]
> 이 문서에서는 Azure Monitor 로그의 비용을 이해하고 관리하는 방법을 설명합니다. 관련 문서인 [사용량 및 예상 비용 모니터링](../usage-estimated-costs.md)에서는 다른 가격 책정 모델에 대해 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다. 이 문서의 모든 가격과 비용은 예시 목적으로만 제시한 것입니다. 

Azure Monitor 로그는 Azure에 배포되거나 회사의 모든 원본에서 매일 대량의 데이터를 수집, 인덱싱, 저장할 때 크기 조정 및 지원을 할 수 있도록 설계됐습니다.  조직에 대한 주 드라이버인 반면 비용 효율성은 궁극적으로 기본 드라이버입니다. 그 목적을 위해서는 Log Analytics 작업 영역의 비용이 수집된 데이터 볼륨만을 기반으로 하지 않고 선택한 플랜에 따라 달라지며, 연결된 원본에서 생성된 데이터를 저장한 기간을 이해하는 것이 중요합니다.  

이 문서에서는 수집된 데이터 볼륨 및 스토리지 증가를 사전에 모니터링하는 방법을 검토합니다. 또한 관련 비용을 제어하는 제한을 정의하는 방법에 대해서도 설명합니다. 

## <a name="pricing-model"></a>가격 책정 모델

Log Analytics에 대한 기본 가격은 수집된 데이터 볼륨을 기반으로 하며 필요에 따라 데이터를 더 오래 보존하기 위한 **종량제** 모델에 속합니다. 데이터 볼륨은 GB(10^9바이트) 단위로 저장되는 데이터의 크기로 측정됩니다. 각 Log Analytics 작업 영역은 별도의 서비스로 요금이 부과되며 Azure 구독에 대한 청구서에 추가됩니다. 데이터 수집량은 다음의 제반 요인에 따라 상당 부분 달라질 수 있습니다. 

  - 사용하도록 설정된 관리 솔루션 집합과 해당 구성
  - 모니터링되는 리소스의 수 및 유형
  - 모니터링된 각 리소스에서 수집된 데이터 형식
  
종량제 모델 외에도 Log Analytics에는 종량제 가격 대비 최대 30%의 비용을 절약할 수 있는 **약정 계층** 이 있습니다. 약정 계층 가격 책정을 사용하면 종량제 가격보다 낮은 가격으로 100GB/일부터 시작하는 데이터 수집을 구매할 수 있습니다. 약정 수준을 초과하는 사용량(초과분)은 현재 약정 계층에서 제공하는 것과 동일한 GB당 가격으로 청구됩니다. 약정 계층은 31일의 약정 기간이 있습니다. 약정 기간 동안에는 더 높은 수준의 약정 계층(31일의 약정 기간을 다시 시작함)으로 변경할 수 있습니다. 다만 약정 기간이 완료될 때까지 종량제 또는 더 낮은 약정 계층으로 다시 이동할 수는 없습니다. 약정 계층에 대한 요금은 일일 기준으로 청구됩니다. Log Analytics 종량제 및 약정 계층 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/). 

> [!NOTE]
> 2021년 6월 2일부터 **용량 예약** 을 이제 **약정 계층** 이라고 합니다. 약정 계층 수준을 초과하여 수집된 데이터(초과분)는 현재 약정 계층 수준과 동일한 GB당 가격으로 청구되며, 종량제 요금의 이전 청구 방법에 비해 비용이 절감되며, 대규모 데이터 볼륨을 가진 사용자가 약정 수준을 미세 조정할 필요가 줄어듭니다. 1000, 2000 및 5000GB/일이라는 세 가지 새로운 약정 계층도 추가되었습니다. 

모든 가격 책정 계층에서 이벤트의 데이터 크기는 데이터가 에이전트에서 보내는지 아니면 수집 프로세스 중에 추가되는지에 관계없이 이 이벤트에 대해 Log Analytics에 저장된 속성의 문자열 표현에서 계산됩니다. 여기에는 데이터가 수집되고 Log Analytics에 저장될 때 추가되는 [사용자 지정 필드](custom-fields.md)가 포함됩니다. 일부 [Log Analytics 표준 속성](./log-standard-columns.md)을 포함하여 모든 데이터 형식에 공통되는 몇 가지 속성은 이벤트 크기 계산에서 제외됩니다. 여기에는 `_ResourceId`, `_SubscriptionId`, `_ItemId`, `_IsBillable`, `_BilledSize` 및 `Type`이 포함됩니다. Log Analytics에 저장된 다른 모든 속성은 이벤트 크기 계산에 포함됩니다. 일부 데이터 형식(예: [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity), [Heartbeat](/azure/azure-monitor/reference/tables/heartbeat), [Usage](/azure/azure-monitor/reference/tables/usage) 및 [Operation](/azure/azure-monitor/reference/tables/operation) 형식)에는 데이터 수집 비용이 전혀 없습니다. 일부 솔루션에는 무료 데이터 수집에 대한 더 많은 솔루션별 정책이 있습니다. 예를 들어 [Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/)는 서버 평가의 처음 180일 동안 종속성 시각화 데이터 비용을 청구하지 않습니다. 이벤트가 데이터 수집에 대한 청구에서 제외되었는지 여부를 확인하려면 [아래](#data-volume-for-specific-events)와 같이 [_IsBillable](log-standard-columns.md#_isbillable) 속성을 사용할 수 있습니다. 사용량은 GB(10^9바이트) 단위로 보고됩니다. 

또한 [Azure Defender(Security Center)](https://azure.microsoft.com/pricing/details/azure-defender/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) 및 [구성 관리](https://azure.microsoft.com/pricing/details/automation/)와 같은 일부 솔루션에는 자체의 고유한 가격 책정 모델이 있습니다. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 전용 클러스터

[Log Analytics 전용 클러스터](logs-dedicated-clusters.md)는 단일 관리형 Azure Data Explorer 클러스터로 수집된 작업 영역들의 컬렉션이며, [고객 관리형 키](customer-managed-keys.md) 등의 고급 시나리오를 지원합니다. Log Analytics 전용 클러스터는 클러스터의 약정 수준이 하루 500GB 이상이어야 한다는 점을 제외하고 작업 영역과 동일한 약정 계층 가격 책정 모델을 사용합니다. 클러스터에 대한 종량제 옵션은 없습니다. 약정 수준을 늘린 후 클러스터 약정 계층은 31일의 약정 기간이 생깁니다. 약정 기간 동안에는 약정 계층 수준을 낮출 수는 없지만 언제든지 늘릴 수는 있습니다. 작업 영역이 클러스터에 연결되면 해당 작업 영역에 대한 데이터 수집 청구는 구성된 약정 계층 수준을 사용하여 클러스터 수준에서 수행됩니다. [Log Analytics 클러스터를 만들고](customer-managed-keys.md#create-cluster) [이 클러스터에 작업 영역을 연결](customer-managed-keys.md#link-workspace-to-cluster)하는 방법에 대해 자세히 알아보세요. 약정 계층 가격 책정 정보는 [Azure Monitor 가격 책정 페이지]( https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

클러스터 약정 계층 수준은 `Sku` 아래의 `Capacity` 매개 변수를 사용하여 Azure Resource Manager를 통해 프로그래밍 방식으로 구성됩니다. `Capacity`는 GB 단위로 지정되며 500, 1,000, 2,000 또는 5,000GB/일 값을 가질 수 있습니다. 약정 수준을 초과하는 사용량(초과분)은 현재 약정 계층에서 제공하는 것과 동일한 GB당 가격으로 청구됩니다.  자세한 내용은 [Azure Monitor 고객 관리형 키](customer-managed-keys.md)를 참조하세요.

클러스터 사용량에 대한 두 가지 청구 모드가 있습니다. 이러한 모드는 `billingType` 매개 변수로 [클러스터를 만들 때](logs-dedicated-clusters.md#create-a-dedicated-cluster) 지정하거나 만든 후에 설정할 수 있습니다. 두 모드는 다음과 같습니다. 

- **클러스터**: 이 경우(기본값) 수집된 데이터에 대한 청구는 클러스터 수준에서 수행됩니다. 클러스터에 연결된 각 작업 영역에서 수집된 데이터 양을 집계하여 클러스터에 대한 일일 청구액을 계산합니다. [Azure Defender(Security Center)](../../security-center/index.yml)의 노드당 할당량은 클러스터의 모든 작업 영역에서 집계된 데이터를 이 방식으로 집계하기 전에 작업 영역 수준에서 적용됩니다. 

- **작업 영역**: 클러스터의 약정 계층 비용은 각 작업 영역의 데이터 수집 볼륨에 따라 클러스터의 작업 영역에 비례하여 계산됩니다(각 작업 영역에 대한 [Azure Defender(Security Center)](../../security-center/index.yml)의 노드별 할당량을 고려한 후). 클러스터에 하루 동안 수집된 총 데이터 볼륨이 약정 계층보다 적은 경우 각 작업 영역에서 수집된 데이터에 대해 약정 계층의 일부만 청구하여 유효 GB당 약정 계층 비율로 청구하고, 약정 계층 중 사용하지 않은 부분은 클러스터 리소스에 청구됩니다. 클러스터에 하루 동안 수집된 총 데이터 볼륨이 약정 계층보다 많은 경우 각 작업 영역에 대한 요금은 해당 일에 수집된 데이터의 일부를 기준으로 약정 계층의 일부 및 약정 계층을 초과하여 수집된 데이터의 일부에 대해 청구됩니다. 작업 영역에 하루 동안 수집된 총 데이터 볼륨이 약정 계층을 초과하는 경우 클러스터 리소스에 대한 요금이 청구되지 않습니다.

클러스터 청구 옵션에서 데이터 보존은 각 작업 영역에 대해 청구됩니다. 클러스터 요금 청구는 작업 영역이 클러스터에 연결되었는지 여부에 관계 없이 클러스터를 만들 때부터 시작됩니다. 클러스터에 연결된 작업 영역에는 더 이상 가격 책정 계층이 없습니다.

## <a name="estimating-the-costs-to-manage-your-environment"></a>환경을 관리하는 데 드는 비용을 추정 

Azure Monitor 로그를 아직 사용하고 있지 않다면 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor)를 사용하여 Log Analytics를 사용하는 데 드는 비용을 추정할 수 있습니다. **검색** 상자에 "Azure Monitor"를 입력하고 결과 Azure Monitor 타일을 선택합니다. 페이지를 아래로 스크롤하여 **Azure Monitor** 를 찾은 다음 **Log Analytics** 섹션을 확장합니다. 여기에 수집할 데이터의 GB를 입력할 수 있습니다. 이미 Azure Monitor 로그를 평가하고 있는 경우, 자신의 환경에서 데이터 통계를 사용할 수 있습니다. [모니터링되는 VM의 수](#understanding-nodes-sending-data)와 [작업 영역에서 수집 중인 데이터의 볼륨](#understanding-ingested-data-volume)을 확인하는 방법은 아래를 참조하세요. Log Analytics를 아직 실행하지 않는 경우 데이터 볼륨을 예측하기 위한 몇 가지 지침은 다음과 같습니다.

1. **VM 모니터링:** 일반적인 모니터링을 사용하도록 설정하면 모니터링되는 VM당 1GB~3GB의 데이터/월이 수집됩니다. 
2. **AKS(Azure Kubernetes Service) 클러스터 모니터링:** 일반적인 AKS 클러스터를 모니터링하기 위한 예상 데이터 볼륨에 대한 자세한 내용은 [여기](../containers/container-insights-cost.md#estimating-costs-to-monitor-your-aks-cluster)에서 확인할 수 있습니다. 이러한 [모범 사례](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)에 따라 AKS 클러스터 모니터링 비용을 제어합니다. 
3. **애플리케이션 모니터링:** Azure Monitor 가격 계산기에는 애플리케이션의 사용량에 대해 사용하고 Application Insights 데이터 볼륨의 통계 분석을 기준으로 하는 데이터 볼륨 예측 도구가 포함되어 있습니다. 가격 계산기의 Application Insights 섹션에서 "애플리케이션 활동을 기반으로 데이터 볼륨 예측" 옆에 있는 스위치를 토글하여 사용합니다. 

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Azure 청구서에서 Log Analytics 사용량 보기 

부분 Log Analytics 작업 영역에 대한 청구된 사용량을 보는 가장 쉬운 방법은 작업 영역의 **개요** 페이지로 이동하고 페이지 맨 위에 있는 Essentials 섹션의 오른쪽 위 모서리에 있는 **비용 보기를** 클릭하는 것입니다. 그러면 이미 이 작업 영역으로 범위가 Azure Cost Management + Billing 비용 분석이 시작됩니다.  

또는 [Azure Cost Management + Billing](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) 허브에서 시작할 수 있습니다. 여기서는 "비용 분석" 기능을 사용하여 Azure 리소스 비용을 볼 수 있습니다. Log Analytics 지출을 추적하려면 "리소스 종류" 기준 필터를 추가할 수 있습니다(Log Analytics의 경우 microsoft.operationalinsights/workspace에 추가, Log Analytics 클러스터의 경우 microsoft.operationalinsights/cluster에 추가). **그룹화 방법** 으로 **미터 범주** 또는 **미터** 를 선택합니다. Azure Defender(Security Center) 및 Azure Sentinel과 같은 다른 서비스에서도 Log Analytics 작업 영역 리소스에 대한 사용량 요금을 청구합니다. 서비스 이름에 대한 매핑을 보기 위해 차트 대신 테이블 보기를 선택할 수 있습니다. 

사용량을 보다 잘 이해하려면 [Azure Portal에서 사용량을 다운로드](../../cost-management-billing/understand/download-azure-daily-usage.md)할 수 있습니다. 다운로드한 스프레드시트에서 Azure 리소스별(예: Log Analytics 작업 영역) 일일 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서 Log Analytics 작업 영역의 사용량은 먼저 “Log Analytics”, “Insight and Analytics”(일부 레거시 가격 책정 계층에서 사용됨) 및 “Azure Monitor”(약정 계층 가격 책정 계층에서 사용됨)를 표시하도록 “미터 범주” 열을 필터링한 다음, “작업 영역 포함” 또는 “클러스터 포함”(후자의 경우 Log Analytics 클러스터 사용량을 포함)인 필터를 “인스턴스 ID” 열에 추가하여 확인할 수 있습니다. 사용량은 "사용한 수량" 열에 표시되며 각 항목에 대한 단위는 "측정 단위" 열에 표시됩니다. 자세한 내용은 [개별 Azure 구독 청구서 검토](../../cost-management-billing/understand/review-individual-bill.md)를 참조하세요. 

## <a name="understand-your-usage-and-optimizing-your-pricing-tier"></a>사용량 이해 및 가격 책정 계층 최적화
<a name="understand-your-usage-and-estimate-costs"></a>

사용량 추세에 대해 알아보고 가장 비용 효율적인 log Analytics 가격 책정 계층을 선택하려면 **Log Analytics 사용량 및 예상 비용을** 사용합니다. 각 솔루션에서 수집되는 데이터의 양, 보존되는 데이터의 양 및 최근 데이터 수집 패턴에 따라 각 가격 책정 계층에 대한 예상 비용을 보여 줍니다. 

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="사용량 및 예상 비용":::

데이터를 더 자세히 탐색하려면 **사용량 및 예상 비용** 페이지의 차트 중 하나의 오른쪽 상단 모서리에 있는 아이콘을 클릭합니다. 이제 이 쿼리에 대해 작업하여 사용량을 자세히 살펴볼 수 있습니다.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="로그 보기":::

**사용량 및 예상 비용** 페이지에서 해당 월의 데이터 볼륨을 검토할 수 있습니다. 여기에는 Log Analytics 작업 영역에서 받고 유지하는 모든 청구 가능한 데이터가 포함됩니다.  
 
Log Analytics 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 **청구** 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.  

## <a name="changing-pricing-tier"></a>가격 책정 계층 변경

작업 영역의 Log Analytics 가격 책정 계층을 변경하려면

1. Azure Portal에서 이 작업 영역에 사용할 수 있는 각 가격 책정 계층의 목록이 표시될 작업 영역에서 **사용량 및 예상 비용** 을 엽니다.

2. 각 가격 책정 계층에 대한 예상 비용을 검토합니다. 이 예상치는 지난 31일간의 사용량을 기준으로 하므로 이 비용 예상치는 일반적인 사용량을 대표하는 지난 31일간의 사용량에 따라 달라집니다. 아래 예제에서는 지난 31일 간의 데이터 패턴에 따라 이 작업 영역이 일일 100GB의 약정 계층(#2)에 비해 종량제 계층(#1)에서 비용이 덜 드는 것을 알 수 있습니다.  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="가격 책정 계층":::
    
3. 지난 31일간의 사용량을 기준으로 예상 비용을 검토한 후 가격 책정 계층을 변경하려면 **선택** 을 선택하세요.  

### <a name="changing-pricing-tier-via-arm"></a>ARM을 통한 가격 책정 계층 변경

또한 `sku` 개체를 사용하여 [Azure Resource Manager를 통해 가격 계층을 설정](./resource-manager-workspace.md)할 수 있으며, 가격 계층이 `capacityresrvation`인 경우 `capacityReservationLevel` 매개 변수를 설정할 수 있습니다. ([ARM을 통해 작업 영역 속성을 설정](/azure/templates/microsoft.operationalinsights/2020-08-01/workspaces?tabs=json#workspacesku-object)하는 방법에 대해 자세히 알아보세요.) 다음은 작업 영역을 300GB/일 약정 계층(Resource Manager에서는 `capacityreservation`이라고 함)으로 설정하는 샘플 Azure Resource Manager 템플릿입니다. 

```
{
  "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "YourWorkspaceName",
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "location": "yourWorkspaceRegion",
      "properties": {
                    "sku": {
                      "name": "capacityreservation",
                      "capacityReservationLevel": 300
                    }
      }
    }
  ]
}
```

PowerShell을 통해 이 템플릿을 사용하려면 [Azure Az PowerShell 모듈을 설치](/powershell/azure/install-az-ps)한 후 `Connect-AzAccount`를 사용하여 Azure에 로그인하고 `Select-AzSubscription -SubscriptionId YourSubscriptionId`를 사용하여 작업 영역이 포함된 구독을 선택하고 템플릿(template.json이라는 파일에 저장)을 적용합니다.

```
New-AzResourceGroupDeployment -ResourceGroupName "YourResourceGroupName" -TemplateFile "template.json"
```

가격 책정 계층을 종량제(SKU에서는 `pergb2018`이라고 부름)와 같은 다른 값으로 설정하려면 `capacityReservationLevel` 속성을 생략합니다. [ARM 템플릿 만들기](../../azure-resource-manager/templates/template-tutorial-create-first-template.md), [템플릿에 리소스 추가](../../azure-resource-manager/templates/template-tutorial-add-resource.md) 및 [템플릿 적용](../resource-manager-samples.md)에 대해 자세히 알아봅니다. 

### <a name="tracking-pricing-tier-changes"></a>가격 책정 계층 변경 내용 추적

작업 영역에 대 한 변경 내용은 "작업 영역 만들기" 라는 작업이 포함 된 이벤트와 함께 [활동 로그](../essentials/activity-log.md) 에 기록 됩니다. 이벤트의 **변경 기록** 탭에는 행의 이전 및 새 가격 책정 계층이 표시 됩니다  `properties.sku.name` .  작업 영역에서 "활동 로그" 옵션을 클릭 하 여 특정 작업 영역으로 범위가 지정 된 이벤트를 확인 합니다. 가격 책정 계층의 변경 내용을 모니터링 하기 위해 "작업 영역 만들기" 작업에 대 한 경고를 만들 수 있습니다. 

## <a name="legacy-pricing-tiers"></a>레거시 가격 책정 계층

2018년 4월 2일에 Log Analytics 작업 영역 또는 Application Insights 리소스가 포함되거나, 2019년 2월 1일 이전에 시작되어 여전히 활성 상태인 기업계약에 연결된 구독은 **평가판**, **독립 실행형(GB당)** 및 **노드당(OMS)** 등 레거시 가격 책정 계층을 계속 사용할 수 있습니다. 평가판 가격 책정 계층의 작업 영역에는 일일 데이터 수집이 500MB로 제한되며([Azure Defender(Security Center)](../../security-center/index.yml)에서 수집된 보안 데이터 형식 제외), 데이터 보존 기간은 7일로 제한됩니다. 평가판 가격 책정 계층은 평가 목적으로만 사용됩니다. 무료 계층에 대해서는 SLA가 제공되지 않습니다.  독립 실행형 또는 노드당 가격 책정 계층의 작업 영역은 사용자 구성 가능 보존 기간이 30~730일입니다.

독립 실행형 가격 책정 계층의 사용량은 수집된 데이터 볼륨을 기준으로 청구됩니다. 이는 **Log Analytics** 서비스에서 보고되고, 미터의 이름은 "분석된 데이터"로 지정됩니다. 

노드당 가격 책정 계층은 모니터링되는 VM(노드)당 1시간 단위로 요금이 청구됩니다. 모니터링되는 각 노드에 대한 작업 영역에는 일일 500MB의 데이터 용량이 할당되며 이에 대한 요금은 청구되지 않습니다. 이 할당량은 시간별 세분성으로 계산되며, 매일 작업 영역 수준에서 집계됩니다. 일일 총 데이터 할당량을 초과하는 데이터 수집량에 대해서는 GB당 데이터 초과분 요금이 청구됩니다. 작업 영역이 노드당 가격 책정 계층에 있는 경우, 청구서에 명시되는 해당 서비스는 Log Analytics 사용량에 대한 **Insight & Analytics** 입니다. 사용량은 다음 세 가지 미터로 보고됩니다.

- **노드**: 노드 개월 단위로 모니터링되는 노드(VM) 수에 대한 사용량입니다.
- **노드당 데이터 초과분**: 집계된 데이터 할당량을 초과하여 수집된 데이터의 GB 수입니다.
- **노드당 포함된 데이터**: 집계된 데이터 할당량에 포함된 수집된 데이터의 양입니다. 이 미터는 작업 영역이 모든 가격 책정 계층에 있는 경우에도 Azure Defender(Security Center)에서 처리하는 데이터 양을 표시하는 데 사용됩니다.

> [!TIP]
> 작업 영역에서 **노드당** 가격 책정 계층에 액세스할 수 있으며 다만 종량제 계층에서 비용이 더 저렴한지 여부가 궁금한 경우, [아래의 쿼리를 사용](#evaluating-the-legacy-per-node-pricing-tier)하면 권장 사항을 쉽게 확인할 수 있습니다. 

2016년 4월 이전에 만든 작업 영역은 데이터 보존 기간이 각각 30일 및 365일로 정해진 **표준** 및 **프리미엄** 가격 책정 계층을 계속 사용할 수도 있습니다. **표준** 또는 **프리미엄** 가격 책정 계층에서는 새 작업 영역을 만들 수 없으며, 작업 영역을 이러한 계층 밖으로 옮기면 다시 이동할 수 없습니다. 이러한 레거시 계층에 대한 Azure 청구서의 데이터 수집 미터를 "분석된 데이터"라고 합니다.

### <a name="legacy-pricing-tiers-and-azure-defender-security-center"></a>레거시 가격 책정 계층 및 Azure Defender (Security Center)

레거시 Log Analytics 계층의 사용과 [Azure Defender(Security Center)](../../security-center/index.yml)의 사용량에 대한 요금이 청구되는 방식 간에도 몇 가지 동작이 있습니다. 

- 작업 영역이 레거시 표준 또는 프리미엄 계층에 있는 경우 Azure Defender는 노드당이 아닌 Log Analytics 데이터 수집에 대해서만 요금이 청구됩니다.
- 작업 영역이 레거시 노드당 계층에 있는 경우 Azure Defender는 현재 [Azure Defender 노드 기반 가격 책정 모델](https://azure.microsoft.com/pricing/details/security-center/)을 사용하여 요금이 청구됩니다. 
- 그 밖의 다른 가격 책정 계층(약정 계층 포함)에서 Azure Defender가 2017년 6월 19일 이전에 사용하도록 설정된 경우 Azure Defender에 대한 요금은 Log Analytics 데이터 수집에 대해서만 청구됩니다. 그렇지 않은 경우 Azure Defender에 대한 요금은 현재 Azure Defender 노드 기반 가격 책정 모델을 사용하여 청구됩니다.

가격 책정 계층 제한 사항에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)에서 확인할 수 있습니다.

레거시 가격 책정 계층에는 지역 기반 가격 책정이 없습니다.  

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="log-analytics-and-azure-defender-security-center"></a>Log Analytics 및 Azure Defender(Security Center)

[Security Center (Azure Defender For Servers)](../../security-center/index.yml) 요금은 Log Analytics 청구와 밀접 하 게 연관 되어 있습니다. Azure Defender는 모니터링 되는 [서비스 수를 기준으로 요금을 청구](https://azure.microsoft.com/pricing/details/azure-defender/) 500 하며 다음 [보안 데이터 형식](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, 업데이트 관리 솔루션이 작업 영역에서 실행 되지 않을 때 Securityalert, securityalert, SecurityBaselineSummary, Securityalert, Securityalert, windowsfirewall, MaliciousIPCommunication, LinuxAuditLog, Sysmonevent, ProtectionStatus) 및 Update 및 UpdateSummary 데이터 형식 이온 대상 지정이 사용 됩니다 ([자세한 정보](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance)). 모니터링 되는 서버 수는 시간별 세분성에서 계산 됩니다. 모니터링 되는 각 서버의 일일 데이터 할당 기여도는 작업 영역 수준에서 집계 됩니다. 작업 영역이 레거시 노드당 가격 책정 계층에 있는 경우 Azure Defender 및 Log Analytics 할당량이 결합되어 청구 가능한 모든 수집된 데이터에 공동으로 적용됩니다.  

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

다음 단계에서는 로그 데이터가 작업 영역에 보존되는 기간을 구성하는 방법을 설명합니다. 작업 영역 수준의 데이터 보존 기간은 레거시 평가판 가격 책정 계층을 사용하는 경우를 제외하고는 모든 작업 영역에 대해 30~730일(2년)의 범위 로 구성할 수 있습니다. 개별 데이터 형식에 대한 보존 기간은 최소 4일로 설정할 수 있습니다. 보다 긴 데이터 보존을 사용할 경우의 가격을 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/). 730일을 초과하여 데이터를 보존하려면 [Log Analytics 작업 영역 데이터 내보내기](logs-data-export.md)를 사용하는 것이 좋습니다.

### <a name="workspace-level-default-retention"></a>작업 영역 수준 기본 보존 기간

작업 영역에 대한 기본 보존 기간을 설정하려면
 
1. Azure Portal의 작업 영역에서 왼쪽 창에 있는 **사용량 및 예상 비용** 을 선택합니다.
2. **사용량 및 예상 비용** 페이지의 상단에서 **데이터 보존** 을 선택합니다.
3. 창에서 슬라이더를 이동하여 일 수를 늘리거나 줄인 다음 **확인** 을 선택합니다.  *무료* 계층에서 작업 중인 경우 데이터 보존 기간을 수정할 수 없으며 이 설정을 제어하기 위해 유료 계층으로 업그레이드해야 합니다.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="작업 영역 데이터 보존 기간 설정 변경":::

보존 기간을 줄이면 새 보존 기간 설정보다 오래된 데이터가 제거될 때까지 며칠의 유예 기간이 있습니다. 

**데이터 보존** 페이지에서는 30, 31, 60, 90, 120, 180, 270, 365, 550 및 730일의 보존 기간 설정을 허용합니다. 다른 설정이 필요한 경우 보존을 사용하여 [Azure Resource Manager](./resource-manager-workspace.md)에서 `retentionInDays` 매개 변수를 사용하여 구성할 수 있습니다. 데이터 보존 기간을 30일로 설정하면 `immediatePurgeDataOn30Days` 매개 변수를 사용하여 오래된 데이터를 즉시 제거할 수 있습니다(며칠의 유예 기간 제거). 이는 즉각적인 데이터 제거가 필수적인 규정 준수 관련 시나리오에 유용할 수 있습니다. 이 즉시 제거 기능은 Azure Resource Manager를 통해서만 표시됩니다. 

보존 기간이 30일인 작업 영역은 실제로 데이터를 31일 동안 보존할 수 있습니다. 데이터를 30일 동안만 유지해야 하는 경우 Azure Resource Manager에서 `immediatePurgeDataOn30Days` 매개 변수를 사용하여 보존 기간을 30일로 설정합니다.  

기본적으로 `Usage` 및 `AzureActivity`의 두 데이터 형식은 최소 90일 동안 무료로 보존됩니다. 작업 영역 보존 기간이 90일 넘게 증가하는 경우, 이러한 데이터 형식의 보존 기간도 증가하게 됩니다. 이러한 데이터 형식에 대해서도 데이터 수집 요금이 부과되지 않습니다. 

작업 영역을 기반으로 한 Application Insights 리소스의 데이터 형식(`AppAvailabilityResults`, `AppBrowserTimings`, `AppDependencies`, `AppExceptions`, `AppEvents`, `AppMetrics`, `AppPageViews`, `AppPerformanceCounters`, `AppRequests`, `AppSystemEvents` 및 `AppTraces`)은 기본적으로 무료로 90일 동안 보존됩니다. 이들 데이터 형식의 보존 기간은 데이터 형식별 보존 기간 기능을 적용하여 조정할 수 있습니다. 

Log Analytics [제거 API](/rest/api/loganalytics/workspacepurge/purge)는 보존 청구에 영향을 미치지 않으며 매우 제한된 경우에만 사용됩니다. 보존 요금을 줄이려면 작업 영역 또는 특정 데이터 형식에 대한 보존 기간을 줄여야 합니다. [Log Analytics 및 Application Insights에 저장 된 개인 데이터](./personal-data-mgmt.md)를 관리 하는 방법에 대해 자세히 알아보세요.

### <a name="retention-by-data-type"></a>데이터 형식별 보존 기간

작업 영역 수준 기본 보존 기간을 재정의하는 개별 데이터 형식에 대해 다른 보존 기간 설정(4~730일)을 지정할 수도 있습니다(레거시 평가판 가격 책정 계층의 작업 영역 제외). 각 데이터 형식은 작업 영역의 하위 리소스에 속합니다. 예를 들어, 다음과 같이 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에서 SecurityEvent 테이블의 주소를 지정할 수 있습니다.

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

데이터 형식(테이블)은 대/소문자를 구분합니다. 특정 데이터 형식(이 예제에서는 SecurityEvent)의 현재 데이터 형식당 보존 기간 설정을 가져오려면 다음을 사용합니다.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> 보존 기간이 명시적으로 설정된 경우에만 데이터 형식에 대한 보존 기간이 반환됩니다. 보존 기간이 명시적으로 설정되지 않은(이에 따라 작업 영역 보존 기간을 상속함) 데이터 형식은 이 호출에서 아무것도 반환하지 않습니다. 

데이터 형식당 보존 기간 설정이 있는 작업 영역의 모든 데이터 형식에 대한 현재 데이터 형식당 보존 기간 설정을 가져오려면 특정 데이터 형식을 생략합니다. 예를 들어 다음과 같습니다.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

특정 데이터 형식(이 예제에서는 SecurityEvent)의 보존 기간을 730일로 설정하려면 다음을 사용합니다.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

에 유효한 값 `retentionInDays` 은 4에서 730 까지입니다.

사용자 지정 보존 기간을 적용하여 `Usage` 및 `AzureActivity` 데이터 형식을 설정할 수 없습니다. 이들 데이터 형식은 기본 작업 영역 보존 기간의 최대값 또는 90일을 사용합니다. 

데이터 형식별 보존 기간을 설정하기 위해 Azure Resource Manager에 직접 연결할 수 있는 유용한 도구는 OSS 도구인 [ARMclient](https://github.com/projectkudu/ARMClient)입니다.  [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 및 Daniel Bowbyes가 작성한 문서에서 ARMClient에 대해 자세히 알아보세요.  ARMClient를 사용하여 SecurityEvent 데이터의 보존 기간을 730일로 설정하는 예제는 다음과 같습니다.

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 개별 데이터 형식에 대한 보존 기간 설정을 사용하면 데이터 보존에 드는 비용을 줄일 수 있습니다. 2019년 10월(이 기능이 릴리스된 시점)부터 수집된 데이터의 경우, 일부 데이터 형식에 대한 보존 기간을 줄이면 시간이 경과함에 따라 보존 비용을 줄일 수 있습니다. 이전에 수집된 데이터의 경우, 개별 형식에 대한 보존 기간을 단축하여 설정하더라도 데이터 보존 비용은 이에 영향을 받지 않습니다.  

## <a name="manage-your-maximum-daily-data-volume"></a>일일 최대 데이터 볼륨 관리

작업 영역에 대한 일일 상한 및 한도를 구성하고 일일 수집량을 제한할 수 있지만 목표치가 일일 한도에 도달하지 않도록 주의하세요. 그렇지 않으면 남은 기간 동안의 데이터가 손실됩니다. 이는 해당 기능이 작업 영역에서 사용할 수 있는 최신 데이터에 의존할 수도 있는 다른 Azure 서비스 및 솔루션에 영향을 줄 수 있습니다. 결과적으로 리소스의 상태 조건이 IT 서비스를 지원할 때 경고를 관찰하고 수신하는 기능이 영향을 받습니다. 일일 상한은 관리되는 리소스에서 데이터 볼륨의 **예기치 않은 증가** 를 관리하고 제한 내에서 유지하거나 작업 영역에 대한 계획되지 않은 요금을 제한하려는 경우에 사용하기 위한 것입니다. 작업 영역에서 일일 상한에 매일 도달하도록 설정하는 것은 적절하지 않습니다.

각 작업 영역에는 하루 중 다른 시간에 적용되는 일일 상한이 있습니다. 다시 설정 시간은 **일일 상한** 페이지(아래 참조)에 나와 있습니다. 이 다시 설정 시간은 구성할 수 없습니다. 

일일 한도에 도달하면 하루의 나머지 시간 동안 청구 가능한 데이터 형식의 수집이 즉시 중지됩니다. 일일 상한 적용에 내재된 대기 시간은 해당 상한이 지정된 일일 상한 수준에서 정확하게 적용되지 않음을 의미합니다. 선택된 Log Analytics 작업 영역에 대한 페이지의 상단에 경고 배너가 표시되고 작업 이벤트가 **LogManagement** 범주 아래의 *작업* 테이블로 전송됩니다. *일일 한도 아래 정의된 재설정 시간이* 로 설정된 후 데이터 수집이 다시 시작합니다. 일일 데이터 한도에 도달했을 때 알려주도록 구성된 이 작업 이벤트를 기반으로 경고 규칙을 정의하는 것이 좋습니다. 자세한 내용은 [일일 상한에 도달한 경우 경고](#alert-when-daily-cap-is-reached) 섹션을 참조하세요. 

> [!NOTE]
> 일일 상한은 데이터 수집을 지정된 상한 수준으로 정확하게 중지할 수 없으며, 특히 작업 영역에서 많은 양의 데이터를 받는 경우 일부 초과 데이터가 예상됩니다. 한도를 초과하여 데이터를 수집하면 요금이 계속 청구됩니다. 일일 상한 동작을 연구하는 데 도움이 되는 쿼리는 이 문서의 [일일 상한 효과 보기](#view-the-effect-of-the-daily-cap) 섹션을 참조하세요. 

> [!WARNING]
> 일일 상한은 2017년 6월 19일 이전에 Azure Defender(Security Center)가 설치된 작업 영역을 제외하고는 **WindowsEvent**, **SecurityAlert**, **SecurityBaseline**, **SecurityBaselineSummary**, **SecurityDetection**, **SecurityEvent**, **WindowsFirewall**, **MaliciousIPCommunication**, **LinuxAuditLog**, **SysmonEvent**, **ProtectionStatus**, **Update**, and **UpdateSummary** 데이터 형식의 수집을 중지하지 않습니다. 

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

데이터 수집 추세 및 정의할 일일 볼륨 한도를 이해하려면 [Log Analytics 사용량 및 예상 비용](../usage-estimated-costs.md)을 검토합니다. 한도에 도달한 후에는 리소스를 모니터링할 수 없으므로 주의해야 합니다. 

### <a name="set-the-daily-cap"></a>일일 상한 설정

다음 단계에서는 Log Analytics 작업 영역이 매일 수집하는 데이터의 볼륨 관리 제한을 구성하는 방법을 설명합니다.  

1. 작업 영역의 왼쪽 창에서 **사용량 및 예상 비용** 을 선택합니다.
2. 선택한 작업 영역에 대한 **사용량 및 예상 비용** 페이지의 상단에서 **데이터 상한** 을 선택합니다. 
3. 기본적으로 **일일 상한** 은 **꺼짐** 으로 설정되어 있습니다. 일일 상한을 사용하도록 설정하려면 **켜짐** 을 선택한 다음, 데이터 볼륨 한도를 GB/일 단위로 설정합니다.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics 데이터 제한 구성":::
    
Azure Resource Manager를 사용하여 일일 상한을 구성할 수 있습니다. 일일 상한을 구성하려면 [작업 영역 - 만들기 또는 업데이트](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)에서 설명한 대로 `WorkspaceCapping` 아래에서 `dailyQuotaGb` 매개 변수를 설정합니다. 

다음 쿼리를 사용하여 일일 상한에 대한 변경 내용을 추적할 수 있습니다.

```kusto
_LogOperation | where Operation == "Workspace Configuration" | where Detail contains "Daily quota"
```

[_LogOperation](./monitor-workspace.md) 함수에 대해 자세히 알아보세요. 

### <a name="view-the-effect-of-the-daily-cap"></a>일일 상한 효과 보기

일일 상한의 효과를 보려면 일일 상한에 포함되지 않은 보안 데이터 형식과 작업 영역의 다시 설정 시간을 고려해야 합니다. 일일 상한 다시 설정 시간은 **일일 상한** 페이지에 표시됩니다. 다음 쿼리를 사용하여 일일 상한 다시 설정 사이의 일일 상한에 적용되는 데이터 볼륨을 추적할 수 있습니다. 다음 예제에서 작업 영역의 다시 설정 시간은 14:00입니다. 작업 영역에 대해 이를 업데이트해야 합니다.

```kusto
let DailyCapResetHour=14;
Usage
| where DataType !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) // Quantity in units of MB
| render areachart  
```
`Update`업데이트 관리 `UpdateSummary` `where Datatype` 솔루션이 작업 영역에서 실행되고 있지 않거나 솔루션 대상 지정을 사용하도록 설정된 경우 및 데이터 형식을 줄에 추가합니다([자세한 내용은 ).](../../security-center/security-center-pricing.md#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="alert-when-daily-cap-is-reached"></a>일일 상한에 도달한 경우 경고

데이터 제한 임계값에 도달할 때 Azure Portal에서 시각적 큐를 표시하지만, 이 동작은 즉각적인 주의가 필요한 운영 문제를 관리하는 방법에 맞출 필요는 없습니다. 경고 알림을 수신하려면 Azure Monitor에서 새 경고 규칙을 만들 수 있습니다. 자세한 내용은 [경고를 만들고 보고 관리하는 방법](../alerts/alerts-metric.md)을 참조하세요.

먼저 `_LogOperation` 함수를 사용하여 `Operation` 테이블을 쿼리하는 경고에 권장되는 설정은 다음과 같습니다([자세한 정보](./monitor-workspace.md)). 

- 대상: Log Analytics 리소스 선택
- 조건: 
   - 신호 이름: 사용자 지정 로그 검색
   - 검색 쿼리: `_LogOperation | where Operation =~ "Data collection stopped" | where Detail contains "OverQuota"`
   - 기준: 결과의 수
   - 조건: 초과
   - 임계값: 0
   - 기간: 5분
   - 빈도: 5분
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)

경고가 정의되고 한계에 도달하면 경고가 트리거되고 작업 그룹에서 정의된 응답을 수행합니다. 다음과 같은 방법으로 팀에 알릴 수 있습니다.

- 전자 메일 및 문자 메시지
- 웹후크를 사용하는 자동화된 작업
- Azure Automation Runbook
- [외부 ITSM 솔루션과 통합됩니다](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>사용량이 예상보다 더 높은 원인 해결

다음 중 하나 또는 두 가지 원인 때문에 사용량이 높아집니다.
- 예상보다 많은 노드가 Log Analytics 작업 영역에 데이터를 전송. 자세한 내용은 이 문서의 [데이터를 보내는 노드 이해](#understanding-nodes-sending-data) 섹션을 참조하세요.
- Log Analytics 작업 영역으로 전송되는 데이터가 예상보다 많음(새 솔루션 사용 시작 또는 기존 솔루션에 대한 구성 변경이 원인일 수 있음). 자세한 내용은 이 문서의 [수집된 데이터 볼륨 이해](#understanding-ingested-data-volume) 섹션을 참조하세요.

`Usage` 레코드를 사용하여 보고된 높은 데이터 수집이 관찰되지만([솔루션별 데이터 볼륨](#data-volume-by-solution) 섹션 참조) [데이터 형식](#data-volume-for-specific-events)에서 `_BilledSize`를 직접 합산하는 것과 동일한 결과를 관찰하지 못할 경우 상당한 지연 도착 데이터가 있을 수 있습니다. 이를 진단하는 방법에 대한 자세한 내용은 이 문서의 [지연 도착 데이터](#late-arriving-data) 섹션을 참조하세요. 

### <a name="log-analytics-workspace-insights"></a>Log Analytics 작업 영역 인사이트

[Log Analytics 작업 영역 인사이트 통합 문서](log-analytics-workspace-insights-overview.md)의 **사용량** 탭에서 데이터 볼륨에 대해 알아봅니다. **사용량 대시보드** 에서는 다음을 확인할 수 있습니다.
- 주 테이블에서 가장 많은 데이터 볼륨을 수집 중인 데이터 테이블  
- 데이터에 기여하는 상위 리소스 
- 데이터 수집의 추세

**추가 쿼리** 로 피벗하여 데이터 패턴을 이해하는 데 유용한 더 많은 쿼리를 간편하게 실행합니다. 

[사용량 탭의 기능](log-analytics-workspace-insights-overview.md#usage-tab)에 대해 자세히 알아봅니다. 

이 통합 문서에서는 쿼리를 실행하지 않고도 많은 질문에 대답할 수 있지만, 보다 구체적인 질문에 대답하거나 심층 분석을 수행하려면 다음 두 섹션을 참조하세요. 

## <a name="understanding-nodes-sending-data"></a>데이터를 전송하는 노드를 파악

지난 달에 매일 에이전트에서 하트비트를 보고하는 노드의 수를 파악하려면 다음 쿼리를 사용합니다.

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
지난 24시간 내 데이터를 전송하는 노드의 수를 가져오려면 다음과 같이 쿼리를 사용합니다. 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

데이터를 전송하는 노드 목록(및 각 노드에서 전송된 데이터의 양)을 가져오려면 다음 쿼리를 사용하세요.

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>레거시 노드당 가격 책정 계층에서 청구되는 노드

[레거시 노드당 가격 책정 계층](#legacy-pricing-tiers)에서는 노드에 대한 요금을 시간별 세분성으로 청구하며 보안 데이터 형식 세트를 보내는 노드만 계산하지 않습니다. 일일 노드 수는 다음 쿼리에 가깝습니다.

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

청구서의 단위 수는 쿼리에서 `billableNodeMonthsPerDay`로 표시되는 노드 개월 단위입니다. 업데이트 관리 솔루션이 작업 영역에 설치되어 있는 경우 **Update** 및 **UpdateSummary** 데이터 형식을 위 쿼리의 where 절에 있는 목록에 추가합니다. 마지막으로 위의 쿼리에 표시되지 않은 솔루션 대상 지정이 사용되는 경우 실제 청구 알고리즘에 몇 가지 추가적인 복잡성이 있습니다. 


> [!TIP]
> 여러 데이터 형식을 검색하면 실행 시 [많은 리소스가 필요](./query-optimization.md#query-performance-pane)하기 때문에 이러한 `find` 쿼리는 자주 사용하지 않도록 합니다. **컴퓨터별** 결과가 필요하지 않은 경우, **사용량** 데이터 형식(아래 참조)을 쿼리합니다.

## <a name="understanding-ingested-data-volume"></a>수집된 데이터 볼륨을 파악

**사용량 및 예상 비용** 페이지에서 *솔루션당 데이터 수집* 차트는 전송된 총 데이터 양과 각 솔루션이 전송하는 데이터 양을 보여 줍니다. 이를 통해 전체 데이터 사용량(또는 특정 솔루션별 사용량)이 증가하는지, 고정적인지, 감소하는지와 같은 추세를 판단할 수 있습니다. 

### <a name="data-volume-for-specific-events"></a>특정 이벤트에 대한 데이터 볼륨

특정 이벤트 집합에 대해 수집된 데이터의 크기를 확인하려면 특정 테이블(이 예제에서는 `Event`)을 쿼리한 다음, 관련 이벤트(이 예제에서는 이벤트 ID 5145 또는 5156)로 쿼리를 제한합니다.

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

여기서 `where _IsBillable = true` 절은 수집 비용이 없는 특정 솔루션에서 데이터 형식을 필터링합니다. `_IsBillable`에 대해 [자세히 알아보세요](./log-standard-columns.md#_isbillable).

### <a name="data-volume-by-solution"></a>솔루션별 데이터 볼륨

지난 한 달 동안(마지막 1일 미만의 시간 제외) 솔루션별로 청구 가능한 데이터 볼륨을 보는 데 사용되는 쿼리는 다음과 같이 [Usage](/azure/azure-monitor/reference/tables/usage) 데이터 형식을 사용하여 작성할 수 있습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

`TimeGenerated`를 사용하는 절은 Azure Portal의 쿼리 환경이 기본값인 24시간을 초과한 기간을 되돌아보는지 확인하는 용도로만 사용됩니다. **사용량** 데이터 형식을 사용하는 경우, `StartTime` 및 `EndTime`은 결과가 표시되는 시간 버킷을 나타냅니다. 

### <a name="data-volume-by-type"></a>형식별 데이터 볼륨

데이터 형식별 데이터 추세를 확인하기 위해 다음과 같이 더 자세한 내용을 다룰 수 있습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

또는 지난 한 달 동안 솔루션 및 형식별로 테이블을 표시하려면

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>컴퓨터별 데이터 볼륨

**사용량** 데이터 형식에는 컴퓨터 수준의 정보가 포함되지 않습니다. 컴퓨터당 수집된 청구 가능한 데이터의 **크기** 를 보려면 바이트 단위의 크기를 제공하는 **_BilledSize**[속성](./log-standard-columns.md#_billedsize)을 사용합니다.

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer, Type
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes desc nulls last
```

**_IsBillable** [속성](./log-standard-columns.md#_isbillable)은 수집된 데이터에서 요금이 발생하는지 여부를 지정합니다. **Usage** 형식은 데이터 추세 분석 전용이므로 생략됩니다. 

컴퓨터당 청구 가능한 이벤트 **수** 를 보려면 다음을 사용합니다. 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true and Type != "Usage"
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount desc nulls last
```

> [!TIP]
> 여러 데이터 형식을 검색하면 실행 시 [많은 리소스가 필요](./query-optimization.md#query-performance-pane)하기 때문에 이러한 `find` 쿼리는 자주 사용하지 않도록 합니다. **컴퓨터별** 결과가 필요하지 않은 경우, **사용량** 데이터 형식을 쿼리합니다.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure 리소스, 리소스 그룹 또는 구독별 데이터 볼륨

Azure에서 호스트되는 노드의 데이터에 대해서는 __컴퓨터별__ 로 수집된 데이터의 **크기** 를 가져올 수 있으며 [_ResourceId 속성](./log-standard-columns.md#_resourceid)을 사용할 수 있는데 이는 리소스에 대한 전체 경로를 제공합니다.

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Azure에서 호스트되는 노드의 데이터에 대해서는 다음과 같이 **_SubscriptionId** 속성을 사용하여 __Azure 구독당__ 수집된 데이터의 **크기** 를 가져올 수 있습니다.

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, _SubscriptionId
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _SubscriptionId | sort by BillableDataBytes nulls last
```

리소스 그룹별 데이터 볼륨을 가져오려면 **_ResourceId** 를 구문 분석할 수 있습니다.

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

필요한 경우 다음과 같이 **_ResourceId** 를 좀 더 완전하게 구문 분석할 수도 있습니다.

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 여러 데이터 형식을 검색하면 실행 시 [많은 리소스가 필요](./query-optimization.md#query-performance-pane)하기 때문에 이러한 `find` 쿼리는 자주 사용하지 않도록 합니다. 구독별, 리소스 그룹별 또는 리소스 이름별 결과가 필요하지 않다면 **Usage** 데이터 형식에 대해 쿼리합니다.

> [!WARNING]
> **Usage** 데이터 형식의 일부 필드가 여전히 스키마에 있지만 더 이상 사용되지 않으며 해당 값은 더 이상 채워지지 않습니다. 이는 **Computer** 일 뿐 아니라 수집과 관련된 필드(**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** 및 **AverageProcessingTimeMs**)이기도 합니다.

## <a name="late-arriving-data"></a>지연 도착 데이터   

데이터가 이전 타임스탬프로 수집되는 상황이 발생할 수 있습니다. 예를 들어 연결 문제로 인해 에이전트가 Log Analytics와 통신할 수 없거나 호스트에 시간 날짜/시간이 잘못된 경우입니다. 이는 **Usage** 데이터 형식에서 보고한 수집된 데이터와 이벤트가 생성될 때의 타임스탬프인 **TimeGenerated** 로 지정된 특정 날에 대한 원시 데이터의 **_BilledSize** 합계를 합한 쿼리 간의 명백한 불일치로 인해 매니페스트할 수 있습니다.

지연 도착 데이터 문제를 진단하려면 **TimeGenerated** 외에 **_TimeReceived** 열([자세히 알아보기](./log-standard-columns.md#_timereceived))을 사용합니다. **_TimeReceived** 는 Azure 클라우드의 Azure Monitor 수집 지점에서 레코드를 받은 시간입니다. 예를 들어 **Usage** 레코드를 사용할 때 2021년 5월 2일에 **W3CIISLog** 데이터의 수집된 데이터 볼륨이 많은 것으로 확인되었습니다. 이 수집된 데이터의 타임스탬프를 식별하는 쿼리는 다음과 같습니다. 

```Kusto
W3CIISLog
| where TimeGenerated > datetime(1970-01-01)
| where _TimeReceived >= datetime(2021-05-02) and _TimeReceived < datetime(2021-05-03) 
| where _IsBillable == true
| summarize BillableDataMB = sum(_BilledSize)/1.E6 by bin(TimeGenerated, 1d)
| sort by TimeGenerated asc 
```

`where TimeGenerated > datetime(1970-01-01)` 문은 단지 Log Analytics 사용자 인터페이스에 대한 단서를 제공하여 모든 데이터를 살펴보기 위해 제공됩니다.  

## <a name="querying-for-common-data-types"></a>공통 데이터 형식에 대한 쿼리

특정 데이터 형식의 데이터 소스를 더 자세히 알아보려면 다음 예와 같은 몇 가지 쿼리를 사용합니다.

+ **작업 영역 기반 Application Insights** 리소스
  - [Application Insights 사용량 및 비용 관리](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)에서 자세히 알아보세요.
+ **보안** 솔루션
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **로그 관리** 솔루션
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **성능** 데이터 형식
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **이벤트** 데이터 형식
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 데이터 형식
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 데이터 형식
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>데이터 볼륨을 줄이기 위한 팁

이 표에는 수집된 로그 볼륨을 줄이기 위한 몇 가지 제안 사항이 포함되어 있습니다.

| 높은 데이터 볼륨의 소스 | 데이터 볼륨을 줄이는 방법 |
| -------------------------- | ------------------------- |
| 데이터 수집 규칙      | [Azure Monitor 에이전트](../agents/azure-monitor-agent-overview.md)는 데이터 수집 규칙을 사용하여 데이터 수집을 관리합니다. 사용자 지정 XPath 쿼리를 사용하여 [데이터 수집을 제한](../agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)할 수 있습니다. | 
| 컨테이너 인사이트         | 필요한 데이터만 수집하도록 [컨테이너 인사이트를 구성](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)합니다. |
| Azure Sentinel | 최근에 추가 데이터 볼륨의 원본으로 사용하도록 설정한 [Sentinel 데이터 원본](../../sentinel/connect-data-sources.md)을 검토합니다. Sentinel 비용 및 청구에 대해 [자세히 알아봅니다](../../sentinel/azure-sentinel-billing.md). |
| 보안 이벤트            | [일반 또는 최소한의 보안 이벤트](../../security-center/security-center-enable-data-collection.md#data-collection-tier)를 선택합니다. <br> 보안 감사 정책을 변경하여 필요한 이벤트만을 수집합니다. 특히, 다음 항목에 대한 이벤트를 수집할 필요를 검토합니다. <br> - [감사 필터링 플랫폼](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [레지스트리 감사](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10)) <br> - [파일 시스템 감사](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10)) <br> - [커널 개체 감사](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10)) <br> - [핸들 조작 감사](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10)) <br> - 이동식 스토리지 감사 |
| 성능 카운터       | [성능 카운터 구성](../agents/data-sources-performance-counters.md)을 다음과 같이 변경합니다. <br> - 컬렉션의 빈도 감소 <br> - 성능 카운터의 수 감소 |
| 이벤트 로그                 | [이벤트 로그 구성](../agents/data-sources-windows-events.md)을 다음과 같이 변경합니다. <br> - 수집된 이벤트 로그의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 수준 이벤트는 수집하지 않습니다. |
| syslog                     | [syslog 구성](../agents/data-sources-syslog.md)을 다음과 같이 변경합니다. <br> - 수집된 기능의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 및 *디버그* 수준 이벤트를 수집하지 않습니다. |
| AzureDiagnostics           | [리소스 로그 수집](../essentials/diagnostic-settings.md#create-in-azure-portal)을 다음으로 변경합니다. <br> - Log Analytics로 보내는 리소스 송신 로그의 수 축소 <br> - 필요한 로그만 수집 |
| 솔루션을 사용하지 않는 컴퓨터의 솔루션 데이터 | [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다. |
| Application Insights | [Application Insights 데이터 볼륨을 관리](../app/pricing.md#managing-your-data-volume)하기 위한 옵션을 검토합니다. |
| [SQL Analytics](../insights/azure-sql.md) | [Set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit)를 사용하여 감사 설정을 튜닝합니다. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>노드당 가격 책정 계층에서 청구되는 노드를 가져오기

작업 영역이 레거시 노드당 가격 책정 계층에 있는 경우, 노드로 요금이 청구되는 컴퓨터 목록을 가져오려면 **청구된 데이터 형식**(일부 데이터 형식은 무료)을 전송하는 노드를 찾습니다. 이렇게 하려면 [_IsBillable 속성](./log-standard-columns.md#_isbillable)을 사용하고 정규화된 도메인 이름(FQDN)의 맨 왼쪽 필드를 사용합니다. 이는 (노드 수를 계산하고 그 요금이 청구되는 단위인) 시간당 데이터 요금이 청구되는 컴퓨터의 수를 다음과 같이 반환합니다.

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>보안 및 Automation 노드의 수 가져오기

고유한 보안 노드의 수를 보려면 다음 쿼리를 사용할 수 있습니다.

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

고유한 Automation 노드의 수를 보려면 다음 쿼리를 사용합니다.

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>레거시 노드당 가격 책정 계층 평가

레거시 **노드당** 가격 책정 계층에 액세스할 수 있는 작업 영역이 해당 계층, 현재 **종량제**, **약정 계층** 중 어느 곳에서 더 나은지에 대한 판단은 고객들이 평가하기가 어려운 경우가 많습니다.  여기에는 노드당 가격 책정 계층에서 모니터링되는 노드당 고정 비용과 이 계층에 포함된 일일 노드당 500MB의 데이터 할당량 그리고 종량제(GB당) 계층의 수집 데이터에 대한 요금 결제 비용 간에 절충값을 파악하는 과정이 포함됩니다. 

이러한 평가를 보다 수월하게 진행하기 위해 다음 쿼리를 사용하여 작업 영역의 사용 패턴을 기반으로 최적의 가격 책정 계층을 추천할 수 있습니다. 이 쿼리는 지난 7일 동안 작업 영역에서 모니터링된 노드 및 수집된 데이터를 각각 확인하며 각 날짜별로 가장 적합한 가격 책정 계층을 평가합니다. 쿼리를 사용하려면 다음을 지정해야 합니다.

- **workspaceHasSecurityCenter** 를 **true** 또는 **false** 로 설정하여 작업 영역에서 Azure Defender(Security Center)를 사용하는지 여부 
- 특정 할인이 있는 경우 가격을 업데이트합니다.
- **daysToEvaluate** 를 설정하여 되돌아가 분석할 기간(일 수)를 지정해야 합니다. 이 방법은 쿼리가 7일간의 데이터를 확인하는 데 너무 많은 시간이 걸리는 경우에 도움이 됩니다. 

가격 책정 계층 권장 사항 쿼리는 다음과 같습니다.

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CommitmentTier100Price = 196.; // Enter your price for the 100 GB/day commitment tier
let CommitmentTier200Price = 368.; // Enter your price for the 200 GB/day commitment tier
let CommitmentTier300Price = 540.; // Enter your price for the 300 GB/day commitment tier
let CommitmentTier400Price = 704.; // Enter your price for the 400 GB/day commitment tier
let CommitmentTier500Price = 865.; // Enter your price for the 500 GB/day commitment tier
let CommitmentTier1000Price = 1700.; // Enter your price for the 1000 GB/day commitment tier
let CommitmentTier2000Price = 3320.; // Enter your price for the 2000 GB/day commitment tier
let CommitmentTier5000Price = 8050.; // Enter your price for the 5000 GB/day commitment tier
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CommitmentTier100DailyCost = CommitmentTier100Price + max_of(billableGB - 100, 0.)* CommitmentTier100Price/100.
| extend CommitmentTier200DailyCost = CommitmentTier200Price + max_of(billableGB - 200, 0.)* CommitmentTier200Price/200.
| extend CommitmentTier300DailyCost = CommitmentTier300Price + max_of(billableGB - 300, 0.)* CommitmentTier300Price/300.
| extend CommitmentTier400DailyCost = CommitmentTier400Price + max_of(billableGB - 400, 0.)* CommitmentTier400Price/400.
| extend CommitmentTier500DailyCost = CommitmentTier500Price + max_of(billableGB - 500, 0.)* CommitmentTier500Price/500.
| extend CommitmentTier1000DailyCost = CommitmentTier1000Price + max_of(billableGB - 1000, 0.)* CommitmentTier1000Price/1000.
| extend CommitmentTier2000DailyCost = CommitmentTier2000Price + max_of(billableGB - 2000, 0.)* CommitmentTier2000Price/2000.
| extend CommitmentTier5000DailyCost = CommitmentTier5000Price + max_of(billableGB - 5000, 0.)* CommitmentTier5000Price/5000.
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CommitmentTier100DailyCost,CommitmentTier200DailyCost,
    CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CommitmentTier100DailyCost, "Commitment tier (100 GB/day)",
    MinCost == CommitmentTier200DailyCost, "Commitment tier (200 GB/day)",
    MinCost == CommitmentTier300DailyCost, "Commitment tier (300 GB/day)",
    MinCost == CommitmentTier400DailyCost, "Commitment tier (400 GB/day)",
    MinCost == CommitmentTier500DailyCost, "Commitment tier (500 GB/day)",
    MinCost == CommitmentTier1000DailyCost, "Commitment tier (1000 GB/day)",
    MinCost == CommitmentTier2000DailyCost, "Commitment tier (2000 GB/day)",
    MinCost == CommitmentTier5000DailyCost, "Commitment tier (5000 GB/day)",
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CommitmentTier100DailyCost, CommitmentTier200DailyCost, CommitmentTier300DailyCost, CommitmentTier400DailyCost, CommitmentTier500DailyCost, CommitmentTier1000DailyCost, CommitmentTier2000DailyCost, CommitmentTier5000DailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

이 쿼리는 사용량이 계산되는 방식을 정확하게 복제한 것은 아니지만 대개의 경우 가격 책정 계층 권장 사항을 제공하는 데 사용됩니다.  

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="create-an-alert-when-data-collection-is-high"></a>데이터 수집량이 많을 때 경고 만들기

이 섹션에서는 Azure Monitor [로그 경고](../alerts/alerts-unified-log.md)를 사용하여 지난 24시간 동안 지정된 양을 초과한 데이터 볼륨에 대한 경고를 만드는 방법에 대해 설명합니다. 

지난 24시간 동안 수집된 청구 가능한 데이터 볼륨이 50GB보다 큰 경우 경고하려면 

- **경고 조건 정의** 는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건** 은 다음을 지정합니다.
   - **신호 이름** 은 **로그 검색 사용자 지정** 을 선택합니다.
   - **검색 쿼리** 를 `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50`으로 지정합니다.  
   - **경고 논리** 는 결과 수에 **기반** 하고 **조건** 은 *0* 의 **임계값** 을 초과합니다.
   - **기간** 을 *1,440* 분으로, **경고 빈도** 를 *1,440* 분마다로 지정합니다(하루에 한 번 실행하도록).
- **경고 세부 정보 정의** 는 다음을 지정합니다.
   - **이름** 을 *24시간 내에 50GB를 초과하는 청구 가능한 데이터 볼륨* 으로 지정합니다.
   - **심각도** 를 *경고* 로

기존 또는 새 [작업 그룹](../alerts/action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

경고를 받는 경우 사용량이 예상보다 높은 원인을 해결하는 방법에 대한 위 섹션의 단계를 사용합니다.

## <a name="data-transfer-charges-using-log-analytics"></a>Log Analytics를 사용하여 데이터 전송 요금 청구

Log Analytics로 데이터를 전송하면 데이터 대역폭 요금이 발생할 수 있습니다. 그렇지만 Log Analytics 에이전트가 설치된 Virtual Machines로 제한되며, 진단 설정을 사용하거나 Azure Sentinel에 기본 제공되는 다른 커넥터를 사용하는 경우에는 적용되지 않습니다. [Azure Bandwidth 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)에 설명된 바와 같이, 두 지역의 Azure 서비스 간 데이터 전송 요금은 일반 요율을 적용한 아웃바운드 데이터 전송 요금으로 청구되었습니다. 인바운드 데이터 전송은 무료입니다. 다만 이 요금은 Log Analytics 데이터 수집에 드는 비용에 비해 매우 적습니다. 따라서 Log Analytics에 대한 비용을 제어하려면 [수집된 데이터 볼륨](#understanding-ingested-data-volume)에 집중해야 합니다. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics에서 더 이상 데이터를 수집하지 않는 문제 해결

레거시 무료 가격 책정 계층을 사용 중이고 하루에 500MB 이상의 데이터를 보낸 경우 남은 날 동안 데이터 수집이 중지됩니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다. Log Analytics는 데이터 수집을 시작하고 중지할 때 **Operation** 형식의 이벤트를 만듭니다. 일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

데이터 수집이 중지되는 경우 **OperationStatus** 가 **Warning** 입니다. 데이터 수집이 시작되는 경우 **OperationStatus** 가 **Succeeded** 입니다. 다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.

|수집 중지 이유| 해결 방법| 
|-----------------------|---------|
|작업 영역의 일일 상한에 도달함|수집이 자동으로 다시 시작될 때까지 대기하거나, 최대 일일 데이터 볼륨 관리의 설명처럼 일일 데이터 볼륨 한도를 늘립니다. 일일 상한 다시 설정 시간이 **일일 상한** 페이지에 표시됩니다. |
| 작업 영역에서 [데이터 수집 볼륨 속도](../service-limits.md#log-analytics-workspaces)에 도달함 | 진단 설정을 사용하여 Azure 리소스에서 전송된 데이터에 대한 기본 수집 볼륨 속도 제한은 대략 작업 영역당 6GB/분입니다. 실제 크기는 로그 길이와 압축 비율에 따라 데이터 형식마다 달라질 수 있으므로 이 값은 근사값입니다. 이 제한은 에이전트 또는 데이터 수집기 API에서 전송된 데이터에는 적용되지 않습니다. 데이터를 더 높은 속도로 단일 작업 영역으로 보내는 경우 일부 데이터가 삭제되고, 임계값을 계속 초과하는 동안 6시간마다 이벤트가 작업 영역에서 Operation 테이블로 전송됩니다. 수집 볼륨이 계속해서 속도 제한을 초과하거나 곧 도달할 것으로 예상되는 경우 LAIngestionRate@microsoft.com으로 이메일을 보내거나 지원 요청을 열어 작업 영역에 대한 증가를 요청할 수 있습니다. 쿼리 `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`를 통해 데이터 수집 속도 제한을 나타내는 이벤트를 찾을 수 있습니다. |
|레거시 무료 가격 책정 계층의 일일 한도에 도달함 |수집이 다음 날에 자동으로 다시 시작될 때까지 대기 또는 유료 가격 책정 계층으로 변경합니다.|
|Azure 구독이 다음으로 인해 일시 중단된 상태:<br> 평가판 종료<br> Azure 암호 만료<br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)|유료 구독으로 전환<br> 한도 제거 또는 한도가 재설정될 때까지 대기|

데이터 수집이 중지될 때 알림을 받으려면 [일일 상한에 도달한 경우 경고](#alert-when-daily-cap-is-reached) 섹션에 설명된 단계를 사용합니다. 경고 규칙에 대한 메일, 웹후크 또는 Runbook 작업을 구성하려면 [작업 그룹 만들기](../alerts/action-groups.md)에 설명된 단계를 사용합니다. 

## <a name="limits-summary"></a>제한 요약

몇 가지 추가적인 Log Analytics 제한이 있으며, 그 중 일부는 Log Analytics 가격 책정 계층에 따라 달라집니다. 이러한 제한은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)에서 설명하고 있습니다.


## <a name="next-steps"></a>다음 단계

- 검색 언어를 사용하는 방법을 알아보려면 [Azure Monitor 로그에서 로그 검색](../logs/log-query-overview.md)을 참조하세요. 사용량 현황 데이터에 대한 추가 분석을 수행하려면 검색 쿼리를 사용할 수 있습니다.
- [새 로그 경고 만들기](../alerts/alerts-metric.md)에 설명한 단계를 사용하여 검색 기준이 충족되는 경우 알림을 받을 수 있습니다.
- [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다.
- [Azure Defender(Security Center) 필터링 정책](../../security-center/security-center-enable-data-collection.md)을 검토하여 효과적인 이벤트 컬렉션 정책을 구성합니다.
- [성능 카운터 구성](../agents/data-sources-performance-counters.md)을 변경합니다.
- 이벤트 컬렉션 설정을 수정하려면 [이벤트 로그 구성](../agents/data-sources-windows-events.md)을 검토합니다.
- syslog 컬렉션 설정을 수정하려면 [syslog 구성](../agents/data-sources-syslog.md)을 검토합니다.
- syslog 컬렉션 설정을 수정하려면 [syslog 구성](../agents/data-sources-syslog.md)을 검토합니다.
