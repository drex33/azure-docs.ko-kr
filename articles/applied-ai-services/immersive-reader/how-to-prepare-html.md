---
title: 몰입형 리더용 HTML 콘텐츠를 준비하는 방법
titleSuffix: Azure Applied AI Services
description: HTML, JavaScript, Python, Android 또는 iOS를 사용하여 몰입형 리더를 시작하는 방법을 알아봅니다. 몰입형 리더는 입증된 기술을 사용하여 언어 학습자, 신흥 독자 및 학습 차이가 있는 학생을 위해 독해력을 향상시킵니다.
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: 505151c22c095f1168ccad4a84870c430b3dd586
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026527"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>몰입형 리더용 HTML 콘텐츠를 준비하는 방법

이 문서에서는 몰입형 리더에서 사용할 수 있도록 HTML을 구성하고 콘텐츠를 검색하는 방법을 보여줍니다.

## <a name="prepare-the-html-content"></a>HTML 콘텐츠 준비

렌더링하려는 콘텐츠를 컨테이너 요소 내의 몰입형 리더에 배치합니다. 컨테이너 요소에 고유한 `id`가 있는지 확인합니다. 몰입형 리더는 기본 HTML 요소에 대한 지원을 제공합니다. 자세한 내용은 [참조](reference.md#html-support)를 참조하세요.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>JavaScript에서 HTML 콘텐츠 가져오기

컨테이너 요소의 `id`를 사용하여 JavaScript 코드에서 HTML 콘텐츠를 가져옵니다.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>HTML 콘텐츠로 몰입형 리더 시작

`ImmersiveReader.launchAsync`를 호출할 때 청크의 `mimeType` 속성을 `text/html`로 설정하여 HTML 렌더링을 사용하도록 설정합니다.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK 참조](reference.md) 살펴보기