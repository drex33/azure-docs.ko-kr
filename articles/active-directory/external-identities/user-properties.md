---
title: B2B 게스트 사용자의 속성 - Azure Active Directory | Microsoft Docs
description: 초대 사용 전후의 Azure Active Directory B2B 초대 게스트 사용자 속성 및 상태
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6a0eb5c97905fa82b15b15f6997568c1d3c03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262606"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 협업 사용자 속성

이 문서에서는 초대 사용 전후에 초대된 Azure AD B2B(Azure Active Directory B2B) 공동 작업 사용자 개체의 속성 및 상태에 대해 설명합니다. Azure AD B2B 공동 작업 사용자는 개인 자격 증명을 사용하여 Azure AD 조직에 로그인하도록 초대하는 외부 사용자로, 일반적으로 파트너 조직의 사용자입니다. 이 B2B 공동작업 사용자(일반적으로 *게스트 사용자* 라고도 함)는 공유하려는 앱과 리소스에 액세스할 수 있습니다. B2B 공동 작업 사용자에 대한 사용자 개체는 직원과 동일한 디렉터리에 만들어집니다. B2B 공동 작업 사용자 개체는 기본적으로 디렉터리에서 제한된 권한을 가지며 직원처럼 관리하거나 그룹에 추가하는 등의 작업을 수행할 수 있습니다.

초대 조직의 요구에 따라 Azure AD B2B 협업 사용자는 다음 계정 상태 중 하나일 수 있습니다.

