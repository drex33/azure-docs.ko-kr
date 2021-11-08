---
title: 음성 번역 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: 음성 번역을 사용하면 실시간 엔드투엔드 다중 언어 번역을 애플리케이션, 도구 및 디바이스에 추가할 수 있습니다. 같은 API를 음성 대 음성 및 음성 대 텍스트 번역 모두에 사용될 수 있습니다. 이 문서는 음성 번역 서비스의 이점과 기능을 개략적으로 설명합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: eur
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: 음성 번역
ms.openlocfilehash: 13d82c334504b2d396f10b1c80b99c0c264c9ea0
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988884"
---
# <a name="what-is-speech-translation"></a>음성 번역이란?

이 개요에서는 오디오 스트림의 실시간 [다중 언어 음성-음성](language-support.md#speech-translation) 및 음성 텍스트 변환을 지원하는 음성 번역 서비의 이점과 기능에 대해 알아봅니다. Speech SDK를 사용하면 애플리케이션, 도구 및 디바이스를 통해 제공된 오디오의 원본 대화 내용 기록과 번역 결과에 액세스할 수 있습니다. 음성이 감지되면 임시 기록 및 번역 결과가 반환되고 최종 결과가 합성 음성으로 변환될 수 있습니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* **빠른 시작** 은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* **방법 가이드** 에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* **개념** 은 서비스 기능에 대한 심층적인 설명을 제공합니다.
* **자습서** 는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="core-features"></a>핵심 기능

* 인식 결과가 포함된 음성 텍스트 변환.
* 음성을 음성으로 변환.
* 여러 대상 언어로의 변환을 지원합니다.
* 중간 인식 및 번역 결과.

## <a name="get-started"></a>시작 

음성 번역을 시작하려면 [빠른 시작](get-started-speech-translation.md)을 참조하세요. 음성 번역 서비스는 [Speech SDK](speech-sdk.md) 및 [Speech CLI](spx-overview.md)를 통해 사용할 수 있습니다.

## <a name="sample-code"></a>예제 코드

Speech SDK의 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 싱글샷 인식/번역, 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

* [음성 텍스트 변환 및 번역 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>마이그레이션 가이드

애플리케이션, 도구 또는 제품에서 [Translator Speech API](./how-to-migrate-from-translator-speech-api.md)를 사용하는 경우 Speech Service로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.

* [Translator Speech API에서 Speech Service로 마이그레이션](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](./speech-sdk.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
* [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
* [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>다음 단계

* 음성 번역 [빠른 시작](get-started-speech-translation.md) 완료
* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [Speech SDK 가져오기](speech-sdk.md)
