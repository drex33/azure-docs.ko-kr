---
title: 대규모 Azure Sentinel 작업 영역 관리
description: Azure Lighthouse는 위임된 고객 리소스에서 Azure Sentinel을 효과적으로 관리할 수 있도록 지원합니다.
ms.date: 08/16/2021
ms.topic: how-to
ms.openlocfilehash: d6eba4cab51fa18164cc5f44e579be23ef8e74a5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777814"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>대규모 Azure Sentinel 작업 영역 관리

서비스 공급자는 여러 고객 테넌트를 [Azure Lighthouse](../overview.md)에 온보딩했을 수 있습니다. 서비스 공급자는 Azure Lighthouse를 사용하여 여러 Azure AD(Azure Active Directory) 테넌트에 걸쳐 대규모로 작업을 한 번에 수행할 수 있으므로 관리 작업을 보다 효율적으로 수행할 수 있습니다.

Azure Sentinel은 보안 분석 및 위협 인텔리전스를 제공하며, 경고 검색, 위협 가시성, 주도적 헌팅 및 위협 대응을 위한 단일 솔루션을 제공합니다. Azure Lighthouse를 사용하여 테넌트 전반의 여러 Azure Sentinel 작업 영역을 대규모로 관리할 수 있습니다. 이렇게 하면 여러 작업 영역에 걸쳐 쿼리를 실행하거나 통합 문서를 만들어 연결된 데이터 원본의 데이터를 시각화하고 모니터링하여 인사이트를 얻을 수 있습니다. 쿼리 및 플레이북과 같은 IP는 관리 테넌트에 상주하지만 고객 테넌트에서 보안 관리를 수행하는 데 사용할 수 있습니다.

이 항목에서는 테넌트 간 가시성 및 관리되는 보안 서비스를 위해 확장 가능한 방법으로 [Azure Sentinel](../../sentinel/overview.md)을 사용하는 방법에 대한 개요를 제공합니다.

