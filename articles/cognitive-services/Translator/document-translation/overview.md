---
title: Microsoft Azure Cognitive Services 문서 번역이란?
description: 클라우드 기반 배치 문서 변환 서비스 및 프로세스에 대한 개요입니다.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 08/09/2021
ms.openlocfilehash: 64198fce7acb3ea02349978de0c2787cb744e6b4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433893"
---
# <a name="what-is-document-translation"></a>문서 번역이란?

문서 번역은 [Azure Translator](../translator-overview.md) 서비스의 클라우드 기반 기능이며 REST API의 Azure Cognitive Service 제품군의 일부입니다. 이 개요에서는 문서 번역 API를 사용하여 원래 문서 구조와 데이터 형식은 유지하면서 모든 [지원되는 언어](../../language-support.md)에서 복잡한 여러 문서를 번역하는 방법을 알아봅니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [**빠른 시작**](get-started-with-document-translation.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](create-sas-tokens.md)에는 보다 구체적이거나 사용자 지정된 방식으로 기능을 사용하기 위한 지침이 포함되어 있습니다.
* [**참조**](reference/rest-api-guide.md)에서는 REST API 설정, 값, 키워드, 구성을 제공합니다.

## <a name="document-translation-key-features"></a>문서 번역 주요 기능

| 기능 | Description |
| ---------| -------------|
| **대용량 파일 번역**| 전체 문서를 비동기적으로 번역합니다.|
|**대용량 파일 번역**|문서 구조와 데이터 형식을 유지하면서 지원되는 모든 언어와 방언으로 여러 파일을 번역합니다.|
|**원본 파일 프레젠테이션 보존**| 원래 레이아웃과 형식을 유지하면서 파일을 번역합니다.|
|**사용자 지정 번역 적용**| 일반 및 [사용자 지정 번역](../customization.md#custom-translator) 모델을 사용하여 문서를 번역합니다.|
|**사용자 지정 글로서리 적용**|사용자 지정 글로서리를 사용하여 문서를 번역합니다.|
|**문서 언어 자동 검색**|문서 번역 서비스에서 문서의 언어를 확인하도록 합니다.|
|**여러 언어로 된 콘텐츠가 있는 문서 번역**|자동 검색 기능을 사용하여 여러 언어로 된 콘텐츠가 있는 문서를 대상 언어로 번역합니다.|

> [!NOTE]
> 여러 언어로 된 콘텐츠가 있는 문서를 번역할 때 이 기능은 단일 언어로 된 완전한 문장을 위한 것입니다. 문장이 둘 이상의 언어로 구성된 경우 콘텐츠가 모두 대상 언어로 번역되지 않을 수 있습니다.
>

## <a name="document-translation-development-options"></a>문서 번역 개발 옵션

REST API 또는 클라이언트 라이브러리 SDK를 사용하여 애플리케이션에 문서 번역을 추가할 수 있습니다.

* [**REST API**](reference/rest-api-guide.md)는 HTTP 요청 및 권한 부여 헤더를 만들어 문서를 번역할 수 있는 언어 중립적 인터페이스입니다.

* [**클라이언트 라이브러리 SDK**](client-sdks.md)는 프로젝트에 참조를 추가하여 신속하게 사용할 수 있는 언어별 클래스, 개체, 메서드, 코드입니다. 현재 문서 번역에서는 [**C#/.NET**](/dotnet/api/azure.ai.translation.document) 및 [**Python**](https://pypi.org/project/azure-ai-translation-document/) 같은 프로그래밍 언어를 지원합니다.

## <a name="get-started"></a>시작하기

이 방법 가이드에서는 문서 변환기를 사용하여 빠르게 시작하는 방법을 알아봅니다. 시작하려면 활성 [Azure 계정](https://azure.microsoft.com/free/cognitive-services/)이 필요합니다.  계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free)에 만들 수 있습니다.

> [!div class="nextstepaction"]
> [여기에서 시작](get-started-with-document-translation.md "HTTP REST에서 문서 번역을 사용하는 방법 알아보기")

## <a name="supported-document-formats"></a>지원되는 문서 형식

문서 번역에서 지원되는 문서 파일 형식은 다음과 같습니다.

| 파일 형식| 파일 확장명|Description|
|---|---|--|
|Adobe PDF|pdf|Adobe Acrobat Portable 문서 형식|
|쉼표로 구분된 값 |csv| 스프레드시트 프로그램에서 사용하는 쉼표로 구분된 원시 데이터 파일입니다.|
|HTML|html, htm|Hyper Text Markup Language|
|지역화 교환 파일 형식|xlf. , xliff| 번역 메모리 시스템에서 내보낸 병렬 문서 형식입니다. 사용되는 언어는 파일 안에 정의되어 있습니다.|
|Markdown| markdown, mdown, mkdn, md, mkd, mdwn, mdtxt, mdtext, rmd| 서식이 지정된 텍스트를 만들기 위한 간단한 태그 언어입니다.|
|MHTML|mthml, mht| HTML 코드와 포함 리소스를 결합하는 데 사용되는 웹 페이지 보관 형식입니다.|
|Microsoft Excel|xls, xlsx|데이터 분석 및 설명서에 대한 스프레드 시트 파일입니다.|
|Microsoft Outlook|msg|Microsoft Outlook 내에서 만들어지거나 저장된 이메일 메시지입니다.|
|Microsoft PowerPoint|ppt, pptx| 슬라이드 쇼 형식으로 콘텐츠를 표시하는 데 사용되는 프레젠테이션 파일입니다.|
|Microsoft Word|doc, docx| 텍스트 문서 파일입니다.|
|OpenDocument 텍스트|odt|오픈 소스 텍스트 문서 파일입니다.|
|OpenDocument 프레젠테이션|odp|오픈 소스 프레젠테이션 파일입니다.|
|OpenDocument 스트레드시트|ods|오픈 소스 스프레드시트 파일입니다.|
|서식 있는 텍스트 형식|rtf|서식을 포함하는 텍스트 문서입니다.|
|탭으로 구분된 값/TAB|tsv/tab| 스프레드시트 프로그램에서 사용하는 탭으로 구분된 원시 데이터 파일입니다.|
|텍스트|txt| 서식이 지정되지 않은 텍스트 문서입니다.|

## <a name="supported-glossary-formats"></a>지원되는 글로서리 형식

문서 번역에서 지원되는 글로서리 파일 형식은 다음과 같습니다.

| 파일 형식| 파일 확장명|Description|
|---|---|--|
|쉼표로 구분된 값| csv |스프레드시트 프로그램에서 사용하는 쉼표로 구분된 원시 데이터 파일입니다.|
|지역화 교환 파일 형식| xlf , xliff| 번역 메모리 시스템에서 내보낸 병렬 문서 형식입니다. 사용되는 언어는 파일 내에 정의되어 있습니다.|
|탭으로 구분된 값/TAB|tsv, tab| 스프레드시트 프로그램에서 사용하는 탭으로 구분된 원시 데이터 파일입니다.|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [문서 번역 시작](get-started-with-document-translation.md)
