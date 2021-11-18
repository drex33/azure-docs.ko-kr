---
title: Microsoft Sentinel에 대한 작업 영역 아키텍처 모범 사례
description: Microsoft Sentinel 작업 영역 디자인 모범 사례에 대해 알아봅니다.
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 30c2d8e983db572d7577f6970efca823e51f2d62
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755485"
---
# <a name="microsoft-sentinel-workspace-architecture-best-practices"></a>Microsoft Sentinel 작업 영역 아키텍처 모범 사례

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel 작업 영역 배포를 계획할 때 Log Analytics 작업 영역 아키텍처도 디자인해야 합니다. 작업 영역 아키텍처에 대한 결정은 일반적으로 비즈니스 및 기술 요구 사항에 따라 결정됩니다. 이 문서에서는 다음을 포함하여 조직에 적합한 작업 영역 아키텍처를 결정하는 데 도움이 되는 주요 결정 요소를 검토합니다.

- 단일 테넌트 또는 다중 테넌트를 사용할지 여부
- 데이터 수집 및 저장에 대한 모든 규정 준수 요구 사항
- Microsoft Sentinel 데이터에 대한 액세스를 제어하는 방법
- 다양한 시나리오에 대한 비용 영향

자세한 내용은 [Microsoft Sentinel 작업 영역 아키텍처 디자인](design-your-workspace-architecture.md) 및 일반적인 시나리오에 대한 샘플 작업 영역 [디자인](sample-workspace-designs.md) 및 Microsoft [Sentinel 배포를 위한 배포 전 작업 및 필수 구성요약을](prerequisites.md)참조하세요.

