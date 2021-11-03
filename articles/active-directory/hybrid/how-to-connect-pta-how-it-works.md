---
title: 'Azure AD Connect: 통과 인증 - 작동 방식 | Microsoft Docs'
description: 이 문서에서는 Azure Active Directory 통과 인증이 작동하는 방식을 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d864e0334d4ecc4178ad45c7977ead421e2bcd5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063331"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 통과 인증: 기술 심층 분석
이 문서에서는 Azure AD(Azure Active Directory) 통과 인증이 작동하는 방식에 대해 간략히 설명합니다. 심층적인 기술 및 보안 정보는 [보안 심층 분석](how-to-connect-pta-security-deep-dive.md) 문서를 참조하세요.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 통과 인증 작동 방식은?

>[!NOTE]
>통과 인증이 작동하기 위한 전제 조건으로, Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure AD로 사용자를 프로비전해야 합니다. 통과 인증은 클라우드 전용 사용자에게 적용되지 않습니다.

사용자가 Azure AD에서 보호되는 애플리케이션에 로그인하려고 하고 통과 인증이 테넌트에서 사용하도록 설정되어 있으면 다음 단계가 수행됩니다.

1. 사용자가 애플리케이션(예: [Outlook 웹앱](https://outlook.office365.com/owa/))에 액세스하려고 합니다.
2. 사용자가 아직 로그인하지 않았으면 해당 사용자는 Azure AD **사용자 로그인** 페이지로 리디렉션됩니다.
3. 사용자가 Azure AD 로그인 페이지에 사용자 이름을 입력하고 **다음** 단추를 선택합니다.
4. 사용자가 Azure AD 로그인 페이지에 암호를 입력하고 **로그인** 단추를 선택합니다.
5. Azure AD에서 로그인 요청을 받으면 인증 에이전트의 공개 키로 암호화된 사용자 이름과 암호를 큐에 넣습니다.
6. 온-프레미스 인증 에이전트는 큐에서 사용자 이름 및 암호화된 암호를 검색합니다. 에이전트는 큐의 요청을 자주 폴링하지 않고, 미리 설정된 영구 연결을 통해 요청을 검색합니다.
7. 에이전트에서 해당 프라이빗 키를 사용하여 암호를 해독합니다.
8. 에이전트에서 표준 Windows API(AD FS(Active Directory Federation Services)에서 사용하는 것과 비슷한 메커니즘)를 사용하여 Active Directory에 대한 사용자 이름과 암호의 유효성을 검사합니다. 사용자 이름은 온-프레미스 기본 사용자 이름(일반적으로 `userPrincipalName`) 또는 Azure AD Connect에 구성된 또 다른 특성(`Alternate ID`라고 함) 중 하나일 수 있습니다.
9. 온-프레미스 Active Directory DC(도메인 컨트롤러)에서 요청을 평가하고, 적절한 응답(성공, 실패, 암호가 만료됨 또는 사용자가 잠겨 있음)을 에이전트에 반환합니다.
10. 그러면 인증 에이전트가 이 응답을 다시 Azure AD로 반환합니다.
11. Azure AD에서 응답을 평가하고 사용자에게 적절하게 응답합니다. 예를 들어 Azure AD는 즉시 사용자를 로그인하거나 Azure AD 다단계 인증을 요청합니다.
12. 사용자 로그인에 성공하면 사용자가 애플리케이션에 액세스할 수 있습니다.

다음 다이어그램에서는 관련된 모든 구성 요소와 단계를 보여 줍니다.

![통과 인증](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>다음 단계
- [현재 제한 사항](how-to-connect-pta-current-limitations.md): 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [빠른 시작](how-to-connect-pta-quick-start.md): Azure AD 통과 인증을 구성하고 실행합니다.
- [AD FS에서 통과 인증으로 마이그레이션](https://aka.ms/adfstoPTADP) - AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 가이드입니다.
- [스마트 잠금](../authentication/howto-password-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호합니다.
- [질문과 대답](how-to-connect-pta-faq.yml): 자주 묻는 질문에 대한 대답을 찾아봅니다.
- [문제 해결](tshoot-connect-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](how-to-connect-pta-security-deep-dive.md) - 통과 인증 기능에 대한 자세한 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](how-to-connect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
- [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.

