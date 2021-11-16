---
title: MSAL(퍼블릭 및 기밀 클라이언트 앱) | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)에서 퍼블릭 클라이언트 및 기밀 클라이언트 응용 프로그램에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 03c14170a1093913fe69af2407f587835a2bb76a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440928"
---
# <a name="public-client-and-confidential-client-applications"></a>퍼블릭 클라이언트 및 기밀 클라이언트 응용 프로그램

MSAL(Microsoft 인증 라이브러리)은 퍼블릭 클라이언트와 기밀 클라이언트라는 두 가지 형식의 클라이언트를 정의합니다. 두 클라이언트 형식은 권한 부여 서버를 사용하여 안전하게 인증하고 클라이언트 자격 증명의 기밀성을 유지하는 기능으로 구분됩니다. 반면 Azure ADAL(Azure Active Directory 인증 라이브러리)은 _인증 컨텍스트_(Azure Active Directory에 대한 연결)를 사용합니다.

- **기밀 클라이언트 응용 프로그램** 은 서버(웹앱, 웹 API 앱 또는 서비스/디먼 앱)에서 실행되는 앱입니다. 액세스하기 어려우며, 이러한 이유로 애플리케이션 비밀을 유지할 수 있습니다. 기밀 클라이언트는 구성 시간 비밀을 보유할 수 있습니다. 클라이언트의 각 인스턴스에는 고유한 구성(클라이언트 ID 및 클라이언트 암호 포함)이 있습니다. 이러한 값은 최종 사용자가 추출하기 어렵습니다. 웹앱은 가장 일반적인 기밀 클라이언트입니다. 클라이언트 ID는 웹 브라우저를 통해 노출되지만, 비밀은 백채널에서만 전달되고 직접 노출되지 않습니다.

  기밀 클라이언트 앱: 

  ![웹앱](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![디먼/서비스](media/msal-client-applications/daemon-service.png)

- **퍼블릭 클라이언트 응용 프로그램** 은 디바이스, 데스크톱 컴퓨터 또는 웹 브라우저에서 실행되는 앱입니다. 애플리케이션 비밀을 안전하게 유지하는 것으로 신뢰할 수 없으므로 사용자를 대신하여 웹 API에만 액세스합니다. 퍼블릭 클라이언트 흐름만 지원합니다. 퍼블릭 클라이언트는 구성 시간 비밀을 보유할 수 없으므로 클라이언트 비밀이 없습니다.

  퍼블릭 클라이언트 앱: 

  ![데스크톱 앱](media/msal-client-applications/desktop-app.png) ![브라우저 없는 API](media/msal-client-applications/browserless-app.png) ![모바일 앱](media/msal-client-applications/mobile-app.png)

MSAL.js에는 퍼블릭 및 기밀 클라이언트 앱이 분리되어 있지 않습니다. MSAL.js는 클라이언트 앱을 사용자 에이전트 기반 앱으로, 클라이언트 코드가 웹 브라우저와 같은 사용자 에이전트에서 실행되는 퍼블릭 클라이언트로 나타냅니다. 이러한 클라이언트는 브라우저 컨텍스트에 공개적으로 액세스할 수 있기 때문에 비밀을 저장하지 않습니다.

## <a name="comparing-the-client-types"></a>클라이언트 형식 비교

퍼블릭과 기밀 클라이언트 앱 간의 유사점과 차이점은 다음과 같습니다.

- 두 종류의 앱 모두 사용자 토큰 캐시를 유지하고 토큰을 자동으로 가져올 수 있습니다(토큰이 토큰 캐시에 이미 있는 경우). 기밀 클라이언트 앱에는 앱 자체의 토큰에 대한 앱 토큰 캐시도 있습니다.
- 두 가지 형식의 앱 모두 사용자 계정을 관리하고 사용자 토큰 캐시에서 계정을 가져오거나, 해당 식별자에서 계정을 가져오거나 계정을 제거할 수 있습니다.
- 퍼블릭 클라이언트 앱에는 토큰을 획득하는 4가지 방법(4개의 인증 흐름)이 있습니다. 기밀 클라이언트 앱에는 토큰을 획득하는 3가지 방법과 ID 공급자 권한 부여 엔드포인트의 URL을 계산하는 한 가지 방법이 있습니다. 자세한 내용은 [토큰 획득](msal-acquire-cache-tokens.md)을 참조하세요.

MSAL에서 클라이언트 ID(_애플리케이션 ID_ 또는 _앱 ID_ 라고도 함)는 애플리케이션을 생성할 때 한 번만 전달됩니다. 앱에서 토큰을 획득하는 경우 다시 전달할 필요가 없습니다. 이는 퍼블릭 및 비밀 클라이언트 앱 모두에 적용됩니다. 기밀 클라이언트 앱의 생성자도 클라이언트 자격 증명으로 전달됩니다. 이는 ID 공급자와 공유하는 암호입니다.

## <a name="next-steps"></a>다음 단계

애플리케이션 구성 및 인스턴스화에 대한 자세한 내용은 다음을 참조하세요.

- [클라이언트 응용 프로그램 구성 옵션](msal-client-application-configuration.md)
- [MSAL.NET을 사용하여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)
- [MSAL.js를 사용하여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)
