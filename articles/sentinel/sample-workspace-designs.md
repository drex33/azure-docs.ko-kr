---
title: Azure Sentinel 샘플 작업 영역 디자인 | Microsoft Docs
description: 여러 테넌트, 클라우드 또는 지역이 포함된 Azure Sentinel 아키텍처 디자인 샘플에서 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/18/2021
ms.openlocfilehash: dca6beedfe8fa3d57674323490c2d79cf5aa048f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529827"
---
# <a name="azure-sentinel-sample-workspace-designs"></a>Azure Sentinel 샘플 작업 영역 디자인

이 문서에서는 다음과 같은 샘플 요구 사항이 있는 조직을 위해 제안된 작업 영역 디자인에 대해 설명합니다.

- 유럽 데이터 주권 요구 사항이 있는 다중 테넌트 및 지역
- 여러 클라우드가 있는 단일 테넌트
- 여러 지역 및 중앙 집중식 보안을 갖춘 다중 테넌트

이 문서의 샘플은 [Azure Sentinel 작업 영역 디자인 의사 결정 트리](design-your-workspace-architecture.md)를 사용하여 각 조직에 가장 적합한 작업 영역 디자인을 결정합니다. 자세한 내용은 [Azure Sentinel 작업 영역 아키텍처 모범 사례](best-practices-workspace-architecture.md)를 참조하세요.

## <a name="sample-1-multiple-tenants-and-regions"></a>샘플 1: 다중 테넌트 및 지역

Contoso Corporation은 런던에 본사가 있는 다국적 기업입니다. Contoso는 전 세계에 지사를 두고 있으며, 뉴욕과 도쿄에 중요한 허브가 있습니다. 최근 Contoso는 생산성 제품군을 Office 365로 마이그레이션했으며, 많은 워크로드가 Azure로 마이그레이션되었습니다.

### <a name="contoso-tenants"></a>Contoso 테넌트

몇 년 전의 인수에 따라 Contoso에는 `contoso.onmicrosoft.com` 및 `wingtip.onmicrosoft.com`이라는 두 개의 Azure AD 테넌트가 있습니다. 각 테넌트에는 다음 이미지와 같이 자체 Office 365 인스턴스와 여러 Azure 구독이 있습니다.

:::image type="content" source="media/best-practices/contoso-tenants.png" alt-text="각각 별도의 구독 세트가 있는 Contoso 테넌트의 다이어그램" border="false":::

### <a name="contoso-compliance-and-regional-deployment"></a>Contoso 규정 준수 및 지역 배포

Contoso에는 현재 미국 동부, 북유럽 및 일본 서부의 세 개의 다른 지역에서 호스트되는 Azure 리소스가 있으며, 유럽에서 생성된 모든 데이터를 유럽 지역 내에 유지해야 하는 엄격한 요구 사항이 있습니다.

Contoso의 두 Azure AD 테넌트에는 세 지역(미국 동부, 북유럽 및 일본 서부) 모두의 리소스가 있습니다.

### <a name="contoso-resource-types-and-collection-requirements"></a>Contoso 리소스 종류 및 수집 요구 사항

Contoso는 다음 데이터 원본에서 이벤트를 수집해야 합니다.

-   Office 365
-   Azure AD 로그인 및 감사 로그
-   Azure 활동
-   온-프레미스 및 Azure VM 원본 모두의 Windows 보안 이벤트
-   온-프레미스 및 Azure VM 원본 모두의 Syslog
-   Palo Alto, Cisco ASA 및 Cisco Meraki와 같은 여러 온-프레미스 네트워킹 디바이스의 CEF
-   Azure Firewall, AKS, Key Vault, Azure Storage 및 Azure SQL과 같은 여러 Azure PaaS 리소스
-   Cisco Umbrella

Azure VM은 대부분 북유럽 지역에 있으며, 일부 VM만 미국 동부 및 일본 서부에 있습니다. Contoso는 모든 Azure VM에서 서버용 Azure Defender를 사용합니다.

Contoso는 모든 데이터 원본에서 하루에 약 300GB를 수집해야 합니다.

### <a name="contoso-access-requirements"></a>Contoso 액세스 요구 사항

