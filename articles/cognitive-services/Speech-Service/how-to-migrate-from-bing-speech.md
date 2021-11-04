---
title: Bing Speech에서 Speech Service로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 기존 Bing Speech 구독에서 Azure Cognitive Services의 Speech Service로 마이그레이션하는 방법에 대해 알아봅니다.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: eur
ms.openlocfilehash: d4c1cef5df0ab30eb0413904bcfd2cdc4bbabca9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508328"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Bing Speech에서 Speech Service로 마이그레이션

이 문서를 사용하여 Bing Speech API에서 Speech Service로 애플리케이션을 마이그레이션합니다.

이 문서에서는 Bing Speech API와 Speech Service 간의 차이점을 간략하게 설명하고 애플리케이션을 마이그레이션하기 위한 전략을 제안합니다. Bing Speech API 구독 키는 Speech Service에서 작동하지 않으므로 새 Speech Service 구독이 필요합니다.

단일 Speech Service 구독 키는 다음 기능에 대한 액세스 권한을 부여합니다. 각 기능은 별도로 측정되므로 사용하는 기능에 대해서만 요금이 청구됩니다.

* [음성 텍스트 변환](speech-to-text.md)
* [사용자 지정 음성 텍스트 변환](./custom-speech-overview.md)
* [텍스트 음성 변환](text-to-speech.md)
* [사용자 지정 텍스트 음성 변환](./how-to-custom-voice-create-voice.md)
* [음성 번역](speech-translation.md)([텍스트 번역](../translator/translator-overview.md) 제외)

[Speech SDK](speech-sdk.md) 는 기능 측면에서 Bing Speech 클라이언트 라이브러리를 대신하지만 다른 API를 사용합니다.

## <a name="comparison-of-features"></a>기능 비교

Speech Service는 Bing Speech와 매우 비슷하지만, 다음과 같은 차이점이 있습니다.

| 기능 | Bing Speech | Speech Service | 세부 정보 |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service는 Windows 10, UWP(유니버설 Windows 플랫폼) 및 .NET Standard 2.0을 지원합니다. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Speech Service는 Windows와 Linux를 지원합니다. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service는 Android와 음성 디바이스를 지원합니다. |
| 연속 음성 인식 | 10분 | 제한 없음 | Speech SDK는 무제한 연속 인식을 지원하고 시간 초과 또는 연결 해제 시 자동으로 다시 연결합니다. |
| 부분 또는 중간 결과 | :heavy_check_mark: | :heavy_check_mark: | Speech SDK에서 지원됩니다. |
| Custom Speech 모델 | :heavy_check_mark: | :heavy_check_mark: | Bing Speech에는 별도의 Custom Speech 구독이 필요합니다. |
| 사용자 지정 음성 글꼴 | :heavy_check_mark: | :heavy_check_mark: | Bing Speech에는 별도의 Custom Voice 구독이 필요합니다. |
| 24kHz 음성 | :heavy_minus_sign: | :heavy_check_mark: |
| 음성 의도 인식 | 별도 LUIS API 호출이 필요합니다. | 통합형(SDK 사용) | Speech Service에서 LUIS 키를 사용할 수 있습니다. |
| 간단한 의도 인식 | :heavy_minus_sign: | :heavy_check_mark: |
| 긴 오디오 파일의 일괄 처리 기록 | :heavy_minus_sign: | :heavy_check_mark: |
| 인식 모드 | 엔드포인트 URI 통해 수동 | 자동 | 인식 모드를 Speech Service에서 사용할 수 없습니다. |
| 엔드포인트 위치 | 전역 | 지역 | 지역별 엔드포인트가 대기 시간을 향상시킵니다. |
| REST API | :heavy_check_mark: | :heavy_check_mark: | Speech Service REST API는 Bing Speech(다른 엔드포인트)와 호환됩니다. REST API는 텍스트 음성 변환 및 제한된 음성 텍스트 변환 기능을 지원합니다. |
| WebSocket 프로토콜 | :heavy_check_mark: | :heavy_minus_sign: | Speech SDK는 서비스에 대한 지속적인 연결이 필요한 기능을 위해 웹 소켓 연결을 추상화하므로 더 이상 수동으로 구독할 수 없습니다. |
| 서비스 간 API 호출 | :heavy_check_mark: | :heavy_minus_sign: | C# 서비스 라이브러리를 통해 Bing Speech에 제공됩니다. |
| 오픈 소스 SDK | :heavy_check_mark: | :heavy_minus_sign: |

Speech Service는 트랜잭션 기반 모델 대신 시간 기반 가격 책정 모델을 사용합니다. 자세한 내용은 [Speech Service 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

## <a name="migration-strategies"></a>마이그레이션 전략

사용자 또는 조직의 개발 또는 프로덕션 환경에 Bing Speech API를 사용하는 애플리케이션이 있는 경우 최대한 빨리 Speech Service를 사용하도록 업데이트해야 합니다. 사용 가능한 SDK, 코드 샘플 및 자습서에 대해서는 [Speech Service](index.yml) 설명서를 참조하세요.

Speech Service [REST API](./overview.md#reference-docs)는 Bing Speech API와 호환됩니다. 현재 Bing Speech REST API를 사용 중인 경우 REST 엔드포인트만 변경하고 Speech Service 구독 키로 전환해야 합니다.

특정 프로그래밍 언어에 대해 Bing Speech 클라이언트 라이브러리를 사용하는 경우 API가 다르기 때문에 [Speech SDK](speech-sdk.md)에서 사용 중인 애플리케이션으로 변경해야 합니다. Speech SDK는 새 기능에 액세스하면서 코드를 좀 더 간단히 만들어줄 수 있습니다. Speech SDK는 다양한 프로그래밍 언어로 제공됩니다. 모든 플랫폼의 API는 비슷하며, 다중 플랫폼 개발을 용이하게 수행할 수 있습니다.

Speech Service는 전역 엔드포인트를 제공하지 않습니다. 애플리케이션이 모든 해당 트래픽에 대해 단일 지역별 엔드포인트를 사용할 때 효율적으로 작동하는지를 확인합니다. 그렇지 않을 경우 지리적 위치를 사용하여 가장 효율적인 엔드포인트를 확인합니다. 사용하는 각 지역에 별도의 Speech Service 구독이 필요합니다.

Speech Devices SDK를 시작하려면

1. [Speech SDK](speech-sdk.md)를 다운로드합니다.
1. Speech Service [빠른 시작 가이드](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) 및 [자습서](how-to-recognize-intents-from-speech-csharp.md)를 수행합니다. 또한 [코드 샘플](./speech-sdk.md#sample-source-code)을 살펴보고 새 API를 경험합니다.
1. Speech Service를 사용하도록 애플리케이션을 업데이트합니다.

## <a name="support"></a>지원

Bing Speech 고객은 [지원 티켓](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 개설하여 고객 지원 서비스에 문의해야 합니다. 지원을 받으려면 [기술 지원 플랜](https://azure.microsoft.com/support/plans/)이 필요한 경우에도 Microsoft로 연락 주시면 됩니다.

Speech Service, SDK 및 API 지원이 필요한 경우, Speech Service [지원 페이지](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Speech Service 체험해 보기](overview.md#try-the-speech-service-for-free)
* [음성 텍스트 변환 시작](get-started-speech-to-text.md)
* [텍스트 음성 변환 시작](get-started-text-to-speech.md)

## <a name="see-also"></a>참고 항목

* [Speech Service 릴리스 정보](releasenotes.md)
* [Speech Service란?](overview.md)
* [Speech Service 및 Speech SDK 설명서](speech-sdk.md#get-the-speech-sdk)
