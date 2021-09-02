---
title: 자습서 - Azure Communication Services용 웹앱 준비(Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Azure Communication Services를 지원하는 기준 웹 애플리케이션을 만드는 방법을 알아봅니다.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: 62c72271e1c72b524de1ff2365a78ec240007bfb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250976"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>자습서: Azure Communication Services용 웹앱 준비(Node.js)

Azure Communication Services를 사용하여 실시간 통신을 애플리케이션에 추가할 수 있습니다. 이 자습서에서는 Azure Communication Services를 지원하는 웹 애플리케이션을 설정하는 방법에 대해 알아봅니다. 이는 실시간 통신을 시작하려는 새로운 개발자를 위한 소개 자습서입니다.

이 자습서를 완료하면 Azure Communication Services SDK로 구성된 기준 웹 애플리케이션이 만들어집니다. 그런 다음, 해당 애플리케이션을 사용하여 실시간 통신 솔루션 빌드를 시작할 수 있습니다.

언제든지 [Azure Communication Services GitHub 페이지](https://github.com/Azure/communication)를 방문하여 피드백을 제공해 주세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 개발 환경 구성
> * 로컬 웹 서버 설정
> * 웹 사이트에 Azure Communication Services 패키지 추가
> * Azure 정적 웹 사이트에 웹 사이트 게시

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 체험 계정에는 모든 서비스 조합을 사용해 볼 수 있는 200달러의 Azure 크레딧이 제공됩니다.
- [Visual Studio Code](https://code.visualstudio.com/) - 로컬 개발 환경에서 코드를 편집합니다.
- [webpack](https://webpack.js.org/) - 코드를 번들로 묶어 로컬로 호스팅합니다.
- [Node.js](https://nodejs.org/en/) - Azure Communication Services SDK 및 webpack과 같은 종속성을 설치하고 관리합니다.
- [nvm 및 npm](/windows/nodejs/setup-on-windows)(버전 제어 처리)
- Visual Studio Code용 [Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage). 이 확장은 애플리케이션을 Azure Storage에 게시하는 데 필요합니다. [Azure Storage에서 정적 웹 사이트를 호스팅하는 방법에 대해 자세히 알아보세요](../../storage/blobs/storage-blob-static-website.md).
- [Azure App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) 이 확장을 사용하면 완전 관리형 CI/CD(연속 통합 및 지속적인 업데이트)를 구성하는 옵션이 있는 웹 사이트를 배포할 수 있습니다.
- 사용자 고유의 서버리스 애플리케이션을 빌드하는 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 예를 들어 Azure Functions에서 인증 애플리케이션을 호스팅할 수 있습니다.
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만드는 방법에 대해 자세히 알아보세요](../quickstarts/create-communication-resource.md).
- 사용자 액세스 토큰. 지침은 [액세스 토큰 만들기 및 관리 빠른 시작](../quickstarts/access-tokens.md?pivots=programming-language-javascript) 또는 [신뢰할 수 있는 인증 서비스 빌드 자습서](./trusted-service-tutorial.md)를 참조하세요.


## <a name="configure-your-development-environment"></a>개발 환경 구성

로컬 개발 환경은 다음과 같이 구성됩니다.

:::image type="content" source="./media/step-one-pic-one.png" alt-text="개발 환경의 아키텍처를 보여 주는 다이어그램":::


### <a name="install-nodejs-nvm-and-npm"></a>Node.js, nvm 및 npm 설치

Node.js를 사용하여 클라이언트 쪽 애플리케이션에 필요한 다양한 종속성을 다운로드하고 설치합니다. 이를 사용하여 Azure에서 호스팅하는 정적 파일을 생성하므로 서버에서 구성할 필요가 없습니다.

Windows 개발자는 [이 Node.js 자습서](/windows/nodejs/setup-on-windows)에 따라 Node, nvm 및 npm을 구성할 수 있습니다. 

이 자습서는 LTS 12.20.0 버전을 기반으로 합니다. nvm이 설치되면 다음 PowerShell 명령을 사용하여 사용하려는 버전을 배포합니다.

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Node 버전을 배포하기 위한 명령을 보여 주는 스크린샷":::

### <a name="configure-visual-studio-code"></a>Visual Studio Code 구성

[Visual Studio Code](https://code.visualstudio.com/)는 [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나에서 다운로드할 수 있습니다.

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Azure Communication Services 프로젝트용 작업 영역 만들기

프로젝트 파일을 저장할 폴더(예: `C:\Users\Documents\ACS\CallingApp`)를 만듭니다. Visual Studio Code에서 **파일** > **작업 영역에 폴더 추가** 를 차례로 선택하고, 해당 폴더를 작업 영역에 추가합니다.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="작업 영역에 파일을 추가하기 위한 선택 항목을 보여 주는 스크린샷":::

왼쪽 창의 **탐색기** 로 이동합니다. 그러면 `CallingApp` 폴더가 **제목 없음** 작업 영역에 표시됩니다.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="탐색기 및 제목 없음 작업 영역을 보여 주는 스크린샷":::

작업 영역 이름을 자유롭게 업데이트합니다. 마우스 오른쪽 단추로 `CallingApp` 폴더를 클릭하고 **통합 터미널에서 열기** 를 선택하여 Node.js 버전의 유효성을 검사할 수 있습니다.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="통합 터미널에서 폴더를 열기 위한 선택 항목을 보여 주는 스크린샷":::

터미널에서 다음 명령을 입력하여 이전 단계에서 설치한 Node.js 버전의 유효성을 검사합니다.

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Node 버전의 유효성 검사를 보여 주는 스크린샷":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Visual Studio Code용 Azure 확장 설치

Visual Studio 마켓플레이스 또는 Visual Studio Code(**보기** > **확장** > **Azure Storage**)를 사용하여 [Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)을 설치합니다.

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Azure Storage 확장을 설치하는 단추를 보여 주는 스크린샷":::

[Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 및 [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) 확장에 대해 동일한 단계를 수행합니다.


## <a name="set-up-a-local-web-server"></a>로컬 웹 서버 설정

### <a name="create-an-npm-package"></a>npm 패키지 만들기

터미널의 작업 영역 폴더 경로에서 다음을 입력합니다.

``` console
npm init -y
```

이 명령은 새 npm 패키지를 초기화하고, `package.json`을 프로젝트의 루트 폴더에 추가합니다.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="package.json을 보여 주는 스크린샷":::

`npm init`에 대한 자세한 설명서는 [해당 명령에 대한 npm Docs 페이지](https://docs.npmjs.com/cli/v6/commands/npm-init)를 참조하세요.

### <a name="install-webpack"></a>webpack 설치

[webpack](https://webpack.js.org/)을 사용하여 Azure에 배포할 수 있는 정적 파일에 코드를 번들로 묶을 수 있습니다. 또한 통화 샘플에서 사용하도록 구성할 개발 서버도 있습니다.

터미널에서 다음 명령을 입력하여 webpack을 설치합니다.

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

이 자습서는 이전 명령에 지정된 버전으로 테스트되었습니다. `-dev`를 지정하면 이 종속성이 개발용이며 Azure에 배포하는 코드에 포함되지 않아야 한다고 패키지 관리자에 알려줍니다.

`package.json` 파일에 `devDependencies`로 추가된 두 개의 새 패키지가 표시됩니다. 이러한 패키지는 `./CallingApp/node_modules/` 디렉터리에 설치됩니다.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="webpack 구성을 보여 주는 스크린샷":::

### <a name="configure-the-development-server"></a>개발 서버 구성

브라우저에서 정적 애플리케이션(예: `index.html` 파일)을 실행하면 `file://` 프로토콜이 사용됩니다. npm 모듈이 제대로 작동하려면 webpack을 로컬 개발 서버로 사용하여 HTTP 프로토콜을 사용해야 합니다.

두 개의 구성을 만듭니다. 하나는 개발용이고, 다른 하나는 프로덕션용입니다. 프로덕션용으로 준비된 파일은 축소됩니다. 즉, 사용하지 않는 공백과 문자를 제거할 수 있습니다. 이 구성은 대기 시간을 최소화하거나 코드를 난독 처리해야 하는 프로덕션 시나리오에 적합합니다.

`webpack-merge` 도구를 사용하여 [webpack에 대한 다른 구성 파일](https://webpack.js.org/guides/production/)을 사용합니다.

개발 환경부터 시작해 보겠습니다. 먼저 `webpack merge`를 설치해야 합니다. 터미널에서 다음 명령을 실행합니다.

```Console
npm install --save-dev webpack-merge
```

`package.json` 파일에서 `devDependencies`에 추가된 종속성을 하나 더 볼 수 있습니다.

다음으로 `webpack.common.js`라는 파일을 만들고, 다음 코드를 추가합니다.

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

그런 다음, 각 구성에 대해 하나씩 다음 두 개의 파일을 더 추가합니다.

* `webpack.dev.js`
* `webpack.prod.js`

이제 다음 코드를 추가하여 `webpack.dev.js` 파일을 수정합니다.

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
이 구성에서는 `webpack.common.js`에서 공통 매개 변수를 가져오고, 두 파일을 병합하고, 모드를 `development`로 설정하고, 소스 맵을 `inline-source-map`으로 구성합니다.

development 모드에서는 webpack에서 파일을 축소하지 않고 최적화된 프로덕션 파일을 생성하지 않도록 지시합니다. webpack 모드에 대한 자세한 설명서는 [webpack 모드 웹 페이지](https://webpack.js.org/configuration/mode/)에서 찾을 수 있습니다.

소스 맵 옵션은 [webpack Devtool 웹 페이지](https://webpack.js.org/configuration/devtool/#root)에 나열되어 있습니다. 소스 맵을 설정하면 브라우저를 통해 더 쉽게 디버그할 수 있습니다.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="webpack을 구성하는 코드를 보여 주는 스크린샷":::

개발 서버를 실행하려면 `package.json`으로 이동하여 다음 코드를 `scripts` 아래에 추가합니다.

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

이제 파일은 다음과 같습니다.

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

npm에서 사용할 수 있는 명령이 추가되었습니다.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="package.json의 수정 사항을 보여 주는 스크린샷":::

### <a name="test-the-development-server"></a>개발 서버 테스트

 Visual Studio Code에서 세 개의 파일을 프로젝트 아래에 만듭니다.

* `index.html`
* `app.js`
* `app.css`(선택 사항, 앱 스타일 지정)

이 코드를 `index.html`에 붙여넣습니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="HTML 파일을 보여 주는 스크린샷":::

다음 코드를 `app.js`에 추가합니다.

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
다음 코드를 `app.css`에 추가합니다.

```CSS
html {
    font-family: sans-serif;
  }
```
저장하는 것을 잊지 마세요. 저장되지 않은 파일은 탐색기에서 파일 이름 옆에 흰색 점으로 표시됩니다.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="JavaScript 코드가 있는 App.js 파일을 보여 주는 스크린샷":::

이 페이지를 열면 브라우저의 콘솔에서 경고가 포함된 메시지가 표시됩니다.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App.css 파일을 보여 주는 스크린샷":::

다음 터미널 명령을 사용하여 개발 구성을 테스트합니다.

```Console
npm run build:dev
```

콘솔에서 서버가 실행되는 위치가 표시됩니다. 기본적으로 `http://localhost:8080`입니다. `build:dev` 명령은 이전에 `package.json`에 추가한 명령입니다.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="개발 서버 시작을 보여 주는 스크린샷":::
 
브라우저에서 주소로 이동합니다. 그러면 이전 단계에서 구성한 페이지와 경고가 표시됩니다.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="HTML 페이지의 스크린샷":::
  
 
서버가 실행되는 동안 코드와 서버를 변경할 수 있습니다. HTML 페이지가 자동으로 다시 로드됩니다. 

다음으로, Visual Studio Code에서 `app.js` 파일로 이동하여 `alert('Hello world alert!');`를 삭제합니다. 파일을 저장하고, 브라우저에서 경고가 사라지는지 확인합니다.

서버를 중지하려면 터미널에서 `Ctrl+C`를 실행할 수 있습니다. 서버를 시작하려면 언제든지 `npm run build:dev`를 입력합니다.

## <a name="add-the-azure-communication-services-packages"></a>Azure Communication Services 패키지 추가

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services Calling SDK를 설치합니다.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

이 작업을 수행하면 Azure Communication Services 공통 및 통화 패키지가 패키지의 종속성으로 추가됩니다. `package.json` 파일에 추가된 두 개의 새 패키지를 볼 수 있습니다. `npm install`에 대한 자세한 내용은 [해당 명령에 대한 npm Docs 페이지](https://docs.npmjs.com/cli/v6/commands/npm-install)에서 확인할 수 있습니다.

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Azure Communication Services 패키지를 설치하는 코드를 보여 주는 스크린샷":::

이러한 패키지는 Azure Communication Services 팀에서 제공하며 인증 및 통화 라이브러리를 포함하고 있습니다. `--save` 명령은 애플리케이션이 프로덕션 사용을 위해 이러한 패키지에 종속되고 `package.json` 파일의 `devDependencies`에 포함된다는 신호를 보냅니다. 프로덕션용 애플리케이션을 빌드하는 경우 패키지가 프로덕션 코드에 포함됩니다.


## <a name="publish-your-website-to-azure-static-websites"></a>Azure 정적 웹 사이트에 웹 사이트 게시

### <a name="create-a-configuration-for-production-deployment"></a>프로덕션 배포에 대한 구성 만들기

다음 코드를 `webpack.prod.js`에 추가합니다.

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

이 구성은 `webpack.common.js`(입력 파일 및 결과를 저장할 위치를 지정)와 병합됩니다. 또한 이 구성은 모드를 `production`으로 설정합니다.
 
`package.json`에서 다음 코드를 추가합니다.

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

파일은 다음과 같이 표시됩니다.

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="구성된 파일을 보여 주는 스크린샷":::


터미널에서 다음을 실행합니다.

```Console
npm run build:prod
```

이 명령은 `dist` 폴더 및 이 폴더 안에 프로덕션 준비 `app.js` 정적 파일을 만듭니다. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="프로덕션 빌드를 보여 주는 스크린샷":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Azure Storage에 앱 배포

`index.html` 및 `app.css`를 `dist` 폴더에 복사합니다.

`dist` 폴더에서 파일을 만들고, 이름을 `404.html`로 지정합니다. 다음 태그를 해당 파일에 복사합니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

파일을 저장합니다(Ctrl+S).

마우스 오른쪽 단추로 `dist` 폴더를 클릭하고, **Azure Storage를 통해 정적 웹 사이트에 배포** 를 선택합니다.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Azure에 배포를 시작하기 위한 선택 항목의 스크린샷":::
 
**구독 선택** 아래에서 **Azure에 로그인**(또는 이전에 구독을 만들지 않은 경우 **체험 Azure 계정 만들기**)을 선택합니다.
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Azure에 로그인하기 위한 선택 항목을 보여 주는 스크린샷":::
 
**새 스토리지 계정 만들기** > **고급** 을 차례로 선택합니다.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="스토리지 계정 그룹을 만들기 위한 선택 항목을 보여 주는 스크린샷":::
 
스토리지 그룹의 이름을 제공합니다.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="계정 이름 추가를 보여 주는 스크린샷":::
 
필요한 경우 새 리소스 그룹을 만듭니다.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="새 리소스 그룹을 만들기 위한 선택 항목을 보여 주는 스크린샷":::
  
**정적 웹 사이트 호스팅을 사용하도록 설정하시겠습니까?** 에 대해 **예** 를 선택합니다.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="정적 웹 사이트 호스팅을 사용하도록 설정하는 옵션의 선택을 보여 주는 스크린샷":::
  
**인덱스 문서 이름 입력** 에 대해 기본 파일 이름을 그대로 적용합니다. `index.html` 파일을 이미 만들었습니다.

**404 오류 문서 경로 입력** 에 대해 **404.html** 을 입력합니다.  
  
애플리케이션의 위치를 선택합니다. 선택하는 위치에 따라 그룹 통화의 향후 통화 애플리케이션에서 사용될 미디어 프로세서가 정의됩니다. 

Azure Communication Services는 애플리케이션 위치에 따라 미디어 프로세서를 선택합니다.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="위치 목록을 보여 주는 스크린샷":::
  
리소스 및 웹 사이트가 만들어질 때까지 기다립니다. 
 
**웹 사이트로 이동** 을 선택합니다.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="웹 사이트로 이동할 수 있는 단추와 함께 배포가 완료되었다는 메시지를 보여 주는 스크린샷":::
 
브라우저의 개발 도구에서 원본을 검사하고, 프로덕션용으로 준비된 파일을 볼 수 있습니다.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="파일이 있는 웹 사이트 원본의 스크린샷":::

[Azure Portal](https://portal.azure.com/#home)로 이동하여 리소스 그룹을 선택하고, 만든 애플리케이션을 선택합니다. 그런 다음, **설정** > **정적 웹 사이트** 를 차례로 선택합니다. 정적 웹 사이트가 사용하도록 설정되었음을 확인할 수 있습니다. 기본 엔드포인트, 인덱스 문서 이름 및 오류 문서 경로를 확인합니다.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="정적 웹 사이트 선택 항목을 보여 주는 스크린샷":::

**Blob service** 에서 **컨테이너** 를 선택합니다. 목록에 로그용(`$logs`) 및 웹 사이트 콘텐츠용(`$web`)으로 만든 두 개의 컨테이너가 표시됩니다.

:::image type="content" source="./media/step-one-pic-32.png" alt-text="컨테이너 구성을 보여 주는 스크린샷":::

`$web` 컨테이너를 열면 Visual Studio에서 만들고 Azure에 배포한 파일이 표시됩니다. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Azure에 배포한 파일을 보여 주는 스크린샷":::

애플리케이션은 언제든지 Visual Studio Code에서 다시 배포할 수 있습니다.

이제 첫 번째 Azure Communication Services 웹 애플리케이션을 빌드할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱에 음성 통화 추가](../quickstarts/voice-video-calling/getting-started-with-calling.md)

다음을 수행할 수도 있습니다.

- [앱에 채팅 추가](../quickstarts/chat/get-started.md)
- [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
- [인증에 대한 자세한 정보](../concepts/authentication.md)
