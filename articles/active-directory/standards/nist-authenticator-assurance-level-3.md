---
title: Azure Active Directory를 사용하여 NIST AAL3 달성
description: 이 문서에서는 Azure Active Directory를 사용하여 NIST AAL3(인증자 보증 수준 3)를 달성하는 방법에 대한 지침을 제공합니다.
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
ms.openlocfilehash: e8821d2c3390ddfd07c7230b20e27b46330e6229
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124816157"
---
# <a name="achieve-nist-authenticator-assurance-level-3-by-using-azure-active-directory"></a>Azure Active Directory를 사용하여 NIST 인증자 보증 수준 3 달성

이 문서에서는 미국 국립표준기술연구소 인증자 보증 수준(NIST AAL) 3을 달성하는 방법을 안내합니다. AAL3를 달성하기 전에 다음 리소스를 검토하는 것이 좋습니다.
* [NIST 개요](nist-overview.md): 다른 AAL 수준을 이해합니다.
* [인증 기본 사항](nist-authentication-basics.md): 중요한 용어 및 인증 유형입니다.
* [NIST 인증자 유형](nist-authenticator-types.md): 각 인증자 유형을 이해합니다.
* [NIST AAL](nist-about-authenticator-assurance-levels.md): AAL의 구성 요소 및 Azure AD(Azure Active Directory) 인증 방법이 매핑되는 방법을 설명합니다.

## <a name="permitted-authenticator-types"></a>허용되는 인증자 유형
Microsoft는 필요한 NIST 인증 유형을 충족할 수 있는 인증 방법을 제공합니다. 이 섹션에서는 Microsoft 권장 사항을 제공합니다.
 

| Azure AD 인증 방법| NIST 인증자 유형 |
| - | -|
| **권장 방법**|    |
| FIDO2 보안 키<br>또는<br> 스마트 카드(AD FS[Active Directory Federation Services])<br>또는<br>하드웨어 TPM을 사용하는 비즈니스용 Windows Hello| 다단계 암호화 하드웨어 |
| **추가 방법**|   |
| 암호<br> 및<br>(하드웨어 TPM과 조인된 하이브리드 Azure AD <br>또는 <br> 하드웨어 TPM과 조인된 Azure AD)| 저장된 비밀<br>및<br> 단일 단계 암호화 하드웨어 |
| 암호 <br>및<br>단일 단계 일회용 암호 하드웨어(OTP 제조업체에서 제공) <br>및<br>(소프트웨어 TPM과 조인된 하이브리드 Azure AD <br>또는 <br> 소프트웨어 TPM과 조인된 Azure AD <br>또는<br> 호환되는 관리 디바이스)| 저장된 비밀 <br>및<br>단일 단계 일회용 암호 하드웨어<br> 및<br>단일 단계 암호화 소프트웨어 |

### <a name="our-recommendations"></a>권장 사항 

다단계 암호화 하드웨어 인증을 사용하여 AAL3을 달성하는 것이 좋습니다. 암호 없는 인증은 가장 큰 공격 표면(암호)을 제거하고 사용자에게 간소화된 인증 방법을 제공합니다. 완전히 클라우드에 기반을 둔 조직에서는 FIDO2 보안 키를 사용하는 것이 좋습니다.

비즈니스용 Windows Hello는 필수 FIPS 140 보안 수준에서 유효성이 검사되지 않았으므로 이를 AAL3으로 수락하기 전에 위험 평가 및 평가를 수행해야 합니다. .

자세한 참고 자료는 [Azure Active Directory에서 암호 없는 인증 배포 계획](../authentication/howto-authentication-passwordless-deployment.md)을 참조하세요.

비즈니스용 Windows Hello 구현에 대한 자세한 내용은 [비즈니스용 Windows hello 배포 가이드](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)를 참조하세요.

## <a name="fips-140-validation"></a>FIPS 140 유효성 검사

### <a name="verifier-requirements"></a>검증 도구 요구 사항

Azure AD는 모든 인증 관련 암호화 작업에 Windows FIPS 140 수준 1의 전체   
‎검증 암호화 모듈을 사용합니다. 따라서 FIPS-140 규격 검증 도구입니다.

