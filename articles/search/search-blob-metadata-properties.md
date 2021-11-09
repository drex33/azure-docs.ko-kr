---
title: 콘텐츠 메타데이터 속성
titleSuffix: Azure Cognitive Search
description: 문서의 메타데이터 속성은 검색 인덱스의 필드에 콘텐츠를 제공하거나 런타임에 인덱싱 동작을 알리는 정보를 제공할 수 있습니다. 해당 문서에는 Azure Cognitive Search에서 지원되는 메타데이터 속성을 나열합니다.
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: c6f02a50160d650f44e9793dbfa59e0dbf58ebbc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055572"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Azure Cognitive Search에서 사용하는 콘텐츠 메타데이터 속성

SharePoint Online 및 Azure Blob Storage는 다양한 콘텐츠를 포함할 수 있으며, 이러한 콘텐츠 형식 가운데 다수에는 인덱싱에 유용하게 사용할 수 있는 메타데이터 속성이 있습니다. **`metadata_storage_name`** 같은 표준 Blob 속성 관련 검색 필드를 생성할 수 있는 것과 마찬가지로 어떤 문서 형식에 대해 지정된 메타데이터 속성 관련 필드를 만들 수 있습니다.

## <a name="supported-document-formats"></a>지원되는 문서 형식

Cognitive Search는 다음 문서 형식에 대해 Blob 인덱싱과 SharePoint Online 문서 인덱싱을 지원합니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>문서 형식별 속성

다음 표에서는 각 문서 형식에 대해 수행된 처리를 요약하고 Blob 인덱서 및 SharePoint Online 인덱서에서 추출한 메타데이터 속성에 관해 설명합니다.

| 문서 형식/콘텐츠 형식 | 추출한 메타데이터 | 처리 세부 정보 |
| --- | --- | --- |
| HTML(text/html 또는 application/xhtml+xml) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML 태그를 제거하고 텍스트 추출 |
| PDF(application/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`<br/>`metadata_creation_date` |포함된 문서를 비롯한 텍스트 추출(이미지 제외) |
| DOCX(application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| DOC(application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| DOCM(application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| WORD XML(application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML 태그를 제거하고 텍스트 추출 |
| WORD 2003 XML(application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML 태그를 제거하고 텍스트 추출 |
| XLSX(application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| XLS(application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| XLSM(application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| PPTX(application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPT(application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPTM(application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| MSG(application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |첨부 파일에서 추출한 텍스트를 포함해 텍스트를 추출합니다. `metadata_message_to_email`, `metadata_message_cc_email`, `metadata_message_bcc_email`은 문자열 컬렉션이며 나머지 필드는 문자열입니다.|
| ODT(application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| ODS(application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| ODP(application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| ZIP(application/zip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| GZ(application/gzip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| EPUB(application/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |보관 파일의 모든 문서에서 텍스트 추출 |
| XML(application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> |XML 태그를 제거하고 텍스트 추출 |
| KML(application/vnd.google-earth.kml+xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> |XML 태그를 제거하고 텍스트 추출 |
| JSON(application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |텍스트 추출<br/>참고: JSON BLOB에서 여러 문서 필드를 추출해야 하는 경우 자세한 내용은 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md)을 참조하세요. |
| EML(메시지/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |첨부 파일을 비롯한 텍스트 추출 |
| RTF(application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 텍스트 추출|
| 일반 텍스트(text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/>`metadata_language`<br/> | 텍스트 추출|
| CSV(text/csv) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 텍스트 추출<br/>참고: CSV blob에서 여러 문서 필드를 추출해야 하는 경우 자세한 내용은 [CSV blob 인덱싱](search-howto-index-csv-blobs.md)을 참조하세요. |

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
* [AI를 사용하는 Blob 이해하기](search-blob-ai-integration.md)
* [Blob 인덱싱 개요](search-blob-storage-integration.md)
* [SharePoint Online 인덱싱](search-howto-index-sharepoint-online.md)