---
title: OCR(광학 문자 인식)이란?
titleSuffix: Azure Cognitive Services
description: OCR(광학 문자 인식) 서비스는 이미지에서 표시되는 텍스트를 추출하여 구조화된 문자열로 반환합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 02654c3f196b6c3bc199e636b3601777ed372a99
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992537"
---
# <a name="what-is-optical-character-recognition"></a>OCR(광학 문자 인식)이란?

OCR(광학 문자 인식)을 사용하면 문서&mdash;송장, 청구서, 재무 보고서, 문서 등과 함께 도로 표지판 및 제품 사진과 같은 이미지에서 인쇄되거나 필기된 텍스트를 추출할 수 있습니다. Microsoft OCR 기술은 [여러 언어](./language-support.md)로 인쇄된 텍스트 추출을 지원합니다. [빠른 시작](./quickstarts-sdk/client-library.md)을 따라 시작하세요.

![OCR 데모](./Images/ocr-demo.gif)

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./quickstarts-sdk/client-library.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./Vision-API-How-to-Topics/call-read-api.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>읽기 API 

Computer Vision [Read API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)는 이미지 및 다중 페이지 PDF 문서에서 인쇄된 텍스트(여러 언어로), 필기 텍스트(여러 언어로), 숫자 및 통화 기호를 추출하는 Azure의 최신 OCR 기술([새로운 기능 알아보기](./whats-new.md))입니다. 여러 언어가 혼합된 텍스트를 많이 사용한 이미지 및 다중 페이지 PDF에서 텍스트를 추출하는 데 최적화되어 있습니다. 동일한 이미지나 문서에서 인쇄된 텍스트와 필기 텍스트를 모두 검색하는 것을 지원합니다.

![OCR이 이미지 및 문서를 추출된 텍스트가 포함된 구조화된 출력으로 변환하는 방식](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>입력 요구 사항

**Read** 호출에서는 이미지와 문서를 입력으로 사용합니다. 다음과 같은 요구 사항이 적용됩니다.

* 지원되는 파일 형식: JPEG, PNG, BMP, PDF 및 TIFF
* PDF 및 TIFF 파일의 경우 최대 2000페이지(무료 계층의 경우 처음 두 페이지만)가 처리됩니다.
* 파일 크기는 50MB(무료 계층의 경우 6MB) 미만이어야 하며, 크기는 최소 50 x 50픽셀 및 최대 10000 x 10000 픽셀 이상이어야 합니다. 

## <a name="supported-languages"></a>지원되는 언어
Read API는 미리 보기 언어 및 기능을 포함하여 인쇄 텍스트의 경우 122개의 언어와 필기 텍스트의 경우 7개의 언어를 지원합니다.

인쇄 텍스트용 OCR에는 최신 미리 보기 업데이트를 통해 라틴어 및 키릴 자모 언어와 함께 영어, 프랑스어, 독일어, 이탈리아어, 포르투갈어, 스페인어, 중국어, 일본어, 한국어 및 러시아어(미리 보기)가 지원됩니다.

필기 테스트용 OCR에는 영어 지원과 프랑스어, 독일어, 이탈리아어, 포르투갈어, 스페인어 및 중국어 지원의 미리 보기가 포함됩니다.

미리 보기 언어 및 기능을 사용하려면 [모델 버전을 지정하는 방법](./Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional)을 참조하세요. [OCR 지원 언어](./language-support.md#optical-character-recognition-ocr)의 전체 목록을 참조하세요. 미리 보기 모델에는 현재 GA 버전에 대한 향상된 기능이 포함되어 있습니다.

## <a name="key-features"></a>주요 특징

Read API에는 다음 기능이 포함됩니다.

* 122개 언어로 인쇄 텍스트 추출
* 7개 언어로 작성한 텍스트 추출
* 위치 및 신뢰도 점수가 있는 텍스트 줄 및 단어
* 언어 식별이 필요하지 않음
* 혼합 언어, 혼합 모드 지원(인쇄 및 필기)
* 여러 페이지로 구성된 대형 문서에서 페이지 및 페이지 범위 선택
* 텍스트 줄 출력을 위한 자연스러운 읽기 순서 옵션(라틴어만 해당)
* 텍스트 줄에 대한 필기 분류(라틴어만 해당)
* 온-프레미스 배포를 위한 Distroless Docker 컨테이너로 사용 가능

[OCR 기능을 사용하는 방법](./vision-api-how-to-topics/call-read-api.md)에 대해 알아봅니다.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>클라우드 API 사용 또는 온-프레미스 배포
Read 3.x 클라우드 API는 편리한 통합 및 빠른 생산성 덕분에 대부분의 고객에게 선호되는 옵션입니다. Azure와 Computer Vision 서비스는 사용자가 고객의 요구를 충족하는 데 집중하는 동안 규모, 성능, 데이터 보안 및 규정 준수 요구 사항을 처리합니다.

온-프레미스 배포의 경우 [Docker 컨테이너 읽기(미리 보기)](./computer-vision-how-to-install-containers.md)를 사용하여 새 OCR 기능을 자체 로컬 환경에 배포할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다.

> [!WARNING]
> Computer Vision 2.0 RecognizeText 작업은 이 문서에서 다루는 새로운 [읽기 API](#read-api)에서 더 이상 사용되지 않는 추세입니다. 기존 고객은 [Read 작업을 사용하도록 전환](upgrade-api-versions.md)해야 합니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Computer Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

- [OCR(Read) REST API 또는 클라이언트 라이브러리 빠른 시작](./quickstarts-sdk/client-library.md)을 시작하세요.
- [Read 3.2 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)에 대해 알아봅니다.
