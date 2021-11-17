---
title: Azure Monitor를 사용한 Azure AD B2C 모니터링
titleSuffix: Azure AD B2C
description: 위임된 리소스 관리를 사용하여 Azure Monitor로 Azure AD B2C 이벤트를 로그하는 방법에 관해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: kengaderdus
ms.subservice: B2C
ms.date: 09/15/2021
ms.openlocfilehash: bea95ff80fb3bcd0e32e550196f50c3ffde1719a
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039926"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure Monitor를 사용한 Azure AD B2C 모니터링

Azure Monitor를 사용하여 Azure AD B2C(Azure Active Directory B2C) 다양한 모니터링 솔루션에 관한 로그인 로그 및 [감사](view-audit-logs.md) 로그를 모니터링할 수 있습니다. 로그를 장기간 사용할 수 있도록 보존할 수도 있고, 타사 SIEM(보안 정보 및 이벤트 관리) 도구와 통합하여 환경에 대한 인사이트를 얻을 수도 있습니다.

다음으로 로그 이벤트를 라우팅할 수 있습니다.

- Azure [스토리지 계정](../storage/blobs/storage-blobs-introduction.md).
- 데이터를 분석하고, 대시보드를 만들고, 특정 이벤트에 대해 경고하는 [Log Analytics 작업 영역](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).
- Azure [이벤트 허브](../event-hubs/event-hubs-about.md)(및 Splunk 및 Sumo Logic 인스턴스와 통합).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

이 문서에서는 Azure Log Analytics 작업 영역으로 로그를 전송하는 방법에 관해 알아봅니다. 그런 다음 Azure AD B2C 사용자 활동을 기반으로 대시보드를 만들거나 경고를 만들 수 있습니다.

> [!IMPORTANT]
> Azure AD B2C 로그를 다른 모니터링 솔루션 또는 리포지토리로 전송할 계획이면 다음 사항을 고려하세요. Azure AD B2C 로그는 개인 데이터를 포함합니다. 해당 데이터는 적절한 기술 또는 조직적인 조치를 통해 무단 처리 또는 불법 처리로부터의 보호를 포함하여 개인 데이터의 적절한 보안을 보장하는 방식으로 처리되어야 합니다.

Azure Monitor를 사용하여 Azure AD B2C에 대한 모니터링을 구성하는 방법을 알아보려면 이 비디오를 시청하세요.  

