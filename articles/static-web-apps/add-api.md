---
title: Azure Functions를 사용하여 Azure Static Web Apps에 API 추가
description: Azure Functions를 사용하여 정적 웹앱에 서버리스 API를 추가하여 Azure Static Web Apps를 시작합니다.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/14/2021
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 568117bf629117e42af177845c9444996853f6c2
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067215"
---
# <a name="add-an-api-to-azure-static-web-apps-with-azure-functions"></a>Azure Functions를 사용하여 Azure Static Web Apps에 API 추가

Azure Functions에서 제공하는 Azure Static Web Apps에 서버리스 API를 추가할 수 있습니다. 이 문서에서는 Azure Static Web Apps 사이트에 API를 추가하고 배포하는 방법을 보여 줍니다.

> [!NOTE]
> 정적 Web Apps에서 기본적으로 제공되는 함수는 보안 API 엔드포인트를 제공하도록 미리 구성되며 HTTP 트리거 함수만 지원합니다. 독립형 Azure Functions 앱과 어떻게 다른지에 대한 자세한 내용은 [Azure Functions를 통한 API 지원](apis.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정
  - 계정이 없으면 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code용 [Azure Static Web Apps 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- 프런트 엔드 앱 및 API를 실행하기 위한 [Node.js](https://nodejs.org/download/)

## <a name="create-the-static-web-app"></a>정적 웹앱 만들기

API를 추가하기 전에 Azure Static Web Apps에 프런트 엔드 애플리케이션을 만들고 배포합니다. 이미 배포한 기존 앱을 사용하거나 [Azure Static Web Apps로 첫 번째 정적 사이트 빌드](getting-started.md) 빠른 시작에 따라 앱을 만듭니다.

Visual Studio Code에서 앱 리포지토리의 루트를 엽니다. 폴더 구조에는 프런트 엔드 앱의 원본과 _.github/workflows_ 폴더의 Static Web Apps GitHub 워크플로가 포함됩니다.

```Files
├── .github
│   └── workflows
│       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
│
└── (folders and files from your static web app)
```

## <a name="create-the-api"></a>API 만들기

정적 웹앱의 API에 대한 Azure Functions 프로젝트를 만듭니다. 기본적으로 Static Web Apps Visual Studio Code 확장은 리포지토리의 루트에 있는 _api_ 폴더에 프로젝트를 만듭니다.

1. <kbd>F1</kbd> 키를 눌러 명령 팔레트를 엽니다.

1. **Azure Static Web Apps: HTTP 함수 만들기...** 를 선택합니다. Azure Functions 확장을 설치하라는 메시지가 표시되면 설치하고 이 명령을 다시 실행합니다.

1. 메시지가 표시되면 다음 값을 입력합니다.

    | 프롬프트 | 값 |
    | --- | --- |
    | 언어 선택 | **JavaScript** |
    | 함수 이름 제공 | **message** |

    HTTP 트리거 함수를 사용하여 Azure Functions 프로젝트를 생성합니다. 이제 앱에 다음 예와 유사한 프로젝트 구조가 있습니다.

    ```Files
    ├── .github
    │   └── workflows
    │       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
    │
    ├── api
    │   ├── message
    │   │   ├── function.json
    │   │   └── index.js
    │   ├── host.json
    │   ├── local.settings.json
    │   └── package.json
    │
    └── (folders and files from your static web app)
    ```

1. 다음으로 `message` 함수를 변경하여 프런트 엔드에 메시지를 반환합니다. 다음 코드로 _api/message/index.js_ 의 함수를 업데이트합니다.

    ```javascript
    module.exports = async function (context, req) {
        context.res.json({
            text: "Hello from the API"
        });
    };
    ```

> [!TIP]
> **Azure Static Web Apps: HTTP 함수 만들기...** 명령을 다시 실행하여 더 많은 API 함수를 추가할 수 있습니다.

## <a name="update-the-frontend-app-to-call-the-api"></a>API를 호출하도록 프런트 엔드 앱 업데이트

`/api/message`에서 API를 호출하고 응답 메시지를 표시하도록 프런트 엔드 앱을 업데이트하세요.

빠른 시작을 사용하여 앱을 만든 경우 다음 지침을 사용하여 업데이트를 적용합니다.

# <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

_src/index.html_ 파일의 콘텐츠를 다음 코드로 업데이트하여 API 함수에서 텍스트를 가져오고 화면에 표시합니다.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Vanilla JavaScript App</title>
</head>

<body>
    <main>
    <h1>Vanilla JavaScript App</h1>
    <p>Loading content from the API: <b id="name">...</b></p>
    </main>

    <script>
    (async function() {
        const { text } = await( await fetch(`/api/message`)).json();
        document.querySelector('#name').textContent = text;
    }())
    </script>
</body>

</html>
```

# <a name="angular"></a>[Angular](#tab/angular)

1. 앱에서 `HttpClient`를 사용하도록 설정하려면 다음 코드로 _src/app/app.module.ts_ 의 콘텐츠를 업데이트하세요.

    ```typescript
    import { BrowserModule } from "@angular/platform-browser";
    import { NgModule } from "@angular/core";
    import { HttpClientModule } from '@angular/common/http';
    
    import { AppComponent } from "./app.component";
    
    @NgModule({
      declarations: [AppComponent],
      imports: [BrowserModule, HttpClientModule],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

1. _src/app/app.component.ts_ 의 콘텐츠를 다음 코드로 업데이트하여 API 함수에서 텍스트를 인출하고 화면에 표시합니다.

    ```typescript
    import { HttpClient } from '@angular/common/http';
    import { Component } from '@angular/core';
    
    @Component({
      selector: 'app-root',
      template: `<div>{{message}}</div>`,
    })
    export class AppComponent {
      message = '';
    
      constructor(private http: HttpClient) {
        this.http.get('/api/message')
          .subscribe((resp: any) => this.message = resp.text);
      }
    }
    ```

# <a name="react"></a>[React](#tab/react)

_src/App.js_ 의 콘텐츠를 다음 코드로 업데이트하여 API 함수에서 텍스트를 인출하고 화면에 표시합니다.

```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState('');

  useEffect(() => {
    (async function () {
      const { text } = await( await fetch(`/api/message`)).json();
      setData(text);
    })();
  });

  return <div>{data}</div>;
}

export default App;
```

# <a name="vue"></a>[Vue](#tab/vue)

_src/App.vue_ 의 콘텐츠를 다음 코드로 업데이트하여 API 함수에서 텍스트를 인출하고 화면에 표시합니다.

```javascript
<template>
  <div>{{ message }}</div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      message: ""
    };
  },
  async mounted() {
    const { text } = await (await fetch("/api/message")).json();
    this.message = text;
  }
};
</script>
```

---

## <a name="run-the-frontend-and-api-locally"></a>로컬에서 프런트 엔드 및 API 실행

프런트 엔드 앱과 API를 로컬로 함께 실행하기 위해 Azure Static Web Apps는 클라우드 환경을 에뮬레이트하는 CLI를 제공합니다. CLI는 Azure Functions Core Tools를 활용하여 API를 실행합니다.

### <a name="install-command-line-tools"></a>명령줄 도구 설치

필요한 명령줄 도구가 설치되어 있는지 확인합니다.

1. Azure Static Web Apps CLI를 설치합니다.
    ```bash
    npm install -g @azure/static-web-apps-cli
    ```

1. Azure Functions Core Tools V3을 설치합니다.
    ```bash
    npm install -g azure-functions-core-tools@3
    ```

### <a name="build-frontend-app"></a>프런트 엔드 앱 빌드

앱에서 프레임워크를 사용하는 경우 Static Web Apps CLI를 실행하기 전에 출력을 생성하는 앱을 빌드합니다.

# <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

앱을 빌드할 필요가 없습니다.

# <a name="angular"></a>[Angular](#tab/angular)

_dist/angular-basic_ 폴더에 앱을 빌드합니다.

```bash
npm run build --prod
```

# <a name="react"></a>[React](#tab/react)

_빌드_ 폴더에 앱을 빌드합니다.

```bash
npm run build
```

# <a name="vue"></a>[Vue](#tab/vue)

_dist_ 폴더에 앱을 빌드합니다.

```bash
npm run build
```

---

### <a name="start-the-cli"></a>CLI 시작

Static Web Apps CLI로 앱을 시작하여 프런트 엔드 앱과 API를 함께 실행합니다. 이러한 방식으로 애플리케이션의 두 부분을 실행하면 CLI가 폴더에서 프런트 엔드의 빌드 출력을 제공하고 실행 중인 앱에서 API에 액세스할 수 있습니다.

1. 리포지토리의 루트에서 `start` 명령을 사용하여 정적 웹 애플리케이션 CLI를 시작합니다. 앱의 폴더 구조가 다른 경우 인수를 조정합니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    현재 폴더(`src`) 및 API 폴더(`api`)를 CLI에 전달합니다.
     
    ```bash
    swa start src --api-location api
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    빌드 출력 폴더(`dist/angular-basic`) 및 API 폴더(`api`)를 CLI에 전달합니다.

    ```bash
    swa start dist/angular-basic --api-location api
    ```

    # <a name="react"></a>[React](#tab/react)

    빌드 출력 폴더(`build`) 및 API 폴더(`api`)를 CLI에 전달합니다.

    ```bash
    swa start build --api-location api
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    빌드 출력 폴더(`dist`) 및 API 폴더(`api`)를 CLI에 전달합니다.

    ```bash
    swa start dist --api-location api
    ```

    ---

1. CLI 프로세스가 시작되면 `http://localhost:4280/`에서 앱에 액세스합니다. 페이지가 API를 호출하고 출력 `Hello from the API`를 표시하는 방법을 확인합니다.

1. CLI를 중지하려면 <kbd>Ctrl-C</kbd>를 입력합니다.

## <a name="add-api-location-to-workflow"></a>워크플로에 API 위치 추가

Azure에 앱을 배포하기 전에 리포지토리의 GitHub Actions 워크플로를 API 폴더의 올바른 위치로 업데이트합니다.

1. _.github/workflows/azure-static-web-apps-\<DEFAULT-HOSTNAME>.yml_ 에서 워크플로를 엽니다.

1. `api_location` 특성을 검색하고 값을 `api`로 설정합니다.
1. 파일을 저장합니다.

## <a name="deploy-changes"></a>변경 내용 배포

Azure에서 정적 웹앱에 변경 내용을 게시하려면 코드를 커밋한 후 원격 GitHub 리포지토리에 푸시합니다.

1. <kbd>F1</kbd> 키를 눌러 명령 팔레트를 엽니다.

1. **Git: Commit All** 명령을 선택합니다.

1. 커밋 메시지를 입력하라는 메시지가 표시되면 **API 추가** 를 입력하고 모든 변경 사항을 로컬 git 리포지토리에 커밋합니다.

1. <kbd>F1</kbd> 키를 눌러 명령 팔레트를 엽니다.

1. **Git: push** 명령을 선택합니다.

    변경 내용은 GitHub의 원격 리포지토리로 푸시되며, Static Web Apps GitHub Actions 워크플로를 트리거하여 앱을 빌드하고 배포합니다.

1. GitHub에서 리포지토리를 열어 워크플로 실행 상태를 모니터링합니다.

1. 워크플로 실행이 완료되면 정적 웹앱을 방문하여 변경 내용을 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](./application-settings.md)