Contoso의 Azure 환경에는 이미 운영 팀에서 인프라를 모니터링하는 데 사용하는 기존 단일 Log Analytics 작업 영역이 있습니다. 이 작업 영역은 북유럽 지역 내의 Contoso AAD 테넌트에 있으며, 모든 지역의 Azure VM에서 로그를 수집하는 데 사용됩니다. 현재 하루에 약 50GB를 수집합니다.

Contoso 운영 팀은 현재 작업 영역에 있는 모든 로그에 액세스할 수 있어야 합니다. 여기에는 **Perf**, **InsightsMetrics**, **ContainerLog** 등과 같이 SOC에 필요하지 않은 여러 데이터 형식이 포함됩니다. 운영 팀은 Azure Sentinel에서 수집하는 새 로그에 액세스할 수 *없어야* 합니다.

### <a name="contosos-solution"></a>Contoso의 솔루션

다음 단계에서는 [Azure Sentinel 작업 영역 디자인 의사 결정 트리](design-your-workspace-architecture.md)를 적용하여 Contoso에 가장 적합한 작업 영역 디자인을 결정합니다.

1. Contoso에는 이미 기존 작업 영역이 있으므로 동일한 작업 영역에서 Azure Sentinel을 사용하도록 설정하는 것을 살펴볼 수 있습니다.

    비 SOC 데이터 수집은 100GB/일 미만이므로 [2단계](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies)로 계속 진행할 수 있으며 [5단계](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)에서 관련 옵션을 선택해야 합니다.

1.  Contoso에는 규정 요구 사항이 있으므로 유럽에 하나 이상의 Azure Sentinel 작업 영역이 필요합니다.

1.  Contoso에는 두 개의 서로 다른 Azure AD 테넌트가 있으며, Office 365, Azure AD 로그인 및 감사 로그와 같은 테넌트 수준 데이터 원본에서 수집하므로 테넌트당 하나 이상의 작업 영역이 필요합니다.

1.  Contoso는 [지불을 거절](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back)할 필요가 없으므로 [5단계](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)로 계속 진행할 수 있습니다.

1.  Contoso는 SOC 데이터와 비 SOC 데이터 간에 겹치지 않더라도 비 SOC 데이터를 수집해야 합니다. 또한 SOC 데이터는 약 250GB/일을 차지하므로 비용 효율성을 위해 별도의 작업 영역을 사용해야 합니다.

1.  Contoso의 VM 대부분은 이미 작업 영역이 있는 북유럽 지역입니다. 따라서 이 경우 대역폭 비용은 문제가 되지 않습니다.

1.  Contoso에는 Azure Sentinel을 사용할 단일 SOC 팀이 있으므로 추가 분리가 필요하지 않습니다.

1.  Contoso SOC 팀의 모든 멤버는 모든 데이터에 액세스할 수 있으므로 추가 분리가 필요하지 않습니다.

Contoso에 대한 결과 Azure Sentinel 작업 영역 디자인은 다음 이미지에 나와 있습니다.

:::image type="content" source="media/best-practices/contoso-solution.png" alt-text="운영 팀을 위한 별도의 작업 영역이 있는 Contoso 솔루션의 다이어그램" border="false":::

제안된 솔루션에는 다음이 포함됩니다.

- Contoso 운영 팀을 위한 별도의 Log Analytics 작업 영역. 이 작업 영역에는 **Perf**, **InsightsMetrics** 또는 **ContainerLog** 테이블과 같이 Contoso의 SOC 팀에 필요하지 않은 데이터만 포함됩니다.

- Office 365, Azure 활동, Azure AD 및 모든 Azure PaaS 서비스에서 데이터를 수집하기 위한 두 개의 Azure Sentinel 작업 영역(각 Azure AD 테넌트에 하나씩)

- 온-프레미스 데이터 원본에서 제공되는 다른 모든 데이터는 두 Azure Sentinel 작업 영역 중 하나로 라우팅될 수 있습니다.


## <a name="sample-2-single-tenant-with-multiple-clouds"></a>샘플 2: 여러 클라우드가 있는 단일 테넌트

Fabrikam은 뉴욕에 본사를 두고 있으며 미국 전역에 지사가 있는 조직입니다. Fabrikam은 클라우드 여정을 시작하고 있지만 여전히 첫 번째 Azure 랜딩 존을 배포하고 첫 번째 워크로드를 마이그레이션해야 합니다. Fabrikam의 AWS에는 이미 Azure Sentinel을 사용하여 모니터링하려는 일부 워크로드가 있습니다.

