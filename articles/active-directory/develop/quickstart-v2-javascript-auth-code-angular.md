---
title: '빠른 시작: 인증 코드를 사용하여 JavaScript Angular SPA(단일 페이지 앱)에서 사용자를 로그인하고 Microsoft Graph 호출 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 JavaScript Angular SPA(단일 페이지 애플리케이션)에서 인증 코드 흐름을 사용하고 Microsoft Graph를 호출하여 개인 계정, 회사 및 학교 계정의 사용자에 로그인하는 방법을 알아봅니다.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/12/2021
ROBOTS: NOINDEX
ms.author: jamesmantu
ms.custom: aaddev, "scenarios:getting-started", "languages:JavaScript", devx-track-js, mode-other
ms.openlocfilehash: 784e3cdd85c3cbc0db089a3f2617975cc8077b5f
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138036336"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>빠른 시작: 인증 코드 흐름을 사용하여 Angular SPA에 로그인하고 액세스 토큰을 가져옵니다.


> [!div renderon="docs"]
> 환영합니다! 아마도 기대했던 페이지는 아닐 것입니다. 수정 작업을 진행하는 동안 이 링크를 통해 올바른 문서로 이동해야 합니다.
>
> > [빠른 시작: 사용자 로그인이 포함된 Angular 단일 페이지 앱](single-page-app-quickstart.md?pivots=devlang-angular)
> 
> 이 문제를 해결하는 동안 불편을 끼쳐 드려 죄송하며 양해해 주셔서 감사합니다.
 
> [!div renderon="portal" class="sxs-lookup"]
> 이 빠른 시작에서는 인증 코드 흐름을 사용하여 JavaScript Angular SPA(단일 페이지 애플리케이션)에서 사용자를 로그인하고 Microsoft Graph를 호출할 수 있는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 코드 샘플은 Microsoft Graph API 또는 웹 API를 호출하기 위한 액세스 토큰을 가져오는 방법을 보여줍니다.
> 
> 자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.
> 
> 이 빠른 시작에서는 인증 코드 흐름과 함께 MSAL Angular v2를 사용합니다.
> 
> ## <a name="prerequisites"></a>사전 요구 사항
> 
> * Azure 구독 - [체험용 Azure 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> * [Node.JS](https://nodejs.org/en/download/)
> * [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기
> 
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 `http://localhost:4200/`의 **리디렉션 URI** 를 추가합니다.
> 
> >[!div class="nextstepaction"]
> >[이러한 변경 내용 적용]()
> 
> > [!div class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-javascript/green-check.png) 이러한 > 특성을 사용하여 애플리케이션을 구성합니다.
> 
>  #### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.
> 
> Node.js를 사용하여 웹 서버에서 프로젝트 실행
> 
> >[!div class="nextstepaction"]
> >[코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)
> 
> > [!div class="sxs-lookup"]
> > > [!NOTE]
> > > `Enter_the_Supported_Account_Info_Here`
> 
> 
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 
> 앱 속성 값을 사용하여 프로젝트를 구성했습니다.
> 
> #### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행
> 
> Node.js를 사용하여 웹 서버에서 프로젝트 실행:
> 
> 1. 서버를 시작하려면 프로젝트의 디렉터리 내에서 다음 명령을 실행합니다.
>     ```console
>     npm install
>     npm start
>     ```
> 1. [https://www.microsoft.com]\(`http://localhost:4200/`) 로 이동합니다.
> 
> 1. **로그인** 을 선택하여 로그인 프로세스를 시작한 다음, Microsoft Graph API를 호출합니다.
> 
>     사용자가 처음으로 로그인하면 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다. 성공적으로 로그인한 후 **프로필** 단추를 클릭하여 페이지에 사용자 정보를 표시합니다.
> 
> ## <a name="more-information"></a>자세한 정보
> 
> ### <a name="how-the-sample-works"></a>샘플 작동 방법
> 
> ![단일 페이지 애플리케이션의 인증 코드 흐름을 보여주는 다이어그램.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)
> 
> ### <a name="msaljs"></a>msal.js
> 
> MSAL.js 라이브러리는 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청합니다.
> 
> Node.js가 설치된 경우 Node.js 패키지 관리자(npm)를 사용하여 최신 버전을 다운로드할 수 있습니다.
> 
> ```console
> npm install @azure/msal-browser @azure/msal-angular@2
> ```
> 
> ## <a name="next-steps"></a>다음 단계
> 
> vanilla JavaScript를 사용하여 인증 코드 흐름 애플리케이션을 빌드하는 방법에 대한 자세한 단계별 가이드는 다음 자습서를 참조하세요.
> 
> > [!div class="nextstepaction"]
> > [MS Graph에 로그인하고 호출하는 자습서](./tutorial-v2-javascript-auth-code.md)
