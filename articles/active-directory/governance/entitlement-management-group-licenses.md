---
title: 자습서 - Azure AD에서 그룹 기반 라이선스의 수명 주기 관리
description: Azure Active Directory 권한 관리에서 그룹 기반 라이선스 관리를 위한 액세스 패키지를 만드는 방법을 설명하는 단계별 자습서입니다.
services: active-directory
documentationCenter: ''
author: sama
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 08/18/2021
ms.author: sama
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8577da92657086edeb0d901c54693faa3cabab92
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095226"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>자습서: Azure AD에서 그룹 기반 라이선스의 수명 주기 관리
 
Azure Active Directory에서는 그룹을 사용하여 [애플리케이션의 라이선스](/active-directory/enterprise-users/licensing-groups-assign.md)를 관리할 수 있습니다. 권한 관리를 사용하면 이 그룹을 훨씬 쉽게 관리할 수 있습니다. 

*   라이선스가 필요한 사용자만 그룹에 있도록 주기적인 액세스 검토를 구성합니다. 
*   다른 사용자가 그룹의 멤버 자격을 요청할 수 있도록 허용합니다.

이 자습서에서는 여러분이 WoodGrove Bank에서 IT관리자로 근무한다고 가정합니다. 여러분은 Office 라이선스를 쉽게 얻을 수 있도록 조직의 액세스 패키지를 만들어 달라는 요청을 받았습니다. [Office 라이선스](/active-directory/enterprise-users/licensing-groups-assign.md)를 관리하는 그룹이 이미 있습니다. 사용자들은 매년 라이선스를 검토하고 신규 사용자가 관리자 승인을 기다리고 있는 Office 라이선스를 요청할 수 있도록 허용해야 합니다. Azure AD 권한 관리를 사용하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스 자세한 내용은 [라이선스 요구 사항](entitlement-management-overview.md#license-requirements)을 참조하세요.
## <a name="step-1-configure-basics-for-your-access-package"></a>1단계: 액세스 패키지의 기본 사항 구성

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

1. **Azure Portal** 의 왼쪽 탐색 창에서 **Azure Active Directory** 를 클릭합니다.

2. 왼쪽 메뉴에서 **ID 거버넌스** 를 클릭합니다.

3. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭합니다. 

4. **새 액세스 패키지** 를 클릭합니다.

5. **기본 사항** 탭에서 이름으로 **Office 라이선스** 를 입력하고, 설명으로 **Office 애플리케이션의 라이선스에 대한 액세스 권한** 을 입력합니다.

6. **카탈로그** 드롭다운 목록은 **일반** 으로 두면 됩니다.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>2단계: 액세스 패키지에 대한 리소스 구성

1. **다음** 을 클릭하여 **리소스 역할** 탭을 엽니다.

2. 이 탭에서 액세스 패키지에 포함할 리소스 및 리소스 역할을 선택합니다. 이 예제 시나리오에서는 **그룹 및 팀** 을 클릭하고 [Office 라이선스](/active-directory/enterprise-users/licensing-groups-assign.md)가 할당된 그룹을 검색합니다.

3. **역할** 드롭다운 목록에서 **멤버** 를 선택합니다.

## <a name="step-3-configure-requests-for-your-access-package"></a>3단계: 액세스 패키지에 대한 요청 구성

1. **다음** 을 클릭하여 **요청** 탭을 엽니다.

2. 이 탭에서 요청 정책을 만듭니다. 정책(*policy*)은 액세스 패키지에 액세스하기 위한 규칙 또는 보호책을 정의합니다. 리소스 디렉터리의 특정 사용자가 액세스 패키지를 요청할 수 있도록 허용하는 정책을 만듭니다.

3. **액세스를 요청할 수 있는 사용자** 섹션에서 **디렉터리에 있는 사용자** 를 클릭한 다음, **모든 구성원(게스트 제외)** 를 선택합니다. 그러면 디렉터리 구성원만 Office 라이선스를 요청할 수 있습니다.

4. **승인 필요** 를 **예** 로 설정합니다.

5. **요청자 근거 필요** 는 **예** 로 둡니다.

6. **스테이지 수** 는 **1** 로 둡니다.

7. **승인자** 로 **승인자로서 관리자** 를 선택합니다. 이 옵션을 사용하면 요청자의 관리자가 요청을 승인할 수 있습니다. 시스템에서 관리자를 찾을 수 없는 경우에 대체 승인자가 될 다른 사람을 선택할 수 있습니다.

8. **며칠 내에 결정해야 하나요?** 는 **14** 로 둡니다.

9. **요청자 근거 필요** 는 **예** 로 둡니다.

10. **새 요청 및 할당 사용** 을 **예** 로 설정하여 이 액세스가 생성되는 즉시 요청할 수 있게 합니다.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>4단계: 액세스 패키지에 대한 요청자 정보 구성

1. **다음** 을 클릭하여 **요청자 정보** 탭을 엽니다.

2. 이 화면에서 추가 질문을 하여 요청자로부터 더 많은 정보를 수집할 수 있습니다. 이러한 질문은 요청자의 요청 양식에 표시되며, 필수로 설정할 수도 있고 선택 사항으로 설정할 수도 있습니다. 이 시나리오에서는 이 액세스 패키지에 대한 요청자 정보를 포함하라는 요청을 받지 않았으므로 해당 필드를 비워 두어도 됩니다.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>5단계: 액세스 패키지의 수명 주기 구성

1. **다음** 을 클릭하여 **수명 주기** 탭을 엽니다.

2. **만료** 섹션에서 **액세스 패키지 할당 만료** 를 **일 수** 로 설정합니다.
    
3. **다음 이후에 할당 만료** 를 **365** 일로 설정합니다. 이 필드는 이 액세스 패키지에 대한 액세스 권한이 있는 구성원이 액세스 권한을 갱신해야 하는 시기를 결정합니다. 

4. 사용자가 액세스 패키지에 계속 액세스해야 하는지 여부를 정기적으로 확인할 수 있는 **액세스 검토** 를 구성할 수도 있습니다. 검토는 사용자가 수행하는 자체 검토일 수도 있고, 관리자를 설정하거나 이 작업의 검토자를 설정할 수도 있습니다. 자세한 내용은 [액세스 검토](entitlement-management-access-reviews-create.md)를 참조하세요. 이 시나리오에서는 각 사용자가 매년 Office 라이선스가 여전히 필요한지 여부를 검토하도록 하려고 합니다.

    1.  **액세스 검토 필요** 를 **예** 로 설정합니다.
    2.  **시작 날짜** 는 현재 날짜로 두면 됩니다. 시작 날짜는 액세스 검토 캠페인이 시작되는 날짜입니다. 액세스 검토를 만든 후에는 시작 날짜를 업데이트할 수 없습니다.
    3.  검토가 매년 발생할 것이므로 **검토 빈도** 를 **매년** 으로 설정합니다. 검토 빈도 필드에서는 액세스 검토 캠페인이 실행되는 빈도를 결정합니다.
    4.  **기간(일)** 을 지정합니다.  기간 필드에서는 액세스 검토가 실행되는 일 수를 지정합니다.
    5.  **검토자** 로 **관리자** 를 선택합니다.

## <a name="step-6-review-and-create-your-access-package"></a>6단계: 액세스 패키지 검토 및 만들기

1. **다음** 을 클릭하여 **리뷰 + 만들기** 탭을 엽니다.

2. 이 화면에서는 액세스 패키지를 만들기 전에 구성을 검토할 수 있습니다. 문제가 있는 경우 탭을 사용하여 만들기 환경의 특정 지점으로 이동하여 편집할 수 있습니다.

3. 선택이 끝나면 **만들기** 를 클릭합니다. 잠시 후 액세스 패키지를 성공적으로 만들었다는 알림이 표시됩니다.

4. 액세스 패키지가 만들어지면 액세스 패키지의 **개요** 페이지로 이동됩니다. 여기서 **내 액세스 포털 링크** 를 찾을 수 있습니다. 링크를 복사하여 팀과 공유합니다. 그러면 팀에서는 액세스 패키지에 Office 라이선스를 할당해 달라고 요청할 수 있습니다.

## <a name="step-7-clean-up-resources"></a>7단계: 리소스 정리

이 단계에서는 **Office 라이선스** 액세스 패키지를 삭제할 수 있습니다. 

**필수 역할:** 전역 관리자, ID 거버넌스 관리자 또는 액세스 패키지 관리자

1. **Azure Portal** 의 왼쪽 탐색 창에서 **Azure Active Directory** 를 클릭합니다.

2. 왼쪽 메뉴에서 **ID 거버넌스** 를 클릭합니다.

3. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭합니다. 

4. **Office 라이선스** 액세스 패키지를 엽니다. 

5. **리소스 역할** 을 클릭합니다.

6. 이 액세스 패키지에 추가한 그룹을 선택하고, [세부 정보] 창에서 **리소스 역할 제거** 를 클릭합니다. 표시되는 메시지에서 **예** 를 클릭합니다.

7. 액세스 패키지 목록을 엽니다.

8. **Office 라이선스** 의 경우 줄임표(...)를 클릭한 다음, **삭제** 를 클릭합니다. 표시되는 메시지에서 **예** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

애플리케이션 및 사이트와 같은 다른 종류의 리소스에 대한 액세스 권한을 관리하는 액세스 패키지를 만드는 방법을 알아봅니다. [자습서: Azure AD 자격 관리에서 리소스에 대한 액세스 관리](/active-directory/governance/entitlement-management-access-package-first.md)