### <a name="fabrikam-tenancy-requirements"></a>Fabrikam 테넌트 요구 사항

Fabrikam에는 단일 Azure AD 테넌트가 있습니다.

### <a name="fabrikam-compliance-and-regional-deployment"></a>Fabrikam 규정 준수 및 지역 배포

Fabrikam에는 규정 준수 요구 사항이 없습니다. Fabrikam은 미국에 있는 여러 Azure 지역에 리소스를 보유하고 있지만 지역 간 대역폭 비용은 주요 문제가 아닙니다.

### <a name="fabrikam-resource-types-and-collection-requirements"></a>Fabrikam 리소스 종류 및 수집 요구 사항

Fabrikam은 다음 데이터 원본에서 이벤트를 수집해야 합니다.

-   Azure AD 로그인 및 감사 로그
-   Azure 활동
-   온-프레미스 및 Azure VM 원본 모두의 보안 이벤트
-   온-프레미스 및 Azure VM 원본 모두의 Windows 이벤트
-   온-프레미스 및 Azure VM 원본 모두의 성능 데이터
-   AWS CloudTrail
-   AKS 감사 및 성능 로그

### <a name="fabrikam-access-requirements"></a>Fabrikam 액세스 요구 사항

Fabrikam 운영 팀은 다음에 액세스해야 합니다.

-   온-프레미스 및 Azure VM 원본 모두의 보안 이벤트 및 Windows 이벤트
-   온-프레미스 및 Azure VM 원본 모두의 성능 데이터
-   AKS 성능(컨테이너 인사이트) 및 감사 로그
-   모든 Azure 활동 데이터

Fabrikam SOC 팀은 다음에 액세스해야 합니다.
-   Azure AD 로그인 및 감사 로그
-   모든 Azure 활동 데이터
-   온-프레미스 및 Azure VM 원본 모두의 보안 이벤트
-   AWS CloudTrail 로그
-   AKS 감사 로그
-   전체 Azure Sentinel 포털

### <a name="fabrikams-solution"></a>Fabrikam의 솔루션

다음 단계에서는 [Azure Sentinel 작업 영역 디자인 의사 결정 트리](design-your-workspace-architecture.md)를 적용하여 Fabrikam에 가장 적합한 작업 영역 디자인을 결정합니다.

1.  Fabrikam에는 기존 작업 영역이 없으므로 [2단계](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies)로 계속 진행합니다.

1.  Fabrikam에는 규정 요구 사항이 없으므로 [3단계](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants)로 계속 진행합니다.

1.  Fabrikam에는 단일 테넌트 환경이 있으므로 [4단계](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back)로 계속 진행합니다.

1.  Fabrikam에서 요금을 분할할 필요가 없으므로 [5단계](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)로 계속 진행합니다.

