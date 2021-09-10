---
title: 몰입형 리더 Node.js 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Applied AI Services
description: 이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 API 기능을 추가합니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: de4dda828b821db66d89387f1f63bf20372fd005
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786686"
---
[몰입형 리더](https://www.onenote.com/learningtools)는 새 리더, 언어 학습자 및 학습 차이(예: 난독증)가 있는 사람들을 위해 독해력을 향상시키기 위해 입증된 기술을 구현하는 포괄적으로 설계된 도구입니다. 애플리케이션에서 몰입형 리더를 사용하여 텍스트를 분리하여 포커스를 개선하고, 자주 사용되는 단어에 대한 그림을 표시하고, 품사를 강조 표시하고, 선택한 텍스트를 소리내어 읽고, 단어와 구문을 실시간으로 번역하는 등의 작업을 수행할 수 있습니다.

이 빠른 시작에서는 웹앱을 처음부터 빌드하고 몰입형 리더 클라이언트 라이브러리를 사용하여 몰입형 리더를 통합합니다. 이 빠른 시작의 전체 작동 샘플은 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](../../how-to-create-immersive-reader.md)에 따라 설정하세요. 환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [Node.js](https://nodejs.org/) 및 [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/)와 같은 IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express에서 Node.js 웹앱 만들기

`express-generator` 도구로 Node.js를 만듭니다.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Yarn 종속성을 설치하고, 빠른 시작에서 나중에 사용하게 될 `request` 및 `dotenv` 종속성을 추가합니다.

```bash
yarn
yarn add request
yarn add dotenv
```

다음 명령을 사용하여 **axios** 및 **qs** 라이브러리를 설치합니다.

```bash
npm install axios qs
```

## <a name="set-up-authentication"></a>인증 설정

### <a name="configure-authentication-values"></a>인증 값 구성

프로젝트 루트에 _.env_ 라는 새 파일을 만듭니다. 몰입형 판독기 리소스를 만들 때 지정된 값을 제공하여 다음 코드를 붙여넣습니다.
따옴표 또는 "{" 및 "}" 문자를 포함하지 마세요.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

이 파일에는 공개되어서는 안 되는 비밀이 있으므로 이 파일을 소스 제어에 커밋하지 않아야 합니다.

이제 _app.js_ 를 열고 파일 맨 위에 다음을 추가합니다. 이렇게 하면 env 파일에서 정의한 속성이 Node에 환경 변수로 로드됩니다.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>토큰을 획득하도록 라우터 업데이트

_routes\index.js_ 파일을 열고 자동으로 생성된 코드를 다음 코드로 바꿉니다.

이 코드는 서비스 사용자 암호를 사용하여 Azure AD 인증 토큰을 획득하는 API 엔드포인트를 만듭니다. 하위 도메인도 검색합니다. 그런 다음, 토큰과 하위 도메인을 포함하는 개체를 반환합니다.

```javascript
var axios = require('axios');
var express = require('express');
var router = express.Router();
var qs = require('qs');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        var config ={
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            }
        }
        var data = {
            grant_type: 'client_credentials',
            client_id: process.env.CLIENT_ID,
            client_secret: process.env.CLIENT_SECRET,
            resource: 'https://cognitiveservices.azure.com/'
        };
        var url = `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`
        console.log(qs.stringify(data));
        axios.post(url, qs.stringify(data), config)
        .then(function (response) {
            var token = response.data.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        })
        .catch(function (response) {
            if (response.status !== 200) {
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }
        });
    } catch (error) {
        console.log(error);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

권한 없는 사용자가 토큰을 획득하여 몰입형 판독기 서비스 및 청구에 사용하지 못하도록 **GetTokenAndSubdomain** 백 엔드 API 엔드포인트를 인증 형식(예: [OAuth](https://oauth.net/2/))으로 보호해야 합니다. 이 작업은 이 빠른 시작의 범위를 벗어납니다.

## <a name="add-sample-content"></a>샘플 콘텐츠 추가

이제 이 웹앱에 샘플 콘텐츠를 추가합니다. _views\index.pug_ 를 열고 자동으로 생성된 코드를 다음 샘플로 바꿉니다.

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='icon', href='data:;base64,iVBORw0KGgo=')

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.1.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```

모든 텍스트에는 텍스트의 언어를 설명하는 **lang** 특성이 있습니다. 이 특성은 몰입형 판독기가 관련 언어와 문법 기능을 제공하는 데 도움이 됩니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

이제 웹앱이 준비되었습니다. 다음을 실행하여 앱을 실행합니다.

```bash
npm start
```

브라우저를 열고, _http://localhost:3000_ 으로 이동합니다. 다음이 표시되어야 합니다.

![샘플 앱 - Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>몰입형 판독기 시작

“몰입형 판독기” 단추를 클릭하면 페이지의 콘텐츠와 함께 몰입형 판독기가 시작됩니다.

![몰입형 리더 - Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [리소스 만들기 및 AAD 구성](../../how-to-create-immersive-reader.md)