---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 의무 분리 구성 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지 요청에 대한 의무 분리 적용을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/2/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 171901de8f9037ee6e77f83138ec7a3065c2d71f
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730173"
---
# <a name="configure-separation-of-duties-checks-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 의무 분리 검사 구성(미리 보기)

Azure AD 권한 관리에서 액세스 패키지를 통해 액세스해야 하는 각 사용자 커뮤니티에 대해 각기 다른 설정으로 여러 정책을 구성할 수 있습니다.  예를 들어 직원은 특정 앱에 대한 액세스 권한을 얻기 위해 관리자 승인만 받으면 되지만, 다른 조직에서 들어오는 게스트의 경우 스폰서와 리소스 팀 부서 관리자의 승인이 필요할 수 있습니다. 디렉터리의 기존 사용자에 대한 정책에서 액세스를 요청할 수 있는 특정 사용자 그룹을 지정할 수 있습니다. 그러나 사용자에게 과도한 액세스 권한이 부여되지 않도록 방지해야 하는 요구 사항이 있을 수 있습니다.  이 요구 사항을 충족하려면 요청자의 기존 액세스 권한에 따라 액세스를 요청할 수 있는 사용자를 추가로 제한하는 것이 좋습니다.

액세스 패키지에 대한 의무 분리 설정을 사용하면 그룹의 구성원이거나 한 액세스 패키지에 이미 할당된 사용자가 추가 액세스 패키지를 요청할 수 없도록 구성할 수 있습니다.

