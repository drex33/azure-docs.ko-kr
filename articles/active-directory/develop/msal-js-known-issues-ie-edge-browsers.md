---
title: Internet Explorer 및 Microsoft Edge의 문제(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Internet Explorer 및 Microsoft Edge 브라우저에서 MSAL.js(JavaScript용 Microsoft 인증 라이브러리)를 사용하는 경우의 알려진 문제에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ef1cce7905e07c1fa407c0194d585bb196ecdfd6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232207"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 및 Microsoft Edge 브라우저의 알려진 문제(MSAL.js)

## <a name="issues-due-to-security-zones"></a>보안 영역으로 인한 문제
IE 및 Microsoft Edge의 인증 관련 문제에 대해 여러 번 보고받았습니다(‘Microsoft Edge 브라우저 버전을 40.15063.0.0으로’ 업데이트한 이후). 이 문제를 추적하고 있으며 Microsoft Edge 팀에 알렸습니다. Microsoft Edge에서 해결 방법을 찾고 있으며 다음에서는 자주 발생하는 문제 및 구현할 수 있는 가능한 해결 방법에 대한 설명을 제공합니다.

### <a name="cause"></a>원인
이 문제는 대부분 다음과 같은 이유로 발생합니다. 세션 스토리지와 로컬 스토리지가 Microsoft Edge 브라우저의 보안 영역에 의해 분할됩니다. 이 특정 버전의 Microsoft Edge에서 애플리케이션이 영역 간에 리디렉션되는 경우 세션 스토리지와 로컬 스토리지가 지워집니다. 특히 세션 스토리지는 일반 브라우저 탐색에서 지워지며, 세션 및 로컬 스토리지는 모두 브라우저의 InPrivate 모드에서 지워집니다. MSAL.js는 세션 스토리지에 특정 상태를 저장하며 인증 흐름 중에 이 상태를 확인합니다. 세션 스토리지를 지우면 이 상태가 사라지므로 환경이 손상됩니다.

### <a name="issues"></a>문제

- **인증 중 무한 리디렉션 루프 및 페이지 다시 로드** 사용자가 Microsoft Edge에서 애플리케이션에 로그인하면 AAD 로그인 페이지에서 다시 리디렉션되고, 페이지 다시 로드가 반복되는 무한 리디렉션 루프에 갇히게 됩니다. 일반적으로 이 문제에는 세션 스토리지의 `invalid_state` 오류가 수반됩니다.

- **무제한 토큰 획득 루프 및 AADSTS50058 오류**. Microsoft Edge에서 실행되는 애플리케이션에서 리소스의 토큰을 획득하려고 시도하는 경우 애플리케이션이 네트워크 추적에서 AAD의 다음 오류와 함께 토큰 획득 호출의 무한 루프에 갇힐 수 있습니다.

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **팝업 창을 통해 로그인을 사용하여 인증할 때 팝업 창이 닫히지 않거나 멈춰버립니다.** 자격 증명을 입력하고 로그인한 후에 Microsoft Edge 또는 IE(InPrivate)의 팝업 창을 통해 인증할 때 보안 영역에 있는 여러 도메인이 탐색에 포함되는 경우 `MSAL.js`에서 팝업 창에 대한 핸들이 손실되므로 팝업 창이 닫히지 않습니다.

### <a name="update-fix-available-in-msaljs-023"></a>업데이트: MSAL.js 0.2.3에서 사용할 수 있는 수정 사항
인증 리디렉션 루프 문제에 대한 수정 사항은 [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)에 릴리스되었습니다. 이 수정 사항을 사용하려면 MSAL.js 구성에서 `storeAuthStateInCookie` 플래그를 사용하도록 설정합니다. 기본적으로 이 플래그는 false로 설정됩니다.

`storeAuthStateInCookie` 플래그를 사용하는 경우 MSAL.js는 인증 흐름의 유효성 검사에 필요한 요청 상태를 저장하기 위해 브라우저 쿠키를 사용합니다.

> [!NOTE]
> 이 수정 사항은 아직 `msal-angular` 및 `msal-angularjs` 래퍼에 사용할 수 없습니다. 이 수정 사항은 팝업 창에서 발생하는 문제를 해결하지 않습니다.

아래의 해결 방법을 사용하세요.

#### <a name="other-workarounds"></a>기타 해결 방법
문제가 특정 버전의 Microsoft Edge 브라우저에서만 발생하는지와 다른 브라우저에서도 발생하는지 테스트한 다음에 다음 해결 방법을 채택해야 합니다.
1. 이 문제를 해결하기 위한 첫 번째 단계로, 인증 흐름의 리디렉션과 관련된 애플리케이션 도메인 및 기타 사이트를 브라우저의 보안 설정에서 신뢰할 수 있는 사이트로 추가하여 동일한 보안 영역에 속하도록 해야 합니다.
이렇게 하려면 다음 단계를 따르십시오.
    - **Internet Explorer** 를 열고 오른쪽 위 모퉁이에 있는 **설정**(기어 아이콘)을 클릭합니다.
    - **인터넷 옵션** 을 선택합니다.
    - **보안** 탭을 선택합니다.
    - **신뢰할 수 있는 사이트** 옵션 아래의 **사이트** 단추를 클릭하고 열리는 대화 상자에 URL을 추가합니다.

2. 앞서 언급했듯이 정기적으로 탐색하는 동안에는 세션 스토리지만 지워지므로 로컬 스토리지를 대신 사용하도록 MSAL.js를 구성할 수 있습니다. MSAL을 초기화하는 동안 로컬 스토리지를 `cacheLocation` 구성 매개 변수로 설정할 수 있습니다.

참고로 세션 및 로컬 스토리지가 모두 지워지므로 위 해결 방법으로는 InPrivate 브라우징에 대한 문제가 해결되지 않습니다.

## <a name="issues-due-to-popup-blockers"></a>팝업 차단 기능으로 인한 문제

IE 또는 Microsoft Edge에서 팝업이 차단되는 경우가 있습니다. 예를 들어 [다단계 인증](../authentication/concept-mfa-howitworks.md) 중에 두 번째 팝업이 발생하는 경우입니다. 브라우저에서 팝업 창을 한 번 또는 항상 허용하도록 하는 경고가 표시됩니다. 허용하도록 선택하면 브라우저에서 팝업 창이 자동으로 열리고 해당 창에 대한 `null` 핸들이 반환됩니다. 결과적으로 라이브러리에는 창에 대한 핸들이 없으며 팝업 창을 닫을 수 있는 방법이 없습니다. Chrome에서는 팝업 창을 허용하라는 메시지가 사용자에게 표시되면 동일한 문제가 발생하지 않습니다. Chrome에서 팝업 창이 자동으로 열리지 않기 때문입니다.

**해결 방법** 은 개발자가 앱 사용을 시작하기 전에 IE 및 Microsoft Edge에서 팝업을 허용하여 이 문제를 방지하는 것입니다.

## <a name="next-steps"></a>다음 단계
[Internet Explorer에서 MSAL.js를 사용](msal-js-use-ie-browser.md)하는 것에 대해 자세히 알아보세요.
