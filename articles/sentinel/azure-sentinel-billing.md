---
title: Azure Sentinel 비용 계획 및 관리
description: Azure Portal과 기타 방법에서 비용 분석을 사용하여 Azure Sentinel에 대한 비용 및 청구를 계획, 이해, 관리하는 방법을 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.custom: subject-cost-optimization, ignite-fall-2021
ms.topic: how-to
ms.date: 10/17/2021
ms.openlocfilehash: 8997a8a3d06fb14c4ad76fe01517a7f59c0944eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060861"
---
# <a name="plan-and-manage-costs-for-azure-sentinel"></a>Azure Sentinel 비용 계획 및 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Azure Sentinel을 계획하고 비용을 관리하는 방법을 설명합니다. 서비스를 위한 리소스를 추가하기 전에 먼저 Azure 가격 계산기를 사용하여 Azure Sentinel 비용을 계획합니다. 그런 다음, Azure 리소스를 추가할 때 예상 비용을 검토합니다.

Azure Sentinel 리소스 사용을 시작한 후 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 또한 예상 비용을 검토하고 지출 추세를 식별하여 조치를 취해야 할 영역을 식별할 수 있습니다. 이 문서에서는 Azure Sentinel 비용을 관리하고 최적화하는 여러 방법을 설명합니다.

Azure Sentinel 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 Azure Sentinel 비용을 계획하고 관리하는 방법을 설명하지만, 사용자에게는 파트너 서비스를 포함하여 Azure 구독에서 사용하는 모든 Azure 서비스 및 리소스 요금이 청구됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Cost Management에서 비용 데이터를 보고 비용 분석을 수행하려면 최소한 읽기 액세스 권한이 있는 지원되는 Azure 계정 유형이 있어야 합니다.

    Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만, 모두가 지원되는 것은 아닙니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요.

    Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

