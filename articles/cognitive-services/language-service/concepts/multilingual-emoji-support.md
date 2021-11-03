---
title: 언어에 대 한 Azure 인식 서비스의 다국어 및 서 수 지원
titleSuffix: Azure Cognitive Services
description: 언어 서비스 기능에서 다국어 및가 모 인코딩으로 인 한 오프셋에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48bda7b1e5ab5eaa62c525997530acd9af31a39a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052789"
---
# <a name="multilingual-and-emoji-support-in-language-service-features"></a>언어 서비스 기능에서 다국어 지원

다국어 및 이모지 지원으로 인해 하나 이상의 [코드 포인트](https://wikipedia.org/wiki/Code_point)를 사용하여 문자소라고 하는 단일 표시 문자를 나타내는 유니코드 인코딩이 가능해졌습니다. 예를 들어, 🌷 및 👍와 같은 이모지는 여러 문자를 사용하여 피부 톤과 같은 시각적 특성에 대한 추가 문자로 모양을 구성할 수 있습니다. 마찬가지로 힌디어 단어 `अनुच्छेद`는 5개의 문자와 3개의 결합 표시로 인코딩됩니다.

가능 하면 다국어 및이 모 지 인코딩의 길이가 다르므로 언어 서비스 기능에서 응답의 오프셋을 반환할 수 있습니다.

## <a name="offsets-in-the-api-response"></a>API 응답의 오프셋

API 응답에서 오프셋이 반환 될 때마다 다음 사항을 명심 하십시오.

* 응답의 요소는 호출된 엔드포인트에 특정될 수 있습니다. 
* HTTP POST/GET 페이로드는 [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)로 인코딩되며, 이는 클라이언트 측 컴파일러 또는 운영 체제의 기본 문자 인코딩일 수도 있고, 아닐 수도 있습니다.
* 오프셋은 문자 수가 아닌 [유니코드 8.0.0](https://unicode.org/versions/Unicode8.0.0) 표준을 기반으로 하는 문자소 수를 참조합니다.

## <a name="extracting-substrings-from-text-with-offsets"></a>오프셋이 있는 텍스트에서 부분 문자열 추출

오프셋은 문자 기반 하위 문자열 메서드(예: .NET [substring()](/dotnet/api/system.string.substring) 메서드)를 사용할 때 문제가 발생할 수 있습니다. 한 가지 문제는 오프셋으로 인해 하위 문자열 메서드가 끝이 아닌 다중 문자 문자소 인코딩의 중간에서 끝날 수 있다는 것입니다.

.NET에서는 개별 문자 개체가 아닌 일련의 텍스트 요소로 문자열을 사용할 수 있는 [StringInfo](/dotnet/api/system.globalization.stringinfo) 클래스를 사용하는 것이 좋습니다. 선호하는 소프트웨어 환경에서 문자소 분할기 라이브러리를 찾을 수도 있습니다. 

언어 서비스 기능은 편의상 이러한 텍스트 요소도 반환 합니다.

오프셋을 반환 하는 끝점은 `stringIndexType` 매개 변수를 지원 합니다. 이 매개 변수는 요청된 문자열 반복 체계와 일치하도록 API 출력의 `offset` 및 `length` 특성을 조정합니다. 현재는 다음과 같은 세 가지 형식을 지원합니다.

- `textElement_v8`(기본값): [유니코드 8.0.0](https://unicode.org/versions/Unicode8.0.0) 표준에 정의된 대로 문자소를 반복합니다.
- `unicodeCodePoint`: Python 3의 기본 체계인 [유니코드 코드 포인트](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564)를 반복합니다.
- `utf16CodeUnit`: JavaScript, Java 및 .NET의 기본 체계인 [UTF-16 코드 단위](https://unicode.org/faq/utf_bom.html#UTF16)를 반복합니다.

요청된 `stringIndexType`이 선택한 프로그래밍 환경과 일치하면 표준 하위 문자열 또는 슬라이스 메서드를 사용하여 하위 문자열을 추출할 수 있습니다. 

## <a name="see-also"></a>참고 항목

* [언어 서비스 개요](../overview.md)