![호환되지 않는 액세스를 요청하는 데 사용되는 myaccess 환경](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## <a name="scenarios-for-separation-of-duties-checks"></a>의무 분리 검사 시나리오

예를 들어 해당 조직과 다른 조직의 사용자가 캠페인이 진행되는 동안 조직의 마케팅 부서와 협력하기 위해 액세스를 요청할 수 있는 ‘마케팅 캠페인’ 액세스 패키지가 있습니다. 마케팅 부서의 직원은 이미 마케팅 캠페인 자료에 대한 액세스 권한이 있으므로 해당 액세스 패키지에 대한 액세스를 요청할 수 없도록 하려고 합니다.  또는 모든 마케팅 직원이 포함된 동적 그룹인 ‘마케팅 부서 직원’이 있을 수도 있습니다. 액세스 패키지가 해당 동적 그룹의 멤버 자격과 호환되지 않는다고 지정할 수 있습니다. 그러면 마케팅 부서 직원이 요청할 액세스 패키지를 찾고 있는 경우 ‘마케팅 캠페인’ 액세스 패키지에 대한 액세스를 요청할 수 없습니다.

마찬가지로, **서부 판매** 와 **동부 판매** 라는 두 가지 역할을 포함하는 애플리케이션이 있고 사용자가 한 번에 하나의 판매 구역에만 속할 수 있도록 지정할 수 있습니다.  **서부 판매** 역할을 포함하는 **서부 구역** 액세스 패키지와 **동부 판매** 역할을 포함하는 **동부 구역** 액세스 패키지 등 두 개의 액세스 패키지가 있는 경우 다음과 같이 구성할 수 있습니다.
 - **서부 구역** 액세스 패키지에서 **동부 구역** 패키지는 호환되지 않는 것으로 설정됨
 - **동부 구역** 액세스 패키지에서 **서부 구역** 패키지는 호환되지 않는 것으로 설정됨

온-프레미스 앱에 대한 액세스를 자동화하기 위해 Microsoft Identity Manager 또는 다른 온-프레미스 ID 관리 시스템을 사용하는 경우 해당 시스템을 Azure AD 권한 관리와 통합할 수 있습니다.  권한 관리를 통해 Azure AD 통합 앱에 대한 액세스를 제어하고 사용자에게 호환되지 않는 액세스 권한이 부여되지 않도록 방지하려는 경우 액세스 패키지가 그룹과 호환되지 않는다고 구성할 수 있습니다. 온-프레미스 ID 관리 시스템이 Azure AD Connect를 통해 Azure AD에 보내는 그룹일 수 있습니다. 이 검사는 액세스 패키지가 온-프레미스 앱에서 사용자가 보유한 액세스 권한과 호환되지 않는 액세스 권한을 부여하는 경우 사용자가 액세스 패키지를 요청할 수 없도록 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 권한 관리를 사용하고 액세스 패키지에 사용자를 할당하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

## <a name="configure-another-access-package-or-group-membership-as-incompatible-for-requesting-access-to-an-access-package"></a>다른 액세스 패키지 또는 그룹 멤버 자격을 액세스 패키지에 대한 액세스를 요청할 수 없는 것으로 구성

**필수 역할**: 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

기존 액세스 패키지와 호환되지 않는 그룹 또는 다른 액세스 패키지 목록을 변경하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

1.  **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1.  왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음, 사용자가 요청할 액세스 패키지를 엽니다.

1.  왼쪽 메뉴에서 **의무 분리(미리 보기)** 를 클릭합니다.

1.  이미 다른 액세스 패키지 할당을 사용하는 사용자가 이 액세스 패키지를 요청할 수 없도록 방지하려면 **액세스 패키지 추가** 를 클릭하고 사용자에게 이미 할당된 액세스 패키지를 선택합니다.


    ![호환되지 않는 액세스 패키지 구성](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  기존 그룹 멤버 자격이 있는 사용자가 이 액세스 패키지를 를 요청할 수 없도록 방지하려면 **그룹 추가** 를 클릭하고 사용자가 이미 속해 있는 그룹을 선택합니다.

### <a name="configure-incompatible-access-packages-programmatically"></a>프로그래매틱 방식으로 호환되지 않는 액세스 패키지 구성

Microsoft Graph를 사용하여 액세스 패키지와 호환되지 않는 그룹 및 다른 액세스 패키지를 구성할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션이나 해당 애플리케이션 권한이 있는 애플리케이션에서 적절한 역할이 있는 사용자는 API를 호출하여 [액세스 패키지](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)를 추가 및 제거하고 호환되지 않는 그룹과 액세스 패키지를 나열할 수 있습니다.


## <a name="view-other-access-packages-that-are-configured-as-incompatible-with-this-one"></a>이 패키지와 호환되지 않는 것으로 구성된 다른 액세스 패키지 보기

**필수 역할**: 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

기존 액세스 패키지와 호환되지 않는 것으로 지정된 다른 액세스 패키지 목록을 보려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

1.  **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1.  왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1.  왼쪽 메뉴에서 **의무 분리(미리 보기)** 를 클릭합니다.

1. **호환되지 않음** 을 클릭합니다.

## <a name="monitor-and-report-on-access-assignments"></a>액세스 할당 모니터링 및 보고

Azure Monitor 통합 문서를 사용하여 사용자가 액세스를 받은 방법에 대한 인사이트를 얻을 수 있습니다.

1. [감사 이벤트를 Azure Monitor에 보내도록](entitlement-management-logs-and-reporting.md) Azure AD를 구성합니다.

1. ‘액세스 패키지 작업’이라는 통합 문서에는 특정 액세스 패키지와 관련된 각 이벤트가 표시됩니다.

    ![액세스 패키지 이벤트 보기](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. 액세스 패키지 할당으로 인해 생성되지 않은 애플리케이션에 대한 애플리케이션 역할 할당이 변경되었는지 확인하려면 ‘애플리케이션 역할 할당 작업’이라는 통합 문서를 선택할 수 있습니다.  권한 부여 작업을 생략하도록 선택하면 권한 관리를 통해 수행되지 않은 애플리케이션 역할 변경 내용만 표시됩니다. 예를 들어 전역 관리자가 사용자를 애플리케이션 역할에 직접 할당한 경우 행이 표시됩니다.

    ![앱 역할 할당 보기](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 할당 보기, 추가, 제거](entitlement-management-access-package-assignments.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
