---
title: 그룹 및 애플리케이션의 액세스 검토 완료 - Azure AD
description: Azure Active Directory 액세스 검토에서 그룹 멤버의 액세스 검토 또는 애플리케이션 액세스를 완료하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29dc8e93af91f81a3d9b9426138e8f301c9528f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052339"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 그룹 및 애플리케이션의 액세스 검토 완료
 
관리자는 [그룹 또는 애플리케이션에 대한 액세스 검토를 만들고](create-access-review.md) 검토자는 [액세스 검토를 수행](perform-access-review.md)합니다. 이 문서에서는 액세스 검토 결과를 보고 적용하는 방법에 대해 설명합니다.
 
[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]
 
## <a name="prerequisites"></a>필수 구성 요소
 
- Azure AD Premium P2
- 전역 관리자, 사용자 관리자 또는 ID 거버넌스 관리자는 그룹 및 애플리케이션에 대한 검토 액세스를 관리합니다. 전역 관리자 및 권한 있는 역할 관리자는 역할 할당 가능한 그룹의 검토를 관리할 수 있습니다. [Azure AD 그룹을 사용하여 역할 할당 관리](../roles/groups-concept.md)를 참조하세요.
- 보안 읽기 권한자에게 읽기 액세스 권한이 있습니다.
 
