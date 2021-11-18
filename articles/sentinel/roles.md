---
title: Microsoft 센티널의 권한 | Microsoft Docs
description: 이 문서에서는 Microsoft 센티널에서 Azure 역할 기반 액세스 제어를 사용 하 여 사용자에 게 권한을 할당 하 고 각 역할에 대해 허용 되는 작업을 식별 하는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdca3fe600fe41f0f4a5f54ebe718481881b1ae4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712189"
---
# <a name="permissions-in-microsoft-sentinel"></a>Microsoft 센티널의 사용 권한

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft 센티널은 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 를 사용 하 여 azure의 사용자, 그룹 및 서비스에 할당할 수 있는 [기본 제공 역할](../role-based-access-control/built-in-roles.md) 을 제공 합니다.

Azure RBAC를 사용 하 여 보안 운영 팀 내에서 역할을 만들고 할당 하 여 Microsoft 센티널에 적절 한 액세스 권한을 부여 합니다. 다른 역할을 통해 Microsoft 센티널의 사용자가 보고 수행할 수 있는 작업을 세밀 하 게 제어할 수 있습니다. Microsoft 센티널 작업 영역에서 직접 (아래 참고 참조) 또는 작업 영역이 속한 구독 또는 리소스 그룹에서 Azure 역할을 할당할 수 있습니다 .이 작업은 Microsoft 센티널에서 상속 합니다.

## <a name="roles-for-working-in-microsoft-sentinel"></a>Microsoft 센티널에서 작업 하기 위한 역할

### <a name="microsoft-sentinel-specific-roles"></a>Microsoft 센티널 특정 역할

**모든 Microsoft 센티널 기본 제공 역할은 Microsoft 센티널 작업 영역에 있는 데이터에 대 한 읽기 액세스 권한을 부여 합니다.**

- [Microsoft 센티널 판독기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) 는 데이터, 인시던트, 통합 문서 및 기타 microsoft 센티널 리소스를 볼 수 있습니다.

- [Microsoft 센티널 응답자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) 는 위의 외에도 인시던트 관리 (할당, 해제 등)를 수행할 수 있습니다.

