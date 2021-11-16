---
title: Azure AD Connect 클라우드 동기화 새 에이전트 구성
description: 이 문서에서는 클라우드 동기화를 설치하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1a8602b18c4ec68eacc37faca941211ee83fd63
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742630"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화에 대한 새 구성 만들기

다음 문서에서는 Azure AD Connect 클라우드 동기화를 구성하는 과정을 안내합니다. 클라우드 동기화를 구성하는 방법에 대한 추가 정보 및 예제는 아래 비디오를 참조하세요.


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWKact]


## <a name="configure-provisioning"></a>프로비전 구성
프로비전을 구성하려면 다음 단계를 따릅니다.

 1. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택합니다.

 ![프로비전 관리](media/how-to-install/install-6.png)
 
 4. **새 구성** 을 선택합니다.
 5. 구성 화면에서 도메인을 선택하고 암호 해시 동기화를 사용하도록 설정할지 여부를 선택합니다. **만들기** 를 클릭합니다.  
 
 ![새 구성 만들기](media/how-to-configure/configure-1.png)


 6.  프로비전 구성 편집 화면이 열립니다.

   ![구성 편집](media/how-to-configure/con-1.png)

 7. **알림 이메일** 을 입력합니다. 프로비전이 정상이 아닌 경우 이 이메일로 알림을 보냅니다.  **실수로 인한 삭제 방지** 를 사용하도록 설정하고 **실수로 인한 삭제 임계값** 을 알림 받을 숫자로 설정하는 것이 좋습니다.  자세한 내용은 아래의 [실수로 인한 삭제](#accidental-deletions)를 참조하세요.
 8. 선택기를 사용으로 이동하고 저장을 선택합니다.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>특정 사용자 및 그룹에 프로비전 범위 지정
온-프레미스 Active Directory 그룹 또는 조직 구성 단위를 사용하여 특정 사용자 및 그룹을 동기화하도록 에이전트 범위를 지정할 수 있습니다. 구성 내에서는 그룹 및 조직 구성 단위를 구성할 수 없습니다. 

 1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택합니다.
 4. **구성** 아래에서 구성을 선택합니다.

 ![구성 섹션](media/how-to-configure/scope-1.png)
 
 5. **구성** 아래에서 **범위 지정 필터 편집** 을 선택하여 구성 규칙의 범위를 변경합니다.
 ![범위 편집](media/how-to-configure/scope-3.png)
 7. 오른쪽에서 범위를 변경할 수 있습니다.  완료되면 **완료** 및 **저장** 을 클릭합니다.
 8. 범위를 변경한 후에는 [프로비전을 다시 시작](#restart-provisioning)하여 변경 내용에 대한 즉각적인 동기화를 시작해야 합니다.

## <a name="attribute-mapping"></a>특성 매핑
Azure AD Connect 클라우드 동기화를 사용하면 온-프레미스 사용자/그룹 개체와 Azure AD의 개체 간에 특성을 쉽게 매핑할 수 있습니다.  비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.  자세한 내용은 [특성 매핑](how-to-attribute-mapping.md)을 참조하세요.

## <a name="on-demand-provisioning"></a>주문형 프로비저닝
Azure AD Connect 클라우드 동기화를 사용하면 이러한 변경 사항을 단일 사용자 또는 그룹에 적용하여 구성 변경을 테스트할 수 있습니다.  이를 사용하여 구성에 대한 변경 내용이 제대로 적용되었으며 Azure AD에 올바르게 동기화되었는지 확인할 수 있습니다.  자세한 내용은 [주문형 프로비저닝](how-to-on-demand-provision.md)을 참조하세요.

## <a name="accidental-deletions"></a>실수로 삭제
실수로 삭제 기능은 다수의 사용자 및 그룹에 영향을 주는 실수에 의한 구성 변경 및 온-프레미스 디렉터리 변경을 방지하기 위한 것입니다.  이 기능을 사용하여 다음을 할 수 있습니다.

- 실수로 인한 삭제를 자동으로 방지하는 기능을 구성합니다. 
- 구성이 적용되는 개체의 수(임계값)를 설정합니다. 
- 이 시나리오에 대해 해당 동기화 작업이 격리에 보관되면 이메일 알림을 받을 수 있도록 알림 이메일 주소를 설정합니다. 

자세한 내용은 [실수로 인한 삭제](how-to-accidental-deletes.md)를 참조하세요.

## <a name="quarantines"></a>격리
클라우드 동기화는 구성의 상태를 모니터링하고 비정상 개체를 격리 상태에 배치합니다. 오류(예: 잘못된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행된 대부분 또는 모든 호출이 일관되게 실패하는 경우 동기화 작업은 격리 상태로 표시됩니다.  자세한 내용은 [격리](how-to-troubleshoot.md#provisioning-quarantined-problems)의 문제 해결 섹션을 참조하세요.

## <a name="restart-provisioning"></a>프로비저닝 다시 시작 
다음 예약된 실행을 기다리지 않으려면 **프로비전 다시 시작** 단추를 사용하여 프로비전 실행을 트리거합니다. 
 1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3.  **클라우드 동기화 관리** 를 선택합니다.
 4. **구성** 아래에서 구성을 선택합니다.

   ![프로비전 다시 시작에 대한 구성 선택](media/how-to-configure/scope-1.png)

 5. 상단에서 **프로비전 다시 시작** 을 선택합니다.

## <a name="remove-a-configuration"></a>구성 제거
구성을 삭제하려면 다음 단계를 따릅니다.

 1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택합니다.
 4. **구성** 아래에서 구성을 선택합니다.
   
   ![구성 제거에 대한 구성 선택](media/how-to-configure/scope-1.png)

 5. 구성 화면의 상단에서 **삭제** 를 선택합니다.

>[!IMPORTANT]
>구성을 삭제하기 전에 확인이 없습니다. **삭제** 를 선택하기 전에 수행하려는 작업인지 확인합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
