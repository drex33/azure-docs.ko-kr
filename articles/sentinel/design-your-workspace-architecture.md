---
title: Azure Sentinel 작업 영역 아키텍처 디자인 | Microsoft Docs
description: 의사 결정 트리를 사용하여 Azure Sentinel 작업 영역 아키텍처를 디자인하는 방법을 이해합니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2744552b358fa90180ed085c50b23404473d1745
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037248"
---
# <a name="design-your-azure-sentinel-workspace-architecture"></a>Azure Sentinel 작업 영역 아키텍처 디자인

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Azure Sentinel 작업 영역 아키텍처를 디자인하는 방법에 대한 주요 결정을 내리는 데 도움이 되는 의사 결정 트리를 제공합니다. 자세한 내용은 [Azure Sentinel 샘플 작업 영역 디자인](sample-workspace-designs.md) 및 [Azure Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

의사 결정 트리를 살펴보기 전에 다음 정보가 있는지 확인합니다.

|필수 요소  | Description |
|---------|---------|
|**Azure 데이터 보존과 관련된 규정 요구 사항**     |  Azure Sentinel은 대부분의 작업 영역에서 실행할 수 있지만, 일부 지역은 [Log Analytics GA](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)에서 지원되지 않습니다. 새로 지원되는 Log Analytics 지역에서 Azure Sentinel 서비스를 온보딩하는 데 시간이 걸릴 수 있습니다. <br><br> 인시던트, 책갈피 및 분석 규칙과 같이 Azure Sentinel에 의해 생성된 데이터에는 고객의 Log Analytics 작업 영역에서 소싱된 일부 고객 데이터가 포함될 수 있습니다.<br><br> 자세한 내용은 [지리적 가용성 및 데이터 보존](quickstart-onboard.md#geographical-availability-and-data-residency)을 참조하세요.|
|**데이터 원본**     |   Microsoft 솔루션 및 타사 솔루션 모두에 대한 기본 제공 커넥터를 포함하여 연결해야 하는 [데이터 원본](connect-data-sources.md)을 확인합니다. CEF(Common Event Format), Syslog 또는 REST API를 사용하여 Azure Sentinel에 데이터 원본을 연결할 수도 있습니다. <br><br>Azure VM이 로그를 수집해야 하는 여러 Azure 위치에 있고 데이터 송신 비용을 절약하는 것이 중요한 경우 각 Azure 위치에 대한 [대역폭 가격 계산기](https://azure.microsoft.com/pricing/details/bandwidth/#overview)를 사용하여 데이터 송신 비용을 계산해야 합니다.      |
|**사용자 역할 및 데이터 액세스 수준/권한**     |    Azure Sentinel은 Azure에서 사용자, 그룹, 서비스에 할당할 수 있는 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 제공하는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 사용합니다. <br><br>모든 Azure Sentinel 기본 역할은 Azure Sentinel 작업 영역에 있는 데이터에 대한 읽기 권한을 부여합니다. 따라서 작업 영역 디자인을 결정하는 데 영향을 주므로 데이터 액세스를 데이터 원본 또는 행 수준별로 제어해야 하는지 여부를 확인해야 합니다. 자세한 내용은 [사용자 지정 역할 및 고급 Azure RBAC](roles.md#custom-roles-and-advanced-azure-rbac)를 참조하세요.     |
|**일일 수집 속도**     |  일일 수집 속도(일반적으로 GB/일)는 Azure Sentinel에 대한 비용 관리 및 계획 고려 사항과 작업 영역 디자인의 핵심 요소 중 하나입니다. <br><br>대부분의 클라우드 및 하이브리드 환경에서는 방화벽 또는 프록시와 같은 네트워킹 디바이스와 Windows 및 Linux 서버에서 가장 많은 수집된 데이터를 생성합니다. 가장 정확한 결과를 얻기 위해 Microsoft는 데이터 원본의 전체 인벤토리를 권장합니다. <br><br>또는 Azure Sentinel [비용 계산기](https://cloudpartners.transform.microsoft.com/download?assetname=assets%2FAzure_Sentinel_Calculator.xlsx&download=1)에는 데이터 원본의 공간을 예측하는 데 유용한 표가 포함되어 있습니다. <br><br>**중요**: 이러한 예측은 시작점이며, 자세한 로그 표시 수준 설정과 워크로드에서 차이를 산출합니다. 시스템을 정기적으로 모니터링하여 변경 내용을 추적하는 것이 좋습니다. 시나리오에 따라 정기적인 모니터링을 사용하는 것이 좋습니다. <br><br>자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../azure-monitor/logs/manage-cost-storage.md)를 참조하세요.       |
|     |         |

## <a name="decision-tree"></a>의사 결정 트리

다음 이미지에서는 작업 영역을 가장 잘 디자인하는 방법을 이해하는 데 도움이 되는 전체 의사 결정 트리 순서도를 보여 줍니다.

[ ![Azure Sentinel 작업 영역 디자인 결정 트리](media/best-practices/workspace-decision-tree.png) ](media/best-practices/workspace-decision-tree.png#lightbox)

다음 섹션에서는 이미지에서 참조된 다음 참고 사항을 포함하여 이 의사 결정 트리의 전체 텍스트 버전을 제공합니다.

[참고 #1](#note1) | [참고 #2](#note2)  | [참고 #3](#note3)  | [참고 #4](#note4)  | [참고 #5](#note5)  | [참고 #6](#note6)  | [참고 #7](#note7)  | [참고 #8](#note8)  | [참고 #9](#note9) | [참고 #10](#note10)



### <a name="step-1-new-or-existing-workspace"></a>1단계: 새 작업 영역 또는 기존 작업 영역?

Azure Sentinel에 사용할 수 있는 기존 작업 영역이 있나요?

- **그렇지 않은 경우 어쨌든 간에 새 작업 영역을 만들려면**  [2단계](#step-2-keeping-data-in-different-azure-geographies)로 바로 진행합니다.

- **사용할 수 있는 기존 작업 영역이 있는 경우** 수집할 데이터의 양을 고려합니다.

    - **100GB/일을 *초과* 하여 수집하는 경우** 비용 효율성을 위해 별도의 작업 영역을 사용하는 것이 좋습니다.

    - **100GB/일 *미만* 을 수집하는 경우** 추가 평가를 위해 [2단계](#step-2-keeping-data-in-different-azure-geographies)로 계속 진행합니다. [5단계](#step-5-collecting-any-non-soc-data)에서 이 질문이 발생하면 다시 고려하세요.

### <a name="step-2-keeping-data-in-different-azure-geographies"></a>2단계: 데이터를 다른 Azure 지역에 유지하나요?

- **데이터를 다른 Azure 지역에 유지해야 하는 규정 요구 사항이 있는 경우** 규정 준수 요구 사항이 있는 각 Azure 지역에 대해 별도의 Azure Sentinel 작업 영역을 사용합니다. 자세한 내용은 [지역 고려 사항](best-practices-workspace-architecture.md#region-considerations)을 참조하세요.

- **데이터를 다른 Azure 지역에 유지할 필요가 없는 경우** [3단계](#step-3-do-you-have-multiple-azure-tenants)로 계속 진행합니다.

### <a name="step-3-do-you-have-multiple-azure-tenants"></a>3단계: 여러 Azure 테넌트가 있나요?

- **하나의 테넌트만 있는 경우** [4단계](#step-4-splitting-billing--charge-back)로 바로 진행합니다.

- **여러 Azure 테넌트가 있는 경우** Office 365 또는 Microsoft 365 Defender와 같은 테넌트 경계와 관련된 로그를 수집하는지 여부를 고려합니다.

    - **테넌트와 관련된 로그가 없는 경우** [4단계](#step-4-splitting-billing--charge-back)로 바로 진행합니다.

    - **테넌트와 관련된 로그를 *수집* 하는 경우** 각 Azure AD 테넌트에 대해 별도의 Azure Sentinel 작업 영역을 사용합니다. 다른 고려 사항은 [4단계](#step-4-splitting-billing--charge-back)로 계속 진행합니다.

        <a name="note1"></a>[의사 결정 트리 참고 #1](#decision-tree): Office 365 및 Microsoft Defender와 같은 테넌트 경계와 관련된 로그는 동일한 테넌트 내의 작업 영역에만 저장할 수 있습니다.

        사용자 지정 수집기를 사용하여 다른 테넌트의 작업 영역에서 테넌트와 관련된 로그를 *수집할 수 있지만*, 다음과 같은 단점으로 인해 권장하지 않습니다.

        - 사용자 지정 커넥터에서 수집한 데이터가 사용자 지정 테이블에 수집됩니다. 따라서 기본 제공 규칙과 통합 문서를 모두 사용할 수는 없습니다.
        - 사용자 지정 테이블이 UEBA 및 기계 학습 규칙과 같은 일부 기본 제공 기능에서 고려되지 않습니다.
        - 사용자 지정 커넥터에는 Azure Functions 및 Logic Apps를 사용하는 것과 같은 추가 비용 및 노력이 필요합니다.

        이러한 단점이 조직의 문제가 아닌 경우 별도의 Azure Sentinel 작업 영역을 사용하는 대신 [4단계](#step-4-splitting-billing--charge-back)로 계속 진행합니다.

### <a name="step-4-splitting-billing--charge-back"></a>4단계: 청구 분할/지불 거절?

청구를 분할하거나 지불을 거절해야 하는 경우 사용량 보고 또는 수동 교차 청구가 적합한지 고려합니다.

- **청구를 분할하거나 지불을 거절할 필요가 *없는* 경우** [5단계](#step-5-collecting-any-non-soc-data)로 계속 진행합니다.

- **청구를 분할하거나 지불을 거절할 필요가 *있는* 경우** [사용량 보고 또는 수동 교차 청구](azure-sentinel-billing.md)가 적합한지 고려합니다.

    - **사용량 보고 또는 수동 교차 청구가 적합한 경우** [5단계](#step-5-collecting-any-non-soc-data)로 계속 진행합니다.

    - **사용량 보고 또는 수동 교차 청구가 모두 적합하지 *않은* 경우** 각 비용 소유자에 대해 별도의 Azure Sentinel 작업 영역을 사용합니다.

    <a name="note2"></a>[의사 결정 트리 참고 #2](#decision-tree): 자세한 내용은 [Azure Sentinel 비용 및 청구](azure-sentinel-billing.md)를 참조하세요.

### <a name="step-5-collecting-any-non-soc-data"></a>5단계: 비 SOC 데이터를 수집하나요?

- 작동 데이터와 같은 **비 SOC 데이터를 수집하지 않는 경우** [6단계](#step-6-multiple-regions)로 바로 건너뛸 수 있습니다.

- **비 SOC 데이터를 *수집하는* 경우** SOC및 비 SOC 데이터 모두에 동일한 데이터 원본이 필요한 겹침이 있는지 여부를 고려합니다.

    **SOC와 비 SOC 데이터 사이에 겹침이 *있는* 경우** 겹치는 데이터를 SOC 데이터로만 처리합니다. 그런 다음, SOC 데이터와 비 SOC 데이터 *모두* 에 대한 수집이 개별적으로 100GB/일 미만이지만 결합하는 경우 100GB/일을 초과하는지 여부를 고려합니다.

    - **예**: 추가 평가를 위해 [6단계](#step-6-multiple-regions)로 진행합니다.
    - **아니요**: 비용 효율성을 위해 동일한 작업 영역을 사용하지 않는 것이 좋습니다. 추가 평가를 위해 [6단계](#step-6-multiple-regions)로 진행합니다.

    두 경우 모두 자세한 내용은 [참고 10](#note10)을 참조하세요.

    **겹치는 데이터가 *없는* 경우** SOC 데이터와 비 SOC 데이터 *모두* 에 대한 수집이 개별적으로 100GB/일 미만이지만 결합하는 경우 100GB/일을 초과하는지 여부를 고려합니다.

    - **예**: 추가 평가를 위해 [6단계](#step-6-multiple-regions)로 진행합니다. 자세한 내용은 [참고 3](#combining-your-soc-and-non-soc-data)을 참조하세요.
    - **아니요**: 비용 효율성을 위해 동일한 작업 영역을 사용하지 않는 것이 좋습니다. 추가 평가를 위해 [6단계](#step-6-multiple-regions)로 진행합니다.

#### <a name="combining-your-soc-and-non-soc-data"></a>SOC 및 비 SOC 데이터 결합

<a name="note3"></a>[의사 결정 트리 참고 #3](#decision-tree): 일반적으로 Azure Sentinel 비용이 비 SOC 데이터에 적용되지 않도록 고객이 비 SOC 데이터에 대해 별도의 작업 영역을 유지하는 것이 좋지만, SOC 데이터와 비 SOC 데이터를 결합하는 것이 분리하는 것보다 비용이 덜 드는 경우가 있을 수 있습니다.

예를 들어 50GB/일의 보안 로그 및 50GB/일의 작업 로그를 수집하고 작업 영역이 미국 동부 지역에 있는 조직을 가정해 보겠습니다.

다음 표에서는 별도의 작업 영역이 있는 경우와 없는 경우의 작업 영역 옵션을 비교합니다.

> [!NOTE]
> 아래 표에 나열된 비용 및 용어는 가짜이며 설명 목적으로만 사용됩니다. 최신 비용 정보는 Azure Sentinel 가격 계산기를 참조하세요.
>

|작업 영역 아키텍처  |설명 |
|---------|---------|
|SOC 팀에는 Azure Sentinel이 사용하도록 설정된 자체 작업 영역이 있습니다. <br><br>운영 팀에는 Azure Sentinel이 사용하지 않도록 설정된 자체 작업 영역이 있습니다.     |  **SOC 팀**: <br>50GB/일에 대한 Azure Sentinel 비용은 월 6,500달러입니다.<br>처음 3개월 보존은 무료입니다. <br><br>**운영 팀**:<br>- 50GB/일의 Log Analytics 비용은 월 약 3,500달러입니다.<br>- 처음 31일 보존은 무료입니다.<br><br>둘 모두의 총 비용은 월 10,000달러입니다.       |
|SOC 팀과 운영 팀은 모두 Azure Sentinel이 사용하도록 설정된 동일한 작업 영역을 공유합니다. |두 로그를 모두 결합하면 수집은 100GB/일이 되어 약정 계층(Sentinel의 경우 50%, LA의 경우 15%)에 대한 자격이 있습니다.       <br><br>100GB/일에 대한 Azure Sentinel 비용은 월 9,000달러입니다.      |
|     |         |

이 예에서는 두 작업 영역을 모두 결합하여 월 1,000달러의 비용을 절감할 수 있으며, 운영 팀도 31일이 아닌 3개월의 무료 보존 기간의 혜택을 누릴 수 있습니다.

이 예는 SOC 데이터와 비 SOC 데이터의 수집 크기가 각각 50GB 이상/일 및 100GB 미만/일인 경우에만 관련이 있습니다.

<a name="note10"></a>[의사 결정 트리 참고 #10](#decision-tree): Azure Sentinel 비용이 비 SOC 데이터에 적용되지 않도록 비 SOC 데이터에 대해 별도의 작업 영역을 사용하는 것이 좋습니다.

그러나 비 SOC 데이터의 별도의 작업 영역에 대한 이 권장 사항은 전적으로 비용을 기반으로 하는 관점에서만 나온 것이며, 단일 또는 여러 작업 영역을 사용할지 여부를 결정할 때 검토해야 할 다른 주요 디자인 요소가 있습니다. 이중 수집 비용이 발생하지 않도록 방지하려면 테이블 수준 Azure RBAC만 사용하여 겹치는 데이터를 단일 작업 영역에 수집하는 것이 좋습니다.

### <a name="step-6-multiple-regions"></a>6단계: 여러 지역?

- ***단일* 지역의 Azure VM에서만 로그를 수집하는 경우** [7단계](#step-7-segregating-data-or-defining-boundaries-by-ownership)로 바로 진행합니다.

- ***여러* 지역의 Azure VM에서 로그를 수집하는 경우** 데이터 송신 비용에 대해 얼마나 걱정하나요?

    <a name="note4"></a>[의사 결정 트리 참고 #4](#decision-tree): 데이터 송신은 데이터를 Azure 데이터 센터 외부로 이동하기 위한 [대역폭 비용](https://azure.microsoft.com/pricing/details/bandwidth/)을 나타냅니다. 자세한 내용은 [지역 고려 사항](best-practices-workspace-architecture.md#region-considerations)을 참조하세요.

    - 별도의 작업 영역을 유지하는 데 필요한 작업량을 줄이는 것이 우선 순위인 경우 [7단계](#step-7-segregating-data-or-defining-boundaries-by-ownership)로 계속 진행합니다.

    - 데이터 송신 비용이 별도의 작업 영역을 유지하는 데 매우 중요한 경우 데이터 송신 비용을 줄여야 하는 각 지역에 대해 별도의 Azure Sentinel 작업 영역을 사용합니다.

        <a name="note5"></a>[의사 결정 트리 참고 #5](#decision-tree): 최대한 적은 수의 작업 영역을 유지하는 것이 좋습니다. [Azure 가격 계산기](azure-sentinel-billing.md#estimate-azure-sentinel-costs)를 사용하여 비용을 예측하고, 실제로 필요한 지역을 결정하며, 송신 비용이 낮은 지역의 작업 영역을 결합합니다. 대역폭 비용은 별도의 Azure Sentinel 및 Log Analytics 수집 비용과 비교할 때 Azure 청구서의 작은 부분에 불과할 수 있습니다.

        예를 들어 비용은 다음과 같이 예측할 수 있습니다.

        - 1,000개 VM(각각 1GB/일 생성)
        - 미국 지역에서 EU 지역으로 데이터 보내기
        - 에이전트에서 2:1 압축률 사용

        이 예상 비용에 대한 계산 식은 `1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost`입니다.

        이 샘플 비용은 별도의 Azure Sentinel 및 Log Analytics 작업 영역에 대한 월별 비용에 비해 훨씬 저렴합니다.

        > [!NOTE]
        > 나열된 비용은 가짜이며 설명 목적으로만 사용됩니다. 최신 비용 정보는 Azure Sentinel 가격 계산기를 참조하세요.
        >

### <a name="step-7-segregating-data-or-defining-boundaries-by-ownership"></a>7단계: 데이터를 분리하거나 소유권에 따라 경계를 정의하나요?

- **데이터를 분리하거나 소유권 경계를 정의할 필요가 *없는* 경우** [8단계](#step-8-controlling-data-access-by-data-source--table)로 바로 진행합니다.

- **데이터를 분리하거나 소유권에 따라 경계를 정의할 필요가 *있는* 경우** 각 데이터 소유자가 Azure Sentinel 포털을 사용해야 하나요?

    - 각 데이터 소유자가 Azure Sentinel 포털에 액세스할 수 있어야 하는 경우 각 소유자에 대해 별도의 Azure Sentinel 작업 영역을 사용합니다.

        <a name="note6"></a>[의사 결정 트리 참고 #6](#decision-tree): Azure Sentinel 포털에 액세스하려면 각 사용자에게 작업 영역의 모든 테이블에 대한 **읽기 권한자** 권한이 있는 [Azure Sentinel 읽기 권한자](../role-based-access-control/built-in-roles.md) 이상의 역할이 있어야 합니다. 사용자가 작업 영역의 모든 테이블에 액세스할 수 없는 경우 Log Analytics를 사용하여 검색 쿼리의 로그에 액세스해야 합니다.

    - Azure Sentinel 포털에 대한 액세스 권한이 없는 소유자가 Log Analytics를 통해 로그에 액세스하는 것으로 충분한 경우 [8단계](#step-8-controlling-data-access-by-data-source--table)로 계속 진행합니다.

    자세한 내용은 [Azure Sentinel의 권한](roles.md)을 참조하세요.

### <a name="step-8-controlling-data-access-by-data-source--table"></a>8단계: 데이터 액세스를 데이터 원본/테이블별로 제어하나요?

- **데이터 액세스를 원본 또는 테이블별로 제어할 필요가 *없는* 경우** 단일 Azure Sentinel 작업 영역을 사용합니다.

- **데이터 액세스를 원본 또는 테이블별로 제어할 필요가 *있는* 경우** 다음 상황에서 [리소스 컨텍스트 RBAC](resource-context-rbac.md)를 사용하는 것이 좋습니다.

    - 액세스를 행 수준에서 제어해야 하는 경우(예: 여러 소유자를 각 데이터 원본 또는 테이블에 제공)

    - 각각에 별도의 권한이 필요한 여러 개의 사용자 지정 데이터 원본/테이블이 있는 경우

    액세스를 행 수준에서 제어할 필요가 *없는* 다른 경우에는 별도의 권한이 있는 여러 사용자 지정 데이터 원본/테이블을 제공하고, 테이블 수준 RBAC를 데이터 액세스 제어에 사용하는 단일 Azure Sentinel 작업 영역을 사용합니다.

#### <a name="considerations-for-resource-context-or-table-level-rbac"></a>리소스 컨텍스트 또는 테이블 수준 RBAC에 대한 고려 사항

리소스 컨텍스트 또는 테이블 수준 RBAC를 사용하려는 경우 다음을 고려합니다.

- <a name="note7"></a>[의사 결정 트리 참고 #7](#decision-tree): 비 Azure 리소스에 대한 리소스 컨텍스트 RBAC를 구성하려면 리소스 컨텍스트 RBAC를 사용하여 권한 범위를 지정할 수 있도록 Azure Sentinel에 보낼 때 리소스 ID를 데이터에 연결해야 할 수 있습니다. 자세한 내용은 [명시적으로 리소스 컨텍스트 RBAC 구성](resource-context-rbac.md#explicitly-configure-resource-context-rbac) 및 [배포별 액세스 모드](../azure-monitor/logs/design-logs-deployment.md)를 참조하세요.

- <a name="note8"></a>[의사 결정 트리 참고 #8](#decision-tree): [리소스 권한](../azure-monitor/logs/manage-access.md) 또는 [리소스 컨텍스트](../azure-monitor/logs/design-logs-deployment.md)를 사용하면 액세스 권한이 있는 리소스에 대한 로그만 사용자에게 표시됩니다. 작업 영역 액세스 모드는 **사용자 리소스 또는 작업 영역 권한** 으로 설정되어야 합니다. 사용자에게 권한이 있는 리소스와 관련된 테이블만 Azure Sentinel에 있는 **로그** 페이지의 검색 결과에 포함됩니다.

- <a name="note9"></a>[의사 결정 트리 참고 #9](#decision-tree): [테이블 수준 RBAC](../azure-monitor/logs/manage-access.md)를 사용하면 다른 권한 외에도 Log Analytics 작업 영역에서 데이터에 대한 더 세분화된 제어를 정의할 수 있습니다. 이를 통해 관리자는 특정 사용자 집합만 액세스할 수 있는 특정 데이터 형식을 정의할 수 있습니다. 자세한 내용은 [Azure Sentinel의 테이블 수준 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)를 참조하세요.


## <a name="next-steps"></a>다음 단계

이 의사 결정 트리의 실제 예는 [Azure Sentinel 샘플 작업 영역 디자인](sample-workspace-designs.md)을 참조하세요.

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)
- [Azure Sentinel 모범 사례](best-practices.md)
