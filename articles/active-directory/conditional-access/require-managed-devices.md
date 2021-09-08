---
title: 조건부 액세스에 관리 디바이스 필요 - Azure Active Directory
description: 클라우드 앱 액세스에 대한 관리 디바이스가 필요하도록 Azure AD(Azure Active Directory) 디바이스 기반 조건부 액세스 정책을 구성하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e39a6bbde587e543afcb10cb910a675bf4c69fc
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867564"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>방법: 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 관리 디바이스 필요

모바일 우선, 클라우드 우선 세계에서 Azure AD(Active Directory)는 어디에서나 앱 및 서비스에 대한 Single Sign-On을 가능하게 합니다. 권한 있는 사용자는 모바일 및 개인 디바이스를 포함하여 광범위한 디바이스에서 클라우드 앱에 액세스할 수 있습니다. 하지만 여러 환경에는 보안 및 준수를 위해 표준을 충족하는 디바이스에 의해서만 액세스되어야 하는 몇 가지 앱이 있습니다. 이러한 디바이스는 관리 디바이스라고도 합니다. 

이 문서에서는 사용자 환경의 특정 클라우드 앱에 액세스하려면 관리 디바이스가 필요하도록 조건부 액세스 정책을 구성할 수 있는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 구성 요소

클라우드 앱 액세스에 대한 관리 디바이스가 필요한 경우 **Azure AD 조건부 액세스** 와 **Azure AD 디바이스 관리** 를 연결해야 합니다. 이러한 내용에 익숙하지 않은 경우 먼저 다음 항목을 읽어보세요.

