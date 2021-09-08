---
title: '빠른 시작: JavaScript(Browser)용 Speech SDK 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 사용하여 Speech Service SDK와 함께 JavaScript(Browser)를 사용하기 위한 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: 363f21007d24de5cb4002bf86ff20bfff5868e59
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544215"
---
이 가이드에서는 웹 페이지와 함께 사용할 JavaScript용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여줍니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>새 웹 사이트 폴더 만들기

빈 폴더를 새로 만듭니다. 웹 서버에 샘플을 호스트하려면 웹 서버가 폴더에 액세스할 수 있는지 확인합니다.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript용 Speech SDK를 해당 폴더에 압축 해제합니다.

Speech SDK를 [.zip 패키지](https://aka.ms/csspeech/jsbrowserpackage)로 다운로드하여 새로 만든 폴더에 압축 해체합니다. 이로 인해 다섯 개의 파일이 압축 해제됩니다.
* `microsoft.cognitiveservices.speech.sdk.bundle.js` 사람이 읽을 수 있는 Speech SDK 버전입니다.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` SDK 코드를 디버깅하는 데 사용되는 맵 파일입니다.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` TypeScript와 함께 사용할 개체 정의
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` Speech SDK의 축소 버전입니다.
* `speech-processor.js` 일부 브라우저에서 성능을 개선하는 코드합니다.

## <a name="create-an-indexhtml-page"></a>Index.html 페이지 만들기

폴더에 `index.html`라는 새 파일을 만들고 텍스트 편집기에서 이 파일을 엽니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]