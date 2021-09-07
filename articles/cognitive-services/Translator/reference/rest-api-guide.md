---
title: Text Translation REST API 참조 가이드
titleSuffix: Azure Cognitive Services
description: Text Translation REST API에 대한 링크가 있는 목록을 봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
ms.openlocfilehash: 2b0c4676ca7d085e5fbe042dd2a23fe0ae42db9c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528209"
---
# <a name="text-translation-rest-api"></a>Text Translation REST API

Text Translation은 Azure Translator 서비스의 클라우드 기반 기능이며 REST API의 Azure Cognitive Service 제품군의 일부입니다. Text Translation API는 [지원되는 모든 언어](../../language-support.md)의 언어 쌍 간에 텍스트를 번역합니다. 사용 가능한 메서드는 아래 표에 나와 있습니다.

| 요청| 메서드| 설명|
|---------|--------------|---------|
| [**languages**](v3-0-languages.md) | **GET** | 현재 **translation**, **transliteration** 및 **dictionary** 메서드에서 지원하는 언어 집합을 반환합니다. 이 요청에는 인증 헤더가 필요하지 않으며 지원되는 언어 집합을 보기 위해 Translator 리소스가 필요하지 않습니다.|
|[**translate**](v3-0-translate.md) | **POST**| 지정된 원본 언어 텍스트를 대상 언어 텍스트로 번역합니다.|
|[**음역**](v3-0-transliterate.md) |  **POST** | 원본 언어 스크립트 또는 알파벳을 대상 언어 스크립트 또는 알파벳에 매핑합니다.
|[**검색(detect)**](v3-0-detect.md) | **POST** | 원본 언어를 식별합니다. |
|[**breakSentence**](v3-0-break-sentence.md) | **POST** | 원본 텍스트의 문장 길이를 나타내는 정수 배열을 반환합니다. |
| [**dictionary/lookup**](v3-0-dictionary-lookup.md) | **POST** | 단일 단어 번역의 대안을 반환합니다. |
| [**dictionary/examples**](v3-0-dictionary-lookup.md) | **POST** | 컨텍스트에서 용어가 사용되는 방법을 반환합니다. |

> [!div class="nextstepaction"]
> [Azure Portal에서 Translator 리소스를 만듭니다.](/translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [빠른 시작: REST API 및 프로그래밍 언어](../quickstart-translator.md)