- 데이터 원본에 대한 세부 정보가 있어야 합니다. Azure Sentinel을 사용하면 하나 이상의 데이터 원본에서 데이터를 가져올 수 있습니다. 이러한 데이터 원본 중 일부는 무료이며 나머지는 요금이 부과됩니다. 자세한 내용은 [무료 데이터 원본](#free-data-sources)을 참조하세요.

## <a name="estimate-costs-before-using-azure-sentinel"></a>Azure Sentinel을 사용하기 전에 비용 추정

Azure Sentinel을 아직 사용하지 않는 경우 [Azure Sentinel 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel)를 사용하여 잠재 비용을 예측할 수 있습니다. 검색 상자에서 *Azure Sentinel* 을 입력하고, 결과 Azure Sentinel 타일을 선택합니다. 가격 계산기를 사용하면 예상되는 데이터 수집 및 보존을 기반으로 하여 적절한 비용을 예측할 수 있습니다.

예를 들어 Azure Sentinel에서 수집할 것으로 예상되는 일일 데이터의 GB와 작업 영역에 대한 지역을 입력할 수 있습니다. 계산기는 다음 구성 요소에 대한 월별 집계 비용을 제공합니다.

- Log Analytics 데이터 수집
- Azure Sentinel 데이터 분석
- Log Analytics;데이터 보존

> [!NOTE]
> 이 이미지에 표시된 비용은 예시용으로만 제공됩니다. 실제 비용을 반영하기 위한 것이 아닙니다.

:::image type="content" source="media/billing/pricing-calculator.png" alt-text="Azure 가격 계산기의 예측 비용을 보여 주는 샘플 스크린샷" lightbox="media/billing/pricing-calculator.png" :::

## <a name="understand-the-full-billing-model-for-azure-sentinel"></a>Azure Sentinel에 대한 전체 청구 모델 이해

Azure Sentinel은 유연하고 예측 가능한 가격 책정 모델을 제공합니다. 자세한 내용은 [Azure Sentinel 가격 페이지](https://azure.microsoft.com/pricing/details/azure-sentinel/)를 참조하세요. 관련 Log Analytics 요금은 [Azure Monitor Log Analytics 가격](https://azure.microsoft.com/pricing/details/log-analytics/)을 참조하세요.

Azure Sentinel은 Azure 인프라에서 실행되며 새 리소스를 배포할 때 비용이 발생합니다. 다른 추가 인프라 비용을 발생 시킬 수 있음을 이해 하는 것이 중요 합니다.
### <a name="how-youre-charged-for-azure-sentinel"></a>Azure Sentinel 요금이 청구되는 방식

Azure Sentinel 서비스에 대한 비용을 지불하는 방법에는 **종량제** 및 **약정 계층** 의 두 가지가 있습니다.


- **종량제** 는 저장된 실제 데이터 볼륨을 기반으로 하며, 선택적으로 90일을 초과하는 데이터 보존에 적합한 기본 모델입니다. 데이터 볼륨은 GB(10^9바이트)로 측정됩니다.

- Log Analytics 및 Azure Sentinel에는 이전에 용량 예약이라고 한 **약정 계층** 가격 책정도 있습니다. 이는 종량제 가격 책정에 비해 예측 가능성이 더 높고 최대 65%까지 절약됩니다.

    약정 계층 가격 책정을 사용하면 100GB/일부터 시작하는 약정을 구입할 수 있습니다. 약정 수준을 초과하는 사용량은 선택한 약정 계층 요금으로 청구됩니다. 예를 들어 100 g b의 약정 계층은 커밋된 100 GB 데이터 볼륨에 대 한 요금을 청구 하 고 해당 계층에 대 한 할인 된 요금으로 추가 GB/일을 청구 합니다.

    언제든지 약정 계층을 늘리고 31일마다 줄여 데이터 볼륨이 증가하거나 감소함에 따라 비용을 최적화할 수 있습니다. 현재 Azure Sentinel 가격 책정 계층을 보려면 Azure Sentinel의 왼쪽 탐색 영역에서 **설정** 을 선택한 다음, **가격 책정** 탭을 선택합니다. 현재 가격 책정 계층이 **현재 계층** 으로 표시됩니다.

    약정 계층을 설정하고 변경하려면 [가격 책정 계층 설정 또는 변경](#set-or-change-pricing-tier)을 참조하세요.

### <a name="understand-your-azure-sentinel-bill"></a>Azure Sentinel 청구서 이해

청구 가능 미터는 청구서에 표시되는 서비스의 개별 구성 요소이며, 서비스의 비용 분석에도 표시됩니다. 청구 주기가 끝날 때 각 측정 단위의 요금이 합산됩니다. 청구서 또는 송장에 모든 Azure Sentinel 비용에 대한 섹션이 표시됩니다. 각 측정 단위에 대한 별도의 줄 항목이 있습니다.

Azure 청구서를 보려면 **Cost Management + Billing** 의 왼쪽 탐색 영역에서 **비용 분석** 을 선택합니다. **비용 분석** 화면의 **보기** 필드에서 드롭다운 캐럿을 선택하고, **청구서 세부 정보** 를 선택합니다.

> [!NOTE]
> 이 이미지에 표시된 비용은 예시용으로만 제공됩니다. 실제 비용을 반영하기 위한 것이 아닙니다.

![Azure 청구서 샘플의 Azure Sentinel 섹션을 보여 주는 스크린샷](media/billing/sample-bill.png)

Azure Sentinel 및 Log Analytics 요금은 선택한 요금제에 따라 Azure 청구서에 별도의 품목으로 표시됩니다. 특정 월에 작업 영역의 약정 계층 사용량을 초과하는 경우 Azure 청구서에는 관련 고정 비용이 있는 약정 계층에 대한 하나의 품목과 약정 계층을 초과하는 수집에 대한 별도의 품목이 표시되며, 동일한 약정 계층 요금으로 청구됩니다.

다음 표에서는 Azure Sentinel 및 Log Analytics 비용이 Azure 청구서의 **서비스 이름** 및 **미터** 열에 표시되는 방식을 보여 줍니다.

|비용|서비스 이름|미터|
|----|------------|----------------|
|Azure Sentinel 약정 계층|**sentinel**|**`n`GB 약정 계층**|
|Log Analytics 약정 계층|**azure monitor**|**`n`GB 약정 계층**|
|약정 계층 또는 종량제에 대한 Azure Sentinel 초과분|**sentinel**|**analysis**|
|약정 계층 또는 종량제에 대한 Log Analytics 초과분|**log analytics**|**데이터 수집**|

Azure 청구서 보기 및 다운로드에 대한 자세한 내용은 [Azure 비용 및 청구 정보](../cost-management-billing/understand/download-azure-daily-usage.md)를 참조하세요.

### <a name="costs-for-other-services"></a>다른 서비스에 대한 비용

Azure Sentinel은 다른 많은 Azure 서비스와 통합되어 향상된 기능을 제공합니다. 이러한 서비스로 Azure Logic Apps, Azure Notebooks 및 BYOML(Bring Your Own Machine Learning) 모델이 있습니다. 이러한 서비스 중 일부에는 추가 요금이 부과 될 수 있습니다. Azure Sentinel의 데이터 커넥터 및 솔루션 중 일부는 데이터 수집을 위해 Azure Functions를 사용하며, 여기에도 별도의 관련 비용이 있습니다.

이러한 서비스에 대한 자세한 가격 책정 세부 정보는 다음을 참조하세요.

- [Automation - Logic Apps 가격](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Notebooks 가격](https://azure.microsoft.com/pricing/details/machine-learning/)
- [BYOML 가격](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Azure Functions 가격](https://azure.microsoft.com/pricing/details/functions/)

사용하는 다른 서비스에는 관련 비용이 있을 수 있습니다.

### <a name="data-retention-costs"></a>데이터 보존 비용

Log Analytics 작업 영역에서 Azure Sentinel을 사용하도록 설정하면 작업 영역에 수집된 모든 데이터를 처음 90일 동안 무료로 유지할 수 있습니다. 90일을 초과하는 보존은 표준 [Log Analytics 보존 가격](https://azure.microsoft.com/pricing/details/monitor/)에 따라 청구됩니다.

개별 데이터 형식에 대해 다른 보존 설정을 지정할 수 있습니다. 자세한 내용은 [데이터 형식별 보존 기간](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type)을 참조하세요.

### <a name="other-cef-ingestion-costs"></a>기타 CEF 수집 비용

CEF는 Azure Sentinel에서 지원되는 Syslog 이벤트 형식입니다. CEF를 사용 하 여 다양 한 원본에서 Azure 센티널 작업 영역으로 중요 한 보안 정보를 가져올 수 있습니다. CEF 로그는 최신의 모든 표준 CEF 필드를 포함하는 Azure Sentinel의 CommonSecurityLog 테이블에 있습니다.

많은 디바이스 및 데이터 원본에는 표준 CEF 스키마 이외의 로깅 필드가 허용됩니다. 이러한 추가 필드는 AdditionalExtensions 테이블에 배치 됩니다. 이러한 필드 내의 이벤트 콘텐츠는 가변적일 수 있으므로 이러한 필드에는 표준 CEF 필드보다 더 높은 수집 볼륨이 있을 수 있습니다.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>리소스 삭제 후에도 누적될 수 있는 비용

Azure Sentinel을 제거해도 Azure Sentinel이 배포된 Log Analytics 작업 영역 또는 작업 영역에서 발생할 수 있는 별도의 요금은 제거되지 않습니다.

### <a name="free-trial"></a>평가판

처음 31일 동안 Azure Sentinel 무료로 사용해 보세요. Azure Sentinel 아래에 명시된 제한에 따라 Azure Monitor Log Analytics 작업 영역에서 추가 비용 없이 사용하도록 설정할 수 있습니다.

- **새 Log Analytics 작업 영역은** 처음 31일 동안 최대 10GB의 로그 데이터를 무료로 수집할 수 있습니다. 새 작업 영역에는 3일 미만의 작업 영역이 포함됩니다.

   Log Analytics 데이터 수집 및 Azure Sentinel 요금은 모두 31일 평가 기간 동안 포기됩니다. 이 무료 평가판에는 Azure 테넌트당 20개 작업 영역 제한이 적용됩니다.

- **기존 Log Analytics 작업 영역은** 추가 비용 없이 Azure Sentinel 수 있습니다. 기존 작업 영역에는 3일 전에 만든 모든 작업 영역이 포함됩니다.

   31일 평가 기간 동안에는 Azure Sentinel 요금만 청구됩니다.

이러한 한도를 초과하는 사용량은 Azure Sentinel 가격 책정 페이지에 나열된 [가격 책정에](https://azure.microsoft.com/pricing/details/azure-sentinel) 따라 요금이 청구됩니다. [자동화](automation-in-azure-sentinel.md) 및 [사용자 고유의 기계 학습을](bring-your-own-ml.md) 위한 추가 기능과 관련된 요금은 평가판 중에 계속 적용됩니다.

> [!TIP]
> 평가판 중에 비용 관리, 교육 등에 대한 리소스는 **Azure Sentinel 뉴스 & 가이드 > 무료 평가판** 탭에서 찾을 수 있습니다.
>
> 이 탭에는 평가판 날짜 및 만료될 때까지 남은 기간(일)에 대한 세부 정보도 표시됩니다.
>
### <a name="free-data-sources"></a>무료 데이터 원본

Azure Sentinel에서 무료인 데이터 원본은 다음과 같습니다.

- Azure 활동 로그
- Office 365 감사 로그(모든 SharePoint 활동, Exchange 관리자 활동 및 Teams 포함)
- Microsoft Defender 경고(Azure Defender, Microsoft 365 Defender, Microsoft Defender for Office 365, Microsoft Defender for Identity 및 Microsoft Defender for Endpoint의 경고 포함)
- Azure Security Center 및 MCAS(Microsoft Cloud App Security) 경고 그러나 일부 Microsoft 365 Defender, MCAS, Azure AD(Azure Active Directory) 및 AIP(Azure Information Protection) 데이터 형식에 대한 원시 로그는 유료입니다.

다음 표에는 Azure Sentinel에서 사용하도록 설정할 수 있는 무료 데이터 원본이 나와 있습니다. Microsoft 365 Defender 및 MCAS와 같은 일부 데이터 커넥터에는 무료 및 유료 데이터 형식이 모두 포함되어 있습니다.

| Azure Sentinel 데이터 커넥터   | 데이터 형식 | 무료 또는 유료 |
|-------------------------------------|--------------------------------|------------------|
| **Azure 활동 로그**         | AzureActivity                  | 무료             |
| **Azure AD ID 보호**         | SecurityAlert(IPC)                  | 무료             |
| **Office 365**                     | OfficeActivity(SharePoint)    | 무료|
|| OfficeActivity(Exchange)|무료|
|| OfficeActivity(Teams)          | 무료|
| **Azure Defender**                  | SecurityAlert(ASC)             | 무료             |
| **IoT용 Azure Defender**          | SecurityAlert(IoT용 ASC)     | 무료             |
| **Microsoft 365 Defender**          | SecurityIncident | 무료|
||SecurityAlert| 무료|
||DeviceEvents                    | 유료|
||DeviceFileEvents                | 유료|
||DeviceImageLoadEvents           | 유료|
||DeviceInfo                      | 유료|
||DeviceLogonEvents               | 유료|
||DeviceNetworkEvents             | 유료|
||DeviceNetworkInfo               | 유료|
||DeviceProcessEvents             | 유료|
||DeviceRegistryEvents            | 유료|
||DeviceFileCertificateInfo       | 유료|
| **엔드포인트에 대한 Microsoft Defender** | SecurityAlert(MDATP)          | 무료             |
| **Microsoft Defender for Identity** | SecurityAlert(AATP)           | 무료             |
| **Microsoft Cloud App Security**   | SecurityAlert(MCAS)           | 무료             |
||MCASShadowITReporting           | 유료|

무료 및 유료 데이터 형식을 모두 포함하는 데이터 커넥터의 경우 사용하도록 설정할 데이터 형식을 선택할 수 있습니다.

![무료 보안 경고가 선택되고 유료 MCASShadowITReporting이 선택되지 않은 MCAS에 대한 데이터 커넥터 페이지를 보여 주는 스크린샷](media/billing/data-types.png)

무료 및 유료 데이터 원본과 커넥터에 대한 자세한 내용은 [데이터 원본 연결](connect-data-sources.md)을 참조하세요.

> [!NOTE]
> 퍼블릭 미리 보기로 나열된 데이터 커넥터에서는 비용이 발생하지 않습니다. 데이터 커넥터의 GA(일반 공급) 이후에만 비용이 발생합니다.
>

## <a name="estimate-azure-sentinel-costs"></a>Azure Sentinel 비용 예측

Azure Sentinel을 아직 사용하지 않는 경우 [Azure Sentinel 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel)를 사용하여 Azure Sentinel 사용량에 대한 잠재적 비용을 예측할 수 있습니다. 검색 상자에서 *Azure Sentinel* 을 입력하고, 결과 Azure Sentinel 타일을 선택합니다. 가격 계산기를 사용하면 예상되는 데이터 수집 및 보존을 기반으로 하여 적절한 비용을 예측할 수 있습니다.

예를 들어 Azure Sentinel에서 수집할 것으로 예상되는 일일 데이터의 GB와 작업 영역에 대한 지역을 입력할 수 있습니다. 계산기는 다음 구성 요소에 대한 월별 집계 비용을 제공합니다.

- Log Analytics 데이터 수집
- Azure Sentinel 데이터 분석
- Log Analytics;데이터 보존

## <a name="manage-and-monitor-azure-sentinel-costs"></a>Azure Sentinel 비용 관리 및 모니터링

Azure Sentinel과 함께 Azure 리소스를 사용하는 경우 비용이 발생합니다. Azure 리소스 사용량 단위 비용은 시간 간격(초, 분, 시간, 일) 또는 단위 사용량(바이트, 메가바이트)에 따라 달라집니다. Azure Sentinel을 시작하는 즉시 비용이 발생하며 [비용 분석](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 볼 수 있습니다.

비용 분석을 사용하면 서로 다른 시간 간격에 대한 그래프 및 테이블의 Azure Sentinel 비용을 볼 수 있습니다. 몇 가지 예로 일, 현재 및 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

[Azure Cost Management + Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md) 허브는 유용한 기능을 제공합니다. Azure Portal에서 **Cost Management + Billing** 이 열리면 왼쪽 탐색 영역에서 **Cost Management** 를 선택한 다음, 조사할 리소스의 [범위](..//cost-management-billing/costs/understand-work-scopes.md) 또는 집합(예: Azure 구독 또는 리소스 그룹)을 선택합니다.

**비용 분석** 화면에는 다양한 컨트롤과 필터를 적용하는 옵션이 있는 Azure 사용량 및 비용에 대한 자세한 보기가 표시됩니다.

예를 들어 특정 시간 프레임 동안의 일일 비용 차트를 보려면 다음을 수행합니다.
1. **보기** 필드에서 드롭다운 캐럿을 선택하고, **누적 비용** 또는 **일별 비용** 을 선택합니다.
1. 날짜 필드에서 드롭다운 캐럿을 선택하고, 날짜 범위를 선택합니다.
1. **세분성** 옆에 있는 드롭다운 캐럿을 선택하고, **매일** 을 선택합니다.

> [!NOTE]
> Azure Sentinel 데이터 수집 볼륨은 일부 포털 사용량 차트의 **보안 인사이트** 아래에 표시됩니다.

Azure Sentinel 가격 책정 계층에는 Log Analytics 요금이 포함되지 않습니다. Log Analytics에 대한 가격 책정 계층 약정을 변경하려면 [가격 책정 계층 변경](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier)을 참조하세요.

자세한 내용은 [예산 만들기](#create-budgets) 및 Azure Sentinel 비용을 관리하고 줄이는 [기타 방법을](#other-ways-to-manage-and-reduce-azure-sentinel-costs)참조하세요.

### <a name="using-azure-prepayment-with-azure-sentinel"></a>Azure Sentinel과 함께 Azure 선불 사용

Azure 선불 크레딧을 사용하여 Azure Sentinel 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용하여 타사 조직에서 제품 및 서비스 또는 Azure Marketplace 제품에 대한 요금을 지불할 수 없습니다.

### <a name="run-queries-to-understand-your-data-ingestion"></a>쿼리를 실행하여 데이터 수집 이해

Azure Sentinel은 광범위한 쿼리 언어를 사용하여 몇 초 만에 대량의 운영 데이터를 분석하고, 상호 작용하고 인사이트를 얻습니다. 데이터 수집 볼륨을 이해하는 데 사용할 수 있는 몇 가지 Kusto 쿼리는 다음과 같습니다.

다음 쿼리를 실행하여 솔루션별 데이터 수집 볼륨을 표시합니다. 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

다음 쿼리를 실행하여 데이터 형식별 데이터 수집 볼륨을 표시합니다. 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```
다음 쿼리를 실행하여 솔루션 및 데이터 형식별 데이터 수집 볼륨을 표시합니다. 

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>통합 문서를 배포하여 데이터 수집 시각화

**작업 영역 사용 현황 보고서 통합 문서** 는 작업 영역의 데이터 사용량, 비용 및 사용량 통계를 제공합니다. 통합 문서는 작업 영역의 데이터 수집 상태와 무료 및 청구 가능한 데이터의 양을 제공합니다. 통합 문서 논리를 사용하여 데이터 수집 및 비용을 모니터링하고, 사용자 지정 보기 및 규칙 기반 경고를 작성할 수 있습니다.

이 통합 문서는 세부적인 수집 정보도 제공합니다. 통합 문서는 작업 영역의 데이터를 데이터 테이블별로 분류하고, 수집 패턴을 더 잘 이해할 수 있도록 테이블 및 항목당 볼륨을 제공합니다.

작업 영역 사용 현황 보고서 통합 문서를 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Sentinel의 왼쪽 탐색 영역에서 **위협 관리** > **통합 문서** 를 차례로 선택합니다.
1. 검색 창에서 *작업 영역 사용량* 을 입력한 다음, **작업 영역 사용 현황 보고서** 를 선택합니다.
1. **템플릿 보기** 를 선택하여 통합 문서를 있는 그대로 사용하거나, **저장** 을 선택하여 통합 문서의 편집 가능한 복사본을 만듭니다. 복사본을 저장하는 경우 **저장된 통합 문서 보기** 를 선택합니다.
1. 통합 문서에서 보려는 **구독** 및 **작업 영역** 을 선택한 다음, **TimeRange** 를 보려는 시간 프레임으로 설정합니다. **도움말 표시** 토글을 **예** 로 설정하여 통합 문서에 적절한 설명을 표시할 수 있습니다.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 비용 데이터를 내보내는 것은 비용에 대해 더 많은 데이터 분석을 수행해야 하거나 다른 사람들이 필요할 때 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터 세트를 검색하려면 비용 데이터를 내보내는 것이 좋습니다.

## <a name="create-budgets"></a>예산 만들기

[예산](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. 예산 및 경고는 Azure 구독 및 리소스 그룹에 대해 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링을 더 세분화하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하는 예산을 만들 수도 있습니다. 필터는 추가 비용이 드는 새 리소스를 실수로 만들지 않도록 도움을 줍니다. 예산을 만들 때 사용할 수 있는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

### <a name="use-a-playbook-for-cost-management-alerts"></a>비용 관리 경고에 플레이북 사용

Azure Sentinel 예산을 제어하는 ​​데 도움이 되도록 비용 관리 플레이북을 만들 수 있습니다. Azure Sentinel 작업 영역에서 지정된 시간 프레임 내에 정의한 예산을 초과하는 경우 플레이북에서 경고를 보냅니다.

Azure Sentinel GitHub 커뮤니티는 GitHub에서 [Send-IngestionCostAlert](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) 비용 관리 플레이북을 제공합니다. 이 플레이북은 반복 트리거를 통해 활성화되며 높은 수준의 유연성을 제공합니다. 요구 사항에 따라 실행 빈도, 수집 볼륨 및 트리거할 메시지를 제어할 수 있습니다.

### <a name="define-a-data-volume-cap-in-log-analytics"></a>Log Analytics에서 데이터 볼륨 상한 정의

Log Analytics에서는 작업 영역에 대한 일일 수집을 제한하는 일일 볼륨 상한을 사용하도록 설정할 수 있습니다. 일일 상한은 예기치 않은 데이터 볼륨 증가를 관리하고, 상한 내에서 유지하며, 계획되지 않은 요금을 제한하는 데 도움이 될 수 있습니다.

일일 볼륨 상한을 정의하려면 Log Analytics 작업 영역의 왼쪽 탐색 영역에서 **사용량 및 예상 비용** 을 선택한 다음, **일일 상한** 을 선택합니다. **켜기** 를 선택하고, 일일 볼륨 상한 양을 입력한 다음, **확인** 을 선택합니다.

![사용량 및 예상 비용 화면과 일일 상한 창을 보여 주는 스크린샷](media/billing/daily-cap.png)

**사용량 및 예상 비용** 화면에는 지난 31일 동안 수집된 데이터 볼륨 추세와 보존된 총 데이터 볼륨도 표시됩니다.

> [!IMPORTANT]
> 일일 상한은 모든 데이터 형식의 수집을 제한하지 않습니다. 보안 데이터는 상한에서 제외됩니다. Log Analytics에서 일별 상한을 관리하는 방법에 대한 자세한 내용은 [일일 최대 데이터 볼륨 관리](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume)를 참조하세요.

## <a name="other-ways-to-manage-and-reduce-azure-sentinel-costs"></a>Azure Sentinel 비용을 관리하고 줄이기 위한 기타 방법

데이터 수집 및 보존 비용을 관리하려면 다음을 수행합니다.

- [약정 계층 가격 책정을 사용](#set-or-change-pricing-tier)하여 데이터 수집 볼륨에 따라 비용을 최적화합니다.
- [다른 작업 영역에서 비보안 데이터를 구분](#separate-non-security-data-in-a-different-workspace)합니다.
- [전용 클러스터를 사용하여 Log Analytics 비용을 최적화](#optimize-log-analytics-costs-with-dedicated-clusters)합니다.
- [ADX(Azure Data Explorer)를 사용하여 장기 데이터 보존 비용을 줄입니다](#reduce-long-term-data-retention-costs-with-adx).
- [Windows 보안 이벤트에 대한 데이터 수집 규칙을 사용합니다](#use-data-collection-rules-for-your-windows-security-events).

### <a name="set-or-change-pricing-tier"></a>가격 책정 계층 설정 또는 변경

최대 절감 효과를 위해 최적화하려면 수집 볼륨을 모니터링하여 수집 볼륨 패턴과 가장 가깝게 일치하는 약정 계층이 있는지 확인합니다. 변경되는 데이터 볼륨에 맞게 약정 계층을 늘리거나 줄일 수 있습니다.

약정 계층은 언제든지 늘릴 수 있으며, 이 경우 31일의 약정 기간이 다시 시작됩니다. 그러나 종량제 또는 더 낮은 약정 계층으로 다시 이동하려면 31일의 약정 기간이 끝날 때까지 기다려야 합니다. 약정 계층에 대한 요금은 일일 기준으로 청구됩니다.

현재 Azure Sentinel 가격 책정 계층을 보려면 Azure Sentinel의 왼쪽 탐색 영역에서 **설정** 을 선택한 다음, **가격 책정** 탭을 선택합니다. 현재 가격 책정 계층이 **현재 계층** 으로 표시됩니다.

가격 책정 계층 약정을 변경하려면 가격 책정 페이지에서 다른 계층 중 하나를 선택한 다음, **적용** 을 선택합니다. 가격 책정 계층을 변경하려면 Azure Sentinel에서 **기여자** 또는 **소유자** 역할이 있어야 합니다.

![종량제가 현재 가격 책정 계층으로 표시된 Azure Sentinel 설정의 가격 책정 페이지를 보여 주는 스크린샷](media/billing/pricing.png)

> [!NOTE]
> Azure Sentinel 데이터 수집 볼륨은 일부 포털 사용량 차트의 **보안 인사이트** 아래에 표시됩니다.

Azure Sentinel 가격 책정 계층에는 Log Analytics 요금이 포함되지 않습니다. Log Analytics에 대한 가격 책정 계층 약정을 변경하려면 [가격 책정 계층 변경](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier)을 참조하세요.

### <a name="separate-non-security-data-in-a-different-workspace"></a>다른 작업 영역에서 비보안 데이터 구분

Azure Sentinel은 Azure Sentinel 사용 Log Analytics 작업 영역에 수집된 모든 데이터를 분석합니다. Azure Sentinel 비용이 발생하지 않도록 비보안 작업 데이터에 대한 별도의 작업 영역이 있는 것이 가장 좋습니다.

Azure Sentinel에서 위협을 헌팅하거나 조사하는 경우 이러한 독립 실행형 Azure Log Analytics 작업 영역에 저장된 운영 데이터에 액세스해야 할 수 있습니다. 이 데이터는 로그 검색 환경 및 통합 문서에서 작업 영역 간 쿼리를 사용하여 액세스할 수 있습니다. 그러나 모든 작업 영역에서 Azure Sentinel을 사용하도록 설정하지 않으면 작업 영역 간 분석 규칙 및 헌팅 쿼리를 사용할 수 없습니다.

### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>전용 클러스터를 사용하여 Log Analytics 비용 최적화

동일한 지역의 Azure Sentinel 작업 영역 또는 작업 영역에 최소 500GB를 기록하는 경우 비용을 줄이기 위해 Log Analytics 전용 클러스터로 이동하는 것이 좋습니다. Log Analytics 전용 클러스터 약정 계층은 총 500GB 이상을 통칭하여 수집되는 작업 영역에서 데이터 볼륨을 집계합니다.

Log Analytics 전용 클러스터는 Azure Sentinel 약정 계층에 적용되지 않습니다. Azure Sentinel 비용은 여전히 전용 클러스터의 작업 영역별로 적용됩니다.

여러 Azure Sentinel 작업 영역을 Log Analytics 전용 클러스터에 추가할 수 있습니다. Azure Sentinel용 Log Analytics 전용 클러스터를 사용하는 경우 다음과 같은 두 가지 이점이 있습니다.

- 쿼리와 관련된 모든 작업 영역이 전용 클러스터에 있는 경우 작업 영역 간 쿼리가 더 빠르게 실행됩니다. 아직도 환경에서 작업 영역을 가능한 한 적게 유지하는 것이 가장 좋으며, 전용 클러스터는 작업 영역 간 단일 쿼리에 포함하기 위해 여전히 [100개의 작업 영역 제한](../azure-monitor/logs/cross-workspace-query.md)을 유지합니다.

- 전용 클러스터의 모든 작업 영역은 클러스터에 설정된 Log Analytics 약정 계층을 공유할 수 있습니다. 각 작업 영역에 대해 별도의 Log Analytics 약정 계층을 약정할 필요가 없으므로 비용을 절감하고 효율성을 높일 수 있습니다. 전용 클러스터를 사용하도록 설정하면 하루에 500GB의 최소 Log Analytics 약정 계층으로 커밋할 수 있습니다.

비용 최적화를 위해 전용 클러스터로 이동할 때 고려해야 할 몇 가지 사항은 다음과 같습니다.

- 지역 및 구독당 최대 클러스터 수는 2개입니다.
- 클러스터에 연결되는 모든 작업 영역은 동일한 지역에 있어야 합니다.
- 클러스터에 연결되는 최대 작업 영역은 1,000개입니다.
- 클러스터에서 연결된 작업 영역의 연결을 해제할 수 있습니다. 특정 작업 영역에 대한 연결 작업의 수는 30일 동안 2개로 제한됩니다.
- 기존 작업 영역을 CMK(고객 관리형 키) 클러스터로 이동할 수 없습니다. 클러스터에서 작업 영역을 만들어야 합니다.
- 클러스터를 다른 리소스 그룹 또는 구독으로 이동하는 것은 현재 지원되지 않습니다.
- 작업 영역이 다른 클러스터에 연결된 경우 클러스터에 대한 작업 영역 연결이 실패합니다.

전용 클러스터에 대한 자세한 내용은 [Log Analytics 전용 클러스터](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters)를 참조하세요.

### <a name="reduce-long-term-data-retention-costs-with-adx"></a>ADX를 사용하여 장기 데이터 보존 비용 절감

Azure Sentinel 데이터 보존은 처음 90일 동안 무료입니다. Log Analytics에서 데이터 보존 기간을 조정하려면 왼쪽 탐색 영역에서 **사용량 및 예상 비용** 을 선택하고, **데이터 보존** 을 선택한 다음, 슬라이더를 조정합니다.

Azure Sentinel 보안 데이터는 몇 개월 후에 일부 값이 손실될 수 있습니다. SOC(보안 운영 센터) 사용자는 최신 데이터만큼 이전 데이터에 자주 액세스할 필요가 없지만, 산발적인 조사 또는 감사를 위해 데이터에 액세스해야 할 수도 있습니다. Azure Sentinel 데이터 보존 비용을 줄이기 위해 Azure Data Explorer를 사용하여 데이터를 더 저렴한 비용으로 장기간 보존할 수 있습니다. ADX는 더 이상 Azure Sentinel 보안 인텔리전스가 필요하지 않은 오래된 데이터에 대해 비용과 유용성의 적절한 균형을 제공합니다.

ADX를 사용하면 데이터를 더 저렴한 가격으로 저장할 수 있지만, Azure Sentinel에서와 동일한 KQL(Kusto 쿼리 언어) 쿼리를 사용하여 데이터를 검색할 수 있습니다. ADX 프록시 기능을 사용하여 플랫폼 간 쿼리를 수행할 수도 있습니다. 이러한 쿼리는 ADX, Application Insights, Azure Sentinel 및 Log Analytics에 분산된 데이터를 집계하고 상관 관계를 지정합니다.

자세한 내용은 [장기 로그 보존을 위한 Azure Data Explorer 통합](store-logs-in-azure-data-explorer.md)을 참조하세요.

### <a name="use-data-collection-rules-for-your-windows-security-events"></a>Windows 보안 이벤트에 대한 데이터 수집 규칙 사용

[Windows 보안 이벤트 커넥터](connect-windows-security-events.md?tabs=LAA)를 사용하면 물리적, 가상, 온-프레미스 서버 또는 클라우드를 포함하여 Azure Sentinel 작업 영역에 연결된 Windows Server를 실행하는 컴퓨터에서 보안 이벤트를 스트리밍할 수 있습니다. 이 커넥터에는 데이터 수집 규칙을 사용하여 각 에이전트에서 수집할 데이터를 정의하는 Azure Monitor 에이전트에 대한 지원이 포함되어 있습니다. 

데이터 수집 규칙을 사용하면 수집 설정을 대규모로 관리하면서도 계속해서 머신의 하위 집합에 대해 고유하고 범위가 지정된 구성을 사용할 수 있습니다. 자세한 정보는 [Azure Monitor 에이전트를 위한 데이터 수집 구성](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md)을 참조하세요.

모든 이벤트, 최소 또는 일반과 같이 수집하도록 선택할 수 있는 미리 정의된 이벤트 집합 외에도 데이터 수집 규칙을 사용하면 사용자 지정 필터를 빌드하고 수집할 특정 이벤트를 선택할 수 있습니다. Azure Monitor 에이전트는 이러한 규칙을 사용하여 원본의 데이터를 필터링한 다음, 선택한 이벤트만 수집하는 한편 다른 모든 이벤트는 남겨 둡니다. 수집할 특정 이벤트를 선택하면 비용을 최적화하고 더 줄일 수 있습니다.

> [!NOTE]
> 이 이미지에 표시된 비용은 예시용으로만 제공됩니다. 실제 비용을 반영하기 위한 것이 아닙니다.

![Cost Management + Billing 비용 분석 화면을 보여 주는 스크린샷](media/billing/cost-management.png)

추가 컨트롤을 적용할 수도 있습니다. 예를 들어 Azure Sentinel과 관련된 비용만 보려면 **필터 추가** 를 선택하고, **서비스 이름** 을 선택한 다음, **sentinel**, **log analytics** 및 **azure monitor** 를 선택합니다.

## <a name="next-steps"></a>다음 단계
- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수강합니다.
- Log Analytics 데이터 볼륨을 줄일 수 있는 방법에 대한 추가 팁은 [데이터 볼륨을 줄이기 위한 팁](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume)을 참조하세요.