1.  Fabrikam에는 SOC 팀과 운영 팀을 위한 별도의 작업 영역이 필요합니다.

    Fabrikam 운영 팀은 VM 및 AKS 모두에서 성능 데이터를 수집해야 합니다. AKS는 진단 설정을 기반으로 하므로 특정 작업 영역에 보낼 특정 로그를 선택할 수 있습니다. Fabrikam은 AKS 감사 로그를 Azure Sentinel 작업 영역에 보내고, 모든 AKS 로그를 Azure Sentinel이 사용하도록 설정되지 않은 별도의 작업 영역에 보내도록 선택할 수 있습니다. Azure Sentinel이 사용하도록 설정되지 않은 작업 영역에서 Fabrikam은 컨테이너 인사이트 솔루션을 사용하도록 설정합니다.

    Windows VM의 경우 Fabrikam은 [AMA(Azure 모니터링 에이전트)](connect-windows-security-events.md#connector-options)를 사용하여 로그를 분할하고, 보안 이벤트를 Azure Sentinel 작업 영역에 보내고, Azure Sentinel을 사용하지 않고 성능 및 Windows 이벤트를 작업 영역에 보낼 수 있습니다.

    Fabrikam은 보안 이벤트 및 Azure 활동 이벤트와 같은 겹치는 데이터를 SOC 데이터로만 간주하도록 선택하고, Azure Sentinel을 사용하여 이 데이터를 작업 영역에 보냅니다.

1.  대역폭 비용은 Fabrikam의 주요 문제가 아니므로 [7단계](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership)로 계속 진행합니다.

1.  Fabrikam은 이미 SOC 팀과 운영 팀을 위해 별도의 작업 영역을 사용하도록 결정했습니다. 추가 분리가 필요하지 않습니다.

1.  Fabrikam은 보안 이벤트 및 Azure 활동 이벤트를 포함하여 겹치는 데이터에 대한 액세스를 제어해야 하지만 행 수준 요구 사항은 없습니다.

    보안 이벤트와 Azure 활동 이벤트는 모두 사용자 지정 로그가 아니므로 Fabrikam은 운영 팀을 위해 테이블 수준 RBAC를 사용하여 이러한 두 테이블에 대한 액세스 권한을 부여할 수 있습니다.

Fabrikam에 대한 결과 Azure Sentinel 작업 영역 디자인은 다음 이미지에 나와 있으며, 디자인을 간단히 하기 위해 주요 로그 원본만 포함되었습니다.

:::image type="content" source="media/best-practices/fabrikam-solution.png" alt-text="운영 팀을 위한 별도의 작업 영역이 있는 Fabrikam 솔루션의 다이어그램" border="false" :::

제안된 솔루션에는 다음이 포함됩니다.

- 미국 지역에 있는 두 개의 개별 작업 영역: 하나는 Azure Sentinel이 사용하도록 설정된 SOC 팀을 위한 것이고, 다른 하나는 Azure Sentinel이 없는 운영 팀을 위한 것입니다.

- [AMA(Azure 모니터링 에이전트)](connect-windows-security-events.md#connector-options): Azure 및 온-프레미스 VM에서 각 작업 영역에 보내는 로그를 결정하는 데 사용됩니다.

- 진단 설정: AKS와 같은 Azure 리소스에서 각 작업 영역에 보내는 로그를 결정하는 데 사용됩니다.

- Azure Sentinel 작업 영역에 보내는 겹치는 데이터: 필요한 경우 테이블 수준 RBAC를 사용하여 액세스 권한을 운영 팀에 부여합니다.

## <a name="sample-3-multiple-tenants-and-regions-and-centralized-security"></a>샘플 3: 여러 테넌트/지역 및 중앙 집중식 보안

Adventure Works는 도쿄에 본사가 있는 다국적 기업입니다. Adventure Works에는 전 세계 여러 국가에 기반을 둔 10개의 서로 다른 하위 엔터티가 있습니다.

Adventure Works는 Microsoft 365 E5 고객이며 이미 Azure에 워크로드가 있습니다.

### <a name="adventure-works-tenancy-requirements"></a>Adventure Works 테넌트 요구 사항

Adventure Works에는 하위 엔터티가 있는 각 대륙(아시아, 유럽 및 아프리카)에 대해 하나씩 세 개의 서로 다른 Azure AD 테넌트가 있습니다. 다른 하위 엔터티의 국가는 이들이 속한 대륙의 테넌트에서 ID를 갖습니다. 예를 들어 일본 사용자는 *아시아* 테넌트에 있고, 독일 사용자는 *유럽* 테넌트에 있으며, 이집트 사용자는 *아프리카* 테넌트에 있습니다.

### <a name="adventure-works-compliance-and-regional-requirements"></a>Adventure Works 규정 준수 및 지역 요구 사항

Adventure Works는 현재 하위 엔터티가 있는 대륙에 맞춘 세 개의 Azure 지역을 사용하고 있습니다. Adventure Works에는 엄격한 규정 준수 요구 사항이 없습니다.

### <a name="adventure-works-resource-types-and-collection-requirements"></a>Adventure Works 리소스 종류 및 수집 요구 사항

Adventure Works는 각 하위 엔터티에 대해 다음과 같은 데이터 원본을 수집해야 합니다.

-   Azure AD 로그인 및 감사 로그
-   Office 365 로그
-   엔드포인트용 Microsoft 365 Defender 원시 로그
-   Azure 활동
-   Azure Defender
-   Azure Firewall, Azure Storage, Azure SQL 및 Azure WAF와 같은 Azure PaaS 리소스
-   Azure VM의 보안 및 Windows 이벤트
-   온-프레미스 네트워크 디바이스의 CEF 로그

Azure VM은 세 대륙에 분산되어 있지만, 대역폭 비용은 문제가 되지 않습니다.

### <a name="adventure-works-access-requirements"></a>Adventure Works 액세스 요구 사항

Adventure Works에는 모든 하위 엔터티에 대한 보안 작업을 감독하는 단일 중앙 SOC 팀이 있습니다.

또한 Adventure Works에는 각 대륙에 대해 하나씩 세 개의 독립적인 SOC 팀이 있습니다. 각 대륙의 SOC 팀은 다른 대륙의 데이터를 보지 않고 해당 지역 내에서 생성된 데이터에만 액세스할 수 있어야 합니다. 예를 들어 아시아 SOC 팀은 아시아에 배포된 Azure 리소스의 데이터, 아시아 테넌트의 AAD 로그인 및 아시아 테넌트의 엔드포인트용 Defender 로그에만 액세스해야 합니다.

각 대륙의 SOC 팀은 전체 Azure Sentinel 포털 환경에 액세스해야 합니다.

Adventure Works의 운영 팀은 독립적으로 운영되며, Azure Sentinel이 없는 자체 작업 영역이 있습니다.

### <a name="adventure-works-solution"></a>Adventure Works 솔루션

다음 단계에서는 [Azure Sentinel 작업 영역 디자인 의사 결정 트리](design-your-workspace-architecture.md)를 적용하여 Adventure Works에 가장 적합한 작업 영역 디자인을 결정합니다.

1.  Adventure Works의 운영 팀에는 자체 작업 영역이 있으므로 [2단계](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies)로 계속 진행합니다.

1.  Adventure Works에는 규정 요구 사항이 없으므로 [3단계](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants)로 계속 진행합니다.

1.  Adventure Works에는 세 개의 Azure AD 테넌트가 있으며 Office 365 로그와 같은 테넌트 수준 데이터 원본을 수집해야 합니다. 따라서 Adventure Works는 각 테넌트에 대해 하나 이상의 Azure Sentinel 작업 영역을 만들어야 합니다.

1.  Adventure Works에서 요금을 분할할 필요가 없으므로 [5단계](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)로 계속 진행합니다.

1.  Adventure Works의 운영 팀에는 자체 작업 영역이 있으므로 이 결정에서 고려되는 모든 데이터는 Adventure Works SOC 팀에서 사용합니다.

1.  대역폭 비용은 Adventure Works의 주요 문제가 아니므로 [7단계](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership)로 계속 진행합니다.

1.  각 콘텐츠의 SOC 팀은 해당 콘텐츠와 관련된 데이터에만 액세스해야 하므로 Adventure Works는 데이터를 소유권별로 분리해야 합니다. 그러나 각 대륙의 SOC 팀에는 Azure Sentinel 포털에 대한 액세스 권한도 필요합니다.

1.  Adventure Works는 데이터 액세스를 테이블별로 제어할 필요가 없습니다.

Adventure Works에 대한 결과 Azure Sentinel 작업 영역 디자인은 다음 이미지에 나와 있으며, 디자인을 간단히 하기 위해 주요 로그 원본만 포함되었습니다.

:::image type="content" source="media/best-practices/adventure-works-solution.png" alt-text="각 Azure AD 테넌트에 대한 별도의 작업 영역이 있는 Adventure Works 솔루션의 다이어그램" border="false":::

제안된 솔루션에는 다음이 포함됩니다.

- 각 Azure AD 테넌트에 대한 별도의 Azure Sentinel 작업 영역. 각 작업 영역은 모든 데이터 원본에 대해 해당 테넌트와 관련된 데이터를 수집합니다.

- 각 대륙의 SOC 팀은 자체 테넌트의 작업 영역에만 액세스할 수 있으므로 각 SOC 팀은 테넌트 경계 내에 생성된 로그에만 액세스할 수 있습니다.

- 중앙 SOC 팀은 각각의 서로 다른 Azure Sentinel 환경에 액세스할 수 있는 Azure Lighthouse를 사용하여 별도의 Azure AD 테넌트에서 계속 운영할 수 있습니다. 추가 테넌트가 없는 경우에도 중앙 SOC 팀은 여전히 Azure Lighthouse를 사용하여 원격 작업 영역에 액세스할 수 있습니다.

- 중앙 SOC 팀은 대륙 SOC 팀에 숨겨져 있는 아티팩트를 저장해야 하거나 대륙 SOC 팀과 관련이 없는 다른 데이터를 수집하려는 경우 추가 작업 영역을 만들 수도 있습니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Sentinel 온보딩](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)