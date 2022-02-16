---
title: '빠른 시작: Python 웹앱에 Microsoft로 로그인 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Python 웹앱이 사용자를 로그인하고, Microsoft ID 플랫폼에서 액세스 토큰을 가져오고, Microsoft Graph API를 호출하는 방법에 대해 알아봅니다.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: abpati
ms.custom: aaddev, devx-track-python, "scenarios:getting-started", "languages:Python", mode-api
ms.openlocfilehash: 2e20938c33ddee1d48d3fd4eae930940de55141e
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138039311"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>빠른 시작: Python 웹앱에 Microsoft로 로그인 추가


> [!div renderon="docs"]
> 환영합니다! 아마도 기대했던 페이지는 아닐 것입니다. 수정 작업을 진행하는 동안 이 링크를 통해 올바른 문서로 이동해야 합니다.
>
> > [빠른 시작: 사용자 로그인이 포함된 Python 웹앱](web-app-quickstart.md?pivots=devlang-python)
> 
> 이 문제를 해결하는 동안 불편을 끼쳐 드려 죄송하며 양해해 주셔서 감사합니다.

> [!div renderon="portal" class="sxs-lookup"]
> 이 빠른 시작에서는 Python 웹 애플리케이션이 사용자를 로그인하고 Microsoft Graph API를 호출할 액세스 토큰을 가져오는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 개인 Microsoft 계정 또는 Azure AD(Azure Active Directory) 조직의 계정이 있는 사용자는 애플리케이션에 로그인할 수 있습니다.
> 
> 자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.
> 
> ## <a name="prerequisites"></a>필수 구성 요소
> 
> - 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Python 2.7+](https://www.python.org/downloads/release/python-2713) 또는 [Python 3+](https://www.python.org/downloads/release/python-364/)
> - [Flask](http://flask.pocoo.org/), [Flask-세션](https://pypi.org/project/Flask-Session/), [요청](https://requests.kennethreitz.org/en/master/)
> - [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 
> 이 빠른 시작의 코드 샘플이 작동하려면 다음을 수행합니다.
> 
> 1. 회신 URL을 `http://localhost:5000/getAToken`으로 입력합니다.
> 1. 클라이언트 비밀을 만듭니다.
> 1. Microsoft Graph API의 User.ReadBasic.All 위임된 권한을 추가합니다.
> 
> > [!div class="nextstepaction"]
> > [이러한 변경 내용 적용]()
> 
> > [!div class="alert alert-info"]
> > ![이미 구성됨](./media/quickstart-v2-aspnet-webapp/green-check.png) 이 특성을 사용하여 애플리케이션을 구성합니다.
> 
> #### <a name="step-2-download-your-project"></a>2단계: 프로젝트 다운로드
> 
> 프로젝트를 다운로드하고 zip 파일을 루트 폴더에 가까운 로컬 폴더(예제: **C:\Azure-Samples**)로 추출합니다.
> > [!div class="nextstepaction"]
> > [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)
> 
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> 
> #### <a name="step-3-run-the-code-sample"></a>3단계: 코드 샘플 실행
> 
> 1. 다음과 같이 pip를 사용하여 MSAL Python 라이브러리, Flask 프레임워크, 서버 쪽 세션 관리 및 요청을 위한 Flask 세션을 설치해야 합니다.
> 
>     ```shell
>     pip install -r requirements.txt
>     ```
> 
> 2. 셸 또는 명령줄에서 `app.py`를 실행합니다.
> 
>     ```shell
>     python app.py
>     ```
> 
>    > [!IMPORTANT]
>    > 이 빠른 시작 애플리케이션에서는 클라이언트 비밀을 사용하여 자체를 기밀 클라이언트로 식별합니다. 클라이언트 비밀은 보안상의 이유로 프로젝트 파일에 일반 텍스트로 추가되므로, 이 애플리케이션을 프로덕션 애플리케이션으로 사용하는 방안을 고려하기 전에 클라이언트 비밀 대신 인증서를 사용하는 것이 좋습니다. 인증서를 사용하는 방법에 대한 자세한 내용은 [다음 지침](./active-directory-certificate-credentials.md)을 참조하세요.
> 
> ## <a name="more-information"></a>자세한 정보
> 
> ### <a name="how-the-sample-works"></a>샘플 작동 방법
> ![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-python-webapp/python-quickstart.svg)
> 
> ### <a name="getting-msal"></a>MSAL 가져오기
> MSAL은 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 라이브러리입니다.
> Pip을 사용하여 애플리케이션에 MSAL Python을 추가할 수 있습니다.
> 
> ```Shell
> pip install msal
> ```
> 
> ### <a name="msal-initialization"></a>MSAL 초기화
> MSAL을 사용할 파일 맨 위에 다음 코드를 추가하여 MSAL Python에 대한 참조를 추가할 수 있습니다.
> 
> ```Python
> import msal
> ```
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>다음 단계
> 
> 여러 부분으로 구성된 시나리오 시리즈에서 사용자를 로그인하는 웹앱에 대해 자세히 알아보세요.
> 
> > [!div class="nextstepaction"]
> > [시나리오: 사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