자세한 내용은 [라이선스 요구 사항](access-reviews-overview.md#license-requirements)을 참조하세요.

 
## <a name="view-the-status-of-an-access-review"></a>액세스 검토 상태 보기
 
액세스 검토가 완료되면 진행 상황을 추적할 수 있습니다.
 
1. Azure Portal에 로그인하고 [Identity Governance 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)를 엽니다.
 
1. 왼쪽 메뉴에서 **액세스 검토** 를 클릭합니다.
 
1. 목록에서 액세스 검토를 클릭합니다.
 
 
    **개요** 페이지에서 검토의 **현재** 인스턴스의 진행 상황을 볼 수 있습니다. 현재 열려 있는 활성 인스턴스가 없으면 이전 인스턴스에 대한 정보가 표시됩니다. 액세스 권한은 검토가 완료될 때까지 디렉터리에서 변경되지 않습니다.
 
     ![모든 회사 그룹의 검토](./media/complete-access-review/all-company-group.png)
 
    **현재** 아래의 모든 블레이드는 각 검토 인스턴스 기간 동안에만 볼 수 있습니다. 
    > [!NOTE]
    > **현재** 액세스 검토에는 활성 검토 인스턴스에 대한 정보만 표시되지만 **예약 검토** 섹션에서 **시리즈** 에서 아직 진행되지 않은 검토에 대한 정보를 얻을 수 있습니다.
 
    결과 페이지는 중지, 재설정 및 결과 다운로드 기능을 포함하여 인스턴스에서 검토 중인 각 사용자에 대한 추가 정보를 제공합니다.
 
    ![Microsoft 365 그룹에서 게스트 액세스 검토](./media/complete-access-review/all-company-group-results.png)
 
    Microsoft 365 그룹 전체의 게스트 액세스를 검토하는 액세스 검토를 보고 있는 경우 개요 블레이드에 검토의 각 그룹이 나열됩니다. 
   
    ![Microsoft 365 그룹에서 게스트 액세스 검토](./media/complete-access-review/review-guest-access-across-365-groups.png)
 
    그룹을 클릭하면 해당 그룹에 대한 검토 진행 상황을 볼 수 있으며 중지, 재설정, 적용 및 삭제도 수행할 수 있습니다.
 
   ![Microsoft 365 그룹의 게스트 액세스를 자세히 검토](./media/complete-access-review/progress-group-review.png)
 
1. 예정된 종료 날짜에 도달하기 전에 액세스 검토를 중지하려면 **중지** 단추를 클릭합니다.
 
    검토를 중지하면 검토자가 더 이상 응답을 제공할 수 없습니다. 중단한 검토는 다시 시작할 수 없습니다.
 
1. 액세스 검토에 더 이상 관심이 없으면 **삭제** 단추를 클릭하여 삭제할 수 있습니다.
 
## <a name="retrieve-the-results"></a>결과 검색
 
검토 결과를 보려면 **결과** 페이지를 클릭합니다. 사용자의 액세스 권한만 보려면 검색 상자에 액세스가 검토된 사용자의 표시 이름 또는 사용자 계정 이름을 입력합니다.
 
![액세스 검토 결과 검색](./media/complete-access-review/retrieve-results.png) 
 
 
되풀이되는 액세스 검토의 완료된 인스턴스의 결과를 보려면 **기록 검토** 을 클릭한 다음, 인스턴스의 시작 및 종료 날짜를 기준으로 완료된 액세스 검토 인스턴스 목록에서 특정 인스턴스를 선택합니다. 이 인스턴스의 결과는 **결과** 페이지에서 얻을 수 있습니다. 반복적인 액세스 검토를 통해 일회성 액세스 검토보다 더 자주 업데이트해야 할 수 있는 리소스에 대한 액세스를 지속적으로 파악할 수 있습니다.
 
진행 중이거나 완료된 액세스 검토 결과를 검색하려면 **다운로드** 단추를 클릭합니다. 결과 CSV 파일은 Excel 또는 UTF-8로 인코딩된 CSV 파일을 열 수 있는 다른 프로그램에서 볼 수 있습니다.


 

## <a name="apply-the-changes"></a>변경 내용 적용
 
**완료 시 설정** 에서 선택한 항목에 따라 **결과를 리소스에 자동 적용** 이 설정된 경우 검토 인스턴스가 완료되면 자동 적용이 실행되고, 수동으로 검토를 중지하는 경우 더 일찍 실행됩니다.
 
검토에 대해 **결과를 리소스에 자동 적용** 이 설정되지 않은 경우 검토 기간이 종료되거나 검토가 조기에 중단된 후 **시리즈** 아래의 **검토 기록** 으로 이동하여 적용하려는 검토의 인스턴스를 클릭합니다.
 
![액세스 검토 변경 내용 적용](./media/complete-access-review/apply-changes.png)
 
변경 내용을 수동으로 적용하려면 **적용** 을 클릭합니다. 검토에서 사용자의 액세스가 거부된 경우 **적용** 을 클릭하면 Azure AD에서 해당 멤버 자격 또는 애플리케이션 할당을 제거합니다.
 
![액세스 검토 변경 내용 적용 단추](./media/complete-access-review/apply-changes-button.png)
 
검토의 상태는 **완료됨** 에서 **적용 중** 과 같은 중간 상태를 거쳐 최종적으로 **결과가 적용됨** 상태로 변경됩니다. 거부된 사용자(있는 경우)가 몇 분 내에 그룹 구성원 자격 또는 애플리케이션 할당에서 제거되는 것을 볼 수 있어야 합니다.
 
수동 또는 자동으로 결과를 적용해도 온-프레미스 디렉터리에서 시작된 그룹에는 영향을 미치지 않습니다. 온-프레미스에서 시작된 그룹을 변경하려면 결과를 다운로드하고 이러한 변경 내용을 해당 디렉터리의 그룹 표시에 적용합니다.

> [!NOTE]
> 일부 거부된 사용자는 결과를 적용할 수 없습니다. 이러한 상황이 발생할 수 있는 시나리오는 다음과 같습니다.
> - 동기화된 온-프레미스 Windows AD 그룹의 멤버 검토: 온-프레미스 Windows AD에서 동기화되는 그룹은 Azure AD에서 관리할 수 없으므로 멤버 자격을 변경할 수 없습니다.
> - 중첩된 그룹이 할당된 리소스(역할, 그룹, 애플리케이션) 검토: 중첩된 그룹을 통해 멤버 자격이 있는 사용자의 경우 중첩된 그룹에 대한 멤버 자격을 제거하지 않으므로 검토 중인 리소스에 대한 액세스 권한은 유지됩니다.
> - 사용자를 찾을 수 없음/기타 오류로 인해 적용 결과가 지원되지 않을 수도 있습니다.
 

## <a name="actions-taken-on-denied-guest-users-in-an-access-review"></a>액세스 검토에서 거부된 게스트 사용자에 대한 작업
 
검토를 만들 때 작성자는 액세스 검토에서 거부된 게스트 사용자에 대해 두 가지 옵션 중에서 선택할 수 있습니다. 
 - 거부된 게스트 사용자는 리소스에 대한 액세스 권한을 제거할 수 있습니다. 이것이 기본값입니다.
 - 거부된 게스트 사용자는 30일 동안 로그인을 차단한 후 테넌트에서 삭제할 수 있습니다. 30일 동안 게스트 사용자는 관리자가 테넌트에 대한 액세스를 복원할 수 있습니다. 30일 기간이 완료된 후 게스트 사용자에게 부여된 리소스에 대한 액세스 권한이 다시 부여되지 않으면 해당 사용자는 테넌트에서 영구적으로 제거됩니다. 또한 Azure Active Directory 포털을 사용하여 전역 관리자는 해당 기간이 만료되기 전에 명시적으로 [최근에 삭제된 사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있습니다. 사용자가 영구적으로 삭제되면 해당 게스트 사용자에 대한 데이터는 활성 액세스 검토에서 제거됩니다. 삭제된 사용자에 대한 감사 정보는 감사 로그에 남아 있습니다.


## <a name="next-steps"></a>다음 단계
 
- [액세스 검토 관리](manage-access-review.md) 
- [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)

