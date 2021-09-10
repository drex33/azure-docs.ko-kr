---
title: B2B 게스트 사용자의 일회용 암호 인증 - Azure AD
description: 메일 일회성 암호를 사용하여 Microsoft 계정을 사용하지 않고 B2B 게스트 사용자를 인증하는 방법입니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/26/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019, contperf-fy21q4-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7b12d0611f6488a9ab5475ec6488328a764434f
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721051"
---
# <a name="email-one-time-passcode-authentication"></a>이메일 일회용 암호 인증

메일 일회용 암호 기능은 Azure AD, Microsoft 계정(MSA), 소셜 ID 공급자 등의 다른 방법으로 인증할 수 없는 B2B 협업 사용자를 인증하는 방법입니다. B2B 게스트 사용자가 초대를 사용하거나 공유 리소스에 로그인하려고 하면 이메일 주소로 전송되는 임시 암호를 요청할 수 있습니다. 그런 다음, 이 암호를 입력하여 로그인을 계속합니다.

테넌트의 외부 ID 설정에서 이메일 일회용 암호 ID 공급자를 구성하여 언제든지 Azure Portal에서 이 기능을 사용하도록 설정할 수 있습니다. 기능을 활성화 또는 비활성화하거나, 2021년 10월에 자동으로 활성화되도록 선택할 수 있습니다.

