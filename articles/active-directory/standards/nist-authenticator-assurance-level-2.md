---
title: Azure Active Directory를 사용하여 NIST AAL2 달성
description: Azure Active Directory를 사용하여 NIST AAL2(인증자 보증 수준 2)를 달성하는 방법에 대한 지침입니다.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30287957ca9d44c27f0d46efb6ae358db7d6ab44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662270"
---
# <a name="achieve-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Azure Active Directory를 사용하여 NIST 인증자 보증 수준 2 달성

NIST(미국 국립표준기술원)는 ID 솔루션을 구현하는 미국 연방 기관에 대한 기술 요구 사항을 개발합니다. 연방 기관과 협업하는 조직도 이러한 요구 사항을 충족해야 합니다. 

AAL2(인증자 보증 수준 2)를 시도하기 전에 다음 리소스를 확인하는 것이 좋습니다.
* [NIST 개요](nist-overview.md): 다양한 AAL 수준을 이해합니다.
* [인증 기본 사항](nist-authentication-basics.md): 중요한 용어 및 인증 유형입니다.
* [NIST 인증자 유형](nist-authenticator-types.md): 각 인증자 유형을 이해합니다.
* [NIST AAL](nist-about-authenticator-assurance-levels.md): AAL의 구성 요소 및 Azure AD(Azure Active Directory) 인증 방법이 매핑되는 방법을 설명합니다.

## <a name="permitted-authenticator-types"></a>허용되는 인증자 유형

다음 표에서는 AAL2에 허용되는 인증자 형식에 대해 자세히 설명합니다.

| Azure AD 인증 방법| NIST 인증자 유형 | 
| - | - |
| **권장 방법** |   | 
| 암호 없는 iOS용 Microsoft Authenticator 앱<br>소프트웨어 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하는 비즈니스용 Windows Hello | 다단계 암호화 소프트웨어 |
| FIDO 2 보안 키<br>암호 없는 Android용 Microsoft Authenticator 앱<br>하드웨어 TPM을 사용하는 비즈니스용 Windows Hello<br>스마트 카드(Active Directory Federation Services) | 다단계 암호화 하드웨어 |
| **추가 방법** |  |
| 암호 + 휴대폰(SMS) | 저장된 비밀 + 대역 외 |
| 암호 + Microsoft Authenticator 앱(OTP)<br>암호 + SF OTP | 저장된 비밀 + 단일 단계 일회용 암호 |
| 암호 + 소프트웨어 TPM과 조인된 Azure AD <br>암호 + 규격 모바일 디바이스<br>암호 + 소프트웨어 TPM과 조인된 하이브리드 Azure AD <br>암호 + Microsoft Authenticator 앱(알림) | 저장된 비밀 + 단일 단계 암호화 SW |
| 암호 + 하드웨어 TPM과 조인된 Azure AD <br>암호 + 하드웨어 TPM과 조인된 하이브리드 Azure AD | 저장된 비밀 + 단일 단계 암호화 하드웨어 |

> [!NOTE]
> 조건부 액세스 정책에서 디바이스를 규격 또는 하이브리드 Azure AD 조인으로 표시해야 하는 경우 Authenticator는 검증 도구 가장 저항 역할을 합니다.

### <a name="our-recommendations"></a>권장 사항

AAL2를 달성하려면 다단계 암호화 하드웨어 또는 소프트웨어 인증자를 사용합니다. 암호 없는 인증은 가장 큰 공격 표면(암호)을 제거하고 사용자에게 간소화된 인증 방법을 제공합니다. 

암호 없는 인증 방법을 선택하는 방법에 대한 자세한 지침은 [Azure Active Directory에서 암호 없는 인증 배포 계획](../authentication/howto-authentication-passwordless-deployment.md)을 참조하세요.

비즈니스용 Windows Hello를 구현하는 방법에 대한 자세한 내용은 [비즈니스용 Windows Hello 배포 가이드](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)를 참조하세요.

## <a name="fips-140-validation"></a>FIPS 140 검증