- **[Azure Active Directory의 조건부 액세스](./overview.md)** - 이 문서에서는 조건부 액세스 및 관련 용어의 개념을 개괄적으로 설명합니다.
- **[Azure Active Directory의 디바이스 관리 소개](../devices/overview.md)** - 이 문서에서는 디바이스를 조직에서 제어해야 하는 다양한 옵션에 대해 간략히 설명합니다. 
- **Windows 10 Creators Update(버전 1703)** 이상에서 Chrome 지원을 사용하려면 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치합니다. 이 확장은 조건부 액세스 정책에서 디바이스 관련 세부 정보를 요구하는 경우에 필요합니다.
- Firefox 지원의 경우 **Windows 10 버전 1809 이상** 에서 **Firefox 91** 을 시작하고 [Windows SSO](https://support.mozilla.org/en-US/kb/windows-sso)를 구성합니다.

>[!NOTE] 
> 초기 디바이스 인증 후에 적용을 최적화하려면 Azure AD 디바이스 기반 조건부 액세스 정책을 사용하는 것이 좋습니다. 여기에는 디바이스가 규정 준수 및 디바이스 코드 흐름을 벗어날 경우 세션 닫기가 포함됩니다.


## <a name="scenario-description"></a>시나리오 설명

보안과 생산성 간의 균형을 유지하기가 어렵습니다. 클라우드 리소스에 액세스하도록 지원되는 디바이스의 범위가 넓어지면 사용자의 생산성을 개선할 수 있습니다. 반대로 환경에서 특정 리소스에 알려지지 않은 보호 수준을 가진 디바이스가 액세스하지 않도록 하는 것이 좋습니다. 영향을 받는 리소스의 경우 사용자가 관리 디바이스를 사용해서만 액세스할 수 있도록 해야 합니다. 

Azure AD 조건부 액세스를 사용하면 액세스 권한을 부여하는 단일 정책으로 이 요구 사항을 처리할 수 있습니다.

- 선택한 클라우드 앱으로
- 선택한 사용자 및 그룹에 대해
- 관리 디바이스 필요

## <a name="managed-devices"></a>관리되는 디바이스  

간단히 말해서 관리되는 디바이스는 *일종* 의 조직 제어 하에 있는 디바이스입니다. Azure AD에서 관리 디바이스의 필수 조건은 Azure AD에 등록된 것입니다. 디바이스를 등록하면 디바이스 개체 형태로 디바이스의 ID가 만들어집니다. 이 개체는 Azure에서 디바이스에 대한 상태 정보를 추적하는 데 사용됩니다. Azure AD 관리자는 이미 이 개체를 사용하여 디바이스의 상태를 토글(설정/해제)할 수 있습니다.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="사용 및 사용 안 함 항목이 강조 표시되어 있는 Azure AD의 디바이스 창 스크린샷" border="false":::

Azure AD에 등록된 디바이스를 가져오는 데는 세 가지 옵션이 있습니다. 

- **Azure AD 등록 디바이스** - Azure AD에 등록된 개인 디바이스를 가져옵니다.
- **Azure AD 조인 디바이스** - Azure AD에 등록된 온-프레미스 AD에 조인되지 않은 조직의 Windows 10 디바이스를 가져옵니다. 
- **Azure AD 조인 디바이스** - Azure AD에 등록된 온-프레미스 AD에 조인된 Windows 10 또는 지원되는 하위 수준 디바이스를 가져옵니다.

세 가지 옵션에 대해서는 [디바이스 ID란?](../devices/overview.md) 문서에서 설명합니다.

관리되는 디바이스가 되려면, 등록된 디바이스는 **하이브리드 Azure AD 조인 디바이스** 또는 **준수 상태로 표시된 디바이스** 여야 합니다.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="규격이고 하이브리드 Azure AD 조인된 디바이스의 확인란과 액세스 권한 부여가 선택된 Azure AD 권한 부여 창 스크린샷" border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 조인된 디바이스 필요

조건부 액세스 정책에서 **하이브리드 Azure AD 조인된 디바이스 필요** 를 선택하면 관리 디바이스를 사용해야만 선택한 클라우드 앱에 액세스하도록 설정할 수 있습니다. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="액세스 권한 부여와 하이브리드 Azure AD 조인된 디바이스 필요 확인란이 선택된 Azure AD 권한 부여 창 스크린샷" border="false":::

이 설정은 온-프레미스 AD에 조인된 Windows 10 또는 Windows 7 또는 Windows 8과 같은 하위 수준 디바이스에만 적용됩니다. 등록된 Windows 10 디바이스를 가져오기 위한 [자동화된 프로세스](../devices/hybrid-azuread-join-plan.md)인 하이브리드 Azure AD 조인을 사용하여 이러한 디바이스를 Azure AD에만 등록할 수 있습니다. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="디바이스의 이름, 사용 상태, OS, 버전, 조인 유형, 소유자, MDM, 규격 상태가 나열된 테이블. 규격 상태는 아니요입니다." border="false":::

하이브리드 Azure AD 조인 디바이스를 관리 디바이스로 어떻게 만드나요?  온-프레미스 AD에 조인된 디바이스의 제어는 **Configuration Manager** 또는 **GP(그룹 정책)** 와 같은 디바이스 관리 솔루션을 사용하여 적용된다고 가정합니다. Azure AD가 이러한 방법 중 어떤 것을 디바이스에 적용할지 결정할 방법이 없기 때문에 하이브리드 Azure AD 조인 디바이스를 요구하는 것은 관리 디바이스를 필요로 하는 비교적 약한 메커니즘입니다. 온-프레미스 도메인에 가입된 디바이스에 적용되는 방법이 관리 디바이스(이러한 디바이스가 하이브리드 Azure AD 조인 디바이스이기도 하다면)를 구성할 만큼 충분히 강력한지 여부를 판단하는 것은 관리자의 몫입니다.

## <a name="require-device-to-be-marked-as-compliant"></a>디바이스를 준수 상태로 표시해야 함

*디바이스를 준수 상태로 표시해야 하는* 옵션은 관리 디바이스를 요청하는 가장 강력한 형태입니다.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="액세스 권한 부여와 규격으로 표시된 디바이스 필요 확인란이 선택된 Azure AD 권한 부여 창 스크린샷" border="false":::

이 옵션을 사용하려면 디바이스를 Azure AD에 등록해야 하며 다음과 같이 준수 상태로도 표시되어야 합니다.
         
- Intune
- Azure AD 통합을 통해 Windows 10 디바이스를 관리하는 타사 MDM(모바일 디바이스 관리) 시스템입니다. Windows 10 이외의 디바이스 OS 유형에 대한 타사 MDM 시스템은 지원되지 않습니다.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="디바이스의 이름, 사용 상태, OS, 버전, 조인 유형, 소유자, MDM, 규격 상태가 나열된 테이블. 규격 상태가 강조 표시되어 있습니다." border="false":::

준수 상태로 표시된 디바이스의 경우 다음과 같이 가정할 수 있습니다. 

- 직원이 회사 데이터에 액세스하는 데 사용하는 모바일 디바이스가 관리됩니다.
- 직원이 사용하는 모바일 앱이 관리됩니다.
- 직원이 정보에 액세스하고 공유하는 방식을 제어함으로써 회사 정보가 보호됩니다.
- 디바이스와 해당 앱은 회사 보안 요구 사항을 준수합니다.

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>시나리오: iOS 및 Android 디바이스의 디바이스 등록 필요

이 시나리오에서 Contoso는 Microsoft 365 리소스에 대한 모든 모바일 액세스에서 등록된 디바이스를 사용해야 하도록 결정했습니다. 모든 사용자는 이미 Azure AD 자격 증명을 사용하여 로그인하고 Azure AD Premium P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스가 할당되어 있습니다.

조직은 등록된 모바일 디바이스를 사용하도록 요구하기 위해 다음 단계를 완료해야 합니다.

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹** 을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **Office 365** 를 선택합니다.
1. **조건** 에서 **디바이스 플랫폼** 을 선택합니다.
   1. **구성** 을 **예** 로 설정합니다.
   1. **Android** 및 **iOS** 를 포함합니다.
1. **액세스 제어** > **권한 부여** 에서 다음 옵션을 선택합니다.
   - **디바이스를 준수 상태로 표시해야 함**
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 만들고 사용하도록 설정합니다.

### <a name="known-behavior"></a>알려진 동작

[디바이스 코드 OAuth 흐름](../develop/v2-oauth2-device-code.md)을 사용하는 경우 관리 디바이스 필요 권한 부여 컨트롤 또는 디바이스 상태 조건은 지원되지 않습니다. 이는 인증을 수행하는 디바이스가 코드를 제공하는 디바이스에 디바이스 상태를 제공할 수 없으며, 토큰의 디바이스 상태가 인증을 수행하는 디바이스에 대해 잠겨 있기 때문입니다. 대신 다단계 인증 필요 권한 부여 컨트롤을 사용합니다.

Windows 7, iOS, Android, macOS, 일부 타사 웹 브라우저에서 Azure AD는 디바이스가 Azure AD에 등록될 때 프로비저닝되는 클라이언트 인증서를 사용하여 디바이스를 식별합니다. 사용자가 브라우저를 통해 처음으로 로그인하면 인증서를 선택하라는 메시지가 사용자에게 표시됩니다. 최종 사용자가 이 인증서를 선택해야 브라우저를 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[보고 전용 모드로 광범위하게 사용하도록 설정하기 전에 조건부 액세스 정책의 영향을 평가](concept-conditional-access-report-only.md)합니다.