> [!TIP]
> 이 항목은 서비스 공급자 및 고객을 염두에 두고 만들었지만 이 지침은 [Azure Lighthouse를 사용하여 여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에도 적용됩니다.

## <a name="architectural-considerations"></a>아키텍처 고려 사항

Azure Sentinel을 사용하여 Security-as-a-service 제품을 빌드하려고 하는 MSSP(관리되는 보안 서비스 공급자)의 경우, 개별 고객 테넌트 내에 배포된 여러 Azure Sentinel 작업 영역을 중앙에서 모니터링, 관리 및 구성하기 위해 단일 SOC(보안 작업 센터)가 필요할 수 있습니다. 마찬가지로, 여러 Azure AD 테넌트가 있는 기업은 테넌트 전반에 배포된 여러 Azure Sentinel 작업 영역을 중앙에서 관리하려고 할 수 있습니다.

이 중앙 집중형 배포 모델의 장점은 다음과 같습니다.

- 데이터의 소유권은 관리되는 각 테넌트에 남아 있습니다.
- 지리적 경계 내에 데이터를 저장하기 위한 요구 사항을 지원합니다.
- 여러 고객에 대한 데이터가 동일한 작업 영역에 저장되지 않기 때문에 데이터 격리를 보장합니다.
- 관리되는 테넌트의 데이터 반출을 방지하여 데이터 규정 준수를 보장합니다.
- 관련 비용은 관리 테넌트 대신 관리되는 각 테넌트에 부과됩니다.
- Azure Sentinel(예: Azure AD 활동 로그, Office 365 로그 또는 Microsoft Threat Protection 경고)과 통합된 모든 데이터 원본 및 데이터 커넥터의 데이터는 각 고객 테넌트 내에 유지됩니다.
- 네트워크 대기 시간을 줄입니다.
- 새 자회사 또는 고객을 쉽게 추가하거나 제거할 수 있습니다.

> [!NOTE]
> 여러 [지역](../../availability-zones/az-overview.md#regions)에 있는 위임된 리소스를 관리할 수 있습니다. 그러나 [국가별 클라우드](../../active-directory/develop/authentication-national-cloud.md) 및 Azure 퍼블릭 클라우드 또는 별도의 두 국가별 클라우드 간에 구독을 위임하는 것은 지원되지 않습니다.

> [!IMPORTANT]
> 모든 작업 영역이 고객 테넌트에 생성된 경우 Microsoft.SecurityInsights 및 Microsoft.OperationalInsights 리소스 공급자도 관리 테넌트의 구독에 [등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)해야 합니다.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>세분화된 Azure RBAC(Azure 역할 기반 액세스 제어)란?

MSSP에서 관리하는 각 고객 구독은 [Azure Lighthouse아 온보딩](onboard-customer.md)되어야 합니다. 이렇게 하면 관리 테넌트의 지정된 사용자가 고객 테넌트에 배포된 Azure Sentinel 작업 영역에 액세스하고 관리 작업을 수행할 수 있습니다.

권한 부여를 만들 때 관리 테넌트의 사용자, 그룹 또는 서비스 사용자에게 Azure Sentinel 기본 제공 역할을 할당할 수 있습니다.

- [Azure Sentinel reader](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel responder](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel contributor](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

추가 기능을 수행하기 위해 추가 기본 제공 역할을 할당할 수도 있습니다. Azure Sentinel에서 사용할 수 있는 특정 역할에 대한 자세한 내용은 [Azure Sentinel의 권한](../../sentinel/roles.md)을 참조하세요.

고객을 온보딩하면 지정된 사용자가 관리 테넌트에 로그인하고, 할당된 역할을 사용하여 [고객의 Azure Sentinel 작업 영역에 직접 액세스](../../sentinel/multiple-tenants-service-providers.md)할 수 있습니다.

## <a name="view-and-manage-incidents-across-workspaces"></a>작업 영역 전반에서 인시던트 보기 및 관리

여러 고객의 Azure Sentinel 리소스를 관리하는 경우 한 번에 여러 테넌트의 여러 작업 영역에서 인시던트를 보고 관리할 수 있습니다. 자세한 내용은 [한 번에 여러 작업 영역에서 인시던트 사용](../../sentinel/multiple-workspace-view.md)과 [작업 영역 및 테넌트 간에 Azure Sentinel 확장](../../sentinel/extend-sentinel-across-workspaces-tenants.md)을 참조하세요.

> [!NOTE]
> 관리 테넌트의 사용자에게 관리되는 모든 작업 영역에 대한 읽기 및 쓰기 권한이 할당되었는지 확인합니다. 사용자에게 일부 작업 영역에 대한 읽기 권한만 있는 경우, 해당 작업 영역에서 인시던트를 선택할 때 경고 메시지가 표시될 수 있으며, 해당 사용자가 이러한 인시던트나 선택된 다른 인시던트를 수정할 수 없습니다(다른 항목에 대한 권한이 있는 경우에도).

## <a name="configure-playbooks-for-mitigation"></a>완화를 위해 플레이북 구성

[플레이북](../../sentinel/tutorial-respond-threats-playbook.md)은 경고가 트리거될 때 자동 완화에 사용될 수 있습니다. 이러한 플레이북은 수동으로 실행할 수 있으며, 특정 경보가 트리거될 때 자동으로 실행될 수 있습니다. 플레이북은 보안 위협에 대응하기 위해 조치를 취해야 하는 테넌트의 사용자에 따라 구성된 대응 절차를 사용하여 관리 테넌트 또는 고객 테넌트에 배포할 수 있습니다.

## <a name="create-cross-tenant-workbooks"></a>테넌트 간 통합 문서 만들기

[Azure Sentinel의 Azure Monitor 통합 문서](../../sentinel/overview.md#workbooks)는 연결된 데이터 원본의 데이터를 시각화하고 모니터링하여 인사이트를 확보하는 데 도움이 됩니다. Azure Sentinel에서 기본 제공 통합 문서 템플릿을 사용하거나 시나리오에 맞는 사용자 지정 통합 문서를 만들 수 있습니다.

관리 테넌트에 통합 문서를 배포하고 대규모 대시보드를 만들어 고객 테넌트 간에 데이터를 모니터링하고 쿼리할 수 있습니다. 자세한 내용은 [작업 영역 간 모니터링](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)을 참조하세요. 

해당 고객과 관련된 시나리오를 위해 관리하는 개별 테넌트에 직접 통합 문서를 배포할 수도 있습니다.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Azure Sentinel 작업 영역에서 Log Analytics 및 헌팅 쿼리를 실행합니다.

관리 테넌트에서 중앙 집중식으로 위협 탐지를 수행하기 위해 [헌팅 쿼리](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting)를 포함한 Log Analytics 쿼리를 만들고 저장합니다. 그러면 모든 고객의 Azure Sentinel 작업 영역에서 Union 연산자와 workspace () 식을 사용하여 이러한 쿼리를 실행할 수 있습니다. 자세한 내용은 [작업 영역 간 쿼리](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)를 참조하세요.

## <a name="use-automation-for-cross-workspace-management"></a>작업 영역 간 관리를 위한 자동화 사용

자동화를 사용하여 여러 Azure Sentinel 작업 영역을 관리하고, [헌팅 쿼리](../../sentinel/hunting.md), 플레이북 및 통합 문서를 구성할 수 있습니다. 자세한 내용은 [자동화를 사용하여 작업 영역 간 관리](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)를 참조하세요.

## <a name="monitor-security-of-office-365-environments"></a>Office 365 환경의 보안 모니터링

Azure Lighthouse를 Azure Sentinel과 함께 사용하여 테넌트 전반에서 Office 365 환경의 보안을 모니터링합니다. 먼저, Exchange 및 SharePoint(OneDrive 포함)의 사용자 및 관리자 활동에 대한 정보가 관리되는 테넌트 내 Azure Sentinel 작업 영역에 수집될 수 있도록 기본 제공 [Office 365 데이터 커넥터를 관리되는 테넌트에서 사용하도록 설정해야 합니다](../../sentinel/data-connectors-reference.md#microsoft-office-365). 여기에는 파일 다운로드, 보낸 액세스 요청, 그룹 이벤트에 대한 변경 내용 및 사서함 작업 등의 동작에 대한 세부 정보와 해당 동작을 수행한 사용자에 대한 정보가 포함됩니다. 기본 제공 Office 365 커넥터의 일부로 [Office 365 DLP 경고](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820)도 지원됩니다.

[MCAS(Microsoft Cloud App Security) 커넥터](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)를 사용하도록 설정하여 경고 및 Cloud Discovery 로그를 Azure Sentinel에 스트림할 수 있습니다. 이를 통해 클라우드 앱에 대한 가시성을 확보하고, 정교한 분석 자료를 통해 사이버 위협을 식별한 후 대응하고, 데이터가 이동하는 방식을 제어할 수 있습니다. MCAS의 활동 로그는 [CEF(Common Event Format)를 사용하여 소비](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849)될 수 있습니다.

Office 365 데이터 커넥터를 설정한 후 통합 문서에서 데이터 보기 및 분석, 쿼리를 사용하여 사용자 지정 경고 만들기, 위협에 대응하도록 플레이북 구성 등 테넌트 간 Azure Sentinel 기능을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Sentinel](../../sentinel/overview.md)에 대해 알아봅니다.
- [Azure Sentinel 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sentinel/)를 검토 합니다.
- Azure Sentinel 환경의 배포 및 초기 구성 작업 속도를 높이는 프로젝트인 [Azure Sentinel All in One](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/Sentinel-All-In-One)을 살펴보세요.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.