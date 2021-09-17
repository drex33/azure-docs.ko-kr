---
title: 음성 텍스트 변환 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 변환 소프트웨어를 사용하면 오디오 스트림을 텍스트로 실시간 전사할 수 있습니다. 애플리케이션, 도구 또는 디바이스는 이 텍스트 입력을 사용하고, 표시하고, 관련 작업을 수행할 수 있습니다. 이 문서는 음성 텍스트 변환 서비스의 이점과 기능에 대한 개요입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 음성 텍스트 변환, 음성 텍스트 변환 소프트웨어
ms.openlocfilehash: 7da34e76d760b8f29a90112c00a1f8f05655d940
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543985"
---
# <a name="what-is-speech-to-text"></a>음성 텍스트 변환이란?

이 개요에서는 음성 텍스트 변환 서비스의 이점과 기능에 대해 알아봅니다.
음성 인식이라고도 하는 음성 텍스트 변환을 사용하면 오디오 스트림을 텍스트로 실시간 전사할 수 있습니다. 애플리케이션, 도구 또는 디바이스는 이 텍스트를 명령 입력으로 사용하고, 표시하고, 관련 작업을 수행할 수 있습니다. 이 서비스는 Microsoft에서 Cortana 및 Office 제품에 사용하는 것과 동일한 인식 기술로 구동됩니다. <a href="./speech-translation.md" target="_blank">번역</a> 및 <a href="./text-to-speech.md" target="_blank">텍스트 음성 변환</a> 서비스 제품에서 원활하게 작동합니다. 사용 가능한 음성 텍스트 변환 언어의 전체 목록은 [지원되는 언어](language-support.md#speech-to-text)를 참조하세요.

음성-텍스트 변환 서비스는 기본적으로 유니버설 언어 모델을 사용합니다. 이 모델은 Microsoft 소유의 데이터를 사용하여 학습되었으며 클라우드에 배포됩니다. 이 모델은 대화형 및 받아쓰기 시나리오에 적합합니다. 고유 환경에서 인식 및 전사를 위해 음성-텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습할 수 있습니다. 사용자 지정은 주변 소음 또는 산업용 어휘 문제를 해결하는 데 유용합니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* **빠른 시작** 은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* **방법 가이드** 에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* **개념** 은 서비스 기능에 대한 심층적인 설명을 제공합니다.
* **자습서** 는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

> [!NOTE]
> Bing Speech는 2019년 10월 15일에 서비스가 해제되었습니다. 애플리케이션, 도구 또는 제품에서 Bing Speech API를 사용하는 경우 Speech Service로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.
> - [Bing Speech에서 Speech Service로 마이그레이션](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>시작

음성 텍스트 변환을 시작하려면 [빠른 시작](get-started-speech-to-text.md)을 참조하세요. 이 서비스는 [Speech SDK](speech-sdk.md), [REST API](rest-speech-to-text.md#pronunciation-assessment-parameters)및 [Speec성 CLI](spx-overview.md)를 통해 사용할 수 있습니다.

## <a name="sample-code"></a>예제 코드

Speech SDK의 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

- [음성 텍스트 변환 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [발음 평가 샘플(REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>사용자 지정

표준 Speech Service 모델 외에도 사용자 지정 모델을 만들 수 있습니다. 사용자 지정은 말하는 스타일, 어휘 및 배경 소음과 같은 음성 인식 장벽을 극복하는 데 도움이 됩니다 [Custom Speech](./custom-speech-overview.md)를 참조하세요. 사용자 지정 옵션은 언어/로캘에 따라 다릅니다. [지원되는 언어](./language-support.md)를 참조하여 지원을 확인하세요.

## <a name="batch-transcription"></a>일괄 처리 기록

일괄 처리 전사는 스토리지에 있는 많은 양의 오디오를 전사할 수 있는 REST API 작업 세트입니다. SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기적으로 전사 결과를 받을 수 있습니다. 일괄 처리 전사 API를 사용하는 방법에 대한 자세한 내용은 [방법](batch-transcription.md)을 참조하세요.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>다음 단계

- [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
- [Speech SDK 가져오기](speech-sdk.md)