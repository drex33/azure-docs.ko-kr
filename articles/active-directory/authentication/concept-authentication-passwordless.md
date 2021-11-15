---
title: Azure Active Directory 암호 없는 로그인
description: FIDO2 보안 키 또는 Microsoft Authenticator 앱을 사용하여 Azure Active Directory에 암호 없이 로그인하는 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9e12d8d112bd9c3d9ea44b29803bc475baa9e4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500263"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory의 암호 없는 인증 옵션

MFA(다단계 인증) 같은 기능은 조직을 보호하는 좋은 방법이지만, 암호를 기억해야 하는 데다가 추가 보안 계층까지 있어 사용자가 답답함을 느끼게 됩니다. 암호 없는 인증 방법 사용 시 암호가 제거되고 사용자에게 있는 인증 수단으로 대체되기 때문에 더 편리합니다.

| 인증  | 사용자가 소유하고 있는 것 | 사용자 또는 사용자가 알고 있는 것 |
| --- | --- | --- |
| 암호 없음 | Windows 10 디바이스, 휴대폰 또는 보안 키 | 생체 인식 또는 PIN |

조직마다 인증 요구 수단은 다릅니다. Microsoft 글로벌 Azure 및 Azure Government는 Azure Active Directory(Azure AD)와 통합되는 세 가지 암호 없는 인증 옵션을 다음과 같이 제공합니다.

- 비즈니스용 Windows Hello
- Microsoft Authenticator 앱
- FIDO2 보안 키

