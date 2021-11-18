---
title: Microsoft Sentinel | 권한 Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel이 Azure 역할 기반 액세스 제어를 사용하여 사용자에게 권한을 할당하는 방법을 설명하고 각 역할에 대해 허용되는 작업을 식별합니다.
author: yelevin
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a1cfff1f84c7193352ae16fc3b2be43f34e89038
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132763623"
---
# <a name="permissions-in-microsoft-sentinel"></a>Microsoft Sentinel의 권한

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel은 [Azure RBAC(Azure 역할 기반 액세스 제어)를](../role-based-access-control/role-assignments-portal.md) 사용하여 [Azure의](../role-based-access-control/built-in-roles.md) 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공합니다.

Azure RBAC를 사용하여 보안 운영 팀 내에서 역할을 만들고 할당하여 Microsoft Sentinel에 대한 적절한 액세스 권한을 부여합니다. 다양한 역할을 통해 Microsoft Sentinel 사용자가 보고 수행할 수 있는 작업을 세밀하게 제어할 수 있습니다. Azure 역할은 Microsoft Sentinel 작업 영역에서 직접 할당하거나(아래 참고 참조) Microsoft Sentinel이 상속할 작업 영역이 속한 구독 또는 리소스 그룹에서 할당할 수 있습니다.

## <a name="roles-for-working-in-microsoft-sentinel"></a>Microsoft Sentinel에서 작업하기 위한 역할

### <a name="microsoft-sentinel-specific-roles"></a>Microsoft Sentinel 관련 역할

**모든 Microsoft Sentinel 기본 제공 역할은 Microsoft Sentinel 작업 영역의 데이터에 대한 읽기 권한을 부여합니다.**

- [Microsoft Sentinel Reader는](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) 데이터, 인시던트, 통합 문서 및 기타 Microsoft Sentinel 리소스를 볼 수 있습니다.

- [Microsoft Sentinel 응답자는](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) 위와 함께 인시던트(할당, 해제 등)를 관리할 수 있습니다.

