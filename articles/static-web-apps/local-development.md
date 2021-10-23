---
title: Azure Static Web Apps에 대한 로컬 개발 설정
description: Azure Static Web Apps에 대한 로컬 개발 환경을 설정하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 7419eea1503d8d0692bd1b112226c8ce626d9776
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261579"
---
# <a name="set-up-local-development-for-azure-static-web-apps"></a>Azure Static Web Apps에 대한 로컬 개발 설정

Azure Static Web Apps 사이트는 클라우드에 게시될 때 동일한 애플리케이션인 것처럼 함께 작동하는 많은 서비스를 함께 연결합니다. 이러한 서비스에는 다음이 포함됩니다.

- 정적 웹앱
- Azure Functions API
- 인증 및 권한 부여 서비스
- 라우팅 및 구성 서비스

이러한 서비스는 서로 통신해야 하며, Azure Static Web Apps는 클라우드에서 이 통합을 처리합니다.

그러나, 로컬로 실행하는 경우 이러한 서비스는 자동으로 함께 연결되지 않습니다.

Azure에서 제공하는 것과 유사한 환경을 제공하기 위해 [Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli)는 다음과 같은 서비스를 제공합니다.

- 로컬 정적 사이트 서버
- 프런트 엔드 프레임워크 개발 서버에 대한 프록시
- API 엔드포인트에 대한 프록시 - Azure Functions Core Tools를 통해 사용 가능
- 모의 인증 및 권한 부여 서비스
- 로컬 경로 및 구성 설정 적용

> [!NOTE]
> 일반적으로 프런트 엔드 프레임워크를 사용하여 빌드된 사이트에서는 `api` 경로에서 요청을 올바르게 처리하려면 프록시 구성 설정이 필요합니다. Azure Static Web Apps CLI를 사용하는 경우 프록시 위치 값은 `/api`이고 CLI를 사용하지 않는 경우 값은 `http://localhost:7071/api`입니다.

## <a name="how-it-works"></a>작동 방식

다음 차트에서는 요청을 로컬로 처리하는 방법을 보여 줍니다.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure Static Web App CLI 요청 및 응답 흐름":::

> [!IMPORTANT]
> `http://localhost:4280`으로 이동하여 CLI에서 제공하는 애플리케이션에 액세스합니다.

- 포트 `4280`으로 전송되는 **요청** 은 요청 형식에 따라 적합한 서버로 전달됩니다.

- HTML 또는 CSS와 같은 **정적 콘텐츠** 요청은 내부 CLI 정적 콘텐츠 서버에서 처리되거나 디버깅을 위해 프런트 엔드 프레임워크 서버에서 처리됩니다.

- **인증 및 권한 부여** 요청은 애플리케이션에 가짜 ID 프로필을 제공하는 에뮬레이터에서 처리됩니다.

- **Functions Core Tools 런타임** 은 사이트의 API에 대한 요청을 처리합니다.

