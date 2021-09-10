---
title: Azure Active Directory를 사용하여 NIST 인증자 보증 수준 달성
description: 개요
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: ''
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500b22546089335563fd12953414aa74612cedf3
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537073"
---
# <a name="configure-azure-active-directory-to-meet-nist-authenticator-assurance-levels"></a>NIST 인증자 보증 수준을 충족하기 위한 Azure Active Directory 구성

연방 기관에 서비스를 제공하는 것은 충족해야 하는 표준의 수와 복잡성으로 인해 복잡합니다. CSP(클라우드 서비스 공급자) 또는 연방 기관은 모든 관련 표준을 준수할 책임이 있습니다. Azure 및 Azure Active Directory(Azure AD)를 사용하면 인증을 활용한 다음 특정 요구 사항을 구성할 수 있으므로 이를 더 쉽게 수행할 수 있습니다.

Azure는 본 문서 작성 당시 90개 이상의 규정 준수 제품에 대해 인증을 받았습니다. 자세한 내용은 [여러분의 클라우드를 신뢰하세요](https://azure.microsoft.com/overview/trusted-cloud/)를 참조하세요.

## <a name="why-meet-nist-standards"></a>NIST 표준을 충족하는 이유는 무엇인가요? 

NIST(미국 국립표준기술원)는 ID 솔루션을 구현하는 미국 연방 기관에 대한 기술 요구 사항을 개발합니다. 연방 기관과 협력하는 조직도 이러한 요구 사항을 충족해야 합니다. NIST ID 요구 사항에 대한 자세한 사항은 [특별 출판물 800-63 개정 3](https://pages.nist.gov/800-63-3/sp800-63-3.html)(NIST SP 800-63-3)를 참조하세요.

NIST SP 800-63은 다음에서도 참조됩니다.
* 규제 물질 전자 처방 [EPCS](https://deadiversion.usdoj.gov/ecomm/e_rx/) 프로그램. 
* [FINRA(금융 산업 규제 기관) 요구 사항](https://www.finra.org/rules-guidance). 
* 의료, 국방 및 기타 산업 협회는 종종 NIST SP 800-63-3을 ID 및 액세스 관리 요구 사항의 기준으로 사용합니다.

NIST 지침은 다른 표준, 특히 CSP에 대한 FedRAMP(Federal Risk and Authorization Management Program)에서 참조됩니다. Azure는 FedRAMP High Impact 인증을 받았습니다. 

NIST 디지털 ID 지침은 직원, 파트너, 공급자, 고객 또는 시민과 같은 사용자의 증명 및 인증을 다룹니다. 

NIST SP 800-63-3 디지털 ID 지침에는 다음 세 가지 영역이 포함됩니다.

* [SP 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html)는 등록 및 ID 증명을 다룹니다.

* [SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)는 인증 및 수명 주기 관리를 다룹니다.

* [SP 800-63C](https://pages.nist.gov/800-63-3/sp800-63c.html)는 페더레이션 및 어설션을 다룹니다.

각 영역에는 보증 수준이 매핑되었습니다. 이 문서 집합은 Azure AD 및 기타 Microsoft 솔루션을 사용하여 NIST SP 800-63B에서 AAL(인증자 보증 수준)을 달성하기 위한 지침을 제공합니다.

## <a name="next-steps"></a>다음 단계 

[AAL에 대한 자세한 정보](nist-about-authenticator-assurance-levels.md)

[인증 기본 사항](nist-authentication-basics.md)

[NIST 인증자 유형](nist-authenticator-types.md)

[Azure AD를 사용하여 NIST AAL1 달성](nist-authenticator-assurance-level-1.md)

[Azure AD를 사용하여 NIST AAL2 달성](nist-authenticator-assurance-level-2.md)

[Azure AD를 사용하여 NIST AAL3 달성](nist-authenticator-assurance-level-3.md) 