- [Microsoft Sentinel 기여자는](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 위와 함께 통합 문서, 분석 규칙 및 기타 Microsoft Sentinel 리소스를 만들고 편집할 수 있습니다.

- [Microsoft Sentinel Automation 기여자를](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 사용하면 Microsoft Sentinel에서 자동화 규칙에 플레이북을 추가할 수 있습니다. 이 역할은 사용자 계정에 할당할 수 없습니다.

> [!NOTE]
>
> - 최상의 결과를 위해 Microsoft Sentinel 작업 영역이 포함된 **리소스 그룹에** 이러한 역할을 할당해야 합니다. 이러한 방식으로 역할은 Microsoft Sentinel을 지원하기 위해 배포된 모든 리소스에 적용됩니다. 이러한 리소스는 동일한 리소스 그룹에도 배치되어야 합니다.
>
> - 또 다른 옵션은 Microsoft Sentinel 작업 **영역** 자체에서 직접 역할을 할당하는 것입니다. 이렇게 하는 경우 해당 작업 영역의 SecurityInsights **솔루션 리소스** 에 동일한 역할을 할당해야 합니다. 다른 리소스에도 역할을 할당해야 할 수 있으며 리소스에 대한 역할 할당을 지속적으로 관리해야 합니다.

### <a name="additional-roles-and-permissions"></a>추가 역할 및 권한

특정 작업 요구 사항을 가진 사용자는 작업을 수행하기 위해 추가 역할 또는 특정 권한을 할당받아야 할 수 있습니다.

- **플레이북을 사용하여 위협에 대한 대응 자동화**

    Microsoft Sentinel은 자동화된 위협 **대응에 플레이북을** 사용합니다. 플레이북은 **Azure Logic Apps** 를 기반으로 하는 별도의 Azure 리소스입니다. SOAR(보안 오케스트레이션, 자동화, 응답) 작업에 Logic Apps를 사용하는 기능은 보안 운영 팀의 특정 구성원에게 할당하는 것이 좋습니다. [Logic App 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor) 역할을 사용하여 플레이북 사용에 대한 명시적 권한을 할당할 수 있습니다.

- **Microsoft Sentinel에 데이터 원본 연결**

    사용자가 **데이터 커넥터를** 추가하려면 Microsoft Sentinel 작업 영역에 대한 사용자 쓰기 권한을 할당해야 합니다. 또한 커넥터별로 필요한 추가 권한을 확인하세요. 관련 커넥터 페이지에 나열되어 있습니다.

- **인시던트를 할당하는 게스트 사용자**

    게스트 사용자가 인시던트 할당을 할 수 있어야 하는 경우 Microsoft Sentinel 응답기 역할 외에도 사용자에게 [디렉터리 읽기](../active-directory/roles/permissions-reference.md#directory-readers)권한자 역할을 할당해야 합니다. 이 역할은 Azure 역할이 ‘아닌’ **Azure Active Directory** 역할이며 일반(비 게스트) 사용자에게는 이 역할이 기본적으로 할당되어 있습니다.

- **통합 문서 만들기 및 삭제**

    사용자가 Microsoft Sentinel 통합 문서를 만들고 삭제하려면 모니터링 [기여자](../role-based-access-control/built-in-roles.md#monitoring-contributor)의 Azure Monitor 역할로 사용자를 할당해야 합니다. 이 역할은 통합 문서를 *사용하는* 데 필요하지 않으며 만들고 삭제하는 경우에만 필요합니다.

### <a name="other-roles-you-might-see-assigned"></a>할당된 것으로 표시되는 다른 역할

Microsoft Sentinel 특정 Azure 역할을 할당할 때 다른 용도로 사용자에게 할당되었을 수 있는 다른 Azure 및 Log Analytics Azure 역할이 발생할 수 있습니다. 이러한 역할은 Microsoft Sentinel 작업 영역 및 기타 리소스에 대한 액세스를 포함하는 광범위한 권한 집합을 부여합니다.

- **Azure 역할:** [소유자](../role-based-access-control/built-in-roles.md#owner), [기여자](../role-based-access-control/built-in-roles.md#contributor) 및 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader)입니다. Azure 역할은 Log Analytics 작업 영역 및 Microsoft Sentinel 리소스를 비롯한 모든 Azure 리소스에 대한 액세스 권한을 부여합니다.

- **Log Analytics 역할:** [Log Analytics 기여자](../role-based-access-control/built-in-roles.md#log-analytics-contributor) 및 [Log Analytics 읽기 권한자](../role-based-access-control/built-in-roles.md#log-analytics-reader)입니다. Log Analytics 역할은 Log Analytics 작업 영역에 대한 액세스 권한을 부여합니다.

예를 들어 **Microsoft Sentinel Reader** 역할이 할당되었지만 Microsoft **Sentinel 기여자** 역할은 할당되지 않은 사용자는 Azure 수준 **기여자** 역할이 할당된 경우에도 Microsoft Sentinel의 항목을 편집할 수 있습니다. 따라서 Microsoft Sentinel에서 사용자에게만 사용 권한을 부여하려는 경우 이 사용자의 이전 권한을 신중하게 제거하여 다른 리소스에 대한 필요한 액세스를 중단하지 않도록 해야 합니다.

## <a name="microsoft-sentinel-roles-and-allowed-actions"></a>Microsoft Sentinel 역할 및 허용되는 작업

다음 표에는 Microsoft Sentinel 역할 및 Microsoft Sentinel에서 허용되는 작업이 요약되어 있습니다.

| 역할 | 플레이북 생성 및 실행| 분석 규칙 및 기타 Microsoft Sentinel 리소스 만들기 및 편집 [*](#workbooks) | 인시던트 관리(해제, 할당 등) | 데이터, 인시던트, 통합 문서 및 기타 Microsoft Sentinel 리소스 보기 |
|---|---|---|---|---|
| Microsoft Sentinel 판독기 | -- | -- | -- | &#10003; |
| Microsoft Sentinel 응답기 | -- | -- | &#10003; | &#10003; |
| Microsoft Sentinel 기여자 | -- | &#10003; | &#10003; | &#10003; |
| Microsoft Sentinel 기여자 + 논리 앱 기여자 | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* 통합 문서를 만들고 삭제하려면 추가 [Monitoring Contributor](../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할이 필요합니다. 자세한 내용은 [추가 역할 및 권한](#additional-roles-and-permissions)을 참조하세요.
## <a name="custom-roles-and-advanced-azure-rbac"></a>사용자 지정 역할 및 고급 Azure RBAC

- **사용자 지정 역할**. Azure 기본 제공 역할 외에도 또는 대신 Microsoft Sentinel에 대한 Azure 사용자 지정 역할을 만들 수 있습니다. Microsoft Sentinel에 대한 Azure 사용자 지정 역할은 [Microsoft Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) 및 Azure Log Analytics 리소스에 대한 특정 권한에 따라 다른 Azure [사용자 지정 역할을](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)만드는 것과 동일한 방식으로 [만들어집니다.](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)

- **Log Analytics RBAC**. Microsoft Sentinel 작업 영역의 데이터에서 Log Analytics 고급 Azure 역할 기반 액세스 제어를 사용할 수 있습니다. 여기에는 데이터 형식 기반 Azure RBAC 및 리소스-컨텍스트 Azure RBAC가 모두 포함됩니다. 자세한 내용은 다음을 참조하세요.

    - [Azure Monitor에서 로그 데이터 및 작업 영역 관리](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Microsoft Sentinel에 대한 리소스 컨텍스트 RBAC](resource-context-rbac.md)
    - [테이블 수준 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    리소스 컨텍스트 및 테이블 수준 RBAC는 전체 Microsoft Sentinel 환경 액세스를 허용하지 않고 Microsoft Sentinel 작업 영역의 특정 데이터에 대한 액세스를 제공하는 두 가지 방법입니다.

## <a name="role-recommendations"></a>역할 권장 사항

Microsoft Sentinel에서 역할 및 권한의 작동 방식을 이해한 후에는 사용자에게 역할을 적용하기 위해 다음 모범 사례 지침을 사용할 수 있습니다.

|사용자 유형  |역할 |Resource group  |설명  |
|---------|---------|---------|---------|
|**보안 분석가**     | [Microsoft Sentinel 응답기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)        | Microsoft Sentinel의 리소스 그룹        | 데이터, 인시던트, 통합 문서 및 기타 Microsoft Sentinel 리소스를 봅니다. <br><br>인시던트를 할당하거나 해제하는 등, 인시던트를 관리합니다.        |
|     | [Logic Apps 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Microsoft Sentinel의 리소스 그룹 또는 플레이북이 저장된 리소스 그룹        | 플레이북을 분석 및 자동화 규칙에 연결하고 플레이북을 실행합니다. <br><br>**참고**: 이 역할을 통해 사용자는 플레이북을 수정할 수도 있습니다.         |
|**보안 엔지니어**     | [Microsoft Sentinel 기여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)       |Microsoft Sentinel의 리소스 그룹         |   데이터, 인시던트, 통합 문서 및 기타 Microsoft Sentinel 리소스를 봅니다. <br><br>인시던트를 할당하거나 해제하는 등, 인시던트를 관리합니다. <br><br>통합 문서, 분석 규칙 및 기타 Microsoft 센티널 리소스를 만들고 편집 합니다.      |
|     | [Logic Apps 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Microsoft 센티널의 리소스 그룹 또는 플레이 북이 저장 되는 리소스 그룹        | 플레이북을 분석 및 자동화 규칙에 연결하고 플레이북을 실행합니다. <br><br>**참고**: 이 역할을 통해 사용자는 플레이북을 수정할 수도 있습니다.         |
|  Service Principal   | [Microsoft 센티널 참여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)      |  Microsoft 센티널의 리소스 그룹       | 관리 작업에 대한 자동화된 구성 |
|     |         |        | |

> [!TIP]
> 수집하거나 모니터링하는 데이터에 따라 추가 역할이 필요할 수 있습니다. 예를 들어, 다른 Microsoft 포털의 서비스에 대한 데이터 커넥터를 설정하려면 전역 관리자 또는 보안 관리자 역할과 같은 Azure AD 역할이 필요할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널 사용자를 위해 역할을 수행 하는 방법 및 사용자가 수행할 수 있는 각 역할에 대해 알아보았습니다.

[Microsoft 센티널 블로그에서](https://aka.ms/azuresentinelblog)Azure 보안 및 규정 준수에 대 한 블로그 게시물을 찾습니다.
