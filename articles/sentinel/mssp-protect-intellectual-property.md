---
title: Microsoft Sentinel | MSSP(관리형 보안 서비스 공급자) 지적 재산 보호 Microsoft Docs
description: MSSP(관리형 보안 서비스 공급자)가 Microsoft Sentinel에서 만든 지적 재산권을 보호하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: a0e2874782432c707aabc571ed77370b512e2dbf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713120"
---
# <a name="protecting-mssp-intellectual-property-in-microsoft-sentinel"></a>Microsoft Sentinel에서 MSSP 지적 재산 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 MSSP(관리형 보안 서비스 공급자)가 Microsoft Sentinel 분석 규칙, 헌팅 쿼리, 플레이북 및 통합 문서와 같이 Microsoft Sentinel에서 개발한 지적 재산권을 보호하는 데 사용할 수 있는 방법을 설명합니다.

선택하는 방법은 [CSP(클라우드 솔루션 공급자)](#cloud-solutions-providers-csp) 역할을 하는지, 고객에게 [EA(기업계약)/PAYG(종량제)](#enterprise-agreements-ea--pay-as-you-go-payg) 계정이 있는지 등 각 고객이 Azure를 구입한 방법에 따라 달라집니다. 다음 섹션에서는 이러한 각 방법에 대해 개별적으로 설명합니다.

## <a name="cloud-solutions-providers-csp"></a>CSP(클라우드 솔루션 공급자)

Azure를 CSP(클라우드 솔루션 공급자)로 재판매하는 경우 고객의 Azure 구독을 관리하고 있는 것입니다. [AOBO(관리자 위임)](/partner-center/azure-plan-manage) 덕분에 MSSP 테넌트의 Admin Agents 그룹에 속한 사용자에게는 고객의 Azure 구독에 대한 소유자 액세스 권한이 부여되며 고객에게는 기본적으로 액세스 권한이 없습니다.

Admin Agents 그룹에 속하지 않는 MSSP 테넌트의 다른 사용자가 고객 환경에 액세스해야 하는 경우 [Azure Lighthouse](multiple-tenants-service-providers.md)를 사용하는 것이 좋습니다. Azure Lighthouse를 사용하면 기본 제공 역할 중 하나를 사용하여 리소스 그룹이나 구독과 같은 특정 범위에 대한 액세스 권한을 사용자 또는 그룹에 부여할 수 있습니다.

고객 사용자에게 Azure 환경에 대한 액세스 권한을 제공해야 하는 경우 필요에 따라 환경의 일부를 표시/숨길 수 있도록 전체 구독이 아닌 ‘리소스 그룹’ 수준에서 액세스 권한을 부여하는 것이 좋습니다.

예를 들어:

- 고객에게 애플리케이션이 있는 여러 리소스 그룹에 대한 액세스 권한을 부여할 수 있지만, Microsoft Sentinel 작업 영역은 고객이 액세스할 수 없는 별도의 리소스 그룹에 유지할 수 있습니다.

- 이 방법을 사용하여 고객은 자체 리소스 그룹에 상주할 수 있는 별도의 리소스인 선택한 통합 문서 및 플레이북을 볼 수 있습니다.

리소스 그룹 수준에서 액세스 권한을 부여하더라도 고객은 Microsoft Sentinel에 액세스하지 않아도 VM의 로그와 같이 액세스할 수 있는 리소스에 대한 로그 데이터에 계속 액세스할 수 있습니다. 자세한 내용은 [리소스로 Microsoft Sentinel 데이터에 대한 액세스 관리를 참조하세요.](resource-context-rbac.md)

> [!TIP]
> 고객에게 전체 구독에 대한 액세스 권한을 제공해야 하는 경우 [EA(기업계약)/PAYG(종량제)](#enterprise-agreements-ea--pay-as-you-go-payg)의 지침을 참조할 수 있습니다.
>

### <a name="sample-microsoft-sentinel-csp-architecture"></a>샘플 Microsoft Sentinel CSP 아키텍처

다음 이미지에서는 CSP 고객에게 액세스 권한을 제공할 때 [이전 섹션](#cloud-solutions-providers-csp)에서 설명한 권한이 작동하는 방식을 설명합니다.

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="CSP 고객을 사용하여 Microsoft Sentinel 지적 재산권을 보호합니다.":::

이 이미지의 경우

- CSP 구독에 대한 **소유자** 액세스 권한이 부여된 사용자는 MSSP Azure AD 테넌트의 Admin Agents 그룹에 속하는 사용자입니다.
- MSSP의 다른 그룹은 Azure Lighthouse를 통해 고객 환경에 액세스할 수 있습니다.
- Azure 리소스에 대한 고객 액세스는 리소스 그룹 수준에서 Azure RBAC를 통해 관리됩니다.

    이를 통해 MSSP는 분석 규칙 및 헌팅 쿼리와 같은 Microsoft Sentinel 구성 요소를 필요에 따라 숨길 수 있습니다.

자세한 내용은 [Azure Lighthouse 설명서](../lighthouse/concepts/cloud-solution-provider.md)를 참조하세요.

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>EA(기업계약)/PAYG(종량제)

고객이 Microsoft에서 직접 구매하는 경우 고객은 이미 Azure 환경에 대한 모든 권한을 보유하며 고객의 Azure 구독에 있는 모든 항목을 숨길 수 없습니다.

대신 보호해야 하는 리소스 유형에 따라 다음과 같이 Microsoft Sentinel에서 개발한 지적 재산권을 보호합니다.

### <a name="analytics-rules-and-hunting-queries"></a>분석 규칙 및 헌팅 쿼리

분석 규칙 및 헌팅 쿼리는 모두 Microsoft Sentinel 내에 포함되어 있으므로 Microsoft Sentinel 작업 영역과 분리할 수 없습니다.

사용자에게 Microsoft Sentinel 판독기 권한만 있는 경우에도 쿼리를 볼 수 있습니다. 이 경우 고객 테넌트 대신 고유한 MSSP 테넌트에서 분석 규칙 및 헌팅 쿼리를 호스트하는 것이 좋습니다.

이렇게 하려면 Microsoft Sentinel을 사용하도록 설정된 고유한 테넌트의 작업 영역이 [필요하며, Azure Lighthouse](multiple-tenants-service-providers.md)통해 고객 작업 영역도 확인해야 합니다.

고객 테넌트의 데이터를 참조하는 MSSP 테넌트에서 분석 규칙 또는 헌팅 쿼리를 만들려면 다음과 같이 `workspace` 문을 사용해야 합니다.

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

분석 규칙에 `workspace` 문을 추가하는 경우 다음 사항을 고려하세요.

- **고객 작업 영역에 경고가 없습니다**. 이러한 방식으로 만든 규칙은 고객 작업 영역에 경고 또는 인시던트를 만들지 않습니다. 경고와 인시던트는 모두 MSSP 작업 영역에만 있습니다.

- **각 고객에 대해 별도의 경고를 만듭니다**. 이 방법을 사용하는 경우 작업 영역 문이 경우에 따라 달라지기 때문에 각 고객 및 검색에 대해 별도의 경고 규칙을 사용하는 것이 좋습니다.

    경고 규칙 이름에 고객 이름을 추가하여 경고가 트리거되는 고객을 쉽게 식별할 수 있습니다. 별도의 경고로 인해 스크립팅을 사용하여 관리하거나 [Microsoft Sentinel을 코드로](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928)사용하여 관리할 수 있는 많은 규칙이 발생할 수 있습니다.

    예를 들어:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="각 고객에 대한 MSSP 작업 영역에 별도의 규칙 만들기":::

- **각 고객에 대해 별도의 MSSP 작업 영역을 만듭니다**. 각 고객 및 검색에 대해 별도의 규칙을 만들면 작업 영역에 대한 최대 분석 규칙 수(512)에 도달할 수 있습니다. 고객이 많고 이 한도에 도달할 것으로 예상되는 경우 각 고객에 대해 별도의 MSSP 작업 영역을 만들 수 있습니다.

    예를 들어:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="각 고객에 대한 MSSP 테넌트에 작업 영역 및 규칙 만들기":::

> [!IMPORTANT]
> 이 방법을 성공적으로 사용하려면 자동화를 사용하여 작업 영역에서 많은 규칙 세트를 관리하는 것이 중요합니다.
>
> 자세한 내용은 [Cross-workspace analytics rules](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)(작업 영역 간 분석 규칙)를 참조하세요.
>

### <a name="workbooks"></a>통합 문서

고객이 복사하지 않으려는 Microsoft Sentinel 통합 문서를 개발한 경우 MSSP 테넌트에서 통합 문서를 호스트합니다. Azure Lighthouse를 통해 고객 작업 영역에 액세스할 수 있는지 확인한 다음, 해당 고객 작업 영역을 사용하도록 통합 문서를 수정해야 합니다.

예를 들어:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="작업 영역 간 통합 문서":::

자세한 내용은 [작업 영역 간 통합 문서](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)를 참조하세요.

사용자가 통합 문서 시각화를 볼 수 있도록 하면서 코드를 비밀로 유지하려면 통합 문서를 Power BI로 내보내는 것이 좋습니다.

통합 문서를 Power BI로 내보내면 다음을 수행할 수 있습니다.

- **통합 문서 시각화를 더 쉽게 공유** 할 수 있습니다. 고객에게 Power BI 대시보드에 대한 링크를 전송하여 Azure 액세스 권한이 없어도 보고된 데이터를 볼 수 있습니다.
- **일정 예약을 사용** 합니다. 해당 시간의 대시보드 스냅샷이 포함된 메일을 정기적으로 보내도록 Power BI를 구성합니다.

자세한 내용은 [Power BI로 Azure Monitor 로그 데이터 가져오기](../azure-monitor/logs/log-powerbi.md)를 참조하세요.

### <a name="playbooks"></a>플레이북

플레이북를 트리거하는 분석 규칙을 만든 위치에 따라 다음과 같이 플레이북를 보호할 수 있습니다.

- **MSSP 작업 영역에서 만든 분석 규칙**.  MSSP 테넌트에서 플레이북를 만들고 MSSP 작업 영역에서 모든 인시던트 및 경고 데이터를 가져와야 합니다. 작업 영역에서 새 규칙을 만들 때마다 플레이북을 연결할 수 있습니다.

    예를 들어:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="MSSP 작업 영역에서 만든 규칙":::

- **고객 작업 영역에서 만든 분석 규칙**. Azure Lighthouse를 사용하여 고객 작업 영역의 분석 규칙을 MSSP 작업 영역에서 호스트되는 플레이북에 연결합니다. 이 경우 플레이북은 고객 작업 영역에서 경고 및 인시던트 데이터와 다른 모든 고객 정보를 가져옵니다.

    예를 들어:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="고객 작업 영역에서 만든 규칙":::

두 경우 모두, 플레이북에서 고객의 Azure 환경에 액세스해야 하는 경우 Lighthouse를 통해 액세스 권한이 있는 사용자 또는 서비스 주체를 사용합니다.

그러나 플레이북이 고객의 테넌트에서 Azure AD, Office 365, Microsoft 365 Defender 같은 비 Azure 리소스에 액세스해야 하는 경우 고객 테넌트에서 적절한 권한이 있는 서비스 주체를 만든 다음, 플레이북에서 해당 ID를 추가해야 합니다.

> [!NOTE]
> 플레이북과 함께 자동화 규칙을 사용하는 경우 플레이북이 있는 리소스 그룹에서 자동화 규칙 권한을 설정해야 합니다.
> 자세한 내용은 [플레이북을 실행할 자동화 규칙에 대한 권한](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks)을 참조하세요.
>

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [MSSP용 Microsoft Sentinel 기술 플레이북](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Microsoft Sentinel에서 MSSP로 여러 테넌트 관리](multiple-tenants-service-providers.md)
- [작업 영역 및 테넌트에 걸쳐 Microsoft Sentinel 확장](extend-sentinel-across-workspaces-tenants.md)
- [데이터 시각화 및 모니터링](monitor-your-data.md)
- [자습서: Microsoft Sentinel에서 자동화된 위협 대응 설정](tutorial-respond-threats-playbook.md)
