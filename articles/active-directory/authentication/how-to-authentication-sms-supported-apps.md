---
title: Azure Active Directory에서 SMS 기반 인증에 대한 지원 추가
description: 사용자가 SMS를 사용하여 Azure Active Directory에 로그인할 수 있도록 지원되는 앱 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: aanjusingh
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a35994441ad2b0cd2ecdf39675ef51840dc0de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027952"
---
# <a name="app-support-for-sms-based-authentication"></a>SMS 기반 인증을 위한 앱 지원

SMS 기반 인증은 Microsoft ID 플랫폼(Azure AD)과 통합된 Microsoft 앱에서 사용할 수 있습니다. 표에는 SMS 기반 인증을 지원하는 일부 웹 및 모바일 앱이 나열되어 있습니다. 앱을 추가하거나 유효성을 검사하려면 [문의하세요](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789). 

| 앱 | 웹/브라우저 앱 | 네이티브 모바일 앱 |
| --- |:---:|:---:|
| Office 365 - Microsoft Online Services* | ● |   |
| Microsoft One Note | ● |   |
| Microsoft Teams | ● | ● |
| Microsoft Intune/회사 포털 | ● | ● |
| 내 앱 포털 | ● |사용할 수 없음|
| Microsoft Forms | ● |사용할 수 없음|
| Microsoft Edge | ● |   |
| Microsoft Power BI | ● |   |
| Microsoft Stream | ● |   |
| Microsoft Power Apps | ● |   |
| Microsoft Azure | ● | ● |
| Microsoft Authenticator |   | ● |
| Azure Virtual Desktop | ● |  | 

*_SMS 로그인은 Word, Excel 등과 같은 Office 애플리케이션에서 웹에 직접 액세스한 경우 사용할 수 없으나, [Office 365 웹앱](https://www.office.com)_ 을 통해 액세스한 경우에는 가능합니다.

위에서 언급한 Microsoft 앱은 SMS 로그인을 지원합니다. 사용자가 전화 번호 및 SMS 코드를 입력할 수 있는 Microsoft ID 로그인(`https://login.microsoftonline.com/`)을 사용하기 때문입니다.

## <a name="unsupported-microsoft-apps"></a>지원되지 않는 Microsoft 앱

웹에서 직접 액세스하는 Microsoft 365 데스크톱(Windows 또는 Mac) 앱 및 Microsoft 365 웹앱(MS One Note 제외)은 SMS 로그인을 지원하지 않습니다. 이러한 앱은 로그인하는 데 암호가 필요한 Microsoft Office 로그인(`https://office.live.com/start/*`)을 사용합니다.
동일한 이유로 Microsoft Office 모바일 앱(Microsoft Teams, Intune 회사 포털 및 Microsoft Azure 제외)은 SMS 로그인을 지원하지 않습니다.

| 지원되지 않는 Microsoft 앱| 예제 |
| --- | --- |
| 네이티브 데스크톱 Microsoft 앱 | Microsoft Teams, O365 앱, Word, Excel 등.|
| 네이티브 모바일 Microsoft 앱(Microsoft Teams, Intune 회사 포털 및 Microsoft Azure 제외) | Outlook, Edge, Power BI, Stream, SharePoint, Power Apps, Word 등.|
| Microsoft 365 웹앱(웹에서 직접 액세스) | [Outlook](https://outlook.live.com/owa/), [Word](https://office.live.com/start/Word.aspx), [Excel](https://office.live.com/start/Excel.aspx), [PowerPoint](https://office.live.com/start/PowerPoint.aspx), [OneDrive](https://onedrive.live.com/about/signin)|  

## <a name="support-for-non-microsoft-apps"></a>비 Microsoft 앱 지원 

Microsoft 이외의 앱이 SMS 로그인 기능과 호환되도록 하려면 다음을 수행합니다. 
- 비 Microsoft 웹앱을 Azure AD와 통합하고 Azure AD 인증을 사용합니다. SAML[Security Assertion Markup Language](../manage-apps/add-application-portal-setup-sso.md) 또는 OIDC[Open ID Connect](../manage-apps/add-application-portal-setup-oidc-sso.md)를 사용하여 Azure AD SSO와 통합합니다. 
- [Azure AD 애플리케이션 프록시](../app-proxy/application-proxy-add-on-premises-application.md)를 사용하여 비 Microsoft 온-프레미스 앱을 Azure AD와 통합
- 인증을 위해 비 Microsoft 클라이언트 앱을 [Microsoft ID 플랫폼](../develop/v2-overview.md)과 통합 
    - [샘플 앱 iOS](../develop/tutorial-v2-ios.md)
    - [샘플 앱 Android](../develop/tutorial-v2-android.md)

## <a name="next-steps"></a>다음 단계

- [사용자에 대해 SMS 기반 로그인을 사용하도록 설정하는 방법](howto-authentication-sms-signin.md)
- MSAL 라이브러리를 사용하여 네이티브 모바일 앱에 SMS 로그인을 사용하도록 설정하려면 다음 링크를 참조하세요. 
  - [iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)
  - [Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [Azure Active Directory와 SAAS 애플리케이션 통합](../saas-apps/tutorial-list.md)

## <a name="recommended-content"></a>권장 콘텐츠

- [애플리케이션을 Azure Active Directory에 추가](../manage-apps/add-application-portal.md)
- [사용자를 인증하기 위해 Microsoft ID 플랫폼에서 토큰을 획득하는 MSAL 라이브러리 개요](../develop/msal-overview.md)
- [Azure AD를 사용하여 Microsoft Managed Home Screen 구성](/mem/intune/apps/app-configuration-managed-home-screen-app)
