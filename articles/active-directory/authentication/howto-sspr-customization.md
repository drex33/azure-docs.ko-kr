---
title: 셀프 서비스 암호 재설정 사용자 지정 - Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정의 사용자 표시 및 환경 옵션을 사용자 지정하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741629"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 셀프 서비스 암호 재설정의 사용자 환경 사용자 지정

Azure AD(Azure Active Directory)에서 SSPR(셀프 서비스 암호 재설정)을 사용하면 관리자 또는 지원 센터에서 개입하지 않고도 사용자가 암호를 변경하거나 다시 설정할 수 있습니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다.

사용자의 SSPR 환경을 개선하기 위해 암호 재설정 페이지, 메일 알림 또는 로그인 페이지의 모양과 느낌을 사용자 지정할 수 있습니다. 이 사용자 지정 옵션을 사용하면 사용자가 분명히 적합한 위치에 있을 수 있고 확실하게 회사 리소스에 액세스할 수 있습니다.
    
이 문서에서는 사용자, 회사 브랜딩, AD FS 로그인 페이지 링크에 대한 SSPR 메일 링크를 사용자 지정하는 방법을 보여 줍니다.

## <a name="customize-the-contact-your-administrator-link"></a>“관리자에게 문의” 링크 사용자 지정

사용자가 셀프 서비스 암호 재설정에 대한 도움을 요청하는 데 도움이 되도록 “관리자에게 문의” 링크가 암호 재설정 포털에 표시됩니다. 사용자가 이 링크를 선택하면 다음 두 작업 중 하나가 수행됩니다.

* 이 연락처 링크가 기본 상태로 유지되면 메일이 관리자에게 전송되고 사용자 암호 변경을 위한 지원을 제공하도록 요청합니다. 다음 샘플 메일은 기본 메일 메시지를 보여 줍니다.

    ![관리자에게 보낸 메일을 다시 설정하기 위한 샘플 요청](./media/howto-sspr-customization/sspr-contact-admin.png)

* 사용자 지정된 경우에는 사용자가 지원을 위해 관리자가 지정한 웹 페이지나 메일 주소로 이동됩니다.
    * 사용자 지정하는 경우 사용자가 지원을 위해 이미 익숙한 것으로 설정하는 것이 좋습니다.

    > [!WARNING]
    > 암호 재설정이 필요한 메일 주소와 계정을 사용하여 이 설정을 사용자 지정하는 경우에는 사용자가 지원을 요청하지 못할 수 있습니다.

### <a name="default-email-behavior"></a>기본 메일 동작

기본 문의 메일은 다음 순서로 받는 사람에게 전송됩니다.

1. ‘기술 지원팀 관리자’ 역할이나 ‘암호 관리자’ 역할이 할당된 경우 해당 역할을 소유한 관리자에게 알림이 제공됩니다. 
1. 기술 지원팀 관리자나 암호 관리자가 할당되지 않은 경우에는 ‘사용자 관리자’ 역할을 소유한 관리자에게 알림이 제공됩니다.
1. 이전 역할이 둘 다 할당되지 않은 경우 ‘전역 관리자’에게 알림이 제공됩니다.

어떠한 경우에도 최대 100명의 받는 사람에게 알림이 제공됩니다.

다양한 관리자 역할과 각 역할을 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)을 참조하세요.

### <a name="disable-contact-your-administrator-emails"></a>“관리자에게 문의” 메일 사용 안 함

조직에서 관리자에게 암호 재설정 요청을 알리지 않으려는 경우 다음 구성 옵션을 사용할 수 있습니다.

* 사용자가 도움을 받을 수 있는 웹 URL 또는 mailto: 주소를 제공하는 고객 지원 센터 링크를 사용자 지정합니다. 이 옵션은 **암호 재설정** > **사용자 지정** >  **사용자 지정 기술 지원 팀 메일 또는 URL** 아래에 있습니다.
* 모든 사용자에 대해 셀프 서비스 암호 재설정을 사용하도록 설정합니다. 이 옵션은 **암호 재설정** > **속성** 아래에 있습니다. 사용자가 자신의 암호를 재설정할 수 없도록 하려면 빈 그룹에 대한 액세스 범위를 지정하면 됩니다. *이 옵션은 권장되지 않습니다.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>로그인 페이지 및 액세스 패널 사용자 지정

회사 브랜딩에 맞는 이미지와 함께 표시되는 로고를 추가하는 것과 같이 로그인 페이지를 사용자 지정할 수 있습니다. 회사 브랜딩을 구성하는 방법에 관한 자세한 내용은 [Azure AD의 로그인 페이지에 회사 브랜딩 추가](../fundamentals/customize-branding.md)를 참조하세요.

이미지는 다음과 같은 경우에 표시됩니다.

* 사용자가 사용자 이름을 입력한 뒤
* 사용자가 다음과 같은 방식으로 사용자 지정된 URL에 액세스하는 경우
   * `whr` 매개 변수를 암호 재설정 페이지에 전달(예: `https://login.microsoftonline.com/?whr=contoso.com`)
   * `username` 매개 변수를 암호 재설정 페이지에 전달(예: `https://login.microsoftonline.com/?username=admin@contoso.com`)

### <a name="directory-name"></a>디렉터리 이름

사용자에게 더 친숙해 보이도록 포털과 자동화된 통신에서 조직 이름을 변경할 수 있습니다. Azure Portal에서 디렉터리 이름 특성을 변경하려면 **Azure Active Directory** > **속성** 으로 이동합니다. 이 친숙한 조직 이름 옵션은 다음 예제와 같이 자동화된 메일에서 가장 잘 보입니다.

* 메일의 식별 이름(예: “CONTOSO 데모를 대신하는 Microsoft”)
* 메일의 제목 줄(예: “CONTOSO 데모 계정 메일 확인 코드”)

## <a name="customize-the-ad-fs-sign-in-page"></a>AD FS 로그인 페이지 사용자 지정

사용자 로그인 이벤트에서 AD FS(Active Directory Federation Services)를 사용하는 경우 [로그인 페이지 설명 추가](/windows-server/identity/ad-fs/operations/add-sign-in-page-description) 문서의 지침을 사용하여 로그인 페이지의 링크를 추가할 수 있습니다.

사용자에게 SSPR 워크플로를 입력할 수 있는 페이지의 링크를 제공합니다(예: *https://passwordreset.microsoftonline.com* ). AD FS 로그인 페이지의 링크를 추가하려면 AD FS 서버에서 아래 명령을 사용합니다.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>다음 단계

환경에서 SSPR 사용을 이해하려면 [Azure AD 암호 관리에 대한 보고 옵션](howto-sspr-reporting.md)을 참조하세요.

SSPR에 문제가 발생하는 경우 [셀프 서비스 암호 재설정 문제 해결](./troubleshoot-sspr.md)을 참조하세요.