- 상태 1: Azure AD의 외부 인스턴스에 속하며 초대하는 조직의 게스트 사용자로 표시됩니다. 이 경우 B2B 사용자는 초대된 테넌트에 속한 Azure AD 계정을 사용하여 로그인합니다. 파트너 조직이 Azure AD를 사용하지 않는 경우에도 Azure AD에서 게스트 사용자는 여전히 생성됩니다. 단, 본인의 초대를 사용해야 합니다. Azure AD에서는 해당 사용자의 전자 메일 주소를 확인합니다. 이것을 JIT(Just In Time) 테넌시, ‘바이럴’ 테넌시, 또는 관리되지 않는 Azure AD 테넌시라고도 합니다.

   > [!IMPORTANT]
   > **2021년 11월 1일부터** 모든 기존 테넌트용 이메일 일회용 암호 기능을 켜고 새 테넌트용 기능을 기본적으로 사용하도록 설정하는 변경 내용의 배포를 시작합니다. 이러한 변경의 일환으로 Microsoft는 B2B Collaboration 초대를 사용하는 동안에는 새로운, 관리되지 않는(“바이럴”) Azure AD 계정 및 테넌트 만들기를 중지합니다. 휴일 및 배포 잠금 동안 중단을 최소화하기 위해 대부분의 테넌트에는 2022년 1월에 롤아웃되는 변경 사항이 표시됩니다. 게스트 사용자에게 원활한 대체 인증 방법을 제공하므로 이메일 일회용 암호 기능을 사용하고 있습니다. 하지만, 이 기능을 원하지 않는다면 [사용하지 않도록 설정](one-time-passcode.md#disable-email-one-time-passcode)할 수 있습니다.

- 상태 2: Microsoft 또는 다른 계정에 속하고 호스트 조직의 게스트 사용자로 표시됩니다. 이 경우 게스트 사용자는 Microsoft 계정 또는 소셜 계정(google.com 또는 유사한)으로 로그인합니다. 초대된 사용자의 ID는 제안 상환 도중 초대 조직의 디렉터리에서 Microsoft 계정으로 만들어집니다.

- 상태 3: 호스트 조직의 온-프레미스 Active Directory에 속하며 호스트 조직의 Azure AD와 동기화와 동기화됩니다. Azure AD Connect를 사용하여 클라우드에 대한 파트너 계정을 UserType이 Guest인 Azure AD B2B 사용자로 동기화할 수 있습니다. [로컬로 관리되는 파트너 계정에 클라우드 리소스에 대한 액세스 권한 부여](hybrid-on-premises-to-cloud.md)를 참조하세요.

- 상태 4: UserType이 Guest이고 호스트 조직에서 관리하는 자격 증명을 사용하는 호스트 조직의 Azure AD에 속합니다.

  ![네 가지 사용자 상태를 보여 주는 다이어그램](media/user-properties/redemption-diagram.png)


이제 Azure AD에서 Azure AD B2B 협업 사용자가 어떻게 보이는지 살펴보겠습니다.

### <a name="before-invitation-redemption"></a>초대 상환 전

상태 1 및 상태 2 계정은 게스트 사용자의 고유한 자격 증명을 사용하여 공동 작업하는 게스트 사용자를 초대한 결과입니다. 초대가 초기에 게스트 사용자에게 전송되면 계정이 사용자 디렉터리에 생성됩니다. 인증이 게스트 사용자의 ID 공급 기업에서 수행되기 때문에 이 계정에는 연결된 자격 증명이 없습니다. 디렉터리의 게스트 사용자 계정에 대한 **원본** 속성은 **초대된 사용자** 로 설정됩니다. 

![제안 사용 전의 사용자 속성을 보여 주는 스크린샷](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>초대 상환 후

게스트 사용자가 초대를 수락한 후 **원본** 속성이 게스트 사용자의 ID 공급 기업에 따라 업데이트됩니다.

상태 1의 게스트 사용자의 경우 **원본** 은 **외부 Azure Active Directory** 입니다.

![제안 상환 후 상태 1 게스트 사용자](media/user-properties/after-redemption-state1.png)

상태 2의 게스트 사용자의 경우 **원본** 은 **Microsoft 계정** 입니다.

![제안 상환 후 상태 2 게스트 사용자](media/user-properties/after-redemption-state2.png)

상태 3 및 상태 4에서 게스트 사용자의 경우 **원본** 속성은 다음 섹션에 설명된 대로 **Azure Active Directory** 또는 **Windows Server AD** 로 설정됩니다.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B 협업 사용자의 주요 속성
### <a name="usertype"></a>UserType
이 속성은 사용자와 호스트 테넌트 사이의 관계를 나타냅니다. 이 속성에는 다음 두 가지 값이 사용될 수 있습니다.
- 구성원: 이 값은 호스트 조직의 직원과 조직의 급여 부서에 있는 사용자를 나타냅니다. 예를 들어 이 사용자는 내부 전용 사이트에 대한 액세스 권한을 요구합니다. 이 사용자는 외부 공동 작업자로 간주되지 않습니다.

- 게스트: 이 값은 외부 협력자, 파트너 또는 고객과 같이 회사 내부 인사로 간주되지 않는 사용자를 나타냅니다. 이러한 사용자는 CEO의 내부 메모를 수신하거나 회사 혜택을 받을 것으로 예상되지 않습니다.

  > [!NOTE]
  > UserType은 사용자가 로그인하는 방법, 사용자의 디렉터리 역할 등과 관계가 없습니다. 이 속성은 단순히 사용자와 호스트 조직 사이의 관계를 나타내며, 조직에서 이 속성에 속한 모든 정책을 시행할 수 있게 합니다.

가격 책정에 관한 세부 정보는 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory)을 참조하세요.

### <a name="source"></a>원본
이 속성은 사용자가 로그인하는 방법을 나타냅니다.

- 초대된 사용자: 이 사용자는 초대되었지만 초대를 아직 상환하지 않았습니다.

- 외부 Azure Active Directory: 이 사용자는 외부 조직에 속하며 다른 조직에 속한 Azure AD 계정을 사용하여 인증합니다. 이 유형의 로그인은 상태 1에 해당합니다.

- Microsoft 계정: 이 사용자는 Microsoft 계정에 속하며 Microsoft 계정을 사용하여 인증합니다. 이 유형의 로그인은 상태 2에 해당합니다.

- Windows Server AD: 이 사용자는 이 조직에 속한 온-프레미스 Active Directory에서 로그인됩니다. 이 유형의 로그인은 상태 3에 해당합니다.

- Azure Active Directory: 이 사용자는 이 조직에 속한 Azure AD 계정을 사용하여 인증합니다. 이 유형의 로그인은 상태 4에 해당합니다.
  > [!NOTE]
  > Source와 UserType은 독립적인 속성입니다. Source의 값은 UserType에 대한 특정 값을 의미하지 않습니다.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Azure AD B2B 사용자를 게스트 대신 구성원으로 추가할 수 있습니까?
일반적으로 Azure AD B2B 사용자와 게스트 사용자는 동의어입니다. 따라서 Azure AD B2B 협업 사용자는 기본적으로 UserType = Guest인 사용자로 추가됩니다. 그러나 경우에 따라 파트너 조직은 호스트 조직이 속한 더 큰 조직의 구성원일 수 있습니다. 만약 그렇다면 호스트 조직에서 파트너 조직의 사용자를 게스트가 아닌 구성원으로 처리하려 할 수 있습니다. Azure AD B2B 초대 관리자 API를 사용하여 호스트 조직에 파트너 조직의 사용자를 구성원으로 추가하거나 초대합니다.

## <a name="filter-for-guest-users-in-the-directory"></a>디렉터리의 게스트 사용자 필터링

![게스트 사용자에 대한 필터를 보여 주는 스크린샷](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>UserType 변환
PowerShell을 사용하여 UserType을 Member에서 Guest로 또는 그 반대로 변환할 수 있습니다. 그러나 UserType 속성은 사용자와 조직 사이의 관계를 나타냅니다. 따라서 사용자와 조직의 관계가 변할 때에만 이 속성을 변경해야 합니다. 사용자의 관계가 변경되면 UPN(사용자 계정 이름)을 변경해야 합니까? 사용자가 같은 리소스에 대한 액세스 권한을 계속 가져야 합니까? 사서함을 할당해야 합니까?와 같은 다른 질문에 답변해야 합니다. 

## <a name="remove-guest-user-limitations"></a>게스트 사용자 제한 제거
게스트 사용자에게 더 높은 권한을 부여하려는 경우가 있을 수 있습니다. 게스트 사용자를 모든 역할에 추가하고 디렉터리에서 기본 게스트 사용자 제한을 제거하여 사용자에게 구성원과 동일한 권한을 부여할 수 있습니다.

기본 제한을 해제하여 회사 디렉터리의 게스트 사용자에게 구성원 사용자와 동일한 권한을 부여할 수 있습니다.

![사용자 설정의 외부 사용자 옵션을 보여 주는 스크린샷](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>게스트 사용자를 Exchange 전역 주소 목록에 표시할 수 있나요?
예. 기본적으로 게스트 개체는 조직의 글로벌 주소 목록에 표시되지 않지만, Azure Active Directory PowerShell을 사용하여 표시할 수 있습니다. 자세한 내용은 [Microsoft 365 그룹별 게스트 액세스 문서](/microsoft-365/solutions/per-group-guest-access)에서 "전체 주소 목록에 게스트 추가"를 참조하세요.

## <a name="can-i-update-a-guest-users-email-address"></a>게스트 사용자의 이메일 주소를 업데이트할 수 있나요?

게스트 사용자가 초대를 수락하고 그 후에 이메일 주소를 변경한 경우, 새 이메일 주소는 디렉터리의 게스트 사용자 개체와 자동으로 동기화되지 않습니다. 해당 메일의 속성은 [Microsoft Graph API](/graph/api/resources/user)를 통해 생성됩니다. Microsoft Graph API, Exchange 관리 센터 또는 [Exchange Online PowerShell](/powershell/module/exchange/users-and-groups/set-mailuser)을 통해 메일 속성을 업데이트할 수 있습니다. 변경 내용은 Azure AD 게스트 사용자 개체에 반영됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD B2B 협업이란?](what-is-b2b.md)
* [B2B 협업 사용자 토큰](user-token.md)
* [B2B 협업 사용자 클레임 매핑](claims-mapping.md)