>[!Video https://www.youtube.com/embed/tF2JS6TGc3g]

## <a name="deployment-overview"></a>배포 개요

Azure AD B2C는 [Azure Active Directory 모니터링](../active-directory/reports-monitoring/overview-monitoring.md)을 활용합니다. Azure AD 테넌트와 달리 Azure AD B2C 테넌트에는 구독이 연결될 수 없으므로 로그를 보낼 Log Analytics와 Azure AD B2C 간 통합을 위해 몇 가지 추가 단계를 수행해야 합니다.
Azure AD B2C 테넌트 내의 Azure Active Directory에서 _진단 설정_ 을 사용하려면 [Azure Lighthouse](../lighthouse/overview.md)를 사용하여 [리소스를 위임](../lighthouse/concepts/architecture.md)합니다. 이를 통해 Azure AD B2C(**서비스 공급자**)가 Azure AD(**고객**) 리소스를 관리할 수 있습니다.

> [!TIP]
> Azure Lighthouse는 일반적으로 여러 고객의 리소스를 관리하는 데 사용됩니다. 그러나 단일 리소스 그룹의 관리만 위임한다는 점을 제외하고, 여기서 수행하는 것처럼 **여러 개의 자체 Azure AD 테넌트가 있는 기업 내** 의 리소스를 관리하는 데도 사용할 수 있습니다.

이 문서의 단계를 완료하면 새 리소스 그룹(여기서는 _azure-ad-b2c-monitor_ 라고 함)을 만들고, **Azure AD B2C** 포털의 [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md)을 포함하는 동일한 리소스 그룹에 액세스할 수 있게 됩니다. 또한 Azure AD B2C에서 Log Analytics 작업 영역으로 로그를 전송할 수 있습니다.

이 배포 중에는 Azure 구독이 포함된 테넌트 내에서 Log Analytics 작업 영역 인스턴스를 구성하도록 Azure AD B2C 디렉터리의 사용자 또는 그룹에 권한을 부여합니다. 권한 부여를 만들려면 Log Analytics 작업 영역이 포함된 구독에 [Azure Resource Manager](../azure-resource-manager/index.yml) 템플릿을 배포합니다.

다음 다이어그램은 Azure AD 및 Azure AD B2C 테넌트에서 구성할 구성 요소를 보여 줍니다.

![리소스 그룹 프로젝션](./media/azure-monitor/resource-group-projection.png)

이 배포 중에 Log Analytics 작업 영역이 호스팅될 Azure AD B2C 테넌트와 Azure AD 테넌트를 모두 구성합니다. 사용 중인 Azure AD B2C 계정(예: 관리자 계정)에 Azure AD B2C 테넌트에 대한 [전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator) 역할을 할당해야 합니다. 배포를 실행하는 데 사용되는 Azure AD 계정에 Azure AD 구독의 [소유자](../role-based-access-control/built-in-roles.md#owner) 역할을 할당해야 합니다. 설명된 대로 각 단계를 완료할 때 올바른 디렉터리에 로그인했는지 확인하는 것도 중요합니다.

요약하면, Azure Lighthouse를 사용하여 Azure AD B2C 테넌트의 사용자나 그룹이 다른 테넌트(Azure AD 테넌트)와 연결된 구독의 리소스 그룹을 관리하도록 허용합니다. 이 권한 부여가 완료되면 Azure AD B2C의 진단 설정에서 구독과 로그 분석 작업 영역을 대상으로 선택할 수 있습니다.

## <a name="1-create-or-choose-resource-group"></a>1. 리소스 그룹 만들기 또는 선택하기

먼저 Azure AD B2C에서 데이터를 받을 대상 Log Analytics 작업 영역을 포함하는 리소스 그룹을 만들거나 선택합니다. Azure Resource Manager 템플릿을 배포할 때 리소스 그룹 이름을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. [리소스 그룹을 만들거나](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) 기존 항목을 선택합니다. 이 예제에서는 _azure-ad-b2c-monitor_ 라는 리소스 그룹을 사용합니다.

## <a name="2-create-a-log-analytics-workspace"></a>2. Log Analytics 작업 영역 만들기

**Log Analytics 작업 영역** 은 Azure Monitor 로그 데이터를 위한 고유한 환경입니다. 이 Log Analytics 작업 영역을 사용하여 Azure AD B2C [감사 로그](view-audit-logs.md)에서 데이터를 수집한 다음 쿼리 및 통합 문서를 사용하여 시각화하거나 경고를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. [Log Analytics 작업 영역을 만듭니다](../azure-monitor/logs/quick-create-workspace.md). 이 예제에서는 _azure-ad-b2c-monitor_ 이라는 리소스 그룹에서 이름이 _AzureAdB2C_ 인 Log Analytics 작업 영역을 사용합니다.

## <a name="3-delegate-resource-management"></a>3. 리소스 관리 위임

이 단계에서는 Azure AD B2C 테넌트를 **서비스 공급자** 로 선택합니다. Azure AD 테넌트의 그룹에 적절한 Azure 기본 제공 역할을 할당하는 데 필요한 권한 부여도 정의합니다.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 Azure AD B2C 테넌트 ID 가져오기

먼저 Azure AD B2C 디렉터리(디렉터리 ID라고도 함)의 **테넌트 ID** 를 가져옵니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure Active Directory** 를 선택하고 **개요** 를 선택합니다.
1. **테넌트 ID** 를 기록합니다.

### <a name="32-select-a-security-group"></a>3.2 보안 그룹 선택

이제 구독을 포함하는 디렉터리에서 이전에 만든 리소스 그룹에 사용 권한을 부여하려는 Azure AD B2C 그룹 또는 사용자를 선택합니다.

좀 더 쉽게 관리하기 위해서는 해당 사용자에게 직접 사용 권한을 할당하는 대신, 각 역할에 Azure AD 사용자 _그룹_ 을 사용하여 그룹에서 개별 사용자를 추가하거나 제거할 수 있게 하는 것이 좋습니다. 이 연습에서는 보안 그룹을 추가합니다.

> [!IMPORTANT]
> Azure AD 그룹에 대한 사용 권한을 추가하려면 **그룹 유형** 이 **보안** 으로 설정되어 있어야 합니다. 이 옵션은 그룹을 만들 때 선택됩니다. 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

1. **Azure AD B2C** 디렉터리에서 **Azure Active Directory** 를 아직 선택된 상태에서 **그룹** 을 선택한 다음, 그룹을 선택합니다. 기존 그룹이 없는 경우 **보안** 그룹을 만들고 멤버를 추가합니다. 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)의 절차를 따릅니다.
1. **개요** 를 선택하고 그룹의 **개체 ID** 를 기록합니다.

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 Azure Resource Manager 템플릿 만들기

Azure Lighthouse에서 사용자 지정 권한 부여와 위임을 만들기 위해 이전에 만든 Azure AD 리소스 그룹(예: _azure-ad-b2c-monitor_)에 대한 액세스 권한을 Azure AD B2C에 부여하는 Azure Resource Manager 템플릿을 사용합니다. **Azure에 배포** 단추를 사용하여 GitHub 샘플에서 템플릿을 배포합니다. 그러면 Azure Portal 열리며 포털에서 직접 템플릿을 구성하고 배포할 수 있습니다. 이 단계를 수행하려면 Azure AD 테넌트(Azure AD B2C 테넌트가 아님)에 로그인해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. **Azure에 배포** 단추를 사용하여 Azure Portal을 열고 포털에서 직접 템플릿을 배포합니다. 자세한 내용은 [Azure Resource Manager 템플릿 만들기](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template)를 참조하세요.

   [![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

1. **사용자 지정 배포** 페이지에서 다음 정보를 제공합니다.

   | 필드                 | 정의                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
   | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | Subscription          | _azure-ad-b2c-monitor_ 리소스 그룹이 만들어진 Azure 구독이 포함된 디렉터리를 선택합니다.                                                                                                                                                                                                                                                                                                                                                                                                       |
   | 지역                | 리소스가 배포될 지역을 선택합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
   | Msp 제안 이름        | 이 정의를 설명하는 이름입니다. 예: _Azure AD B2C Monitoring_. 해당 이름이 Azure Lighthouse에 표시됩니다.                                                                                                                                                                                                                                                                                                                                                                                          |
   | Msp 제안 설명 | 제안에 관한 간단한 설명. 예: _Enables Azure Monitor in Azure AD B2C_.                                                                                                                                                                                                                                                                                                                                                                                                                                     |
   | 테넌트 ID로 관리  | Azure AD B2C 테넌트의 **테넌트 ID**(디렉터리 ID라고도 함).                                                                                                                                                                                                                                                                                                                                                                                                                                              |
   | 권한 부여        | Azure AD `principalId`, `principalIdDisplayName`, 및 Azure`roleDefinitionId`를 포함하는 개체의 JSON 배열을 지정합니다. `principalId`는 이 Azure 구독의 리소스에 액세스할 수 있는 B2C 그룹 또는 사용자의 **개체 ID** 입니다. 이 연습에서는 이전에 기록한 그룹의 개체 ID를 지정합니다. `roleDefinitionId`에는 _기여자 역할_ 을 위한 [기본 제공 역할](../role-based-access-control/built-in-roles.md) 값을 사용 `b24988ac-6180-42a0-ab88-20f7382dd24c`합니다. |
   | Rg 이름               | Azure AD 테넌트 내에 있는 앞서 만든 리소스 그룹의 이름입니다. 예: _azure-ad-b2c-monitor_.                                                                                                                                                                                                                                                                                                                                                                                                              |

   다음 예제에서는 하나의 보안 그룹을 사용하는 권한 부여 배열을 보여 줍니다.

   ```json
   [
     {
       "principalId": "<Replace with group's OBJECT ID>",
       "principalIdDisplayName": "Azure AD B2C tenant administrators",
       "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
     }
   ]
   ```

템플릿을 배포한 후에는 리소스 프로젝션이 완료될 때까지 몇 분 정도(일반적으로 5분) 걸릴 수 있습니다. Azure AD 테넌트에서 배포를 확인하고 리소스 프로젝션의 세부 정보를 가져올 수 있습니다. 자세한 내용은 [서비스 공급자 보기 및 관리](../lighthouse/how-to/view-manage-service-providers.md)를 참조하세요.

## <a name="4-select-your-subscription"></a>4. 구독 선택

템플릿을 배포하고 리소스 프로젝션이 완료될 때까지 몇 분 기다린 후, 다음 단계에 따라 구독을 Azure AD B2C 디렉터리와 연결합니다.

1. Azure Portal에 현재 로그인되어 있으면 로그아웃합니다. 이렇게 하면 다음 단계에서 세션 자격 증명을 새로 고칠 수 있습니다.
1. **Azure AD B2C** 관리 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 이 계정은 [리소스 관리 위임](#3-delegate-resource-management) 단계에서 지정한 보안 그룹의 멤버여야 합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 사용자가 만든 Azure 구독 및 _azure-ad-b2c-monitor_ 리소스 그룹이 있는 Azure AD 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 올바른 디렉터리 및 구독을 선택했는지 확인합니다.

## <a name="5-configure-diagnostic-settings"></a>5. 진단 설정 구성

진단 설정은 리소스에 대한 로그 및 메트릭을 보낼 위치를 정의합니다. 가능한 대상은 다음과 같습니다.

- [Azure Storage 계정](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- [Event hubs](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) 솔루션
- [Log Analytics 작업 영역](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

이 예제에서는 Log Analytics 작업 영역을 사용하여 대시보드를 만듭니다.

### <a name="51-create-diagnostic-settings"></a>5.1 진단 설정 만들기

Azure Portal에서 [진단 설정을 만들](../active-directory/reports-monitoring/overview-monitoring.md) 준비가 되었습니다.

Azure AD B2C 활동 로그를 위한 모니터링 설정을 구성하려면

1. Azure AD B2C 관리 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 이 계정은 [보안 그룹 선택](#32-select-a-security-group) 단계에서 지정한 보안 그룹의 멤버여야 합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure Active Directory 선택**
1. **모니터링** 아래에서 **진단 설정** 을 선택합니다.
1. 리소스에 대한 기존 설정이 있는 경우 이미 구성된 설정 목록이 표시됩니다. **진단 설정 추가** 를 선택하여 새 설정을 추가하거나 **편집** 을 선택하여 기존 설정을 편집합니다. 각 설정에는 대상 유형이 하나만 있을 수 있습니다.

   ![Azure Portal의 진단 설정 창](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 아직 없는 경우 설정에 이름을 지정합니다.
1. 로그를 보낼 각 대상의 확인란을 선택합니다. **구성** 을 선택하여 **다음 표에 설명된 대로** 설정을 지정합니다.
1. **Log Analytics 작업 영역에 보내기** 를 선택하고, 앞에서 만든 **작업 영역 이름**`AzureAdB2C`을 선택합니다.
1. **AuditLogs** 및 **SignInLogs** 를 선택합니다.
1. **저장** 을 선택합니다.

> [!NOTE]
> 이벤트를 내보낸 후 [Log Analytics 작업 영역에 표시](../azure-monitor/logs/data-ingestion-time.md)되기까지 최대 15분이 걸릴 수 있습니다. 또한 데이터 부실에 영향을 주고 보고에서 중요한 역할을 수행할 수 있는 [Active Directory 보고 대기 시간](../active-directory/reports-monitoring/reference-reports-latencies.md)에 관해 자세히 알아봅니다.

“Azure AD B2C 디렉터리에 Azure Monitor를 사용하도록 진단 설정을 지정하려면 위임된 리소스 관리를 설정해야 합니다.”라는 오류 메시지가 표시되는 경우 [보안 그룹](#32-select-a-security-group)의 구성원인 사용자로 로그인하고 [구독을 선택](#4-select-your-subscription)해야 합니다.

## <a name="6-visualize-your-data"></a>6. 데이터 시각화

이제 Log Analytics 작업 영역을 구성하여 데이터를 시각화하고 경고를 구성할 수 있습니다. Azure AD 테넌트와 Azure AD B2C 테넌트 모두에서 이렇게 구성할 수 있습니다.

### <a name="61-create-a-query"></a>6.1 쿼리 만들기

로그 쿼리를 사용하면 Azure Monitor 로그에서 수집된 데이터의 값을 완벽하게 활용할 수 있습니다. 강력한 쿼리 언어를 사용하면 여러 테이블의 데이터를 조인하고, 큰 데이터 집합을 집계하고, 최소한의 코드로 복잡한 작업을 수행할 수 있습니다. 지원 데이터가 수집되는 동안 거의 모든 질문에 답변이 제공되고 분석되며, 올바른 쿼리를 구성하는 방법을 이해할 수 있습니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조하세요.

1. **Log Analytics 작업 영역** 에서 **로그** 를 선택합니다.
1. 쿼리 편집기에서 다음 [Kusto 쿼리 언어](/azure/data-explorer/kusto/query/) 쿼리를 붙여넣습니다. 이 쿼리는 지난 x일 동안의 정책 사용량을 작업별로 보여 줍니다. 기간은 기본적으로 90일(90d)로 설정됩니다. 쿼리는 토큰/코드가 정책에 의해 발급되는 작업에만 초점을 맞추고 있습니다.

   ```kusto
   AuditLogs
   | where TimeGenerated  > ago(90d)
   | where OperationName contains "issue"
   | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
   | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
   | summarize SignInCount = count() by Policy, OperationName
   | order by SignInCount desc  nulls last
   ```

1. **실행** 을 선택합니다. 쿼리 결과가 화면 아래쪽에 표시됩니다.
1. 쿼리를 나중에 사용하기 위해 저장하려면 **저장** 을 선택합니다.

   ![Log Analytics 로그 편집기](./media/azure-monitor/query-policy-usage.png)

1. 다음 세부 정보를 입력합니다.

   - **이름**: 쿼리의 이름을 입력합니다.
   - **다른 이름으로 저장** - `query`를 선택합니다.
   - **범주** - `Log`를 선택합니다.

1. **저장** 을 선택합니다.

[render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 연산자를 사용하여 데이터를 시각화하도록 쿼리를 변경할 수도 있습니다.

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics 로그 편집기 파이](./media/azure-monitor/query-policy-usage-pie.png)

더 많은 샘플은 Azure AD B2C [SIEM GitHub 리포지토리](https://aka.ms/b2csiem)를 참조하세요.

### <a name="62-create-a-workbook"></a>6.2 통합 문서 만들기

통합 문서는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. 이를 통해 Azure에서 여러 데이터 원본을 탭하여 통합된 대화형 환경으로 결합할 수 있습니다. 자세한 내용은 [Azure Monitor Workbooks](../azure-monitor/visualize/workbooks-overview.md)를 참조하세요.

JSON 갤러리 템플릿을 사용하여 새 통합 문서를 만들려면 아래 지침을 따르세요. 이 통합 문서는 Azure AD B2C 테넌트를 위한 **사용자 인사이트** 및 **인증** 대시보드를 제공합니다.

1. **Log Analytics 작업 영역** 에서 **통합 문서** 를 선택합니다.
1. 도구 모음에서 **+ 새로 만들기** 옵션을 선택하여 새 통합 문서를 만듭니다.
1. **새 통합 문서** 페이지의 도구 모음에서 **</>** 옵션을 사용하여 **고급 편집기** 를 선택합니다.

   ![갤러리 템플릿](./media/azure-monitor/wrkb-adv-editor.png)

1. **갤러리 템플릿** 을 선택합니다.
1. **갤러리 템플릿** 의 JSON을 [Azure AD B2C 기본 통합 문서](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)의 내용으로 바꿉니다.
1. **적용** 단추를 사용하여 템플릿을 적용합니다.
1. 도구 모음에서 **편집 완료** 단추를 선택하여 통합 문서 편집을 마칩니다.
1. 마지막으로 도구 모음에서 **저장** 단추를 사용하여 통합 문서를 저장합니다.
1. **제목**(_Azure AD B2C 대시보드_ 등)을 입력합니다.
1. **저장** 을 선택합니다.

   ![통합 문서를 저장합니다.](./media/azure-monitor/wrkb-title.png)

통합 문서는 보고서를 대시보드 양식으로 표시합니다.

![통합 문서 첫 번째 대시보드](./media/azure-monitor/wkrb-dashboard-1.png)

![통합 문서 두 번째 대시보드](./media/azure-monitor/wrkb-dashboard-2.png)

![통합 문서 세 번째 대시보드](./media/azure-monitor/wrkb-dashboard-3.png)

## <a name="create-alerts"></a>경고 만들기

경고는 Azure Monitor에서 경고 규칙에 의해 생성되고 정기적으로 저장된 쿼리 또는 사용자 지정 로그 검색을 자동으로 실행할 수 있습니다. 특정 성능 메트릭을 기반으로 알림을 만들거나 특정 이벤트가 생성되거나 이벤트가 없거나 특정 기간 내에 여러 이벤트가 만들어질 때 알림을 만들 수 있습니다. 예를 들어, 평균 로그인 수가 특정 임계값을 초과하는 경우 경고를 사용하여 사용자에게 알릴 수 있습니다. 자세한 내용은 [경고 만들기](../azure-monitor/alerts/alerts-log.md)를 참조하세요.

다음 지침에 따라 새 Azure 경고를 만들 수 있습니다. 그러면 이전 기간과 비교하여 **전체 요청** 을 25% 삭제할 때마다 [메일 알림](../azure-monitor/alerts/action-groups.md#configure-notifications)을 보냅니다. 5분마다 경고가 실행되어 그 이전 1시간과 비교하여 지난 1시간 동안의 감소를 찾습니다. 이 경고는 Kusto 쿼리 언어를 사용하여 생성됩니다.

1. **Log Analytics 작업 영역** 에서 **로그** 를 선택합니다.
1. 아래 쿼리를 사용하여 새 **Kusto 쿼리** 를 만듭니다.

   ```kusto
   let start = ago(2h);
   let end = now();
   let threshold = -25; //25% decrease in total requests.
   AuditLogs
   | serialize TimeGenerated, CorrelationId, Result
   | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated from start to end step 1h
   | mvexpand TimeGenerated, TotalRequests
   | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-MM-dd [HH:mm:ss]')
   | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
   | order by TimeGeneratedFormatted desc
   | where PercentageChange <= threshold   //Trigger's alert rule if matched.
   ```

1. **실행** 을 선택하여 쿼리를 테스트합니다. 지난 1시간 이내에 총 요청 수가 25% 이상 감소한 경우 결과가 표시됩니다.
1. 위의 쿼리를 기반으로 경고 규칙을 만들려면 도구 모음에서 사용할 수 있는 **+ 새 경고 규칙** 옵션을 사용합니다.
1. **경고 규칙 만들기** 페이지에서 **조건 이름** 을 선택합니다.
1. **신호 논리 구성** 페이지에서 다음 값을 설정하고 **완료** 단추를 사용하여 변경 내용을 저장합니다.

   - 경고 논리: **0** **보다 큰** **결과의 수** 를 설정합니다.
   - 평가 기준: 기간(분)에서 **120**, 빈도(분)에서 **5** 를 선택합니다.

   ![경고 규칙 조건 만들기](./media/azure-monitor/alert-create-rule-condition.png)

경고가 생성된 후 **Log Analytics 작업 영역** 으로 이동하여 **경고** 를 선택합니다. 이 페이지에는 **시간 범위** 옵션으로 설정된 기간에 실행되는 모든 경고가 표시됩니다.

### <a name="configure-action-groups"></a>작업 그룹 구성

Azure Monitor 및 Service Health 경고는 작업 그룹을 사용하여 경고가 트리거되었음을 사용자에게 알립니다. 음성 통화, SMS, 메일 보내기나, 자동화된 여러 작업 형식 트리거를 포함할 수 있습니다. [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md) 참고 자료를 따르세요.

경고 알림 메일의 예는 다음과 같습니다.

![메일 알림](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>다중 테넌트

여러 Azure AD B2C 테넌트 로그를 동일한 Log Analytics 작업 영역(또는 Azure 스토리지 계정이나 이벤트 허브)에 온보딩하려면 다른 **Msp 제품 이름** 값을 사용한 별도의 배포가 필요합니다. Log Analytics 작업 영역이 [리소스 그룹 만들기 또는 선택하기](#1-create-or-choose-resource-group)에서 구성한 것과 동일한 리소스 그룹에 있는지 확인합니다.

여러 Log Analytics 작업 영역에서 작업하는 경우 [작업 영역 간 쿼리](../azure-monitor/logs/cross-workspace-query.md)를 사용하여 여러 작업 영역에서 작동하는 쿼리를 만듭니다. 예를 들어 다음 쿼리는 동일한 범주(예: 인증)를 기반으로 서로 다른 테넌트의 두 감사 로그를 조인합니다.

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

Azure Monitor 로그는 Azure에 배포되거나 회사의 모든 원본에서 매일 대량의 데이터를 수집, 인덱싱, 저장할 때 크기 조정 및 지원을 할 수 있도록 설계됐습니다. 기본적으로 로그는 30일 동안 유지되지만, 재방문 주기는 최대 2년까지 늘릴 수 있습니다. [Azure Monitor 로그를 사용하여 사용량 및 비용 관리](../azure-monitor/logs/manage-cost-storage.md)하는 방법을 알아봅니다. 가격 책정 계층을 선택한 후에 [데이터 보존 기간](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)을 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure AD B2C [SIEM 갤러리](https://aka.ms/b2csiem)에서 더 많은 샘플을 찾습니다.

- Azure Monitor에서 진단 설정을 추가하고 구성하는 방법에 관한 자세한 내용은 [자습서: Azure 리소스에서 리소스 로그 수집 및 분석](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.

- Azure AD 로그를 이벤트 허브로 스트리밍하는 방법에 관한 자세한 내용은 [자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)을 참조하세요.