![인증: 보안 대 편의성](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

비즈니스용 Windows Hello는 지정된 고유 Windows PC가 있는 정보 근로자에게 적합합니다. 생체 인식 및 PIN 자격 증명은 사용자 PC에 직접 연결되기 때문에 소유자 이외의 사용자가 액세스하지 못합니다. 비즈니스용 Windows Hello는 PKI(공개 키 인프라) 통합 및 기본 제공 SSO(Single Sign-On)를 사용하여 온-프레미스 및 클라우드에서 회사 리소스에 원활하게 액세스할 수 있는 편리한 방법을 제공합니다.

![비즈니스용 Windows Hello를 사용한 사용자 로그인 예](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

다음 단계는 Azure AD에서 로그인 프로세스의 작동 방식을 보여줍니다.

![비즈니스용 Windows Hello에서 사용자 로그인 단계를 간략하게 설명하는 다이어그램](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 사용자가 생체 인식 또는 PIN 제스처를 사용하여 Windows에 로그인합니다. 이 제스처는 비즈니스용 Windows Hello 프라이빗 키의 잠금을 해제하며, 클라우드 AP 공급자라고 하는 클라우드 인증 보안 지원 공급자에게 전송됩니다.
1. 클라우드 AP 공급자는 Azure AD에 nonce(한 번만 사용할 수 있는 무작위 임의 숫자)를 요청합니다.
1. Azure AD는 5분 동안 유효한 nonce를 반환합니다.
1. 클라우드 AP 공급자는 사용자의 프라이빗 키를 사용하여 nonce에 서명하고, 서명한 nonce를 Azure AD에 반환합니다.
1. Azure AD는 안전하게 등록된 사용자의 공개 키를 사용하여 서명한 nonce를 nonce 서명과 비교하여 유효성을 검사합니다. 서명의 유효성을 검사한 후 Azure AD는 반환된 서명된 nonce의 유효성을 검사합니다. Nonce의 유효성이 확인되면 Azure AD는 디바이스의 전송 키로 암호화된 세션 키를 사용하여 PRT(기본 새로 고침 토큰)를 만들어 클라우드 AP 공급자에게 반환합니다.
1. 클라우드 AP 공급자는 세션 키를 사용하여 암호화된 PRT를 수신합니다. 클라우드 AP 공급자는 디바이스의 개인 전송 키를 사용하여 세션 키의 암호를 해독하고 디바이스의 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 세션 키를 보호합니다.
1. 클라우드 AP 공급자는 Windows로 인증 성공 응답을 반환합니다. 이후에 사용자는 다시 인증하지 않아도(SSO) Windows는 물론 클라우드와 온-프레미스 애플리케이션에도 액세스할 수 있게 됩니다.

비즈니스용 Windows Hello [계획 가이드](/windows/security/identity-protection/hello-for-business/hello-planning-guide)를 사용하여 비즈니스용 Windows Hello 배포 유형과 고려해야 할 옵션을 쉽게 결정할 수 있습니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

직원의 휴대폰을 암호 없는 인증 방법으로 사용하도록 허용할 수도 있습니다. 암호 외에도 편리한 다단계 인증 옵션으로 Microsoft Authenticator 앱을 이미 사용하고 있을 수도 있습니다. Authenticator 앱을 암호 없는 옵션으로 사용할 수도 있습니다.

![Microsoft Authenticator 앱을 사용하여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator 앱으로 iOS 또는 Android 휴대폰이 강력한 암호 없는 자격 증명으로 바뀝니다. 사용자는 휴대폰으로 알림을 받고, 화면에 표시되는 숫자를 휴대폰에 표시되는 숫자와 일치시킨 다음, 생체 인식 데이터(지문 또는 얼굴 인식) 또는 PIN을 사용하여 확인함으로써 플랫폼 또는 브라우저에 로그인할 수 있습니다. 설치에 대한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a)를 참조하세요.

Authenticator 앱을 사용하는 암호 없는 인증은 비즈니스용 Windows Hello와 동일한 기본 패턴을 따릅니다. Azure AD가 사용되는 Microsoft Authenticator 앱 버전을 찾을 수 있도록 사용자를 식별해야 하기 때문에 좀 더 복잡합니다.

![Microsoft Authenticator 앱을 사용하는 사용자 로그인 단계를 간략하게 설명하는 다이어그램](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 사용자가 자신의 사용자 이름을 입력합니다.
1. Azure AD는 사용자에게 강력한 자격 증명이 있음을 감지하고 강력한 자격 증명 흐름을 시작합니다.
1. 알림은 iOS 디바이스에서 APNS(Apple Push Notification Service)를 통해 또는 Android 디바이스의 FCM(Firebase Cloud Messaging)을 통해 앱으로 전송됩니다.
1. 사용자가 푸시 알림을 수신하고 앱을 엽니다.
1. 이 앱은 Azure AD를 호출하고 상태 증명 질문 및 nonce를 수신합니다.
1. 사용자는 생체 인식 또는 PIN을 입력하여 문제를 풀고 프라이빗 키의 잠금을 해제합니다.
1. Nonce는 프라이빗 키로 서명된 후 Azure AD로 다시 전송됩니다.
1. Azure AD는 공개/프라이빗 키 유효성 검사를 실시하고 토큰을 반환합니다.

암호 없는 로그인을 시작하려면 다음 방법을 완료합니다.

> [!div class="nextstepaction"]
> [Authenticator 앱을 사용하여 암호 없는 서명 사용](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO(Fast IDentity Online) Alliance는 공개 인증 표준을 알리고 암호를 인증 형태로 사용하는 것을 줄이도록 지원합니다. FIDO2는 WebAuthn(웹 인증) 표준을 통합하는 최신 표준입니다.

FIDO2 보안 키는 모든 폼 팩터로 제공할 수 있으며, 피싱이 불가능한 표준 기반의 암호 없는 인증 방법입니다. FIDO(Fast Identity Online)는 암호 없는 인증을 위한 공개 표준입니다. FIDO를 사용하여 사용자와 조직은 사용자 이름 또는 암호 없이 외부 보안 키 또는 디바이스의 기본 제공 플랫폼 키로 해당 리소스에 로그인할 수 있습니다.

사용자는 로그인 인터페이스에서 FIDO2 보안 키를 등록한 다음에 인증의 기본 수단으로 선택할 수 있습니다. 이러한 FIDO2 보안 키는 일반적으로 USB 디바이스이지만 Bluetooth 또는 NFC를 사용할 수도 있습니다. 인증을 처리하는 하드웨어 디바이스를 사용하면 노출되거나 추측될 수 있는 암호가 없으므로 계정의 보안이 강화됩니다.

FIDO2 보안 키를 사용하여 Azure AD 또는 하이브리드 Azure AD에 가입된 Windows 10 디바이스에 로그인하고 클라우드 및 온-프레미스 리소스에 SSO(single sign-on)을 사용할 수 있습니다. 사용자는 지원되는 브라우저에도 로그인할 수 있습니다. FIDO2 보안 키는 보안이 매우 중요하거나, 휴대폰을 두 번째 인증 단계로 사용하지 않거나 사용할 수 없는 시나리오 또는 직원이 있는 기업의 경우 유용한 옵션입니다.

[개발하는 애플리케이션에서 FIDO2 인증을 지원](../develop/support-fido2-authentication.md)하려는 개발자를 위한 모범 사례 뿐만 아니라, [Azure AD로 FIDO2 인증을 지원하는 브라우저](fido2-compatibility.md)에 대한 참조 문서가 준비되어 있습니다.

![보안 키를 사용하여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

사용자가 FIDO2 보안 키를 사용하여 로그인하는 경우 다음 프로세스가 사용됩니다.

![FIDO2 보안 키를 사용하는 사용자 로그인 단계를 간략하게 설명하는 다이어그램](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 사용자가 FIDO2 보안 키를 해당 컴퓨터에 연결합니다.
2. Windows에서 FIDO2 보안 키를 감지합니다.
3. Windows에서 인증 요청을 보냅니다.
4. Azure AD가 nonce를 다시 보냅니다.
5. 사용자가 FIDO2 보안 키의 보안 enclave에 저장된 프라이빗 키의 잠금을 해제하는 제스처를 완료합니다.
6. FIDO2 보안 키에서 프라이빗 키를 사용하여 nonce에 서명합니다.
7. 서명된 nonce를 포함하는 PRT(기본 새로 고침 토큰) 토큰 요청이 Azure AD로 전송됩니다.
8. Azure AD는 FIDO2 공개 키를 사용하여 서명된 nonce를 확인합니다.
9. Azure AD는 온-프레미스 리소스에 액세스할 수 있도록 PRT를 반환합니다.

### <a name="fido2-security-key-providers"></a>FIDO2 보안 키 공급자

다음 공급자는 암호 없는 환경과 호환되는 것으로 확인된 다양한 폼 팩터의 FIDO2 보안 키를 제공합니다. FIDO Alliance뿐만 아니라 공급업체에 문의하여 이러한 키의 보안 속성을 평가하는 것이 좋습니다.

| 공급자                  |     생체 인식     | USB | NFC | BLE | FIPS 인증 | 연락처                                                                                             |
|---------------------------|:-----------------:|:---:|:---:|:---:|:--------------:|-----------------------------------------------------------------------------------------------------|
| AuthenTrend               | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://authentrend.com/about-us/#pg-35-3                                                           |
| Ensurity                  | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://www.ensurity.com/contact                                                                    |
| Excelsecu                 | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://www.excelsecu.com/productdetail/esecufido2secu.html                                         |
| Feitian                   | ![y]              | ![y]| ![y]| ![y]| ![y]           | https://shop.ftsafe.us/pages/microsoft                                                              |
| Fortinet                  | ![n]              | ![y]| ![n]| ![n]| ![n]           | https://www.fortinet.com/                                                                           |
| GoTrustID Inc.            | ![n]              | ![y]| ![y]| ![y]| ![n]           | https://www.gotrustid.com/idem-key                                                                  |
| HID                       | ![n]              | ![y]| ![y]| ![n]| ![n]           | https://www.hidglobal.com/contact-us                                                                |
| Hypersecu                 | ![n]              | ![y]| ![n]| ![n]| ![n]           | https://www.hypersecu.com/hyperfido                                                                 |
| IDmelon Technologies Inc. | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://www.idmelon.com/#idmelon                                                                    |
| Kensington                | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://www.kensington.com/solutions/product-category/why-biometrics/                               |
| KONA I                    | ![y]              | ![n]| ![y]| ![y]| ![n]           | https://konai.com/business/security/fido                                                            |
| NEOWAVE                   | ![n]              | ![y]| ![y]| ![n]| ![n]           | https://neowave.fr/en/products/fido-range/                                                          |
| Nymi                      | ![y]              | ![n]| ![y]| ![n]| ![n]           | https://www.nymi.com/nymi-band                                                                      | 
| Octatco                   | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://octatco.com/                                                                                |
| OneSpan Inc.              | ![n]              | ![y]| ![n]| ![y]| ![n]           | https://www.onespan.com/products/fido                                                               |
| Thales 그룹              | ![n]              | ![y]| ![y]| ![n]| ![n]           | https://cpl.thalesgroup.com/access-management/authenticators/fido-devices                           |
| Thetis                    | ![y]              | ![y]| ![y]| ![y]| ![n]           | https://thetis.io/collections/fido2                                                                 |
| Token2 Switzerland        | ![y]              | ![y]| ![y]| ![n]| ![n]           | https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key               |
| TrustKey Solutions        | ![y]              | ![y]| ![n]| ![n]| ![n]           | https://www.trustkeysolutions.com/security-keys/                                                    |
| VinCSS                    | ![n]              | ![y]| ![n]| ![n]| ![n]           | https://passwordless.vincss.net                                                                     |
| Yubico                    | ![y]              | ![y]| ![y]| ![n]| ![y]           | https://www.yubico.com/solutions/passwordless/                                                      |


<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png

> [!NOTE]
> NVC 기반 보안 키를 구매하여 사용할 경우에는 보안 키에 지원되는 NFC 판독기 필요합니다. NFC 판독기는 Azure 요구 사항이나 제한 사항이 아닙니다. 지원되는 NFC 리더 목록은 NFC 기반 보안 키 공급업체에 문의하세요.

공급업체이고 지원되는 디바이스 목록에 디바이스를 추가하려는 경우 [Microsoft 호환 FIDO2 보안 키 공급업체가 되는 방법](concept-fido2-hardware-vendor.md)에 대한 안내를 확인하세요.

FIDO2 보안 키를 시작하려면 다음 방법을 완료하세요.

> [!div class="nextstepaction"]
> [FIDO2 보안 키를 사용하여 암호 없는 서명 사용](howto-authentication-passwordless-security-key.md)

## <a name="supported-scenarios"></a>지원되는 시나리오

고려 사항은 다음과 같습니다.

- 관리자는 테넌트에 암호 없는 인증 방법을 사용하도록 설정할 수 있습니다.

- 관리자는 모든 사용자를 대상으로 지정하거나 테넌트 내에서 각 방법의 사용자/그룹을 선택할 수 있습니다.

- 사용자는 자신의 계정 포털에서 이러한 암호 없는 인증 방법을 등록하고 관리할 수 있습니다.

- 사용자는 다음과 같은 암호 없는 인증 방법으로 로그인할 수 있습니다.
   - Microsoft Authenticator 앱: 모든 브라우저에서, Windows 10을 설치할 때, 운영체제에 통합 모바일 앱이 있는 경우를 포함해 Azure AD 인증이 사용되는 시나리오에서 작동합니다.
   - 보안 키: Microsoft Edge(레거시 및 새 Edge 모두)와 같이 지원되는 브라우저에서 Windows 10 및 웹의 잠금 화면에서 작동합니다.

- 사용자는 암호 없는 자격 증명을 사용하여 게스트인 테넌트에서 리소스에 액세스할 수 있지만 해당 리소스 테넌트에서 MFA를 수행해야 할 수도 있습니다. 자세한 내용은 [가능한 이중 다단계 인증](../external-identities/current-limitations.md#possible-double-multi-factor-authentication)을 참조하세요.  

- 사용자는 게스트인 테넌트 내에서 암호 없는 자격 증명을 등록할 수 없습니다. 해당 테넌트에서 관리되는 암호가 없는 것과 같습니다.  


## <a name="choose-a-passwordless-method"></a>암호 없는 방법 선택

이러한 세 가지 암호 없는 옵션 중에서 무엇을 선택할지는 회사의 보안, 플랫폼 및 앱 요구 사항에 따라 결정됩니다.

Microsoft 암호 없는 기술을 선택할 때 고려할 몇 가지 요소는 다음과 같습니다.

||**비즈니스용 Windows Hello**|**Microsoft Authenticator 앱으로 암호 없는 로그인**|**FIDO2 보안 키**|
|:-|:-|:-|:-|
|**필수 구성 요소**| Windows 10, version 1809 이상<br>Azure Active Directory| Microsoft Authenticator 앱<br>휴대폰(Android 6.0 이상을 실행하는 iOS 및 Android 디바이스)|Windows 10 버전 1903 이상<br>Azure Active Directory|
|**모드**|플랫폼|소프트웨어|하드웨어|
|**시스템 및 디바이스**|TPM(신뢰할 수 있는 플랫폼 모듈)이 기본으로 제공되는 PC<br>PIN 및 생체 인식 |휴대폰의 PIN 및 생체 인식|Microsoft와 호환되는 FIDO2 보안 장치|
|**사용자 환경**|PIN 또는 생체 인식(얼굴, 홍채 또는 지문)을 사용하여 Windows 디바이스에 로그인합니다.<br>Windows Hello 인증은 디바이스와 연결됩니다.따라서 사용자가 회사 리소스에 액세스하려면 PIN 또는 생체 인식 요소 같은 로그인 구성 요소와 디바이스가 모두 필요합니다.|지문 검색, 얼굴이나 홍채 인식 또는 PIN이 있는 휴대폰을 사용하여 로그인합니다.<br>사용자는 자신의 PC 또는 휴대폰에서 회사 또는 개인 계정에 로그인합니다.|FIDO2 보안 장치(생체 인식, PIN 및 NFC)를 사용하여 로그인<br>사용자는 조직 제어 기반 디바이스에 액세스할 수 있으며, PIN 및 USB 보안 키, NFC 지원 스마트 카드, 키, 착용식 컴퓨터와 같은 생체 인식 기기를 통해 인증할 수 있습니다.|
|**사용 시나리오**| Windows 디바이스의 암호 없는 환경.<br>디바이스 및 애플리케이션의 SSO(Single Sign-On) 기능이 있는 전용 회사 PC에 적용할 수 있습니다.|휴대폰을 사용하는 암호 없는 원격 솔루션.<br>디바이스로 웹의 회사 또는 개인 애플리케이션에 액세스하는 데 적용할 수 있습니다.|생체 인식, PIN 및 NFC를 사용하는 작업자의 암호 없는 환경.<br>휴대폰을 사용할 수 없는 경우(예: 지원 센터 직원, 공공 키오스크 또는 병원 팀)와 공유 PC에 적용할 수 있습니다.|

다음 표를 사용하여 요구 사항 및 사용자를 지원할 방법을 선택할 수 있습니다.

|가상 사용자|시나리오|Environment|암호 없는 기술|
|:-|:-|:-|:-|
|**관리자**|관리 작업용 디바이스에 안전하게 액세스|할당된 Windows 10 디바이스|비즈니스용 Windows Hello 및/또는 FIDO2 보안 키|
|**관리자**|비-Windows 디바이스에서의 관리 작업| 모바일 또는 비-Windows 디바이스|Microsoft Authenticator 앱으로 암호 없는 로그인|
|**정보 근로자**|생산성 작업|할당된 Windows 10 디바이스|비즈니스용 Windows Hello 및/또는 FIDO2 보안 키|
|**정보 근로자**|생산성 작업| 모바일 또는 비-Windows 디바이스|Microsoft Authenticator 앱으로 암호 없는 로그인|
|**최전선 근로자**|공장, 플랜트, 소매 또는 데이터 입력 키오스크|공유 Windows 10 디바이스|FIDO2 보안 키|

## <a name="next-steps"></a>다음 단계

Azure AD에서 암호 없는 로그인을 시작하려면 다음 방법 중 하나를 수행합니다.

* [FIDO2 보안 키 암호 없는 로그인 사용](howto-authentication-passwordless-security-key.md)
* [Authenticator 앱으로 전화 기반 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>외부 링크

* [FIDO Alliance](https://fidoalliance.org/)
* [FIDO2 CTAP 사양](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
