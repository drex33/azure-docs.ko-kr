---
title: Azure Active Directory에서 자격 증명 관리를 사용하여 복원력 빌드
description: 복원력 있는 자격 증명 전략을 구축하는 데 필요한 설계자 및 IT 관리자를 위한 가이드입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd03ce16f0695c23f30146cb8099eb67614982dc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754423"
---
# <a name="build-resilience-with-credential-management"></a>자격 증명 관리를 사용하여 복원력 빌드

자격 증명이 토큰 요청에서 Azure AD에 표시되면 유효성 검사에 사용할 수 있는 여러 종속성이 있습니다. 첫 번째 인증 단계는 Azure AD 인증에 따라 다르며, 일부 경우에는 온-프레미스 인프라에 따라 다릅니다. 하이브리드 인증 아키텍처에 대한 자세한 내용은 [하이브리드 인프라에서 복원력 빌드](resilience-in-hybrid.md)를 참조하세요. 

두 번째 단계를 구현하는 경우 두 번째 단계의 종속성은 첫 번째 단계의 종속성에 추가됩니다. 예를 들어 첫 번째 단계가 PTA를 통해 수행되고 두 번째 단계가 SMS인 경우 종속성은 다음과 같습니다.

* Azure AD 인증 서비스

* Azure MFA 서비스

* 온-프레미스 인프라

* 전화 통신 사업자

* 사용자의 디바이스(그림 없음)

 
![인증 방법 및 종속성 이미지](./media/resilience-in-credentials/admin-resilience-credentials.png)

자격 증명 전략은 각 인증 유형의 종속성을 고려해야 하며 일말의 실패까지 방지하는 방법을 프로비저닝해야 합니다. 

인증 방법에는 서로 다른 종속성이 있기 때문에 사용자가 두 번째 단계 옵션에 가능한 한 많이 등록할 수 있도록 하는 것이 좋습니다. 가능하면 서로 다른 종속성을 사용하여 두 번째 단계를 포함해야 합니다. 예를 들어 음성 통화와 SMS가 두 번째 단계로 동일한 종속성을 공유하므로 이 옵션을 선택하면 위험을 완화할 수 없습니다.

가장 복원력이 있는 자격 증명 전략은 암호 없는 인증을 사용하는 것입니다. 비즈니스용 Windows Hello 및 FIDO 2.0 보안 키에는 두 가지 별도의 단계를 사용하는 강력한 인증보다 종속성이 적습니다. Microsoft Authenticator 앱, 비즈니스용 Windows Hello 및 Fido 2.0 보안 키가 가장 안전합니다. 

두 번째 단계의 경우 TOTP(시간 기반 일회용 암호) 또는 OATH 하드웨어 토큰을 사용하는 Microsoft Authenticator 앱 또는 다른 Authenticator 앱에는 최소한의 종속성이 있으므로 복원력이 향상됩니다.

## <a name="how-do-multiple-credentials-help-resilience"></a>여러 자격 증명은 복원력에 어떤 도움이 되나요?

여러 자격 증명 유형을 프로비저닝하면 사용자의 기본 설정 및 환경 제약 조건을 수용할 수 있는 옵션이 제공됩니다. 따라서 사용자에게 다단계 인증에 대한 메시지가 표시되는 대화형 인증은 요청 시 특정 종속성을 사용할 수 없어도 복원 가능합니다. [다단계 인증에 대한 재인증 프롬프트를 최적화](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)할 수 있습니다.

위에서 설명한 개별 사용자 복원력 외에도 회사에서는 잘못된 구성, 자연 재해 또는 온-프레미스 페더레이션 서비스(특히 다단계 인증에 사용되는 경우)에 대한 엔터프라이즈 수준의 리소스 중단으로 이어지는 운영 오류와 같은 대규모 중단 사태에 대비한 계획을 세워야 합니다. 

## <a name="how-do-i-implement-resilient-credentials"></a>복원력 있는 자격 증명을 어떻게 구현하나요?

* 비즈니스용 Windows Hello, 전화 인증 및 FIDO2 보안 키와 같은 [암호 없는 자격 증명](../authentication/howto-authentication-passwordless-deployment.md)을 배포하여 종속성을 줄입니다.

* 두 번째 단계로 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 배포합니다.

* Windows Server Active Directory에서 동기화되는 하이브리드 계정에 [암호 해시 동기화](../hybrid/whatis-phs.md)를 설정합니다. 이 옵션은 AD FS와 같은 페더레이션 서비스와 함께 사용하도록 설정할 수 있으며, 페더레이션 서비스에 오류가 발생하는 경우 대체를 제공합니다.

* [다단계 인증 방법의 사용을 분석](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)하여 사용자 환경을 개선합니다.

* [복원력 있는 액세스 제어 전략 구현](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원력 리소스
 
* [디바이스 상태를 사용하여 복원력 빌드](resilience-with-device-states.md)

* [CAE(연속 액세스 평가)를 사용하여 복원력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증에서 복원력 빌드](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용하여 응용 프로그램 액세스에서 복원력 빌드](resilience-on-premises-access.md)

개발자를 위한 복원력 리소스

* [애플리케이션에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)