---
title: Azure AD Multi-Factor Authentication 개요
description: Azure AD Multi-Factor Authentication이 간단한 로그인 프로세스에 대한 사용자 요구를 충족하면서 데이터 및 애플리케이션에 대한 액세스를 보호하는 방법을 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 133ec8c46bb227f2f43cb9243ca5db4a4e9ac32b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566828"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>작동 방법: Azure AD Multi-Factor Authentication

다단계 인증은 로그인 프로세스 중에 코드를 휴대폰에 입력하거나 지문 검사를 제공하는 것과 같은 추가 형식의 식별을 요구하는 프로세스입니다.

암호만 사용하여 사용자를 인증하면 공격에 안전하지 않은 벡터가 유지됩니다. 암호가 약하거나 다른 곳에 노출된 경우 실제로 사용자 이름과 암호를 사용하는 사용자 로그인일까요, 아니면 공격자일까요? 두 번째 인증 형식이 필요한 경우 이 추가 요소는 공격자가 쉽게 얻거나 복제할 수 있는 것이 아니므로 보안이 향상됩니다.

![다양한 형식의 다단계 인증에 대한 개념적 이미지](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication은 다음 인증 방법 중 둘 이상을 요구하여 작동합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자의 소유물 정보(예: 휴대폰 또는 하드웨어 키와 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 생체 인식 정보(예: 지문 또는 얼굴 스캔)

Azure AD Multi-Factor Authentication은 암호 재설정을 추가로 보호할 수도 있습니다. 사용자가 Azure AD Multi-Factor Authentication에 등록할 때 한 번에 셀프 서비스 암호 재설정을 등록할 수도 있습니다. 관리자는 구성 결정에 따라 보조 인증 양식을 선택하고 MFA에 대한 과제를 구성할 수 있습니다. 

Azure AD Multi-Factor Authentication을 사용하기 위해 앱 및 서비스를 변경할 필요가 없습니다. 확인 프롬프트는 Azure AD 로그인 이벤트의 일부에서 필요한 경우 MFA 챌린지를 자동으로 요청하고 처리합니다.

![로그인 화면에서 사용하는 인증 방법](media/concept-authentication-methods/overview-login.png)

## <a name="available-verification-methods"></a>사용 가능한 인증 방법

사용자가 애플리케이션 또는 서비스에 로그인하고 MFA 프롬프트를 받으면 등록된 추가 인증 형식 중 하나를 선택할 수 있습니다. 사용자는 [내 프로필](https://myprofile.microsoft.com)에 액세스하여 인증 방법을 편집하거나 추가할 수 있습니다.

Azure AD Multi-Factor Authentication에 사용할 수 있는 추가 인증 형식은 다음과 같습니다.

* Microsoft Authenticator 앱
* OATH 하드웨어 토큰(미리 보기)
* OATH 소프트웨어 토큰
* sms
* 음성 통화

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication을 사용하도록 설정하는 방법

모든 Azure AD 테넌트는 [보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md)을 사용하여 모든 사용자에 대해 Microsoft Authenticator를 빠르게 활성화할 수 있습니다. Azure AD Multi-Factor Authentication에 대해 사용자 및 그룹을 사용하도록 설정하여 로그인 이벤트 중에 추가 인증 메시지를 표시할 수 있습니다. 

보다 세부적으로 컨트롤하려는 경우 [조건부 액세스](../conditional-access/overview.md) 정책을 사용하여 MFA가 필요한 이벤트 또는 애플리케이션을 정의할 수 있습니다. 해당 정책은 사용자가 회사 네트워크 또는 등록된 디바이스를 사용하는 경우 일반 로그인 이벤트를 허용하지만 원격 또는 개인 디바이스에서는 추가 인증 요소를 요청하는 메시지를 표시할 수 있습니다.

![조건부 액세스를 적용하여 로그인 프로세스를 보호하는 방법에 대한 개략적인 다이어그램](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>다음 단계

라이선스에 대한 자세한 내용은 [Azure AD Multi-Factor Authentication 기능 및 라이선스](concept-mfa-licensing.md)를 참고하세요.

다양한 인증 및 유효성 검사 방법에 대한 자세한 내용은 [Azure Active Directory의 인증 방법](concept-authentication-methods.md)을 참조하세요.

MFA가 작동하는 모습을 보려면 다음 자습서에서 테스트 사용자 집합에 대해 Azure AD Multi-Factor Authentication을 사용하도록 설정하세요.

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication 사용](./tutorial-enable-azure-mfa.md)