### <a name="authenticator-requirements"></a>인증자 요구 사항

단일 단계 및 다단계 암호화 하드웨어 인증자의 인증자 요구 사항은 서로 다릅니다. 

**단일 단계 암호화 하드웨어** 인증자는 다음과 같아야 합니다.

* FIPS 140 Level 1 전체(또는 그 이상)

* FIPS 140 Level 3 물리적 보안(또는 그 이상)

Azure AD Joined 및 Hybrid Azure AD Joined 디바이스는 다음과 같은 경우에 이 요구 사항을 충족합니다. 

* [FIPS 140 승인 작업 모드에서 Windows](/windows/security/threat-protection/fips-140-validation)를 실행하는 경우 

* FIPS 140 Level 1 전체(또는 그 이상)와 FIPS 140 Level 3 물리적 보안에 해당하는 TPM을 지원하는 컴퓨터를 사용하는 경우 

   * [암호화 모듈 유효성 검사 프로그램](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search) 페이지에서 "신뢰할 수 있는 플랫폼 모듈" 및 "TPM"을 검색하여 호환되는 TPM을 찾습니다.

FIPS 140 준수에 대한 자세한 내용은 모바일 디바이스 공급업체에 문의하세요.

**다단계 암호화 하드웨어** 인증자는 다음과 같아야 합니다. 

* FIPS 140 Level 2 전체(또는 그 이상)

* FIPS 140 Level 3 물리적 보안(또는 그 이상)

FIDO2 보안 키, 스마트 카드 및 비즈니스용 Windows Hello는 이러한 요구 사항을 충족하는 데 도움이 될 수 있습니다.

* FIDO2 키 공급자는 유효성 검사를 완료한 일부를 포함하여 다양한 FIPS 인증 단계에 있습니다. [지원되는 FIDO2 주요 공급업체 목록](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers)을 검토하고 현재 FIPS 유효성 검사 상태를 공급자에게 확인하는 것이 좋습니다.

* 스마트 카드는 입증된 기술입니다. 여러 공급업체 제품이 FIPS 요구 사항을 충족합니다.

   * [암호화 모듈 유효성 검사 프로그램](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search) 페이지에서 자세히 알아보세요.

**비즈니스용 Windows Hello**

FIPS 140은 소프트웨어, 펌웨어 및 하드웨어를 비롯한 전체 암호화 경계가 평가 범위 내에 있을 것을 요구합니다. Windows 운영 체제는 수천 개의 하드웨어 조합과 페어링할 수 있는 개방형 컴퓨팅 플랫폼입니다. Microsoft는 각 조합에 대해 FIPS 인증을 유지할 수 없습니다. 비즈니스용 Windows Hello를 AAL3 인증자로 사용하기 위한 위험 평가의 일환으로 다음과 같은 구성 요소에 대한 개별 인증을 평가해야 합니다.

* **Windows 10 및 Windows Server** 는 NIAP(National Information Assurance Partnership)의 [미국 정부 승인을 받은 Protection Profile for General Purpose Operating Systems Version 4.2.1](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442)을 사용합니다. NIAP는 자국의 프로그램을 감독하여 COTS(Commercial Off-The-Shelf) IT(정보 기술) 제품이 국제 Common Criteria를 준수하는지 평가합니다. 

* **Windows 암호화 라이브러리**[는 NIST CMVP(Cryptographic Module Validation Program)에서 FIPS Level 1 전체를 달성했습니다](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544). NIST와 캐나다 사이버 보안 센터의 합작품인 CMVP는 FIPS 표준에 따라 암호화 모듈의 유효성을 검사합니다. 

* FIPS 140 Level 2 전체 및 FIPS 140 Level 3 물리적 보안에 해당하는 **TPM(신뢰할 수 있는 플랫폼 모듈)** 을 선택합니다. 조직에서는 사용 중인 하드웨어 TPM이 달성하려는 AAL 수준의 요구 사항을 충족하는지 확인해야 합니다.   
현재 표준을 충족하는 TPM을 확인하려면 [NIST 컴퓨터 보안 리소스 센터 암호화 모듈 유효성 검사 프로그램](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search) 페이지로 이동합니다. **모듈 이름** 상자에 **신뢰할 수 있는 플랫폼 모듈** 을 입력합니다. 결과 목록에 현재 표준에 맞는 하드웨어 TPM이 표시됩니다.

