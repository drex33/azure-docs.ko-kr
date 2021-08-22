---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: lajanuar
ms.custom: devx-track-js
ms.openlocfilehash: 6a1fc21f5ff88326ba0a8e63cd80d42eafb0eb7a
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529621"
---
:::row:::
    :::column span="3":::
        JavaScript용 Speech SDK는 npm 패키지로 사용할 수 있습니다. <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk</a> 및 해당 GitHub 리포지토리 <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js</a>를 참조하세요.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> JavaScript용 Speech SDK는 npm 패키지로 사용할 수 있지만 클라이언트 웹 브라우저와 Node.js를 모두 사용할 수 있습니다. 각 환경의 다양한 아키텍처의 영향을 고려하세요. 예를 들어 클라이언트 쪽 애플리케이션에서 <a href="https://nodejs.org/api/fs.html" target="_blank">파일 시스템</a>을 사용할 수 없는 경우와 마찬가지로 서버 쪽 애플리케이션에서 <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">DOM(문서 개체 모델)</a>을 사용할 수 없습니다.

### <a name="nodejs-package-manager-npm"></a>NPM(Node.js 패키지 관리자)

JavaScript용 Speech SDK를 설치하려면 아래 `npm install` 명령을 실행합니다.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 스크립트 태그

또는 <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank">**JSDelivr** NPM 게시</a>를 사용하여 HTML `<head>` 요소에 `<script>` 태그를 직접 포함할 수 있습니다.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

자세한 내용은 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">웹 브라우저 Speech SDK 빠른 시작</a>을 참조하세요.
