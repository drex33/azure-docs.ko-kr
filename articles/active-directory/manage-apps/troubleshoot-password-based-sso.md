---
title: 암호 기반 Single Sign-On 문제 해결
description: 암호 기반 Single Sign-On에 대해 구성된 Azure AD 앱의 문제를 해결합니다.
titleSuffix: Azure AD
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 1b33398a9188b55eae3d0ec54fcfe1acc6b6a589
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613702"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 암호 기반 Single Sign-On 문제 해결

내 앱에서 암호 기반 SSO(Single Sign-On)를 사용하려면 브라우저 확장을 설치해야 합니다. 암호 기반 SSO에 대해 구성된 앱을 선택하면 확장이 자동으로 다운로드됩니다. 최종 사용자 관점에서 내 앱 사용에 대해 알아보려면 [내 앱 포털 도움말](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="my-apps-browser-extension-not-installed"></a>내 앱 브라우저 확장이 설치되지 않음

브라우저 확장이 설치되어 있는지 확인합니다. 자세히 알아보려면 [Azure Active Directory 내 앱 배포 계획](my-apps-deployment-plan.md)을 참조하세요.

## <a name="single-sign-on-not-configured"></a>Single Sign-On이 구성되지 않음

암호 기반 Single Sign-On이 구성되어 있는지 확인합니다. 자세히 알아보려면 [암호 기반 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)을 참조하세요.

## <a name="users-not-assigned"></a>사용자가 할당되지 않음

사용자가 앱에 할당되어 있는지 확인합니다. 자세히 알아보려면 [앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)을 참조하세요.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>자격 증명은 입력했지만 확장에서 제출하지 않음

이 문제는 일반적으로 애플리케이션 공급업체에서 최근에 로그인 페이지를 변경하여 필드를 추가했거나, 사용자 이름 및 암호 필드 감지에 사용되는 식별자를 변경했거나, 애플리케이션의 로그인 경험 방식을 수정한 경우에 발생합니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 이러한 통합이 올바르게 작동하지 않는 경우에는 가능한 신속하게 해결할 수 있도록 지원 사례를 엽니다.

**이 애플리케이션의 공급업체와 연락된 경우** Microsoft에서 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위해 작업할 수 있도록 알려주세요. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../develop/v2-howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>자격 증명을 입력하고 제출했지만 페이지에 자격 증명이 잘못되었다고 표시됨

이 문제를 해결하려면 먼저 다음과 같은 작업을 사용합니다.

- 사용자가 먼저 저장된 자격 증명을 사용해서 **애플리케이션 웹 사이트에 직접 로그인** 을 시도하도록 합니다.

  - 로그인이 되면 사용자가 [내 앱](https://myapps.microsoft.com/)의 **앱** 섹션에서 **애플리케이션 타일** 에 있는 **자격 증명 업데이트** 단추를 클릭하여 작동하는 최신 사용자 이름 및 암호로 업데이트합니다.

  - 자신이나 다른 관리자가 이 사용자에게 자격 증명을 할당한 경우는 애플리케이션의 **사용자 및 그룹** 탭으로 이동하여 할당을 선택하고 **자격 증명 업데이트** 단추를 클릭하여 사용자 또는 그룹의 애플리케이션 할당을 찾습니다.

- 사용자가 직접 자격 증명을 할당한 경우는 사용자에게 **애플리케이션에서 암호가 만료되지 않았는지 확인하라고 한** 다음, 만료되었으면 애플리케이션에 직접 로그인하여 **만료된 암호를 업데이트** 하도록 합니다.

  - 애플리케이션에서 암호가 업데이트된 후에 사용자에게 [내 앱](https://myapps.microsoft.com/)의 **앱** 섹션에서 **애플리케이션 타일** 에 있는 **자격 증명 업데이트** 단추를 클릭하여 작동하는 최신 사용자 이름 및 암호로 업데이트하도록 요청합니다.

  - 자신이나 다른 관리자가 이 사용자에게 자격 증명을 할당한 경우는 애플리케이션의 **사용자 및 그룹** 탭으로 이동하여 할당을 선택하고 **자격 증명 업데이트** 단추를 클릭하여 사용자 또는 그룹의 애플리케이션 할당을 찾습니다.

- 내 앱 브라우저 확장이 실행되고 있고 사용자의 브라우저에서 사용하도록 설정되어 있는지 확인합니다.

- **incognito, inPrivate 또는 프라이빗 모드** 일 때 사용자가 내 앱의 애플리케이션에 로그인하려고 하지 않았는지 확인합니다. 이러한 모드에서는 내 앱 확장이 지원되지 않습니다.

이전 방법이 통하지 않는 경우는 애플리케이션 쪽에서 변경이 일어나 일시적으로 애플리케이션과 Azure AD의 통합이 깨졌을 수 있습니다. 예를 들어, 애플리케이션 공급업체에서 수동 입력과 자동 입력에 대해 다르게 작동하는 스크립트를 페이지에 포함하는 바람에 자동화 통합이 깨지는 경우에 이 문제가 발생할 수 있습니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 애플리케이션 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 통합이 올바로 작동하지 않는 경우 최대한 신속하게 해결하도록 지원 사례를 열 수 있습니다.

또한 **이 애플리케이션의 공급업체와 연락된 경우는 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위한 작업을 함께 진행할 수 있도록** **Microsoft로 보내 주세요**. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../develop/v2-howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>애플리케이션의 로그인 페이지가 최근에 변경되었거나 추가 필드가 필요한지 확인

애플리케이션의 로그인 페이지가 최근에 변경된 경우는 그 때문에 통합이 깨질 수도 있습니다. 이런 예로는 애플리케이션 공급업체에서 로그인 필드, chaptcha 또는 다단계 인증을 경험에 추가하는 경우가 있습니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 애플리케이션 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 통합이 올바로 작동하지 않는 경우 최대한 신속하게 해결하도록 지원 사례를 열 수 있습니다.

또한 **이 애플리케이션의 공급업체와 연락된 경우는 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위한 작업을 함께 진행할 수 있도록** **Microsoft로 보내 주세요**. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../develop/v2-howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="capture-sign-in-fields-for-an-app"></a>앱의 로그인 필드 캡처

로그인 필드 캡처는 HTML 지원 로그인 페이지에서만 지원됩니다. Adobe Flash 또는 HTML을 지원하지 않는 다른 기술을 사용하는 페이지처럼 비표준 로그인 페이지에서는 지원되지 않습니다.

사용자 지정 앱의 로그인 필드는 두 가지 방법으로 캡처할 수 있습니다.

- **자동 로그인 필드 캡처** 는 사용자 이름 및 암호 필드에 ‘잘 알려진 DIV ID를 사용하는 경우’ 대부분의 HTML 지원 로그인 페이지에서 잘 작동합니다. 페이지의 HTML을 스크랩하여 특정 조건과 일치하는 DIV ID를 찾습니다. 메타데이터는 저장하여 나중에 앱에서 재생할 수 있습니다.

- **수동 로그인 필드 캡처** 는 앱 공급업체가 ‘로그인 입력 필드에 레이블을 지정하지 않는’ 경우에 사용됩니다. 수동 캡처는 공급업체가 ‘자동으로 검색할 수 없는 여러 필드를 렌더링’하는 경우에도 사용됩니다. Azure AD(Azure Active Directory)는 페이지에서 해당 필드가 있는 위치를 알려 주는 경우 로그인 페이지에 있는 것만큼 많은 필드에 대한 데이터를 저장할 수 있습니다.

일반적으로 자동 로그인 필드 캡처가 작동하지 않는 경우 수동 옵션을 사용해 보세요.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>앱의 로그인 필드를 자동으로 캡처

자동 로그인 필드 캡처를 사용하여 암호 기반 SSO를 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자로 로그인합니다.
2. 왼쪽의 탐색 창에서 **모든 서비스** 를 선택하여 Azure AD 확장을 엽니다.
3. 필터 검색 상자에 **Azure Active Directory** 를 입력한 다음, **Azure Active Directory** 를 선택합니다.
4. Azure AD 탐색 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
5. **모든 애플리케이션** 을 선택하여 앱 목록을 표시합니다.
   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 **모든 애플리케이션** 목록 맨 위에 있는 **필터** 컨트롤을 사용합니다. **표시** 옵션을 “모든 애플리케이션”으로 설정합니다.
6. SSO를 구성하려는 앱을 선택합니다.
7. 앱이 로드된 후 왼쪽의 탐색 창에서 **Single Sign-On** 을 선택합니다.
8. **암호 기반 로그온** 모드를 선택합니다.
9. 사용자가 로그인할 때 사용자 이름 및 암호를 입력하는 페이지의 URL인 **로그온 URL** 을 입력합니다. ‘제공한 URL에 대한 페이지에 로그인 필드가 표시되는지 확인합니다.’
10. **저장** 을 선택합니다.
    페이지에서 사용자 이름 및 암호 입력 상자가 자동으로 스크랩됩니다. 이제 Azure AD에서 내 앱 브라우저 확장을 사용하여 해당 앱으로 안전하게 암호를 전송할 수 있습니다.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>앱의 로그인 필드를 수동으로 캡처

로그인 필드를 수동으로 캡처하려면 내 앱 브라우저 확장이 설치되어 있어야 합니다. 또한 브라우저를 *inPrivate*, *incognito* 또는 ‘프라이빗’ 모드에서 실행할 수 없습니다.

수동 로그인 필드 캡처를 사용하여 앱에 대해 암호 기반 SSO를 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자로 로그인합니다.
2. 왼쪽의 탐색 창에서 **모든 서비스** 를 선택하여 Azure AD 확장을 엽니다.
3. 필터 검색 상자에 **Azure Active Directory** 를 입력한 다음, **Azure Active Directory** 를 선택합니다.
4. Azure AD 탐색 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
5. **모든 애플리케이션** 을 선택하여 앱 목록을 표시합니다.
   > [!NOTE]
   > 원하는 앱이 표시되지 않으면 **모든 애플리케이션** 목록 맨 위에 있는 **필터** 컨트롤을 사용합니다. **표시** 옵션을 “모든 애플리케이션”으로 설정합니다.
6. SSO를 구성하려는 앱을 선택합니다.
7. 앱이 로드된 후 왼쪽의 탐색 창에서 **Single Sign-On** 을 선택합니다.
8. **암호 기반 로그온** 모드를 선택합니다.
9. 사용자가 로그인할 때 사용자 이름 및 암호를 입력하는 페이지인 **로그온 URL** 을 입력합니다. ‘제공한 URL에 대한 페이지에 로그인 필드가 표시되는지 확인합니다.’
10. ***&lt;appname&gt;* 암호 Single Sign-On 설정 구성** 을 선택합니다.
11. **로그인 필드 수동 검색** 을 선택합니다.
12. **확인** 을 선택합니다.
13. **저장** 을 선택합니다.
14. 지침에 따라 내 앱을 사용합니다.

## <a name="troubleshoot-problems"></a>문제 해결

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>“해당 URL에서 로그인 필드를 찾을 수 없습니다” 오류가 표시됨

로그인 필드의 자동 검색에 실패하면 이 오류 메시지가 표시됩니다. 문제를 해결하려면 수동 로그인 필드 검색을 사용해 보세요. 이 문서의 [애플리케이션의 로그인 필드를 수동으로 캡처](#manually-capture-sign-in-fields-for-an-app) 섹션을 참조하세요.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>“Single Sign-On 구성을 저장할 수 없음” 오류가 표시됨

드문 경우지만 SSO 구성이 업데이트되지 않을 수 있습니다. 이 문제를 해결하려면 구성을 다시 저장해 보세요.

오류가 계속 표시되면 지원 사례를 엽니다. 이 문서의 [포털 알림 세부 정보 보기](#view-portal-notification-details) 및 [지원 엔지니어에게 알림 정보를 보내 도움받기](#send-notification-details-to-a-support-engineer-to-get-help) 섹션에 설명된 정보를 포함합니다.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>내 앱의 로그인 필드를 수동으로 검색할 수 없음

수동 검색이 작동하지 않는 경우 다음 동작을 관찰할 수 있습니다.

- 수동 캡처 프로세스는 작동하는 것 같지만 캡처된 필드가 잘못되었습니다.
- 캡처 프로세스가 실행될 때 올바른 필드가 강조 표시되지 않습니다.
- 캡처 프로세스를 실행하면 예상대로 앱의 로그인 페이지로 이동하지만 아무 작업도 수행되지 않습니다.
- 수동 캡처가 작동하는 것처럼 보이지만 사용자가 내 앱에서 앱으로 이동할 때 SSO가 발생하지 않습니다.

이러한 문제가 발생하는 경우 다음 작업을 수행합니다.

- 최신 버전의 내 앱 브라우저 확장을 ‘설치하고 사용하도록 설정’했는지 확인합니다.
- 캡처 프로세스 중에 브라우저가 *incognito*, *inPrivate* 또는 ‘프라이빗’ 모드가 아닌지 확인합니다. 이러한 모드에서는 내 앱 확장이 지원되지 않습니다.
- *incognito*, *inPrivate* 또는 ‘프라이빗 모드’일 때 사용자가 내 앱에서 앱에 로그인하려고 하지 않았는지 확인합니다.
- 수동 캡처 프로세스를 다시 시도합니다. 빨간색 표식이 올바른 필드 위에 있는지 확인합니다.
- 수동 캡처 프로세스가 응답하지 않는 것처럼 보이거나 로그인 페이지가 응답하지 않는 경우 수동 캡처 프로세스를 다시 시도합니다. 그러나 이번에는 프로세스를 완료한 후 F12 키를 눌러 브라우저의 개발자 콘솔을 엽니다. **콘솔** 탭을 선택합니다. **window.location=“&lt;앱을 구성할 때 지정한 로그인 URL&gt;”** 을 입력한 다음, Enter 키를 누릅니다. 그러면 페이지가 리디렉션되어 캡처 프로세스를 종료하고 캡처된 필드를 저장합니다.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>암호 기반 SSO 앱에 다른 사용자를 추가할 수 없음

암호 기반 SSO 앱은 사용자 수가 48명으로 제한됩니다. 따라서 앱당 사용자 이름/암호 쌍에 대한 키가 48개로 제한됩니다.
사용자를 더 추가하려면 다음 중 하나를 수행할 수 있습니다.

- 앱의 인스턴스 더 추가
- 앱을 더 이상 사용하지 않는 사용자를 먼저 제거

## <a name="request-support"></a>지원 요청

SSO를 설정하고 사용자를 할당할 때 오류 메시지가 표시되는 경우 지원 티켓을 엽니다. 이때 다음 정보를 최대한 많이 포함하세요.

- 상관 관계 오류 ID
- UPN(사용자 전자 메일 주소)
- TenantID
- 브라우저 종류
- 오류가 발생한 표준 시간대 및 시간/시간 프레임
- Fiddler 추적

### <a name="view-portal-notification-details"></a>포털 알림 세부 정보 보기

포털 알림의 세부 정보를 보려면 다음 단계를 수행합니다.

1. Azure Portal의 오른쪽 위에 있는 **알림** 아이콘(벨)을 선택합니다.
2. ‘오류’ 상태를 표시하는 알림을 선택합니다. 빨간색 “!”가 있습니다.
   > [!NOTE]
   > ‘성공’ 또는 ‘진행 중’ 상태의 알림은 선택할 수 없습니다. 
3. **알림 세부 정보** 창이 열립니다. 문제에 대해 알아보려면 해당 정보를 읽어 보세요.
4. 여전히 도움이 필요한 경우 정보를 지원 엔지니어 또는 제품 그룹과 공유합니다. **오류 복사** 상자 오른쪽에 있는 **복사** 아이콘을 선택하여 공유할 알림 세부 정보를 복사합니다.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>지원 엔지니어에게 알림 세부 정보를 보내 도움받기

신속하게 도움을 받으려면 이 섹션에 나열된 ‘모든’ 세부 정보를 지원팀과 공유해야 합니다. 해당 정보를 기록하려면 스크린샷을 만들거나 **오류 복사** 를 선택할 수 있습니다.

다음 정보는 각 알림 항목의 의미를 설명하고 예를 제공합니다.

#### <a name="essential-notification-items"></a>중요 알림 항목

- **제목**: 알림의 설명이 포함된 제목입니다.

   예: ‘애플리케이션 프록시 설정’

- **설명**: 작업의 결과로 발생한 문제입니다.

   예: ‘입력한 내부 URL이 다른 애플리케이션에서 이미 사용 중입니다.’

- **알림 ID**: 알림의 고유 ID입니다.

    예: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **클라이언트 요청 ID**: 브라우저에서 만든 특정 요청 ID입니다.

    예: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **타임스탬프 UTC**: 알림이 발생한 시간의 타임스탬프(UTC)입니다.

    예: *2017-03-23T19:50:43.7583681Z*

- **내부 트랜잭션 ID**: 시스템에서 오류를 찾는 데 사용되는 내부 ID입니다.

    예: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: 작업을 실행한 사용자입니다.

    예: *tperkins\@f128.info*

- **테넌트 ID**: 작업을 실행한 사용자가 속해 있는 테넌트의 고유 ID입니다.

    예: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **사용자 개체 ID**: 작업을 수행한 사용자의 고유 ID입니다.

    예: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>자세한 알림 항목

- **표시 이름**: (비어 있을 수 있음) 오류에 대한 보다 자세한 표시 이름입니다.

    예: ‘애플리케이션 프록시 설정’

- **상태**: 알림의 특정 상태입니다.

    예: ‘실패’

- **개체 ID**: (비어 있을 수 있음) 작업이 실행된 개체 ID입니다.

   예: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **세부 정보**: 작업의 결과로 발생한 문제에 대한 자세한 설명입니다.

    예: ‘내부 URL ‘<https://bing.com/>’이 이미 사용 중이므로 유효하지 않습니다.’

- **오류 복사**: **오류 복사** 텍스트 상자의 오른쪽에 있는 **복사 아이콘** 을 선택하여 지원팀에 도움이 되는 알림 세부 정보를 복사할 수 있습니다.

    예제:
    
    `{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}`

## <a name="next-steps"></a>다음 단계

- [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
- [내 앱 배포 계획](my-apps-deployment-plan.md)
