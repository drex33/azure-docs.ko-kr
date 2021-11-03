---
title: 내 애플리케이션에 사용자가 프로비전되지 않음
description: Azure AD로 사용자 프로비전을 구성한 Azure AD 갤러리 애플리케이션에 사용자가 표시되지 않는 일반적인 문제를 해결하는 방법
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 0f15e96f19b87f892422df12f7bda7ccf6d0914b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060253"
---
# <a name="no-users-are-being-provisioned"></a>프로비전된 사용자 없음 
>[!NOTE]
>2020년 4월 16일부터 기본 액세스 역할이 할당된 사용자에 대한 동작이 변경되었습니다. 자세한 내용은 아래 섹션을 참조하세요. 
>
앱에 연결할 Azure AD에 제공된 앱 자격 증명이 유효한지 확인을 비롯하여 애플리케이션에 대해 자동 프로비전이 구성되면 사용자 및/또는 그룹이 앱에 프로비전됩니다. 프로비전은 다음 사항에 의해 결정됩니다.

-   애플리케이션에 **할당된** 사용자 및 그룹. 중첩된 그룹 프로비전은 지원되지 않습니다. 할당에 대한 자세한 내용은 [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)을 참조하세요.
-   **특성 매핑** 을 사용하도록 설정했으며 Azure AD의 유효한 특성을 앱에 동기화하도록 구성했는지 여부. 특성 매핑에 대한 자세한 내용은 [Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비전 특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.
-   특정 특성 값을 기반으로 사용자를 필터링하는 **범위 지정 필터** 가 있는지 여부. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

사용자가 프로비전되지 않는 것이 관찰되면 Azure AD의 [프로비전 로그(미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 참조하세요. 특정 사용자에 대한 로그 항목을 검색합니다.

**활동** 섹션에서 **Azure Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비전 로그(미리 보기)** 를 선택하여 Azure Portal에서 프로비전 로그에 액세스할 수 있습니다. 원본 시스템이나 대상 시스템에서 사용자의 이름 또는 식별자를 기준으로 프로비전 데이터를 검색할 수 있습니다. 자세한 내용은 [프로비전 로그(미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 참조하세요. 

프로비전 로그는 프로비전 범위에 있는 할당된 사용자에 대한 Azure AD 쿼리, 해당 사용자의 존재에 대한 대상 앱 쿼리, 시스템 간의 사용자 객체 비교를 비롯하여 프로비전 서비스에서 수행한 모든 작업을 기록합니다. 그런 다음 비교를 기반으로 대상 시스템에서 사용자 계정을 추가, 업데이트 또는 비활성화합니다.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>고려해야 할 프로비전 관련 일반적인 문제 영역
다음은 시작 위치를 파악한 경우 검색할 수 있는 일반적인 문제 영역 목록입니다.

- [프로비전 서비스가 시작하지 않는 것 같음](#provisioning-service-does-not-appear-to-start)
- [사용자가 할당된 경우에도 프로비전 로그에 사용자가 생략되고 프로비전되지 않았다고 표시됨](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>프로비전 서비스가 시작하지 않는 것 같음
Azure Portal의 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; \[애플리케이션 이름\] &gt;프로비전** 섹션에서 **프로비전 상태** 를 **켜기** 로 설정하는 경우 이후 다시 로드 후 기타 상태 세부 정보가 해당 페이지에 표시되지 않으면 서비스가 실행 중이지만 아직 초기 주기를 완료하지 않았을 수 있습니다. 위에 설명된 **프로비전 로그(미리 보기)** 를 확인하여 서비스에서 수행 중인 작업과 오류 유무를 확인합니다.

>[!NOTE]
>초기 주기는 Azure AD 디렉터리의 크기와 프로비전 범위에 있는 사용자 수에 따라 20분에서 수 시간이 걸릴 수 있습니다. 프로비전 서비스는 초기 주기 후에 두 시스템의 상태를 나타내는 워터마크를 저장하므로 초기 주기 이후의 후속 동기화가 빨라집니다. 초기 주기를 통해 후속 동기화의 성능이 향상됩니다.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>사용자가 할당된 경우에도 프로비전 로그에 사용자가 생략되고 프로비전되지 않았다고 표시됨

프로비전 로그에 사용자가 ‘생략됨’으로 표시되는 경우 로그의 **단계** 탭을 검토하여 이유를 확인하는 것이 중요합니다. 다음은 일반적인 원인과 해결 방법입니다.

- **특성 값에 따라 사용자를 필터링하는** **범위 지정 필터가 구성되었습니다**. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 참조하세요.
- **사용자가 “실질적으로 권한을 부여받지 않았습니다.”** 이 특정 오류 메시지가 표시되는 경우 Azure AD에 저장된 사용자 할당 레코드에 문제가 있기 때문입니다. 이 문제를 해결하려면 앱에서 사용자(또는 그룹)의 할당을 취소하고 다시 할당하세요. 할당에 대한 자세한 내용은 [사용자 또는 그룹 액세스 할당](../manage-apps/assign-user-or-group-access-portal.md)을 참조하세요.
- **사용자에게 필요한 특성이 누락되었거나 입력되지 않았습니다.** 프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 애플리케이션으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 이 구성에는 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치하는 데 사용되는 “일치하는 속성” 설정이 포함됩니다. 이 중요한 프로세스에 대한 자세한 내용은 [Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비전 특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.
- **그룹에 대한 특성 매핑:** 일부 애플리케이션에 대해 지원되는 경우, 구성원 외에 그룹 이름 및 그룹 세부 정보 프로비전. **프로비전** 탭에 표시된 그룹 개체의 **매핑** 을 사용하거나 사용하지 않도록 설정하여 이 기능을 사용하거나 사용하지 않을 수 있습니다. 그룹 개체 프로비전을 사용하는 경우 특성 매핑을 검토하여 ‘일치하는 ID’에 적절한 필드가 사용되는지 확인해야 합니다. 일치하는 ID는 표시 이름이나 이메일 별칭일 수 있습니다. Azure AD에 일치하는 속성이 비어 있거나 그룹에 대해 입력되어 있지 않은 경우 그룹 및 해당 멤버가 프로비전되지 않습니다.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>기본 액세스 역할에 할당된 사용자 프로비전
갤러리의 애플리케이션에 대한 기본 역할을 ‘기본 액세스’ 역할이라고 합니다. 지금까지 이 역할에 할당된 사용자는 ‘사실상 자격이 부여되지 않았기’ 때문에 프로비전되지 않고 [프로비전 로그](../reports-monitoring/concept-provisioning-logs.md)에 생략됨으로 표시되었습니다. 

**2020년 4월 16일 이후에 만든 프로비전 구성의 동작:** 기본 액세스 역할에 할당된 사용자는 다른 모든 역할과 동일하게 평가됩니다. 기본 액세스 권한이 할당된 사용자는 ‘사실상 자격이 부여되지 않음’으로 생략되지 않습니다. 

**2020년 4월 16일 이전에 만든 프로비전 구성의 동작:** 다음 3개월 동안 이 동작은 지금처럼 계속됩니다. 기본 액세스 역할이 있는 사용자는 사실상 자격이 부여되지 않음으로 생략됩니다. 2020년 7월 이후에는 모든 애플리케이션에서 동작이 통일됩니다. 기본 액세스 역할이 있는 사용자 프로비전을 ‘사실상 자격이 부여되지 않음’으로 생략되지 않게 됩니다. 이 변경은 Microsoft가 수행하며 고객 작업은 필요하지 않습니다. 이 변경 후에도 이러한 사용자가 계속 생략되게 하려면 적절한 범위 지정 필터를 적용하거나 애플리케이션에서 사용자의 할당을 취소하여 사용자가 범위에서 벗어나도록 하세요.  

이러한 변경에 대한 질문이 있는 경우 provisioningfeedback@microsoft.com으로 문의하세요.
## <a name="next-steps"></a>다음 단계

[Azure AD Connect 동기화: 선언적 프로비전 이해](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