다음 비디오를 참조하세요. [성공을 위한 SecOps 설계: Microsoft Sentinel 배포 모범 사례](https://youtu.be/DyL9MEMhqmI)


## <a name="tenancy-considerations"></a>테넌트 고려 사항

작업 영역이 적을수록 관리가 더 간단하지만, 여러 테넌트 및 작업 영역에 대한 특정 요구 사항이 있을 수 있습니다. 예를 들어 많은 조직에는 인수 및 합병 또는 ID 구분 요구 사항으로 인해 여러 [Azure AD(Active Directory) 테넌트](../active-directory/develop/quickstart-create-new-tenant.md)가 포함된 클라우드 환경이 있습니다.

사용할 테넌트 및 작업 영역의 개수를 결정할 때 대부분의 Microsoft Sentinel 기능은 단일 작업 영역 또는 Microsoft Sentinel 인스턴스를 사용하여 작동하고 Microsoft Sentinel은 작업 영역 내에 있는 모든 로그를 기록하는 것이 좋습니다.

> [!IMPORTANT]
> 비용은 Microsoft Sentinel 아키텍처를 결정할 때 고려해야 할 주요 고려 사항 중 하나입니다. 자세한 내용은 [Microsoft Sentinel 비용 및 청구를 참조하세요.](billing.md)
>
### <a name="working-with-multiple-tenants"></a>다중 테넌트 작업

MSSP(관리되는 보안 서비스 공급자)와 같이 여러 테넌트가 있는 경우 Azure AD 테넌트에 대해 하나 이상의 작업 영역을 만들어 자체 Azure AD 테넌트 내에서만 작동하는 기본 제공 [서비스 간 데이터 커넥터](connect-data-sources.md#service-to-service-integration)를 지원하는 것이 좋습니다.

진단 설정을 기반으로 하는 모든 커넥터는 리소스가 있는 동일한 테넌트에 있지 않은 작업 영역에 연결할 수 없습니다. 이는 [Azure Firewall](./data-connectors-reference.md#azure-firewall), [Azure Storage](./data-connectors-reference.md#azure-storage-account), [Azure 활동](./data-connectors-reference.md#azure-activity) 또는 [Azure Active Directory](connect-azure-active-directory.md)와 같은 커넥터에 적용됩니다.

[Azure Lighthouse](../lighthouse/how-to/onboard-customer.md) 사용하여 다른 테넌트에서 여러 Microsoft Sentinel 인스턴스를 관리할 수 있습니다.

> [!NOTE]
> [파트너 데이터 커넥터](data-connectors-reference.md)는 종종 API 또는 에이전트 컬렉션을 기반으로 하므로 특정 Azure AD 테넌트에 연결되지 않습니다.
>



## <a name="compliance-considerations"></a>규정 준수 고려 사항

데이터를 수집, 저장 및 처리한 후 규정 준수는 Microsoft Sentinel 아키텍처에 상당한 영향을 미치는 중요한 디자인 요구 사항이 될 수 있습니다. 모든 조건에서 어떤 데이터에 액세스할 수 있는지 유효성을 검사하고 증명할 수 있는 기능이 있는 것은 많은 국가 및 지역에서 중요한 데이터 주권 요구 사항이며, Microsoft Sentinel 워크플로에서 위험을 평가하고 인사이트를 얻는 것이 많은 고객에게 우선 순위입니다.

Microsoft Sentinel에서 데이터는 대부분 동일한 지리 또는 지역에 저장되고 처리됩니다. 단, Microsoft의 기계 학습을 활용하는 검색 규칙을 사용하는 경우와 같은 몇 가지 예외가 있습니다. 이러한 경우 데이터는 작업 영역 외부에 복사하여 처리할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [지리적 가용성 및 데이터 상주](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Azure의 데이터 보존](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [EU - EU 정책 블로그의 EU 데이터 저장 및 처리](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

규정 준수 유효성 검사를 시작하려면 데이터 원본 및 여기서 데이터를 보내는 방법과 위치를 평가합니다.

> [!NOTE]
> [Log Analytics 에이전트](connect-windows-security-events.md)는 TLS 1.2를 지원하여 에이전트와 Log Analytics 서비스 간의 전송 중 데이터 보안 및 FIPS 140 표준을 보장합니다. 
>
> 전송 리소스가 Azure에 있는지 여부에 관계없이 Microsoft Sentinel 작업 영역과 다른 지리 또는 지역으로 데이터를 보내는 경우 동일한 지리 또는 지역에서 작업 영역을 사용하는 것이 좋습니다.
>

## <a name="region-considerations"></a>지역 고려 사항

각 지역에 대해 별도의 Microsoft Sentinel 인스턴스를 사용합니다. Microsoft Sentinel을 여러 지역에서 사용할 수 있지만, 팀, 지역 또는 사이트별로 데이터를 분리하거나 다중 지역 모델을 필요 이상으로 복잡하게 만드는 규정 및 제어에 따라 데이터를 분리해야 할 수도 있습니다. 각 지역에 대해 별도의 인스턴스와 작업 영역을 사용하면 데이터를 지역 간에 이동하기 위한 대역폭/송신 비용을 방지할 수 있습니다.

여러 지역을 사용하는 경우 다음을 고려합니다.

- 송신 비용은 일반적으로 가상 머신과 같이 [Log Analytics 또는 Azure Monitor 에이전트](connect-windows-security-events.md)가 로그를 수집하는 데 필요한 경우에 적용됩니다.

- 인터넷 송신에 대한 요금도 청구됩니다. 이는 데이터를 Log Analytics 작업 영역 외부로 내보내는 경우를 제외하고는 적용되지 않을 수 있습니다. 예를 들어 Log Analytics 데이터를 온-프레미스 서버로 내보내는 경우 인터넷 송신 요금이 발생할 수 있습니다.

- 대역폭 비용은 원본 및 대상 지역과 수집 방법에 따라 달라집니다. 자세한 내용은 다음을 참조하세요.

    - [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [Log Analytics를 사용하여 데이터 전송 요금 청구](../azure-monitor/logs/manage-cost-storage.md)

- 분석 규칙, 사용자 지정 쿼리, 통합 문서 및 기타 리소스에 대한 템플릿을 사용하여 더 효율적으로 배포할 수 있습니다. 각 지역의 각 리소스를 수동으로 배포하는 대신 템플릿을 배포합니다.

- 진단 설정을 기반으로 하는 커넥터에는 대역폭 내 비용이 발생하지 않습니다. 자세한 내용은 [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics)를 참조하세요.

예를 들어 미국 동부의 Virtual Machines 로그를 수집하여 미국 서부의 Microsoft Sentinel 작업 영역으로 보내려는 경우 데이터 전송에 대한 수신 비용이 청구됩니다. Log Analytics 에이전트는 전송 중인 데이터를 압축하기 때문에 대역폭에 대해 청구되는 크기가 Microsoft Sentinel의 로그 크기보다 낮을 수 있습니다.

전 세계 여러 원본에서 Syslog 및 CEF 로그를 수집하는 경우 규정 준수가 문제가 되지 않는 경우 대역폭 비용을 방지하기 위해 Microsoft Sentinel 작업 영역과 동일한 지역에 Syslog 수집기를 설정할 수 있습니다.

대역폭 비용이 별도의 Microsoft Sentinel 작업 영역을 정당화하는지 여부를 이해하는 것은 지역 간에 전송해야 하는 데이터의 양에 따라 달라집니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/details/bandwidth/)를 사용하여 비용을 예측합니다.

자세한 내용은 [Azure의 데이터 보존](https://azure.microsoft.com/global-infrastructure/data-residency/)을 참조하세요.

## <a name="access-considerations"></a>액세스 고려 사항

여러 팀에서 동일한 데이터에 액세스해야 하는 상황이 계획되어 있을 수 있습니다. 예를 들어 SOC 팀은 모든 Microsoft Sentinel 데이터에 액세스할 수 있어야 하지만 운영 및 애플리케이션 팀은 특정 부분에만 액세스해야 합니다. 독립 보안 팀은 Microsoft Sentinel 기능에 액세스해야 하지만 다양한 데이터 집합을 사용해야 할 수도 있습니다.

[리소스 컨텍스트 RBAC](resource-context-rbac.md)와 [테이블 수준 RBAC](../azure-monitor/logs/manage-access.md#table-level-azure-rbac)를 결합하여 대부분의 사용 사례를 지원해야 하는 광범위한 액세스 옵션을 팀에 제공합니다.

자세한 내용은 [Microsoft Sentinel의 사용 권한을 참조하세요.](roles.md)

### <a name="resource-context-rbac"></a>리소스 컨텍스트 RBAC

다음 이미지에서는 보안 및 운영 팀에서 다양한 데이터 세트에 액세스해야 하고, 리소스 컨텍스트 RBAC를 사용하여 필요한 권한을 제공하는 작업 영역 아키텍처의 간소화된 버전을 보여 줍니다.


[ ![리소스 컨텍스트 RBAC에 대한 샘플 아키텍처의 다이어그램](media/resource-context-rbac/resource-context-rbac-sample.png) ](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

이 이미지에서 Microsoft Sentinel 작업 영역은 권한을 더 잘 격리하기 위해 별도의 구독에 배치됩니다.

> [!NOTE]
> 또 다른 옵션은 Microsoft Sentinel을 보안 전용의 별도 관리 그룹에 배치하여 최소 권한 할당만 상속되도록 하는 것입니다. 보안 팀 내에서 권한은 해당 기능에 따라 여러 그룹에 할당됩니다. 이러한 팀은 전체 작업 영역에 액세스할 수 있으므로 할당된 Microsoft Sentinel 역할에 의해서만 제한된 전체 Microsoft Sentinel 경험에 액세스할 수 있습니다. 자세한 내용은 [Microsoft Sentinel의 사용 권한을 참조하세요.](roles.md)
>

보안 구독 외에도 별도의 구독이 애플리케이션 팀에서 워크로드를 호스트하는 데 사용됩니다. 애플리케이션 팀에는 리소스를 관리할 수 있는 해당 리소스 그룹에 대한 액세스 권한이 부여됩니다. 이 별도의 구독 및 리소스 컨텍스트 RBAC를 통해 이러한 팀은 로그가 직접 액세스 권한이 *없는* 작업 영역에 저장된 경우에도 액세스할 수 있는 리소스에 의해 생성된 로그를 볼 수 있습니다. 애플리케이션 팀은 Azure Portal의 **로그** 영역을 통해 로그에 액세스하여 특정 리소스에 대한 로그를 표시하거나 Azure Monitor를 통해 동시에 액세스할 수 있는 모든 로그를 표시할 수 있습니다.

Azure 리소스에는 리소스 컨텍스트 RBAC에 대한 기본 제공 지원이 있지만, 비 Azure 리소스를 사용하는 경우 추가 미세 조정이 필요할 수 있습니다. 자세한 내용은 [리소스 컨텍스트 RBAC를 명시적 구성](resource-context-rbac.md#explicitly-configure-resource-context-rbac)을 참조하세요.

### <a name="table-level-rbac"></a>테이블 수준 RBAC

테이블 수준 RBAC를 사용하면 지정된 사용자 세트만 액세스할 수 있는 특정 데이터 형식(테이블)을 정의할 수 있습니다.

예를 들어 위의 이미지에서 아키텍처가 설명된 조직에서 Office 365 로그에 대한 액세스 권한도 내부 감사 팀에 부여해야 하는 경우를 가정해 보겠습니다. 이 경우 테이블 수준 RBAC를 사용하여 다른 테이블에 대한 권한을 부여하지 않고 전체 **OfficeActivity** 테이블에 대한 액세스 권한을 감사 팀에 부여할 수 있습니다.

### <a name="access-considerations-with-multiple-workspaces"></a>여러 작업 영역에 대한 액세스 고려 사항

조직 내에 서로 다른 엔터티, 자회사 또는 지리가 있고 각각 Microsoft Sentinel에 액세스해야 하는 자체 보안 팀이 있는 경우 각 엔터티 또는 자회사에 대해 별도의 작업 영역을 사용합니다. 단일 Azure AD 테넌트 내에서 또는 Azure Lighthouse를 사용하여 여러 테넌트에 간에 별도의 작업 영역을 구현합니다. 

중앙 SOC 팀은 추가적인 선택적 Microsoft Sentinel 작업 영역을 사용하여 분석 규칙 또는 통합 문서와 같은 중앙 집중식 아티팩트도 관리할 수 있습니다.

자세한 내용은 [여러 작업 영역 작업 간소화](#simplify-working-with-multiple-workspaces)를 참조하세요.


## <a name="technical-best-practices-for-creating-your-workspace"></a>작업 영역을 만드는 방법에 대한 기술 모범 사례

Microsoft Sentinel에 사용할 Log Analytics 작업 영역을 만들 때 다음 모범 사례 지침을 사용합니다.

- **작업 영역의 이름을 지정할 때** 다른 작업 영역 간에 쉽게 식별할 수 있도록 이름에 *Microsoft Sentinel* 또는 다른 표시기를 포함합니다.

- Microsoft Defender for Cloud에서 수집한 모든 로그를 **Microsoft Sentinel에서** 수집하고 사용할 수 있도록 Microsoft Sentinel 및 Microsoft Defender for Cloud 모두에 대해 동일한 작업 영역을 사용합니다. Microsoft Defender for Cloud에서 만든 기본 작업 영역은 Microsoft Sentinel에 사용 가능한 작업 영역으로 표시되지 않습니다.

- **예상 데이터 수집이 1TB/일 이상이면 전용 작업 영역 클러스터를 사용합니다**. [전용 클러스터를](../azure-monitor/logs/logs-dedicated-clusters.md) 사용하면 Microsoft Sentinel 데이터에 대한 리소스를 보호할 수 있어 큰 데이터 집합에 대한 쿼리 성능이 향상됩니다. 또한 전용 클러스터는 조직의 키를 더 많이 암호화하고 제어할 수 있는 옵션을 제공합니다.

## <a name="simplify-working-with-multiple-workspaces"></a>여러 작업 영역 작업 간소화

여러 작업 영역에서 작업해야 하는 경우 [각 Microsoft Sentinel 인스턴스의 모든 인시던트 를 단일 위치에 압축하고 나열하여 인시던트](multiple-workspace-view.md)관리 및 조사를 간소화합니다.

다른 Microsoft Sentinel 작업 영역(예: 작업 영역 [간 통합 문서)에](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)보관된 데이터를 참조하려면 작업 영역 간 [쿼리를 사용합니다.](extend-sentinel-across-workspaces-tenants.md)

작업 영역 간 쿼리를 사용하는 데 가장 적합한 경우는 중요한 정보가 다른 작업 영역, 구독 또는 테넌트에 저장되고 값을 현재 작업에 제공할 수 있는 경우입니다. 예를 들어 다음 코드에서는 작업 영역 간 쿼리 샘플을 보여 줍니다.

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

자세한 내용은 [작업 영역 및 테넌트 간에 Microsoft Sentinel 확장을 참조하세요.](extend-sentinel-across-workspaces-tenants.md)
## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
>[Microsoft Sentinel 작업 영역 아키텍처 디자인](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Microsoft Sentinel 샘플 작업 영역 디자인](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[Microsoft Sentinel 온보보드](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[경고 표시](get-visibility.md)