다음 섹션에서는 FIPS 140 유효성 검사를 수행하는 방법을 설명합니다.

### <a name="verifier-requirements"></a>검증 도구 요구 사항

Azure AD는 모든 인증 관련 암호화 작업에 대해 Windows FIPS 140 수준 1 전체 유효성 검사 암호화 모듈을 사용합니다. 따라서 이는 정부 기관에서 요구하는 FIPS 140 규정 준수 검증 도구입니다.

### <a name="authenticator-requirements"></a>인증자 요구 사항

정부 기관의 암호화 인증자는 전체적으로 FIPS 140 수준 1의 유효성을 검사해야 합니다. 이는 비정부 기관에 대한 요구 사항이 아닙니다. 다음 Azure AD 인증자는 [FIPS 140 승인 작업 모드의 Windows](/windows/security/threat-protection/fips-140-validation)에서 실행할 때 해당 요구 사항을 충족합니다.

* 암호

* 소프트웨어 또는 하드웨어 TPM과 조인된 Azure AD

* 소프트웨어 또는 하드웨어 TPM과 조인된 하이브리드 Azure AD

* 소프트웨어 또는 하드웨어 TPM을 사용하는 비즈니스용 Windows Hello

* 스마트 카드(Active Directory Federation Services) 

모든 모드(알림, OTP 및 암호 없음)의 Microsoft Authenticator 앱은 FIPS 140 승인 암호화를 사용하지만 FIPS 140 수준 1의 유효성을 검사하지 않습니다.

FIDO2 보안 키 공급자는 유효성 검사를 완료한 일부를 포함하여 다양한 FIPS 인증 단계에 있습니다. [지원되는 FIDO2 주요 공급 업체 목록](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers)을 검토하고 현재 FIPS 유효성 검사 상태에 대해 공급자에게 확인하는 것이 좋습니다.


## <a name="reauthentication"></a>재인증 

AAL2 수준에서 NIST는 사용자 활동에 관계없이 12시간마다 재인증을 요구합니다. 재인증은 30분 이상 지속되는 비활성 기간 후에도 필요합니다. 세션 비밀은 사용자가 보유한 사항이므로 사용자가 알고 있는 정보 또는 사용자의 신원 정보를 나타내야 합니다.

사용자 활동과 관계없는 재인증에 대한 요구 사항을 충족하려면 [사용자 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)를 12시간으로 구성하는 것이 좋습니다. 

NIST는 또한 보상 컨트롤을 사용하여 구독자의 현재 상태를 확인하도록 지원합니다.

* Microsoft System Center Configuration Manager, GPO(그룹 정책 개체) 또는 Intune을 사용하여 운영 체제 수준에서 디바이스를 잠그면 세션 비활성 시간 제한을 30분으로 설정할 수 있습니다. 또한 잠금을 해제하려면 구독자에 대한 로컬 인증을 요구해야 합니다.

* 활동과 관계없이 12시간 후에 머신을 잠그는 예약된 작업(Configuration Manager, GPO 또는 Intune 사용)을 실행하여 활동과 관계없는 시간 제한을 구현할 수 있습니다.

## <a name="man-in-the-middle-resistance"></a>Man-in-the-Middle 저항 

청구인과 Azure AD 간의 모든 통신은 MitM(Man-in-the-Middle) 공격에 대한 저항을 제공하기 위해 인증되고 보호된 채널을 통해 수행됩니다. 이는 AAL1, AAL2 및 AAL3에 대한 MitM 저항 요구 사항을 충족합니다.

## <a name="replay-resistance"></a>재생 저항

AAL2의 모든 Azure AD 인증 방법은 nonce 또는 챌린지를 사용합니다. 이 메서드는 검증 도구가 재생된 인증 트랜잭션을 쉽게 검색하기 때문에 재생 공격에 대한 저항력이 있습니다. 이러한 트랜잭션에는 적절한 nonce 또는 적시성 데이터를 포함되지 않습니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md)
