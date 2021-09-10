---
title: 문서 번역 REST API 참조 가이드
titleSuffix: Azure Cognitive Services
description: 문서 번역 REST API에 대한 링크가 포함된 목록을 봅니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/21/2021
ms.author: lajanuar
ms.openlocfilehash: 38757efc32a90a74cdf73bc84af28b80f8402948
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112543890"
---
# <a name="document-translation-rest-api-reference-guide"></a>문서 번역 REST API 참조 가이드

문서 번역은 Azure Translator 서비스의 클라우드 기반 기능이며 REST API의 Azure Cognitive Service 제품군의 일부입니다. 문서 번역 API는 문서 구조와 데이터 형식을 유지하면서 [지원되는 모든 언어와 방언](../../language-support.md)으로 문서를 번역합니다. 사용 가능한 메서드는 아래 표에 나와 있습니다.

| 요청| 설명|
|---------|--------------|
| [**지원되는 문서 형식 가져오기**](get-supported-document-formats.md)| 이 메서드는 지원되는 문서 형식 목록을 반환합니다.|
|[**지원되는 용어집 형식 가져오기**](get-supported-glossary-formats.md)|이 메서드는 지원되는 용어집 형식 목록을 반환합니다.|
|[**지원되는 스토리지 원본 가져오기**](get-supported-storage-sources.md)| 이 메서드는 지원되는 스토리지 소스/옵션 목록을 반환합니다.|
|[**번역 시작(POST)** ](start-translation.md)|이 메서드는 문서 번역 작업을 시작합니다. |
|[**문서 상태 가져오기**](get-documents-status.md)|이 메서드는 번역 작업의 모든 문서 상태를 반환합니다.|
|[**문서 상태 가져오기**](get-document-status.md)| 이 메서드는 작업 내 특정 문서의 상태를 반환합니다. |
|[**번역 상태 가져오기**](get-translations-status.md)| 이 메서드는 사용자가 제출한 모든 번역 요청 목록과 각 요청에 대한 상태를 반환합니다.|
|[**번역 상태 가져오기**](get-translation-status.md) | 이 메서드는 특정 문서 번역 요청에 대한 상태 요약을 반환합니다. 여기에는 전체 요청 상태와 해당 요청의 일부로 번역되는 문서의 상태가 포함됩니다.|
|[**번역 취소(DELETE)** ](cancel-translation.md)| 이 메서드는 현재 처리 중이거나 대기 중인 문서 번역을 취소합니다. |

> [!div class="nextstepaction"]
> [C# 및 Python용 클라이언트 라이브러리 및 SDK를 살펴보세요.](../client-sdks.md)