---
title: 웹 API를 호출하는 데스크톱 앱 빌드 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 데스크톱 앱을 빌드하는 방법 알아보기(개요)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 59c14934d6cbcc8e86a111e14be5f57135604f0b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131436045"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>시나리오: Web API를 호출하는 데스크톱 앱

웹 API를 호출하는 데스크톱 앱을 빌드하는 데 필요한 모든 것을 알아보세요.

## <a name="get-started"></a>시작

아직 없는 경우 빠른 시작을 완료하여 애플리케이션을 먼저 만듭니다.

- [빠른 시작: Windows 데스크톱 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](./quickstart-v2-windows-desktop.md)
- [빠른 시작: UWP 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](./quickstart-v2-uwp.md)
- [빠른 시작: macOS 네이티브 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](./quickstart-v2-ios.md)
- [빠른 시작: Node.js 및 Electron 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](./quickstart-v2-nodejs-desktop.md)

## <a name="overview"></a>개요

데스크톱 애플리케이션을 작성하고, 사용자가 이 애플리케이션에 로그인하여 Microsoft Graph, 다른 Microsoft API 또는 자체 웹 API를 호출하도록 하고자 합니다. 여러 옵션이 있습니다.

- 다음과 같은 경우 대화형 토큰 획득을 사용할 수 있습니다.

  - 데스크톱 애플리케이션이 그래픽 컨트롤을 지원하는 경우(예: Windows.Form 애플리케이션, WPF 애플리케이션 또는 macOS 네이티브 애플리케이션인 경우)
  - .NET Core 애플리케이션이며 Azure AD(Azure Active Directory)와의 인증 상호 작용이 시스템 브라우저에서 이루어지는 데 동의한 경우
  - 또는 Chromium 인스턴스에서 실행되는 Node.js Electron 애플리케이션인 경우입니다.

- Windows에서 호스트하는 애플리케이션의 경우, Windows 도메인에 가입된 컴퓨터 또는 Azure AD에 가입된 컴퓨터에서 실행되는 애플리케이션이 Windows 통합 인증을 사용하여 자동으로 토큰을 획득하는 것도 가능합니다.
- 마지막으로, 이 방법은 권장되지는 않지만 공용 클라이언트 애플리케이션에서 사용자 이름 및 암호를 사용할 수 있습니다. 사용자 이름 및 암호를 사용하는 방법은 DevOps와 같은 몇 가지 시나리오에서 여전히 필요합니다. 이 방법을 사용하면 애플리케이션에 제한 사항이 적용됩니다. 예를 들어, [다단계 인증](../authentication/concept-mfa-howitworks.md)(조건부 액세스)을 수행해야 하는 사용자는 로그인할 수 없습니다. 애플리케이션에서 SSO(Single Sign-On)를 사용할 수 없습니다.

  이 흐름은 최신 인증 원칙에 반하며, 레거시 용도로만 제공됩니다.

  ![데스크톱 애플리케이션](media/scenarios/desktop-app.svg)

- 이식 가능 명령줄 도구를 작성하는 경우(예: Linux 또는 Mac에서 실행되는 .NET Core 애플리케이션) 인증을 시스템 브라우저에 위임하는 데 동의하면 대화형 인증을 사용할 수 있습니다. .NET Core는 [웹 브라우저](https://aka.ms/msal-net-uses-web-browser)를 제공하지 않으므로 인증은 시스템 브라우저에서 이루어집니다. 그렇게 하지 않을 경우 가장 좋은 옵션은 디바이스 코드 흐름을 사용하는 것입니다. 이 흐름은 IoT 애플리케이션과 같이 브라우저가 없는 애플리케이션에서도 사용됩니다.

  ![브라우저가 없는 애플리케이션](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>특수 적용 사항

데스크톱 애플리케이션에는 몇 가지 특수 적용 사항이 있으며, 애플리케이션이 대화형 인증을 사용하는지 여부에 따라 달라집니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 등록](scenario-desktop-app-registration.md)으로 이동합니다.