- [Microsoft 센티널 참여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 는 위의 방법 외에도 통합 문서, 분석 규칙 및 기타 Microsoft 센티널 리소스를 만들고 편집할 수 있습니다.

- Microsoft [센티널 automation 기여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) 를 사용 하면 microsoft 센티널에서 플레이 북를 Automation 규칙에 추가할 수 있습니다. 이 역할은 사용자 계정에 할당할 수 없습니다.

> [!NOTE]
>
> - 최상의 결과를 위해 이러한 역할은 Microsoft 센티널 작업 영역을 포함 하는 **리소스 그룹** 에 할당 되어야 합니다. 이러한 방식으로 이러한 리소스는 동일한 리소스 그룹에도 배치 되어야 하므로 Microsoft 센티널을 지원 하기 위해 배포 된 모든 리소스에 역할이 적용 됩니다.
>
> - 또 다른 옵션은 Microsoft 센티널 **작업 영역** 자체에서 역할을 직접 할당 하는 것입니다. 이렇게 하는 경우 해당 작업 영역의 SecurityInsights **솔루션 리소스** 에 동일한 역할을 할당해야 합니다. 다른 리소스에도 역할을 할당해야 할 수 있으며 리소스에 대한 역할 할당을 지속적으로 관리해야 합니다.

### <a name="additional-roles-and-permissions"></a>추가 역할 및 권한

특정 작업 요구 사항을 가진 사용자는 작업을 수행하기 위해 추가 역할 또는 특정 권한을 할당받아야 할 수 있습니다.

- **플레이북을 사용하여 위협에 대한 대응 자동화**

    Microsoft 센티널은 플레이 **북** 을 자동화 된 위협 대응에 사용 합니다. 플레이북은 **Azure Logic Apps** 를 기반으로 하는 별도의 Azure 리소스입니다. SOAR(보안 오케스트레이션, 자동화, 응답) 작업에 Logic Apps를 사용하는 기능은 보안 운영 팀의 특정 구성원에게 할당하는 것이 좋습니다. [Logic App 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor) 역할을 사용하여 플레이북 사용에 대한 명시적 권한을 할당할 수 있습니다.

- **Microsoft 센티널에 데이터 원본 연결**

    사용자가 **데이터 커넥터** 를 추가 하려면 Microsoft 센티널 작업 영역에 대 한 사용자 쓰기 권한을 할당 해야 합니다. 또한 커넥터별로 필요한 추가 권한을 확인하세요. 관련 커넥터 페이지에 나열되어 있습니다.

- **인시던트를 할당하는 게스트 사용자**

    게스트 사용자가 인시던트를 할당할 수 있어야 하는 경우 Microsoft 센티널 응답자 역할 외에도 사용자에 게 [디렉터리 판독기](../active-directory/roles/permissions-reference.md#directory-readers)의 역할을 할당 해야 합니다. 이 역할은 Azure 역할이 ‘아닌’ **Azure Active Directory** 역할이며 일반(비 게스트) 사용자에게는 이 역할이 기본적으로 할당되어 있습니다.

- **통합 문서 만들기 및 삭제**

    사용자가 Microsoft 센티널 통합 문서를 만들고 삭제 하려면 사용자가 [모니터링 참가자](../role-based-access-control/built-in-roles.md#monitoring-contributor)의 Azure Monitor 역할에도 할당 해야 합니다. 이 역할은 통합 문서를 *사용하는* 데 필요하지 않으며 만들고 삭제하는 경우에만 필요합니다.

### <a name="other-roles-you-might-see-assigned"></a>할당된 것으로 표시되는 다른 역할

Microsoft 센티널 특정 Azure 역할을 할당 하는 경우 다른 용도로 사용자에 게 할당 되었을 수 있는 다른 Azure 및 Log Analytics Azure 역할을 통해 제공 될 수 있습니다. 이러한 역할은 Microsoft 센티널 작업 영역 및 기타 리소스에 대 한 액세스를 포함 하는 보다 광범위 한 사용 권한 집합을 부여 한다는 것을 알고 있어야 합니다.

- **Azure 역할:** [소유자](../role-based-access-control/built-in-roles.md#owner), [기여자](../role-based-access-control/built-in-roles.md#contributor) 및 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader)입니다. Azure 역할은 Log Analytics 작업 영역 및 Microsoft 센티널 리소스를 포함 하 여 모든 Azure 리소스에 대 한 액세스 권한을 부여 합니다.

- **Log Analytics 역할:** [Log Analytics 기여자](../role-based-access-control/built-in-roles.md#log-analytics-contributor) 및 [Log Analytics 읽기 권한자](../role-based-access-control/built-in-roles.md#log-analytics-reader)입니다. Log Analytics 역할은 Log Analytics 작업 영역에 대한 액세스 권한을 부여합니다.

예를 들어 microsoft 센티널 구독자 역할이 아닌 microsoft 센티널 **구독자** **역할을** 할당 받은 사용자는 Azure 수준 **기여자** 역할이 할당 된 경우에도 microsoft 센티널의 항목을 편집할 수 있습니다. 따라서 Microsoft 센티널의 사용자 에게만 권한을 부여 하려는 경우이 사용자의 이전 권한을 신중 하 게 제거 하 여 다른 리소스에 대 한 필요한 액세스를 중단 하지 않도록 해야 합니다.

## <a name="microsoft-sentinel-roles-and-allowed-actions"></a>Microsoft 센티널 역할 및 허용 된 작업

다음 표에서는 microsoft 센티널 역할 및 Microsoft 센티널에서 허용 되는 작업을 요약 합니다.

| 역할 | 플레이북 생성 및 실행| 분석 규칙 및 기타 Microsoft 센티널 리소스 만들기 및 편집 [*](#workbooks) | 인시던트 관리(해제, 할당 등) | 데이터, 인시던트, 통합 문서 및 기타 Microsoft 센티널 리소스 보기 |
|---|---|---|---|---|
| Microsoft 센티널 판독기 | -- | -- | -- | &#10003; |
| Microsoft 센티널 응답자 | -- | -- | &#10003; | &#10003; |
| Microsoft 센티널 참여자 | -- | &#10003; | &#10003; | &#10003; |
| Microsoft 센티널 기여자 + 논리 앱 참가자 | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* 통합 문서를 만들고 삭제하려면 추가 [Monitoring Contributor](../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할이 필요합니다. 자세한 내용은 [추가 역할 및 권한](#additional-roles-and-permissions)을 참조하세요.
## <a name="custom-roles-and-advanced-azure-rbac"></a>사용자 지정 역할 및 고급 Azure RBAC

- **사용자 지정 역할**. Azure 기본 제공 역할을 사용 하는 대신, 또는 대신 Microsoft 센티널에 대해 Azure 사용자 지정 역할을 만들 수 있습니다. Microsoft 센티널에 대 한 azure 사용자 지정 역할은 Microsoft 센티널 및 [azure Log Analytics 리소스](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)에 대 한 [특정 권한을](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) 기반으로 하 여 다른 [azure 사용자 지정 역할](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)을 만드는 것과 동일한 방식으로 만들어집니다.

- **Log Analytics RBAC**. Microsoft 센티널 작업 영역에서 데이터에 대해 고급 Azure 역할 기반 액세스 제어를 Log Analytics 사용할 수 있습니다. 여기에는 데이터 형식 기반 Azure RBAC 및 리소스-컨텍스트 Azure RBAC가 모두 포함됩니다. 자세한 내용은 다음을 참조하세요.

    - [Azure Monitor에서 로그 데이터 및 작업 영역 관리](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Microsoft 센티널 용 리소스 컨텍스트 RBAC](resource-context-rbac.md)
    - [테이블 수준 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    리소스 컨텍스트 및 테이블 수준 RBAC는 microsoft 센티널 작업 영역의 특정 데이터에 대 한 액세스를 제공 하는 두 가지 방법으로, 전체 Microsoft 센티널 환경에 대 한 액세스를 허용 하지 않습니다.

## <a name="role-recommendations"></a>역할 권장 사항

Microsoft 센티널에서 역할 및 권한이 어떻게 작동 하는지 이해 한 후에는 사용자에 게 역할을 적용 하기 위한 다음과 같은 모범 사례 지침을 사용할 수 있습니다.

|사용자 유형  |역할 |Resource group  |설명  |
|---------|---------|---------|---------|
|**보안 분석가**     | [Microsoft 센티널 응답자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)        | Microsoft 센티널의 리소스 그룹        | 데이터, 인시던트, 통합 문서 및 기타 Microsoft 센티널 리소스를 봅니다. <br><br>인시던트를 할당하거나 해제하는 등, 인시던트를 관리합니다.        |
|     | [Logic Apps 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Microsoft 센티널의 리소스 그룹 또는 플레이 북이 저장 되는 리소스 그룹        | 플레이북을 분석 및 자동화 규칙에 연결하고 플레이북을 실행합니다. <br><br>**참고**: 이 역할을 통해 사용자는 플레이북을 수정할 수도 있습니다.         |
|**보안 엔지니어**     | [Microsoft 센티널 참여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)       |Microsoft 센티널의 리소스 그룹         |   데이터, 인시던트, 통합 문서 및 기타 Microsoft 센티널 리소스를 봅니다. <br><br>인시던트를 할당하거나 해제하는 등, 인시던트를 관리합니다. <br><br>통합 문서, 분석 규칙 및 기타 Microsoft 센티널 리소스를 만들고 편집 합니다.      |
|     | [Logic Apps 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor)        | Microsoft 센티널의 리소스 그룹 또는 플레이 북이 저장 되는 리소스 그룹        | 플레이북을 분석 및 자동화 규칙에 연결하고 플레이북을 실행합니다. <br><br>**참고**: 이 역할을 통해 사용자는 플레이북을 수정할 수도 있습니다.         |
|  Service Principal   | [Microsoft 센티널 참여자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)      |  Microsoft 센티널의 리소스 그룹       | 관리 작업에 대한 자동화된 구성 |
|     |         |        | |

> [!TIP]
> 수집하거나 모니터링하는 데이터에 따라 추가 역할이 필요할 수 있습니다. 예를 들어, 다른 Microsoft 포털의 서비스에 대한 데이터 커넥터를 설정하려면 전역 관리자 또는 보안 관리자 역할과 같은 Azure AD 역할이 필요할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널 사용자를 위해 역할을 수행 하는 방법 및 사용자가 수행할 수 있는 각 역할에 대해 알아보았습니다.

[Microsoft 센티널 블로그에서](https://aka.ms/azuresentinelblog)Azure 보안 및 규정 준수에 대 한 블로그 게시물을 찾습니다.