## <a name="reauthentication"></a>재인증 

AAL3 수준에서 NIST는 사용자 활동에 관계없이 12시간마다 재인증을 요구합니다. 재인증은 15분 이상 지속되는 비활성 기간 후에도 필요합니다. 두 요소를 모두 제공해야 합니다.

사용자 활동과 관계없는 재인증에 대한 요구 사항을 충족하려면 [사용자 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)를 12시간으로 구성하는 것이 좋습니다. 

NIST는 또한 보상 컨트롤을 사용하여 구독자의 현재 상태를 확인하도록 지원합니다.

* OS 수준에서 디바이스를 잠그면 세션 비활성 시간 제한인 15분을 설정할 수 있습니다. Microsoft Endpoint Configuration Manager, GPO(그룹 정책 개체) 또는 Intune을 사용하여 이 작업을 수행할 수 있습니다. 또한 잠금을 해제하려면 구독자에 대한 로컬 인증을 요구해야 합니다.

* 활동과 관계없이 12시간 후에 머신을 잠그는 예약된 작업(Configuration Manager, GPO 또는 Intune 사용)을 실행하여 활동과 관계없는 시간 제한을 구현할 수 있습니다.

## <a name="man-in-the-middle-resistance"></a>Man-in-the-Middle 저항 

청구인과 Azure AD 간의 모든 통신은 MitM(Man-in-the-Middle) 공격에 대한 저항을 제공하기 위해 인증되고 보호된 채널을 통해 수행됩니다. 이 구성은 AAL1, AAL2, AAL3에 대한 MitM 저항 요구 사항을 충족합니다.

## <a name="verifier-impersonation-resistance"></a>검증 도구 가장 저항

AAL3을 충족하는 모든 Azure AD 인증 방법은 인증할 특정 세션에 인증자 출력을 바인딩하는 암호화 인증자를 사용합니다. 이를 위해 검증 도구에 퍼블릭 키가 알려진 청구인이 제어하는 프라이빗 키를 사용합니다. 이 구성은 AAL3에 대한 검증 도구 가장 저항 요구 사항을 충족합니다.

## <a name="verifier-compromise-resistance"></a>검증 도구 손상 저항

AAL3을 충족하는 모든 Azure AD 인증 방법은 다음 중 하나를 수행합니다.
- 검증 도구에 인증자가 보유하고 있는 프라이빗 키에 해당하는 퍼블릭 키를 저장하도록 요구하는 암호화 인증자를 사용합니다. 
- FIPS-140으로 검증된 해시 알고리즘을 사용하여 필요한 인증자 출력을 저장합니다. 

자세한 내용은 [Azure AD 데이터 보안 고려 사항](https://aka.ms/AADDataWhitepaper)을 참조하세요.

## <a name="replay-resistance"></a>재생 저항

AAL3을 충족하는 모든 Azure AD 인증 방법은 nonce 또는 챌린지를 사용합니다. 이러한 방법은 검증 도구가 재생된 인증 트랜잭션을 쉽게 검색하기 때문에 재생 공격에 대한 저항력이 있습니다. 이러한 트랜잭션에는 적절한 nonce 또는 적시성 데이터를 포함되지 않습니다.

## <a name="authentication-intent"></a>인증 의도

인증 의도의 목표는 직접 연결된 실제 인증자(예: 다단계 암호화 디바이스)가 주체 몰래 사용되기 어렵게 만드는 것입니다. 예를 들어 엔드포인트의 맬웨어 등에서 사용할 수 없게 합니다.

NIST는 맬웨어 위험을 완화할 수 있는 보상 컨트롤 사용을 허용합니다. Windows Defender System Guard 및 Windows Defender ATP를 실행하는 모든 Intune 호환 디바이스는 이 완화 요구 사항을 충족합니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)