![이메일 일회용 암호 개요 다이어그램](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **2021년 10월부터** 모든 기존 테넌트에 대해 이메일 일회용 암호 기능이 설정되고 새 테넌트에 대해 기본적으로 사용하도록 설정됩니다. 이 기능을 원하지 않는다면 사용하지 않도록 설정할 수 있습니다. 아래 [이메일 일회용 암호를 사용하지 않도록 설정](#disable-email-one-time-passcode)을 참조하세요.
> - Azure Portal의 이메일 일회용 암호 설정이 **외부 공동 작업 설정** 에서 **모든 ID 공급자** 로 이동했습니다.

> [!NOTE]
> 일회성 암호 사용자는 테넌트 컨텍스트를 포함하는 링크를 사용하여 로그인해야 합니다(예: `https://myapps.microsoft.com/?tenantid=<tenant id>` 또는 `https://portal.azure.com/<tenant id>`, 혹은 확인된 도메인의 경우 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 게스트 사용자는 현재 테넌트 컨텍스트가 없는 엔드포인트를 사용하여 로그인할 수 없습니다. 예를 들어 `https://myapps.microsoft.com`을 사용하면 `https://portal.azure.com`에서 오류가 발생합니다.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>일회성 암호 게스트 사용자를 위한 사용자 환경

이메일 일회용 암호를 사용하도록 설정하면 [특정 조건을 충족](#when-does-a-guest-user-get-a-one-time-passcode)하는 새로 초대된 사용자는 일회용 암호 인증을 사용합니다. 이메일 일회용 암호 이전에 초대를 사용한 게스트 사용자는 동일한 인증 방법을 계속 사용합니다.

일회성 암호 인증을 사용하면 게스트 사용자가 직접 링크를 클릭하거나 초대 메일을 사용하여 초대를 사용할 수 있습니다. 두 경우 모두 코드가 게스트 사용자의 메일 주소로 전송된다는 메시지가 브라우저에 표시됩니다. 게스트 사용자가 **코드 보내기** 를 선택합니다.

   ![코드 보내기 단추를 보여주는 스크린샷](media/one-time-passcode/otp-send-code.png)

암호가 사용자의 메일 주소로 전송됩니다. 사용자가 메일에서 암호를 검색하고 브라우저 창에 입력합니다.

   ![코드 입력 페이지를 보여주는 스크린샷](media/one-time-passcode/otp-enter-code.png)

이제 게스트 사용자가 인증되었고 공유 리소스를 보거나 로그인을 계속할 수 있습니다.

> [!NOTE]
> 일회성 암호는 30분 동안 유효합니다. 30분이 지나면 특정 일회성 암호가 더 이상 유효하지 않고 사용자는 새 암호를 요청해야 합니다. 사용자 세션은 24시간 후에 만료됩니다. 해당 시간이 지나면 게스트 사용자는 리소스에 액세스할 때 새 암호를 받습니다. 특히 게스트 사용자가 회사를 떠나거나 더 이상 액세스할 필요가 없는 경우 세션 만료로 보안을 강화합니다.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>게스트 사용자가 일회성 암호를 얻을 때는 언제인가요?

게스트 사용자가 초대를 사용하거나 공유된 리소스의 링크를 사용할 때 일회성 암호를 받는 경우는 다음과 같습니다.

- Azure AD 계정이 없는 경우
- Microsoft 계정이 없는 경우
- 초대 테넌트는 소셜(예: [Google](google-federation.md)) 또는 기타 ID 공급자와 페더레이션을 설정하지 않습니다.

초대할 때 초대를 받는 사용자가 일회성 암호 인증을 사용할 것이라는 표시는 없습니다. 그러나 게스트 사용자가 로그인할 때 다른 인증 방법을 사용할 수 없다면 일회성 암호 인증이 대체 방법이 됩니다.


> [!NOTE]
> 사용자가 일회성 암호를 사용하고 나중에 MSA, Azure AD 계정 또는 기타 페더레이션 계정을 얻을 경우 일회성 암호를 사용하여 계속 인증됩니다. 사용자의 인증 방법을 업데이트하려는 경우 [상환 상태를 다시 설정](reset-redemption-status.md)할 수 있습니다.

### <a name="example"></a>예제

게스트 사용자 teri@gmail.com이 Google 페더레이션이 설정되지 않은 Fabrikam에 초대되었습니다. Teri에게는 Microsoft 계정이 없습니다. 인증을 위해 일회용 암호를 받게 됩니다.

## <a name="enable-email-one-time-passcode"></a>이메일 일회용 암호를 사용하도록 설정

1. Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 탐색 창에서 **Azure Active Directory** 를 선택합니다.

3. **외부 ID** > **모든 ID 공급자** 를 차례로 선택합니다.

4. **이메일 일회용 암호** 를 선택하여 구성 창을 엽니다.

5. **게스트용 이메일 일회용 암호** 아래에서 다음 중 하나를 선택합니다.

   - 기능을 즉시 사용하도록 설정하지 않고 2021년 10월 자동 활성화 날짜를 기다리려면 **2021년 10월부터 게스트에 대해 이메일 일회용 암호를 자동으로 사용하도록 설정** 합니다.
   - 지금 기능을 사용하도록 설정하려면 **지금 유효한 게스트용 이메일 일회용 암호를 사용하도록 설정** 합니다.
   - 예/아니요 토글이 표시되면 **예** 를 선택하여 지금 기능을 사용하도록 설정합니다. 이 토글은 이전에 기능을 사용하지 않도록 설정한 경우에 표시됩니다.

   ![이메일 일회용 암호 토글 사용](media/one-time-passcode/enable-email-otp-options.png)

5. **저장** 을 선택합니다.

## <a name="disable-email-one-time-passcode&quot;></a>이메일 일회용 암호 사용 안 함

2021년 10월부터 모든 기존 테넌트에 대해 이메일 일회용 암호 기능이 설정되고 새 테넌트에 대해 기본적으로 사용하도록 설정됩니다. 이때 Microsoft는 B2B 협업 시나리오에 대해 관리되지 않는(&quot;바이럴&quot; 또는 &quot;just-in-time") Azure AD 계정과 테넌트를 만들어 더 이상 초대 상환을 지원하지 않습니다. 게스트 사용자에게 원활한 대체 인증 방법을 제공하므로 이메일 일회용 암호 기능을 사용하고 있습니다. 그러나 이 기능을 사용하지 않으려면 사용하지 않도록 설정하는 옵션이 있습니다.

> [!NOTE]
>
> 테넌트에서 이메일 일회용 암호 기능을 사용하도록 설정하고 이 기능을 끄면 일회용 암호를 사용한 게스트 사용자는 로그인할 수 없습니다. 다른 인증 방법을 사용하여 다시 로그인 할 수 있도록 [상환 상태를 다시 설정](reset-redemption-status.md)할 수 있습니다.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>이메일 일회용 암호 기능을 사용하지 않도록 설정하려면

1. Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 탐색 창에서 **Azure Active Directory** 를 선택합니다.

3. **외부 ID** > **모든 ID 공급자** 를 차례로 선택합니다.

4. **이메일 일회용 암호** 를 선택한 다음, **게스트용 이메일 일회용 암호** 에서 **게스트용 이메일 일회성 암호 사용 안 함** 을 선택합니다(또는 미리 보기 중에 기능이 활성화, 비활성화 또는 옵트인된 경우 **아니요** 선택).

   ![이메일 일회용 암호 토글 사용 안 함](media/one-time-passcode/disable-email-otp-options.png)

   > [!NOTE]
   > Azure Portal의 이메일 일회용 암호 설정이 **외부 공동 작업 설정** 에서 **모든 ID 공급자** 로 이동했습니다.
   > 이 기능을 이전에 사용하거나 사용하지 않도록 설정했거나 미리 보기에 옵트인한 경우에는 이메일 일회용 암호 옵션 대신 토글이 표시됩니다. 기능을 사용하지 않도록 설정하려면 **아니요** 를 선택합니다.

5. **저장** 을 선택합니다.

## <a name="note-for-public-preview-customers"></a>공개 미리 보기 고객에 대한 참고 사항

이전에 이메일 일회용 암호 공개 미리 보기에 옵트인한 경우 자동 기능 사용 설정에 대한 2021년 10월 날짜가 적용되지 않으므로 관련 비즈니스 프로세스는 영향을 받지 않습니다. 또한 Azure Portal의 **게스트의 이메일 일회용 암호** 속성 아래에서 **2021년 10월부터 게스트용 이메일 일회용 암호를 자동으로 사용하도록 설정** 하는 옵션이 표시되지 않습니다. 대신 다음과 같이 **예** 또는 **아니요** 토글이 표시됩니다.

![옵트인된 이메일 일회용 암호](media/one-time-passcode/enable-email-otp-opted-in.png)

그러나 이 기능을 옵트아웃하고 2021년 10월에 자동으로 사용하도록 설정하려는 경우 Microsoft Graph API [이메일 인증 방법 구성 리소스 종류](/graph/api/resources/emailauthenticationmethodconfiguration)를 사용하여 기본 설정으로 되돌릴 수 있습니다. 기본 설정으로 되돌린 후에는 **게스트용 이메일 일회용 암호** 에서 다음 옵션을 사용할 수 있습니다.

![옵트인된 이메일 일회용 암호를 사용하도록 설정](media/one-time-passcode/email-otp-options.png)

- **2021년 10월부터 자동으로 게스트용 이메일 일회용 암호를 사용하도록 설정합니다.** (기본값) 테넌트에 대해 이메일 일회용 암호 기능을 아직 사용하도록 설정하지 않은 경우 2021년 10월부터 자동으로 설정됩니다. 그때 해당 기능을 사용하도록 설정하려면 추가 작업이 필요하지 않습니다. 해당 기능을 이미 사용하도록 설정했거나 아직 설정하지 않은 경우에는 이 옵션을 사용할 수 없습니다.

- **게스트에게 일회용 암호를 메일로 보내도록 설정하면 지금부터 적용됩니다**. 테넌트에 대해 메일 일회용 암호 기능을 설정합니다.

- **게스트에게 일회용 암호를 메일로 보내지 않도록 설정합니다.** 테넌트에 대한 이메일 일회용 암호 기능을 해제하며 2021년 10월에는 해당 기능을 설정할 수 없습니다.

## <a name="note-for-azure-us-government-customers"></a>Azure 미국 정부 고객에 대한 참고 사항

Azure 미국 정부 클라우드에서 이메일 일회용 암호 기능은 기본적으로 사용하지 않도록 설정되어 있습니다. 이 기능을 사용하도록 설정하지 않으면 파트너는 로그인할 수 없습니다. Azure 퍼블릭 클라우드와 달리 Azure 미국 정부 클라우드는 셀프 서비스 Azure Active Directory 계정으로 초대 사용을 지원하지 않습니다.

 ![이메일 일회용 암호 기능 사용 안 함](media/one-time-passcode/enable-email-otp-disabled.png)

Azure 미국 정부 클라우드에서 이메일 일회용 암호 기능을 사용하도록 설정하려면 다음을 수행합니다.

1. Azure AD 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 탐색 창에서 **Azure Active Directory** 를 선택합니다.
3. **조직 관계** > **모든 ID 공급자** 를 선택합니다.

   > [!NOTE]
   > - **조직 관계** 가 표시되지 않는 경우 맨 위의 검색 창에서 "외부 ID"를 검색합니다.

4. **이메일 일회용 암호** 를 선택하고 **예** 를 선택합니다.
5. **저장** 을 선택합니다.

현재 제한 사항에 대한 자세한 내용은 [Azure 미국 정부 클라우드](current-limitations.md#azure-us-government-clouds)를 참조하세요.
