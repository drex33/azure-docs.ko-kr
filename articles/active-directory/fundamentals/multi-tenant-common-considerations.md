---
title: Azure Active Directory의 다중 테넌트 사용자 관리에 대한 일반적인 고려 사항
description: 게스트 계정을 사용하는 Azure Active Directory 테넌트 간의 사용자 액세스에 대한 일반적인 디자인 고려 사항에 대해 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a93eb869cd3135f548331a82de9593497829c0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273584"
---
# <a name="common-considerations-for-multi-tenant-user-management"></a>다중 테넌트 사용자 관리에 대한 일반적인 고려 사항

둘 이상의 협업 패턴과 관련된 많은 고려 사항이 있습니다. 

## <a name="directory-object-considerations"></a>디렉터리 개체 고려 사항

콘솔을 사용하여 게스트 사용자 계정에 대한 초대를 수동으로 만들 수 있습니다. 이렇게 하면 사용자 유형이 *게스트* 인 사용자 개체가 만들어집니다. 다른 기술을 사용하여 초대를 만들면 사용자 유형을 게스트 계정이 아닌 다른 유형으로 설정할 수 있습니다. 예를 들어 API를 사용하는 경우 계정이 멤버 계정인지 게스트 계정인지를 구성할 수 있습니다.  

* [게스트 기능 제한 중 일부를 제거](../external-identities/user-properties.md#remove-guest-user-limitations)할 수 있습니다.

* [게스트 계정을 Member 사용자 유형으로 전환](../external-identities/user-properties.md#can-azure-ad-b2b-users-be-added-as-members-instead-of-guests)할 수 있습니다.

> **중요** 게스트 계정에서 사용자 계정으로 전환하는 경우 Exchange Online에서 B2B 계정을 처리하는 방식에 문제가 있을 수 있습니다. 게스트 멤버로 초대된 계정은 메일을 사용하도록 설정할 수 없습니다. 게스트 멤버 계정에서 메일을 사용하도록 설정하려면 다음을 수행하는 것이 가장 좋은 방법입니다.
>* 조직 간 사용자를 게스트 계정으로 초대합니다.
>* 계정을 GAL에 표시합니다.
>* UserType을 Member로 설정합니다.

이 방법을 사용하면 계정이 Exchange Online에서 MailUser로 표시됩니다.

### <a name="issues-with-using-mail-contact-objects-instead-of-external-users-or-members"></a>외부 사용자 또는 멤버 대신 메일 연락처 개체 사용과 관련된 문제

기존 GAL 동기화를 사용하여 다른 테넌트의 사용자를 나타낼 수 있습니다. Azure AD B2B 협업을 사용하는 대신 GAL 동기화가 수행되면 메일 연락처 개체가 만들어집니다. 

* 메일 연락처 개체와 메일 사용이 가능한 게스트 사용자(멤버 또는 게스트)는 동일한 이메일 주소를 사용하는 동일한 테넌트에서 동시에 공존할 수 없습니다. 

* 초대된 게스트 사용자와 동일한 메일 주소에 대해 메일 연락처 개체가 있는 경우 게스트 사용자가 만들어지지만 메일을 사용하도록 설정되지는 않습니다. 

* 메일 사용이 가능한 게스트 사용자가 동일한 메일로 있는 경우 메일 연락처 개체를 만들려고 하면 생성 시 예외가 발생합니다.

다양한 메일 연락처 개체 및 게스트 사용자 상태에 대한 결과는 다음과 같습니다.

| 기존 상태| 프로비전 시나리오| 유효 결과 |
| - |-|-|
| 없음| B2B 멤버 초대| 메일 사용이 가능하지 않은 멤버 사용자. 위의 중요 참고 사항 참조 |
| 없음| B2B 게스트 초대| 메일 사용이 가능한 게스트 사용자 |
| 메일 연락처 개체가 있음| B2B 멤버 초대| 오류 – 프록시 주소 충돌 |
| 메일 연락처 개체가 있음| B2B 게스트 초대| 메일 연락처 및 메일 사용이 가능하지 않은 게스트 사용자. 위의 중요 참고 사항 참조 |
| 메일 사용이 가능한 B2B 게스트 사용자| 메일 연락처 개체 만들기| Error |
| 메일 사용이 가능한 B2B 멤버 사용자가 있음| 메일 연락처 만들기| Error |


**Microsoft는 기존 GAL 동기화를 권장하지 않습니다**. 대신 Azure AD B2B 협업을 사용하여 다음을 만듭니다.

* GAL에 표시하도록 설정하는 외부 게스트 사용자

* 기본적으로 GAL에 표시되지만 메일 사용이 가능하지 않은 외부 멤버 사용자를 만듭니다.

일부 조직에서는 메일 연락처 개체를 사용하여 사용자를 GAL에 표시합니다. 이 방법은 메일 연락처가 보안 주체가 아니므로 다른 권한을 제공하지 않고 GAL을 통합합니다. 

이 목표를 달성할 수 있는 더 효율적인 방법은 다음과 같습니다.
* 게스트 사용자 초대
* GAL에서 숨김 해제
* [로그인을 차단](/powershell/module/azuread/set-azureaduser&preserve-view=true)하여 사용하지 않도록 설정

메일 연락처 개체는 사용자 개체로 변환할 수 없습니다. 따라서 메일 연락처 개체와 연결된 속성은 전송할 수 없습니다. 예를 들어 그룹 멤버 자격 및 기타 리소스 액세스 권한은 전송되지 않습니다.  

메일 연락처 개체를 사용하여 사용자를 표시하면 다음과 같은 문제가 발생합니다.

* **Office 365 그룹** – Office 365 그룹은 그룹의 멤버가 되고 그룹과 연결된 콘텐츠와 상호 작용할 수 있는 사용자 유형을 제어하는 ​​정책을 지원합니다. 예를 들어 그룹은 게스트 계정의 조인을 허용하지 않을 수 있습니다. 이러한 정책은 메일 연락처 개체를 제어할 수 없습니다.

* **Azure AD SSGM(셀프 서비스 그룹 관리)** – 메일 연락처 개체는 SSGM 기능을 사용하는 그룹의 멤버가 될 수 없습니다. 사용자 개체 대신 연락처로 표시되는 받는 사람이 있는 그룹을 관리하려면 추가 도구가 필요할 수 있습니다.

* **Azure AD Identity Governance - 액세스 검토** – 액세스 검토 기능을 사용하여 Office 365 그룹의 멤버 자격을 검토하고 증명할 수 있습니다. 액세스 검토는 사용자 개체를 기반으로 합니다. 메일 연락처 개체로 표시되는 멤버는 액세스 검토 범위를 벗어납니다.

* **Azure AD Identity Governance - EM(권한 관리)** – 회사의 EM 포털을 통해 외부 사용자에 대한 셀프 서비스 액세스 요청을 사용하도록 설정하는 데 EM을 사용하는 경우 요청 시 사용자 개체가 만들어집니다. 메일 연락처 개체는 지원되지 않습니다. 

## <a name="azure-ad-conditional-access-considerations"></a>Azure AD 조건부 액세스 고려 사항

사용자의 홈 테넌트에 있는 사용자, 디바이스 또는 네트워크의 상태는 리소스 테넌트에 전달되지 않습니다. 따라서 게스트 사용자 계정은 다음 제어를 사용하는 CA(조건부 액세스) 정책을 충족하지 못할 수 있습니다.

* **다단계 인증 필요** – 홈 테넌트에서 MFA가 충족된 경우에도 게스트 사용자는 리소스 테넌트에서 MFA에 등록/응답해야 하므로 여러 MFA 문제가 발생합니다. 또한 MFA 증명을 다시 설정해야 하는 경우 테넌트 간에 여러 MFA 증명 등록을 인식하지 못할 수 있습니다. 인식하지 못하는 경우 사용자가 홈 테넌트, 리소스 테넌트 또는 둘 다의 관리자에게 문의해야 할 수 있습니다.

* **준수 상태로 표시된 디바이스 필요** – 디바이스 ID가 리소스 테넌트에 등록되지 않으므로 게스트 사용자는 이 제어가 필요한 리소스에 액세스하지 못하도록 차단됩니다.

* **하이브리드 Azure AD 조인된 디바이스 필요** - 디바이스 ID가 리소스 테넌트(또는 리소스 테넌트에 연결된 온-프레미스 Active Directory)에 등록되지 않으므로 게스트 사용자는 이 제어가 필요한 리소스에 액세스하지 못하도록 차단됩니다.

* **승인된 클라이언트 앱 필요 또는 앱 보호 정책 필요** – 디바이스 등록도 필요하므로 기 외부 게스트 사용자는 리소스 테넌트 Intune MAM(모바일 앱 관리) 정책을 적용할 수 없습니다. 이 제어를 사용하는 리소스 테넌트 CA(조건부 액세스) 정책에서는 홈 테넌트 MAM 보호에서 정책을 충족하도록 허용하지 않습니다. 외부 게스트 사용자는 모든 MAM 기반 CA 정책에서 제외되어야 합니다. 

또한 다음 CA 조건을 사용할 수 있지만 가능한 결과를 알고 있어야 합니다.

* **로그인 위험 및 사용자 위험** – 로그인 위험 및 사용자 위험은 홈 테넌트에서 사용자 동작에 따라 부분적으로 결정됩니다. 데이터 및 위험 점수는 홈 테넌트에 저장됩니다.  
리소스 테넌트 정책에서 게스트 사용자를 차단하는 경우 리소스 테넌트 관리자가 액세스를 사용하도록 설정하지 못할 수 있습니다. 자세한 내용은 [ID 보호 및 B2B 사용자](../identity-protection/concept-identity-protection-b2b.md)를 참조하세요.

* **위치** – 리소스 테넌트에 정의된 명명된 위치 정의는 정책의 범위를 결정하는 데 사용됩니다. 홈 테넌트에서 관리되는 신뢰할 수 있는 위치는 정책 범위에서 평가되지 않습니다. 일부 시나리오에서는 조직에서 테넌트 간에 신뢰할 수 있는 위치를 공유해야 할 수 있습니다. 신뢰할 수 있는 위치를 공유하려면 리소스 및 조건부 액세스 정책이 정의된 각 테넌트에서 위치를 정의해야 합니다.

## <a name="other-access-control-considerations"></a>기타 액세스 제어 고려 사항

액세스 제어를 구성할 때 고려해야 하는 몇 가지 추가 사항이 있습니다.
* 리소스에 대한 액세스 정책을 제어하는 [액세스 제어 정책](../external-identities/conditional-access.md)을 정의합니다.
* 게스트 사용자를 고려하여 CA 정책을 설계합니다. 
* 게스트 사용자에 대한 정책을 구체적으로 만듭니다. 
* 조직에서 [모든 사용자] 조건을 기존 CA 정책에 사용하는 경우 [게스트] 사용자가 [모든 사용자]의 범위에 있으므로 이 정책은 게스트 사용자에게 영향을 줍니다.
* [게스트] 계정에 대한 전용 CA 정책을 만듭니다. 

[모든 사용자] 식을 활용하는 동적 그룹을 강화하는 방법에 대한 자세한 내용은 [동적 그룹 및 Azure AD B2B 협업](../external-identities/use-dynamic-groups.md)을 참조하세요.

### <a name="require-user-assignment"></a>사용자 할당 필요

애플리케이션에서 [사용자 할당 필요?] 속성이 [아니요]로 설정되어 있으면 게스트 사용자가 애플리케이션에 액세스할 수 있습니다. 특히 중요한 정보가 애플리케이션에 포함된 경우 애플리케이션 관리자는 액세스 제어 영향을 이해해야 합니다. 자세한 내용은 [Azure AD 앱을 사용자 세트로 제한하는 방법](../develop/howto-restrict-your-app-to-a-set-of-users.md)을 참조하세요. 

### <a name="terms-and-conditions"></a>사용 약관

[Azure AD 사용 약관](../conditional-access/terms-of-use.md)은 조직에서 최종 사용자에게 정보를 제공하는 데 사용할 수 있는 간단한 방법을 제공합니다. 사용 약관을 사용하여 게스트 사용자가 리소스에 액세스하기 전에 사용 약관을 승인하도록 요구할 수 있습니다.

### <a name="licensing-considerations-for-guest-users-with-azure-ad-premium-features"></a>Azure AD Premium 기능을 사용하는 게스트 사용자에 대한 라이선스 고려 사항

Azure AD External Identities(게스트 사용자) 가격 책정은 MAU(월간 활성 사용자 수)를 기반으로 합니다. 활성 사용자 수는 달력 월 내에 인증 작업이 있는 고유 사용자의 수입니다. MAU 청구는 무료 계층 및 유연하고 예측 가능한 가격을 제공하므로 비용을 절감할 수 있습니다. 또한 처음 50,000명 MAU/월은 Premium P1 및 Premium P2 기능 모두에 대해 무료입니다. Premium 기능에는 게스트 사용자에 대한 조건부 액세스 정책 및 Azure MFA가 포함됩니다.

자세한 내용은 [Azure AD External Identities에 대한 MAU 청구 모델](../external-identities/external-identities-pricing.md)을 참조하세요.

## <a name="office-365-considerations"></a>Office 365 고려 사항

다음 정보는 이 백서의 시나리오 컨텍스트에서 Office 365를 처리합니다. 자세한 내용은 [Office 365 테넌트 간 협업](/office365/enterprise/office-365-inter-tenant-collaboration)에서 확인할 수 있습니다.

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

Exchange Online은 게스트 사용자에 대한 특정 기능을 제한합니다. 외부 게스트 대신 외부 멤버를 만들어 제한을 줄일 수 있습니다. 그러나 현재 외부 사용자에 대해 다음 중 어느 것도 지원되지 않습니다. 

* 게스트 사용자에게 Exchange Online 라이선스를 할당할 수 있습니다. 그러나 Exchange Online에 대한 토큰은 발급하지 못합니다. 결과는 리소스에 액세스할 수 없다는 것입니다.

   * 게스트 사용자는 리소스 테넌트에서 공유되거나 위임된 Exchange Online 사서함을 사용할 수 없습니다.

   * 게스트 사용자는 공유 사서함에 할당할 수 있지만 액세스할 수는 없습니다.

* GAL에 포함하려면 게스트 사용자를 숨김 해제해야 합니다. 이는 기본적으로 숨겨져 있습니다.

   * 숨겨진 게스트 사용자는 초대 시 만들어집니다. 사용자가 초대를 회수했는지 여부에 관계없이 만들어집니다. 따라서 모든 게스트 사용자가 숨김 해제된 경우 목록에는 초대를 회수하지 않은 게스트 사용자의 사용자 개체가 포함됩니다. 시나리오에 따라 개체를 나열하거나 나열하지 않을 수 있습니다.

   * 게스트 사용자는 [Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true)만 사용하여 숨김을 해제할 수 있습니다. [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) PowerShell cmdlet을 실행하여 HiddenFromAddressListsEnabled 속성을 $false 값으로 설정할 수 있습니다.  
‎  
`‎Set-MailUser [GuestUserUPN] -HiddenFromAddressListsEnabled:$false` ‎  
‎여기서 [GuestUserUPN]은 계산된 UserPrincipalName입니다. 예제:  
‎  
`‎Set-MailUser guestuser1_contoso.com#EXT#@fabricam.onmicrosoft.com -HiddenFromAddressListsEnabled:$false`

* PrimarySmtpAddress, ExternalEmailAddress, EmailAddresses 및 MailTip과 같은 Exchange 특정 속성에 대한 업데이트는 [Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true)을 통해서만 설정할 수 있습니다. Exchange Online 관리 센터에서는 GUI를 사용하여 특성을 수정할 수 없습니다. 

위와 같이 [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) PowerShell cmdlet은 메일 관련 속성에 사용할 수 있습니다. [Set-User](/powershell/module/exchange/set-user?view=exchange-ps&preserve-view=true) PowerShell cmdlet을 사용하여 수정할 수 있는 많은 추가 사용자 속성이 많이 있습니다. 대부분의 속성은 Azure AD Graph API를 사용하여 수정할 수도 있습니다.

### <a name="microsoft-sharepoint-online"></a>Microsoft SharePoint Online

SharePoint Online에는 사용자가 Azure Active Directory 테넌트의 게스트 멤버인지 여부에 따라 고유한 서비스별 권한이 있습니다. 

자세한 내용은 [Office 365 외부 공유 및 Azure Active Directory B2B 협업](../external-identities/o365-external-user.md)을 참조하세요.

SharePoint Online에서 외부 공유를 사용하도록 설정하면 SharePoint Online 사용자 선택기에서 게스트 사용자를 검색하는 기능이 기본적으로 꺼져 있습니다. 게스트 사용자가 Exchange Online GAL에서 숨겨져 있을 때 이 설정은 해당 사용자를 검색하지 못하도록 합니다. 게스트 사용자가 다음 두 가지 방법으로 표시되도록 설정할 수 있습니다(함께 사용할 수 있음).

* 게스트 사용자를 검색하는 기능은 몇 가지 방법으로 사용하도록 설정할 수 있습니다.
   * 테넌트 및 사이트 모음 수준에서 'ShowPeoplePickerSuggestionsForGuestUsers' 설정을 수정합니다. 
   * [Set-SPOTenant](/powershell/module/sharepoint-online/Set-SPOTenant?view=sharepoint-ps&preserve-view=true) 및 [Set-SPOSite](/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps&preserve-view=true) [SharePoint Online PowerShell](/powershell/sharepoint/sharepoint-online/connect-sharepoint-online?view=sharepoint-ps&preserve-view=true) cmdlet을 사용하여 기능을 설정합니다.  
‎

* Exchange Online GAL에 표시되는 게스트 사용자는 SharePoint Online 사용자 선택기에도 표시됩니다. 계정은 'ShowPeoplePickerSuggestionsForGuestUsers' 설정에 관계없이 표시됩니다.

### <a name="microsoft-teams"></a>Microsoft Teams

Microsoft Teams에는 사용자 유형에 따라 액세스를 제한하는 기능이 있습니다. 사용자 유형을 변경하면 사용 가능한 콘텐츠 액세스 및 기능에 영향을 줄 수 있습니다. 

* Microsoft Teams에 대한 "테넌트 전환" 메커니즘을 사용하려면 사용자가 홈 테넌트 외부의 Teams에서 작업할 때 Teams 클라이언트의 컨텍스트를 수동으로 전환해야 할 수 있습니다.

* 다른 전체 외부 도메인의 Teams 사용자가 Teams 페더레이션을 통해 사용자를 찾고, 전화를 걸고, 채팅하고, 모임을 확정하도록 설정할 수 있습니다. 자세한 내용은 [Microsoft Teams에서 외부 액세스 관리](/microsoftteams/manage-external-access)를 참조하세요. 

 

### <a name="licensing-considerations-for-guest-users-in-teams"></a>Teams의 게스트 사용자에 대한 라이선스 고려 사항

Office 365 워크로드에서 Azure B2B를 사용하는 경우 몇 가지 주요 고려 사항이 있습니다. 멤버 계정과 동일한 환경이 게스트 계정에 없는 경우가 있습니다. 

**Microsoft 그룹**. Microsoft 그룹의 게스트 계정 환경을 더 잘 이해하려면 [Office 365 그룹에 게스트 추가](https://support.office.com/article/adding-guests-to-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6)를 참조하세요. 

**Microsoft Teams**. Microsoft Teams의 게스트 계정 환경을 더 잘 이해하려면 [Teams의 팀 소유자, 멤버 및 게스트 기능](https://support.office.com/article/team-owner-member-and-guest-capabilities-in-teams-d03fdf5b-1a6e-48e4-8e07-b13e1350ec7b?ui=en-US&rs=en-US&ad=US)을 참조하세요. 

B2B 외부 멤버를 사용하여 Teams에서 완전한 충실도 환경을 사용하도록 설정할 수 있습니다. 최근에 Office 365는 다중 테넌트 조직에 대한 라이선스 정책을 명확히 했습니다.

* 홈 테넌트에서 사용하도록 허가된 사용자는 동일한 법인 내에서 다른 테넌트의 리소스에 액세스할 수 있습니다. 추가 라이선스 요금 없이 **외부 멤버** 설정을 사용하여 액세스 권한이 부여됩니다. 이 설정은 SharePoint, 비즈니스용 OneDrive, Teams 및 그룹에 적용됩니다. 

   * 홈 테넌트에 있는 사용자의 라이선스 상태를 자동으로 확인하고 추가 라이선스 할당 또는 구성 없이 멤버로 참여할 수 있도록 하는 엔지니어링 작업은 진행 중입니다. 그러나 지금 외부 멤버를 사용하려는 고객의 경우 계정 임원이 Microsoft 비즈니스 데스크와 협력해야 하는 라이선스 해결 방법이 있습니다. 

   * 지금부터 엔지니어링 라이선스 솔루션이 사용하도록 설정될 때까지 고객은 *Teams 평가판 라이선스* 를 활용할 수 있습니다. 라이선스는 외부 테넌트의 각 사용자에게 할당할 수 있습니다. 라이선스 기간은 1년이며, 위에 나열된 모든 워크로드를 사용하도록 설정합니다.

   * B2B 게스트를 B2B 멤버로 전환하려는 고객의 경우 새 채널을 만들 수 없는 기능 및 애플리케이션을 기존 Team에 추가할 수 있는 기능과 같이 Microsoft Teams와 관련된 몇 가지 알려진 문제가 있습니다. 

* **Identity Governance** 기능(권한 관리, 액세스 검토)을 사용하려면 게스트 사용자 또는 외부 멤버에 대한 다른 라이선스가 필요할 수 있습니다. 계정 팀 또는 비즈니스 데스크와 협력하여 조직에 적합한 답변을 받으세요.

**다른 제품**(예: Dynamics CRM)에는 사용자가 표시되는 모든 테넌트의 라이선스가 필요할 수 있습니다. 계정 팀과 협력하여 조직에 적합한 답변을 받으세요.

## <a name="next-steps"></a>다음 단계
[다중 테넌트 사용자 관리 소개](multi-tenant-user-management-introduction.md)

[다중 테넌트 최종 사용자 관리 시나리오](multi-tenant-user-management-scenarios.md)

[일반적인 다중 테넌트 솔루션](multi-tenant-common-solutions.md)