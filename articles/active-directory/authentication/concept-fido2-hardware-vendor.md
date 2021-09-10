---
title: Azure AD에 로그인하기 위한 Microsoft 호환 FIDO2 보안 키 공급업체 되기
description: FIDO2 하드웨어 파트너가 되는 프로세스를 설명합니다.
ms.date: 08/02/2021
services: active-directory
ms.service: active-directory
ms.subservice: authentication
author: knicholasa
ms.author: nichola
ms.topic: conceptual
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d21b99fd1def56b60896d157c8a35b9381a003
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566901"
---
# <a name="become-a-microsoft-compatible-fido2-security-key-vendor"></a>Microsoft 호환 FIDO2 보안 키 공급업체 되기

대부분의 해킹 관련 위반은 도난되거나 약한 암호를 사용합니다. IT는 보안 인시던트 위험을 줄이기 위해 더 강력한 암호 복잡성 또는 빈번한 암호 변경을 적용하는 경우가 많습니다. 그러나 이렇게 하면 사용자가 복잡한 새 암호를 기억하거나 저장해야 해서 사용자 환경이 열악해지고 지원 센터 비용이 증가합니다.

FIDO2 보안 키가 대안을 제시합니다. FIDO2 보안 키는 약한 자격 증명을 서비스 간에 재사용, 재생 또는 공유할 수 없는 강력한 하드웨어 기반 공개/프라이빗 키 자격 증명으로 대체할 수 있습니다. 보안 키는 공유 디바이스 시나리오를 지원하므로 사용자가 자격 증명을 휴대하고 조직에 속하는 Azure Active Directory 조인 Windows 10 디바이스에 안전하게 인증할 수 있습니다.

Microsoft는 보안 디바이스가 Windows, Microsoft Edge 브라우저 및 온라인 Microsoft 계정에서 작동하도록 보장하고, 강력한 암호 없는 인증을 구현하기 위해 FIDO2 보안 키 공급업체와 협력하고 있습니다.

다음 프로세스를 통해 Microsoft 호환 FIDO2 보안 키 공급업체가 될 수 있습니다.  Microsoft는 파트너와 함께 시장 진출 활동을 수행할 것을 약속하지 않으며 고객 요구에 따라 파트너 우선 순위를 평가합니다.

1. 먼저 인증자는 FIDO2 인증을 받아야 합니다. FIDO2 인증을 받지 않은 공급자와는 협력할 수 없습니다. 인증에 대해 자세히 알아보려면 [https://fidoalliance.org/certification/](https://fidoalliance.org/certification/)이라는 웹 사이트를 방문하세요.
2. FIDO2 인증을 받은 후에는 [https://forms.office.com/r/NfmQpuS9hF](https://forms.office.com/r/NfmQpuS9hF)에 있는 양식으로 신청서를 작성하세요. 엔지니어링 팀은 FIDO2 디바이스의 호환성만 테스트합니다. 솔루션의 보안은 테스트하지 않습니다.
3. 테스트 단계 진행이 확정되면 일반적으로 약 3~6개월 동안 프로세스가 진행됩니다. 이 단계에서 일반적으로 수행하는 작업은 다음과 같습니다.
    - Microsoft와 팀 간의 최초 논의.
        - FIDO Alliance 인증 또는 인증 경로 확인(완료되지 않은 경우)
        - 공급업체로부터 디바이스에 대한 개요 받기
    - Microsoft는 테스트 스크립트를 사용자와 공유합니다. 엔지니어링 팀은 사용자에게 특정 요구 사항이 있는 경우 질문에 답변할 수 있습니다.
    - 사용자가 전달받은 모든 결과를 완성해서 Microsoft 엔지니어링 팀에게 전송합니다.
    - 사용자가 Microsoft로부터 확인을 받은 후 각 디바이스의 여러 하드웨어/솔루션 샘플을 Microsoft 엔지니어링 팀에게 전송합니다.
        - 이를 받은 Microsoft 엔지니어링 팀은 테스트 스크립트 확인 및 사용자 환경 흐름을 진행합니다.
4. Microsoft 엔지니어링 팀의 모든 테스트를 성공적으로 통과하면 Microsoft에서 공급업체의 디바이스가 [FIDO MDS](https://fidoalliance.org/metadata/)에 등록되었음을 확인해 줍니다.
5. Microsoft에서 Azure AD 백 엔드 및 승인된 FIDO2 공급업체 목록에 FIDO2 보안 키를 추가합니다.

## <a name="current-partners"></a>현재 파트너

다음 표에는 Microsoft 호환 FIDO2 보안 키 공급업체의 파트너가 나와 있습니다.

| **공급자** | **링크** |
| --- | --- |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Go-Trust ID | [https://www.gotrustid.com/](https://www.gotrustid.com/idem-key) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Kensington  | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Nymi   | [https://www.nymi.com/product](https://www.nymi.com/product) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Thales | [https://cpl.thalesgroup.com/access-management/authenticators/fido-devices](https://cpl.thalesgroup.com/access-management/authenticators/fido-devices) |
| Thetis | [https://thetis.io/collections/fido2](https://thetis.io/collections/fido2) |
| Token2 Switzerland | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| TrustKey Solutions | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net/) |
| Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |

## <a name="next-steps"></a>다음 단계

[FIDO2 호환성](fido2-compatibility.md)

