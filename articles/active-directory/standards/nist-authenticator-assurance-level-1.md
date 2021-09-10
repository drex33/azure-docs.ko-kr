---
title: Azure Active Directory를 사용하여 NIST AAL1 달성
description: Azure Active Directory를 사용하여 NIST AAL1(인증자 보증 수준 1)을 달성하는 방법에 대한 지침입니다.
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
ms.openlocfilehash: d38dfd79eefe8b7b1bfb8119a62b139b654f2fe5
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540474"
---
# <a name="achieve-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Azure Active Directory를 사용하여 NIST 인증자 보증 수준 1 달성

NIST(미국 국립표준기술원)는 ID 솔루션을 구현하는 미국 연방 기관에 대한 기술 요구 사항을 개발합니다. 연방 기관과 협업하는 조직도 이러한 요구 사항을 충족해야 합니다. 

AAL1(인증자 보증 수준 1)을 시도하기 전에 다음 리소스를 확인하는 것이 좋습니다.
* [NIST 개요](nist-overview.md): 다양한 AAL 수준을 이해합니다.
* [인증 기본 사항](nist-authentication-basics.md): 중요한 용어 및 인증 유형입니다.
* [NIST 인증자 유형](nist-authenticator-types.md): 각 인증자 유형을 이해합니다.
* [NIST AAL](nist-about-authenticator-assurance-levels.md) - AAL의 구성 요소, Azure AD(Azure Active Directory) 인증 방법이 해당 항목을 매핑하는 방법 및 TPM(신뢰할 수 있는 플랫폼 모듈)을에 대한 이해를 다룹니다. 

## <a name="permitted-authenticator-types"></a>허용되는 인증자 유형

 AAL1을 얻기 위해 NIST 단일 단계 또는 다단계 [허용 인증자](nist-authenticator-types.md)를 사용할 수 있습니다. 다음 표에는 [AAL2](nist-authenticator-assurance-level-2.md) 및 [AAL3](nist-authenticator-assurance-level-2.md)에서 다루지 않는 내용이 나와 있습니다.

| Azure AD 인증 방법| NIST 인증자 유형 |
| - | - |
| 암호 |저장된 비밀 |
| 휴대폰(SMS)|  대역 외 |
|  FIDO 2 보안 키 <br>암호 없는 iOS용 Microsoft Authenticator 앱<br>소프트웨어 TPM을 사용하는 비즈니스용 Windows Hello <br>스마트 카드(Active Directory Federation Services) |  다단계 암호화 소프트웨어 |

> [!TIP]
> AAL2 이상을 충족하는 것이 좋습니다. 비즈니스 이유, 산업 표준 또는 규정 준수 요구 사항을 위해 필요한 경우 AAL3을 충족합니다.

## <a name="fips-140-validation"></a>FIPS 140 유효성 검사

### <a name="verifier-requirements"></a>검증 도구 요구 사항

Azure AD는 모든 인증 관련 암호화 작업에 대해 Windows FIPS 140 수준 1 전체 유효성 검사 암호화 모듈을 사용합니다. 따라서 이는 정부 기관에서 요구하는 FIPS 140 규정 준수 검증 도구입니다.

## <a name="man-in-the-middle-resistance"></a>Man-in-the-Middle 저항 

청구인과 Azure AD 간의 모든 통신은 MitM(Man-in-the-Middle) 공격에 대한 저항을 제공하기 위해 인증되고 보호된 채널을 통해 수행됩니다. 이는 AAL1, AAL2 및 AAL3에 대한 MitM 저항 요구 사항을 충족합니다.

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md) 