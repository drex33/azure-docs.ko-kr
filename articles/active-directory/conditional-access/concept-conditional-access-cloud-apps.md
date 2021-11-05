---
title: 조건부 액세스 정책의 클라우드 앱, 작업 및 인증 컨텍스트 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 클라우드 앱, 작업 및 인증 컨텍스트란 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d56443885c79f6d5aa153695df76156f4f9407
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012684"
---
# <a name="conditional-access-cloud-apps-actions-and-authentication-context"></a>조건부 액세스: 클라우드 앱, 작업, 인증 컨텍스트

클라우드 앱, 작업 및 인증 컨텍스트는 조건부 액세스 정책의 주요 신호입니다. 관리자는 조건부 액세스 정책을 사용하여 특정 애플리케이션, 작업 또는 인증 컨텍스트에 제어를 할당할 수 있습니다.

- 관리자는 기본 제공되는 Microsoft 애플리케이션과 모든 [Azure AD 통합 애플리케이션](../manage-apps/what-is-application-management.md)(갤러리, 비갤러리 및 [애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)를 통해 게시된 애플리케이션 등)을 포함한 애플리케이션 목록에서 선택할 수 있습니다.
- 관리자는 클라우드 애플리케이션이 아니라 **보안 정보 등록** 또는 **디바이스 등록 또는 조인** 과 같은 [사용자 작업](#user-actions)을 기반으로 정책을 정의할 수 있으므로 조건부 액세스를 통해 해당 작업을 강제로 제어할 수 있습니다.
- 관리자는 [인증 컨텍스트](#authentication-context-preview)를 사용하여 애플리케이션에서 추가 보안 계층을 제공할 수 있습니다. 

![조건부 액세스 정책을 정의하고 클라우드 앱을 지정](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 애플리케이션

기존 Microsoft 클라우드 애플리케이션 중 상당수는 선택할 수 있는 애플리케이션 목록에 포함되어 있습니다. 

관리자들은 Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다. Office 365 및 Microsoft Azure 관리와 같은 일부 앱은 관련된 여러 자식 앱 또는 서비스를 포함합니다. 계속해서 더 많은 앱이 추가하고 있으므로 다음 목록은 완전한 목록이 아니며 변경될 수 있습니다.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- Azure Event Hubs
- Azure Service Bus
- [Azure SQL Database 및 Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Common Data Service
- Microsoft Application Insights 분석
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 관리](#microsoft-azure-management)
- Microsoft Azure 구독 관리
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control 포털
- Microsoft Commerce Tools 인증 서비스
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 등록](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft Power Apps
- Microsoft Power Automate
- Bing에서 Microsoft Search
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 서비스
- Project Online
- 비즈니스 온라인용 Skype
- VPN(가상 사설망)
- Windows Defender ATP

> [!IMPORTANT]
> 조건부 액세스에 사용할 수 있는 애플리케이션이 온보딩 및 유효성 검사 프로세스를 통과했습니다. 이 목록에 모든 Microsoft 앱이 포함되는 것은 아닙니다. 대부분 백 엔드 서비스이고 정책이 직접 적용되지 않기 때문입니다. 누락된 애플리케이션을 찾고 있는 경우 특정 애플리케이션 팀에 문의하거나 [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에 대한 요청을 수행할 수 있습니다.

### <a name="office-365"></a>Office 365

Microsoft 365는 Exchange, SharePoint 및 Microsoft Teams와 같은 클라우드 기반 생산성 및 협업 서비스를 제공합니다. Microsoft 365 클라우드 서비스는 원활한 협업 환경을 보장하기 위해 긴밀하게 통합됩니다. Microsoft 팀과 같은 일부 앱이 SharePoint 또는 Exchange와 같은 다른 앱에 종속되어 있기 때문에 이러한 통합은 정책을 만들 때 혼동을 일으킬 수 있습니다.

Office 365 제품군을 사용하면 해당 서비스를 한 번에 모두 대상으로 지정할 수 있습니다. [서비스 종속성](service-dependencies.md) 문제를 방지하기 위해 개별 클라우드 앱을 대상으로 지정하는 대신 새 Office 365 제품군을 사용하는 것이 좋습니다. 

이 애플리케이션 그룹을 대상으로 지정하면 일관되지 않은 정책과 종속성으로 인해 발생할 수 있는 문제를 방지하는 데 도움이 됩니다. 예: Exchange Online 앱은 메일, 달력, 연락처 정보 등의 기존 Exchange Online 데이터에 연결됩니다. 관련 메타데이터는 검색과 같은 다양한 리소스를 통해 노출될 수 있습니다. 모든 메타데이터가 의도한 대로 보호되려면 관리자는 Office 365 앱에 정책을 할당해야 합니다.

관리자는 정책에서 Office 365 제품군을 포함하고 특정 앱을 제외하여 원하는 경우 정책에서 특정 앱을 제외할 수 있습니다.

Office 365 클라이언트 앱에 포함된 주요 애플리케이션은 다음과 같습니다.

   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search Service
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - Power Automate
   - Power Apps
   - 비즈니스 온라인용 Skype
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 관리

Microsoft Azure 관리 애플리케이션에는 여러 가지 서비스가 포함됩니다. 

   - Azure portal
   - Azure Resource Manager 공급자
   - 클래식 배포 모델 API
   - Azure PowerShell
   - Azure CLI
   - Visual Studio 구독 관리자 포털
   - Azure DevOps
   - Azure Data Factory 포털

> [!NOTE]
> Microsoft Azure 관리 애플리케이션은 Azure PowerShell에 적용되며 이는 Azure Resource Manager API를 호출합니다. Microsoft Graph를 호출하는 Azure AD PowerShell에는 적용되지 않습니다.

### <a name="other-applications"></a>다른 애플리케이션

관리자는 Azure AD에 등록된 애플리케이션을 조건부 액세스 정책에 추가할 수 있습니다. 이러한 애플리케이션에는 다음이 포함될 수 있습니다. 

- [Azure AD 애플리케이션 프록시](../app-proxy/what-is-application-proxy.md)를 통해 게시된 애플리케이션
- [갤러리에서 추가된 애플리케이션](../manage-apps/add-application-portal.md)
- [갤러리에 없는 사용자 지정 애플리케이션](../manage-apps/view-applications-portal.md)
- [앱 제공 컨트롤러 및 네트워크를 통해 게시된 레거시 애플리케이션](../manage-apps/secure-hybrid-access.md)
- [암호 기반 Single Sign-On](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)을 사용하는 애플리케이션

> [!NOTE]
> 조건부 액세스 정책은 서비스에 액세스하기 위한 요구 사항을 설정하므로 클라이언트(퍼블릭/네이티브) 애플리케이션에 적용할 수 없습니다. 다시 말해, 정책은 클라이언트(퍼블릭/네이티브) 애플리케이션에 직접 설정되지 않으며 다만 클라이언트가 서비스를 호출할 때 적용됩니다. 예를 들어, SharePoint 서비스에서 설정된 정책은 SharePoint를 호출하는 클라이언트에 적용됩니다. Exchange에 설정된 정책은 Outlook 클라이언트를 사용하여 이메일에 액세스하려고 할 때 적용됩니다. 따라서 클라우드 앱 선택기에서 클라이언트(퍼블릭/네이티브) 애플리케이션을 사용할 수 없으며, 테넌트에 등록된 클라이언트(퍼블릭/네이티브) 애플리케이션의 애플리케이션 설정에서 조건부 액세스 옵션을 사용할 수 없습니다. 

## <a name="user-actions"></a>사용자 작업

사용자 작업은 사용자가 수행할 수 있는 작업입니다. 현재 조건부 액세스는 다음 두 가지 사용자 작업을 지원합니다. 

- **보안 정보 등록**: 이 사용자 작업을 사용하면 결합된 등록을 사용하도록 설정된 사용자가 보안 정보를 등록하려고 할 때 조건부 액세스 정책을 적용할 수 있습니다. 자세한 내용은 [결합된 보안 정보 등록](../authentication/concept-registration-mfa-sspr-combined.md) 문서에서 확인할 수 있습니다.

- **디바이스 등록 또는 조인**: 이 사용자 작업을 통해 관리자는 사용자가 디바이스를 Azure AD에 [등록](../devices/concept-azure-ad-register.md)하거나 [조인](../devices/concept-azure-ad-join.md)할 때 조건부 액세스 정책을 적용할 수 있습니다. 현재 존재하는 테넌트 전체 정책 대신 디바이스를 등록하거나 조인하기 위한 다단계 인증을 구성하는 세분성을 제공합니다. 이 사용자 작업에는 세 가지 주요 고려 사항이 있습니다. 
   - `Require multi-factor authentication`은 이 사용자 작업에서 사용할 수 있는 유일한 액세스 제어이며 다른 모든 액세스 제어는 사용할 수 없습니다. 이 제한은 Azure AD 디바이스 등록에 종속되거나 Azure AD 디바이스 등록에 적용되지 않는 액세스 제어와의 충돌을 방지합니다. 
   - `Client apps`, `Filters for devices`, `Device state` 조건은 조건부 액세스 정책을 적용하기 위해 Azure AD 디바이스 등록에 종속되므로 이 사용자 작업에서 사용할 수 없습니다.
   - 이 사용자 작업으로 조건부 액세스 정책을 사용하는 경우 **Azure Active Directory** > **디바이스** > **디바이스 설정** - `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication`을 **아니요** 로 설정해야 합니다. 그러지 않으면 이 사용자 작업으로 조건부 액세스 정책이 제대로 적용되지 않습니다. 이 디바이스 설정에 관한 자세한 내용은 [디바이스 설정 구성](../devices/device-management-azure-portal.md#configure-device-settings)에서 확인할 수 있습니다. 

## <a name="authentication-context-preview"></a>인증 컨텍스트(미리 보기)

인증 컨텍스트를 사용하여 애플리케이션에서 데이터 및 작업을 추가로 보호할 수 있습니다. 해당 애플리케이션은 사용자 고유의 사용자 지정 애플리케이션, 사용자 지정 LOB(기간 업무) 애플리케이션, SharePoint와 같은 애플리케이션 또는 MCAS(Microsoft Cloud App Security)로 보호되는 애플리케이션일 수 있습니다. 

예를 들어 조직에서 런치 메뉴 또는 비밀 BBQ 소스 레시피와 같은 파일을 SharePoint 사이트에 유지할 수 있습니다. 모든 사용자가 런치 메뉴 사이트에 액세스할 수 있지만 비밀 BBQ 소스 레시피 사이트에 액세스할 수 있는 사용자는 관리형 디바이스에서 액세스하고 특정 사용 약관에 동의해야 할 수 있습니다.

### <a name="configure-authentication-contexts"></a>인증 컨텍스트 구성

인증 컨텍스트는 Azure Portal의 **Azure Active Directory** > **보안** > **조건부 액세스** > **인증 컨텍스트** 에서 관리합니다.

![Azure Portal에서 인증 컨텍스트 관리](./media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-get-started.png)

> [!WARNING]
> * 미리 보기 중에는 인증 컨텍스트 정의를 삭제할 수 없습니다. 
> * 미리 보기는 Azure Portal에서 총 25개의 인증 컨텍스트 정의로 제한됩니다.

Azure Portal에서 **새 인증 컨텍스트** 를 선택하여 새 인증 컨텍스트 정의를 만듭니다. 다음 속성을 구성합니다.

- **표시 이름** 은 Azure AD와 인증 컨텍스트를 사용하는 애플리케이션에서 인증 컨텍스트를 식별하는 데 사용되는 이름입니다. 필요한 인증 컨텍스트 수를 줄이기 위해 “신뢰할 수 있는 디바이스”와 같은 리소스에서 사용할 수 있는 이름을 사용하는 것이 좋습니다. 설정된 수를 줄이면 리디렉션 수가 제한되고 최종 사용자 환경이 향상됩니다.
- **설명** 은 Azure AD 관리자가 사용하는 정책과 리소스에 인증 컨텍스트를 적용하는 정책에 대한 자세한 정보를 제공합니다.
- **앱에 게시** 확인란을 선택하면 인증 컨텍스트를 앱에 보급하고 할당할 수 있도록 합니다. 선택하지 않으면 다운스트림 리소스에서 인증 컨텍스트를 사용할 수 없습니다. 
- **ID** 는 읽기 전용이며 요청별 인증 컨텍스트 정의에 대한 토큰 및 앱에서 사용됩니다. 문제 해결 및 개발 사용 사례는 여기에 나열되어 있습니다. 

#### <a name="add-to-conditional-access-policy"></a>조건부 액세스 정책에 추가

관리자는 **할당** > **클라우드 앱 또는 작업** 아래에서 **이 정책을 적용할 항목을 선택합니다.** 메뉴의 **인증 컨텍스트** 를 선택하여 조건부 액세스 정책에서 게시된 인증 컨텍스트를 선택할 수 있습니다.

:::image type="content" source="media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-in-policy.png" alt-text="정책에 조건부 액세스 인증 컨텍스트 추가":::

### <a name="tag-resources-with-authentication-contexts"></a>인증 컨텍스트를 통해 리소스에 태그를 추가합니다. 

애플리케이션의 인증 컨텍스트 사용에 대한 자세한 내용은 다음 문서를 참조하세요.

- [SharePoint 사이트를 보호하기 위한 Microsoft Information Protection 민감도 레이블](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Microsoft Cloud App Security](/cloud-app-security/session-policy-aad?branch=pr-en-us-2082#require-step-up-authentication-authentication-context)
- [사용자 지정 애플리케이션](../develop/developer-guide-conditional-access-authentication-context.md)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 조건](concept-conditional-access-conditions.md)
- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
- [클라이언트 애플리케이션 종속성](service-dependencies.md)
