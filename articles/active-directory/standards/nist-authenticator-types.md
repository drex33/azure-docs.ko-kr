---
title: NIST 인증자 유형 및 조정된 Azure Active Directory 메서드
description: Azure Active Directory 인증 방법을 NIST 인증자 유형에 맞춰 조정하는 방법에 대한 설명입니다.
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
ms.openlocfilehash: c3ee6cc0c913533b83be1eb389e7d7402875e4dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012419"
---
# <a name="nist-authenticator-types-and-aligned-azure-active-directory-methods"></a>NIST 인증자 유형 및 조정된 Azure Active Directory 메서드

인증 프로세스는 claimant가 구독자와 연결된 하나 이상의 인증자 컨트롤을 어설션하면 시작됩니다. 구독자는 개인 또는 다른 엔터티가 될 수 있습니다.

| NIST(미국 국립표준기술원) 인증자 유형| Azure AD(Azure Active Directory) 인증 방법 |
| - | - |
|  저장된 비밀 <br> (사용자가 알고 있는 것)|  암호(클라우드 계정)  <br>암호(페더레이션)<br> 암호(암호 해시 동기화)<br>암호(통과 인증) |
|조회 비밀 <br> (사용자가 소유하고 있는 것)| 없음 조회 비밀은 시스템에 보관되지 않은 데이터로 정의됩니다. |
|대역 외 <br>(사용자가 소유하고 있는 것)| 휴대폰(SMS) - 권장하지 않음 |
| 단일 단계 일회용 암호 <br>(사용자가 소유하고 있는 것)| Microsoft Authenticator 앱(일회용 암호)  <br>단일 요소 일회용 암호(OTP 제조업체를 통해)<sup data-htmlnode="">1</sup> | 
| 다단계 일회용 암호<br>(사용자가 소유하고 있는 것 + 사용자가 알고 있는 것 또는 사용자의 신원 정보)| 다단계 일회용 암호(OTP 제조업체를 통해)<sup data-htmlnode="">1</sup>| 
|단일 단계 암호화 소프트웨어<br>(사용자가 소유하고 있는 것)|규격 모바일 디바이스 <br> Microsoft Authenticator 앱(알림) <br> 소프트웨어 TPM과 조인된 하이브리드 Azure AD<sup data-htmlnode="">2</sup><br> 소프트웨어 TPM과 조인된 Azure AD<sup data-htmlnode="">2</sup> |
| 단일 요소 암호화 하드웨어 <br>(사용자가 소유하고 있는 것) | 하드웨어 TPM과 조인된 Azure AD<sup data-htmlnode="">2</sup> <br> 하드웨어 TPM과 조인된 하이브리드 Azure AD<sup data-htmlnode="">2</sup>|
|다단계 암호화 소프트웨어<br>(사용자가 소유하고 있는 것 + 사용자가 알고 있는 것 또는 사용자의 신원 정보) | 암호 없는 iOS용 Microsoft Authenticator 앱<br> 소프트웨어 TPM을 사용하는 비즈니스용 Windows Hello |
|다단계 암호화 하드웨어 <br>(사용자가 소유하고 있는 것 + 사용자가 알고 있는 것 또는 사용자의 신원 정보) |암호 없는 Android용 Microsoft Authenticator 앱<br> 하드웨어 TPM을 사용하는 비즈니스용 Windows Hello<br> 스마트 카드(페더레이션된 ID 공급자) <br> FIDO 2 보안 키 |


<sup data-htmlnode="">1</sup> 30초 또는 60초의 OATH-TOTP SHA-1 토큰.

<sup data-htmlnode="">2</sup> 디바이스 조인 상태에 대한 자세한 내용은 [Azure AD 디바이스 ID 설명서](../devices/index.yml)를 참조하세요. 

## <a name="why-sms-isnt-recommended"></a>SMS가 권장되지 않는 이유 

SMS 문자 메시지는 NIST 표준을 충족하지만, NIST에서 권장하지 않습니다. 디바이스 교환, SIM 변경, 번호 포팅 및 기타 동작의 위험으로 인해 문제가 발생할 수 있습니다. 이러한 작업을 악의적으로 수행하면 환경이 안전해지지 않을 수 있습니다. SMS 문자 메시지는 권장되지 않지만, 해커가 더 많은 노력을 기울여야 하기 대문에 암호만 사용하는 것보다는 좋습니다. 

## <a name="next-steps"></a>다음 단계 

[NIST 개요](nist-overview.md)

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md)