- 모든 서비스의 **응답** 은 모두 단일 애플리케이션처럼 브라우저에 반환됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- **기존 Azure Static Web Apps 사이트**: 사이트가 없는 경우 [vanilla-api](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) 스타터 앱으로 시작합니다.
- **npm이 있는 [Node.js](https://nodejs.org)** : [npm](https://www.npmjs.com/)에 대한 액세스를 포함하는 [Node.js LTS](https://nodejs.org) 버전을 실행합니다.
- **[Visual Studio Code](https://code.visualstudio.com/)** : API 애플리케이션을 디버깅하는 데 사용되지만 CLI에는 필요하지 않습니다.
- **[Azure Functions Core Tools:](https://github.com/Azure/azure-functions-core-tools#installing)** API를 로컬로 실행하는 데 필요합니다.

## <a name="get-started"></a>시작

기존 Azure Static Web Apps 사이트의 루트 폴더에 대한 터미널을 엽니다.

1. CLI를 설치합니다.

    ```console
    npm install -g @azure/static-web-apps-cli
    ```

1. 애플리케이션에 필요한 경우 앱을 빌드합니다.

    `npm run build`를 실행하거나 프로젝트에 해당하는 명령을 실행합니다.

1. 앱의 출력 디렉터리로 변경합니다. 출력 폴더는 _빌드_ 또는 유사한 이름으로 지정되는 경우가 많습니다.

1. CLI를 시작합니다.

    ```console
    swa start
    ```

1. `http://localhost:4280`으로 이동하여 브라우저에서 앱을 봅니다.

### <a name="other-ways-to-start-the-cli"></a>CLI를 시작하는 다른 방법

| Description | 명령 |
|--- | --- |
| 특정 폴더 제공 | `swa start ./output-folder` |
| 실행 중인 프레임워크 개발 서버 사용 | `swa start http://localhost:3000` |
| 폴더에서 함수 앱 시작 | `swa start ./output-folder --api-location ./api` |
| 실행 중인 함수 앱 사용 | `swa start ./output-folder --api-location http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>인증 및 권한 부여 에뮬레이션

Static Web Apps CLI는 Azure에 구현된 [보안 흐름](./authentication-authorization.md)을 에뮬레이트합니다. 사용자가 로그인할 때 앱에 반환되는 가짜 ID 프로필을 정의할 수 있습니다.

예를 들어, `/.auth/login/github`로 이동하려고 하면 ID 프로필을 정의할 수 있는 페이지가 반환됩니다.

> [!NOTE]
> 에뮬레이터는 GitHub뿐만 아니라 모든 보안 공급자와 함께 작동합니다.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="로컬 인증 및 권한 부여 에뮬레이터":::

에뮬레이터는 다음과 같은 [클라이언트 보안 주체](./user-information.md#client-principal-data) 값을 제공할 수 있는 페이지를 제공합니다.

| 값 | 설명 |
| --- | --- |
| **사용자 이름** | 보안 공급자와 연결된 계정 이름입니다. 이 값은 클라이언트 보안 주체의 `userDetails` 속성으로 나타나며 값을 제공하지 않으면 자동으로 생성됩니다. |
| **사용자 ID** | CLI에서 자동으로 생성되는 값입니다.  |
| **역할** | 각 이름이 새 줄에 있는 역할 이름 목록입니다.  |

로그인되면 다음을 수행합니다.

- `/.auth/me` 엔드포인트 또는 함수 엔드포인트를 사용하여 사용자의 [클라이언트 보안 주체](./user-information.md)를 검색할 수 있습니다.

- `/.auth/logout`로 이동하면 클라이언트 보안 주체가 지워지고 모의 사용자가 로그아웃됩니다.

## <a name="debugging"></a>디버깅

정적 웹앱에는 두 가지 디버깅 컨텍스트가 있습니다. 첫 번째는 정적 콘텐츠 사이트용이고, 두 번째는 API 함수용입니다. Static Web Apps CLI가 이러한 컨텍스트 중 하나 또는 둘 모두에 대해 개발 서버를 사용할 수 있도록 하여 로컬 디버깅을 수행할 수 있습니다.

다음 단계에서는 두 가지 디버깅 컨텍스트 모두에 대해 개발 서버를 사용하는 일반적인 시나리오를 설명합니다.

1. 정적 사이트 개발 서버를 시작합니다. 이 명령은 사용 중인 프런트 엔드 프레임워크에만 적용되지만, `npm run build`, `npm start`, 또는 `npm run dev`와 같은 명령의 형태로 제공되는 경우가 많습니다.

1. Visual Studio Code에서 API 애플리케이션 폴더를 열고 디버깅 세션을 시작합니다.

1. 다음 명령을 사용하여 Static Web Apps CLI를 시작합니다.


    ```console
    swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api-location http://localhost:7071
    ```

    `<DEV-SERVER-PORT-NUMBER>`를 개발 서버의 포트 번호로 대체합니다.

다음 스크린샷에는 일반적인 디버깅 시나리오를 위한 터미널을 보여줍니다.

정적 콘텐츠 사이트는 `npm run dev`를 통해 실행되고 있습니다.

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="정적 사이트 개발 서버":::

Azure Functions API 애플리케이션이 Visual Studio Code에서 디버그 세션을 실행하고 있습니다.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API 디버깅":::

Static Web Apps CLI는 두 개발 서버를 사용하여 시작됩니다.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps CLI 터미널":::

이제 포트 `4280`을 통과하는 요청이 정적 콘텐츠 개발 서버 또는 API 디버깅 세션으로 라우팅됩니다.

포트 및 서버 주소를 사용자 지정하는 방법에 대한 지침과 다양한 디버깅 시나리오에 대한 자세한 내용은 [Azure Static Web Apps CLI 리포지토리](https://github.com/Azure/static-web-apps-cli)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [애플리케이션 구성](configuration.md)
