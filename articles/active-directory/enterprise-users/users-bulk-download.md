---
title: Azure Active Directory 포털에서 사용자 목록 다운로드 | Microsoft Docs
description: Azure Active Directory의 Azure 관리 센터에서 사용자 레코드를 대량으로 다운로드합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: d27c95b0b16c36ca24709ec72c741dec633c5c81
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108063966"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Azure Active Directory 포털에서 사용자 목록 다운로드

Azure AD(Azure Active Directory)는 대량 사용자 가져오기(만들기) 작업을 지원합니다.

## <a name="required-permissions"></a>필요한 사용 권한

Azure AD 관리 센터에서 사용자 목록을 다운로드하려면 Azure AD에서 하나 이상의 조직 수준 관리자 역할이 할당된 사용자로 로그인해야 합니다(필요한 최소 역할은 사용자 관리자). 게스트 초대자 및 애플리케이션 개발자는 관리자 역할로 간주하지 않습니다.

## <a name="to-download-a-list-of-users"></a>사용자 목록을 다운로드하려면

1. 조직의 사용자 관리자 계정으로 [Azure AD 조직에 로그인](https://aad.portal.azure.com)합니다.
2. Azure Active Directory > 사용자로 이동합니다. 그런 다음, 각 사용자 옆의 왼쪽 열에 있는 상자를 선택하여 다운로드에 포함하려는 사용자를 선택합니다. 참고: 현재는 내보낼 모든 사용자를 선택할 수 있는 방법이 없습니다. 각 사용자를 개별적으로 선택해야 합니다.
3. Azure AD에서 **사용자** > **사용자 다운로드** 를 선택합니다.
4. **사용자 다운로드** 페이지에서 **시작** 을 선택하여 사용자 프로필 속성이 나열된 CSV 파일을 수신합니다. 오류가 있는 경우 대량 작업 결과 페이지에서 결과 파일을 다운로드하고 볼 수 있습니다. 해당 파일에는 각 오류에 대한 이유가 포함되어 있습니다.

   ![다운로드할 사용자를 나열할 위치 선택](./media/users-bulk-download/bulk-download.png)

   다운로드 파일에는 필터링된 사용자 목록이 포함됩니다.

   포함되는 사용자 특성은 다음과 같습니다.

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 대량 요청의 상태를 볼 수 있습니다.

[![대량 작업 결과 페이지에서 상태를 확인합니다.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

사용자 목록을 만드는 각 대량 활동은 최대 1시간 동안 실행될 수 있습니다. 이렇게 하면 최대 500,000명의 사용자 목록을 만들고 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 일괄 추가](users-bulk-add.md)
- [사용자 대량 삭제](users-bulk-delete.md)
- [사용자 대량 복원](users-bulk-restore.md)
