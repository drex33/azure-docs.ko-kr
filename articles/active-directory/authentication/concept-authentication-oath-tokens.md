---
title: OATH 토큰 인증 방법 - Azure Active Directory
description: Azure Active Directory에서 OATH 토큰을 사용하여 로그인 이벤트를 더 발전시키고 안전하게 만드는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d5e34ee5013a6f4ad28779e92536ff2d8ef868
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730771"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Azure Active Directory의 인증 방법 - OATH 토큰 

OATH TOTP(시간 제약이 있는 일회성 암호)는 OTP(일회용 암호) 코드가 생성되는 방법을 지정하는 개방형 표준입니다. OATH TOTP는 코드를 생성하는 소프트웨어 또는 하드웨어를 사용하여 구현할 수 있습니다. Azure AD는 다른 코드 생성 표준인 OATH HOTP를 지원하지 않습니다.

## <a name="oath-software-tokens"></a>OATH 소프트웨어 토큰

소프트웨어 OATH 토큰은 일반적으로 Microsoft Authenticator 앱 및 다른 인증자 앱과 같은 애플리케이션입니다. Azure AD는 앱에 입력되고 각 OTP를 생성하는 데 사용되는 비밀 키 또는 시드를 생성합니다.

Authenticator 앱은 푸시 알림을 수행하도록 설정된 경우 자동으로 코드를 생성하므로 디바이스가 연결되지 않은 경우에도 사용자에게 백업이 있습니다. OATH TOTP를 사용하여 코드를 생성하는 타사 애플리케이션을 사용할 수도 있습니다.

일부 OATH TOTP 하드웨어 토큰은 프로그래밍 가능합니다. 즉, 사전 프로그래밍된 비밀 키 또는 시드가 제공되지 않습니다. 이러한 프로그래밍 가능한 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 얻은 비밀 키 또는 시드를 사용하여 설정할 수 있습니다. 고객은 선택한 공급업체에서 이러한 토큰을 구매하고 해당 공급업체의 설정 프로세스에서 비밀 키 또는 시드를 사용할 수 있습니다.

## <a name="oath-hardware-tokens-preview"></a>OATH 하드웨어 토큰(미리 보기)

Azure AD는 30초 또는 60초마다 코드를 새로 고치는 OATH-TOTP SHA-1 토큰의 사용을 지원합니다. 고객은 자신이 선택한 공급업체에서 이러한 토큰을 구매할 수 있습니다. 

OATH TOTP 하드웨어 토큰은 일반적으로 토큰에서 사전 프로그래밍된 비밀 키 또는 시드를 제공합니다. 이러한 키는 다음 단계에 설명된 대로 Azure AD에 입력해야 합니다. 비밀 키는 128자로 제한되며 일부 토큰과는 호환되지 않을 수 있습니다. 비밀 키에는 *a-z* 또는 *A-Z* 와 *2-7* 까지의 숫자만 포함할 수 있으며, *Base32* 로 인코딩해야 합니다.

다시 시드될 수 있는 프로그래밍 가능한 OATH TOTP 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 Azure AD를 사용하여 설정할 수도 있습니다.

OATH 하드웨어 토큰은 공개 미리 보기의 일부로 지원됩니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

![OATH 토큰을 MFA OATH 토큰 블레이드에 업로드](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

토큰이 확보되면 아래 예와 같이 UPN, 일련 번호, 비밀 키, 시간 간격, 제조업체 및 모델이 포함된 CSV(쉼표로 구분된 값) 파일 형식으로 업로드해야 합니다.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef2234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> CSV 파일에 머리글 행을 포함해야 합니다. UPN에 작은따옴표가 있으면 작은따옴표를 하나 더 사용하여 이스케이프합니다. 예를 들어, UPN이 my’user@domain.com이면 파일을 업로드할 때 my’’user@domain.com로 변경합니다.

CSV 파일로 올바르게 형식이 지정되면 전역 관리자는 Azure Portal에 로그인하여 **Azure Active Directory > 보안 > MFA > OATH 토큰** 으로 이동하고 CSV 파일을 업로드할 수 있습니다.

CSV 파일의 크기에 따라 처리하는 데 몇 분 정도가 소요될 수 있습니다. 현재 상태를 가져오려면 **새로 고침** 단추를 선택합니다. 파일에 오류가 있는 경우 오류가 나열된 CSV 파일을 다운로드하여 해결할 수 있습니다. 다운로드한 CSV 파일의 필드 이름은 업로드된 버전과 다릅니다.  

오류가 모두 처리되면 관리자는 토큰에 대해 **활성화** 를 선택하고 토큰에 표시된 OTP를 입력하여 각 키를 활성화할 수 있습니다. 5분마다 최대 200개의 OATH 토큰을 활성화할 수 있습니다. 

사용자는 언제든지 사용할 수 있도록 구성된 Microsoft Authenticator 앱과 같은 인증자 애플리케이션 또는 최대 5개의 OATH 하드웨어 토큰을 조합할 수 있습니다. 하드웨어 OATH 토큰은 리소스 테넌트에서 게스트 사용자에게 할당할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview)를 사용하는 인증 구성 방법에 대해 자세히 알아봅니다.
암호 없는 인증과 호환되는 [FIDO2 보안 키 공급자](concept-authentication-passwordless.md#fido2-security-key-providers)에 대해 알아봅니다.
