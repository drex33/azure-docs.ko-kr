---
title: 소버린 클라우드 - Speech Service
titleSuffix: Azure Cognitive Services
description: 소버린 클라우드를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/24/2021
ms.author: alexeyo
ms.openlocfilehash: c7c140b1ff2d3d2151ad8ce01e3ad8f64660543d
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777894"
---
# <a name="speech-services-in-sovereign-clouds"></a>소버린 클라우드의 Speech Service

## <a name="azure-government-united-states"></a>Azure Government(미국)

미국 정부 기관 및 파트너에게만 제공됩니다. Azure Government에 대한 자세한 내용은 [여기](../../azure-government/documentation-government-welcome.md) 및 [여기](../../azure-government/compare-azure-government-global-azure.md)를 참조하세요.

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **지역:**
  - US Gov 애리조나
  - US Gov 버지니아
- **사용 가능한 가격 책정 계층:**
  - 무료(F0) 및 표준(S0). 자세한 내용은 [여기](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)를 참조하세요.
- **지원되는 기능:**
  - 음성 텍스트 변환
    - Custom Speech(AM(음향 모델) 및 LM(언어 모델) 적응)
      - [Speech Studio](https://speech.azure.us/)
  - 텍스트 음성 변환
    - 표준 음성
    - 인공신경망 음성
  - 음성 변환기
- **지원되지 않는 기능:**
  - Custom Voice
- **지원되는 언어:**
  - [여기](language-support.md)에서 지원되는 언어 목록을 참조하세요.

### <a name="endpoint-information"></a>엔드포인트 정보

이 섹션에는 [Speech SDK](speech-sdk.md), [Speech-to-text REST API](rest-speech-to-text.md) 및 [Text-to-speech REST API](rest-text-to-speech.md) 사용에 대한 Speech Services 엔드포인트 정보가 포함되어 있습니다.

#### <a name="speech-services-rest-api"></a>Speech Services REST API

Azure Government의 Speech Services REST API 엔드포인트 형식은 다음과 같습니다.

|  REST API 형식/작업 | 엔드포인트 형식 |
|--|--|
| 액세스 토큰 | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Speech-to-text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Text-to-Speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

`<REGION_IDENTIFIER>`를 다음 표의 구독 지역과 일치하는 식별자로 바꿉니다.

|                     | 지역 식별자 |
|--|--|
| **US Gov 애리조나**  | `usgovarizona` |
| **US Gov 버지니아** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech SDK

소버린 클라우드의 Speech SDK의 경우 `SpeechConfig` 클래스의 "호스트에서" 인스턴스화 또는 `--host`Speech CLI[의 ](spx-overview.md) 옵션을 사용해야 합니다. ("엔드포인트에서" 인스턴스화 및 `--endpoint` Speech CLI 옵션을 사용할 수도 있습니다.)

`SpeechConfig` 클래스는 다음과 같이 인스턴스화되어야 합니다.
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Speech CLI는 다음과 같이 사용해야 합니다(`--host` 옵션 참고).
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey`를 Speech 리소스 키로 바꿉니다. `usGovHost`를 필수 서비스 제공 사항 및 다음 표의 구독 지역과 일치하는 식으로 바꿉니다.

|  지역/서비스 제공 사항 | 호스트 식 |
|--|--|
| **US Gov 애리조나** | |
| 음성 텍스트 변환 | `wss://usgovarizona.stt.speech.azure.us` |
| Text to Speech | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov 버지니아** | |
| 음성 텍스트 변환 | `wss://usgovvirginia.stt.speech.azure.us` |
| Text to Speech | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure 중국

중국에 사업체가 있는 조직에서 사용할 수 있습니다. Azure 중국에 대한 자세한 내용은 [여기](/azure/china/overview-operations)를 참조하세요. 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **지역:**
  - 중국 동부 2
  - 중국 북부 2
- **사용 가능한 가격 책정 계층:**
  - 무료(F0) 및 표준(S0). 자세한 내용은 [여기](https://www.azure.cn/pricing/details/cognitive-services/index.html)를 참조하세요.
- **지원되는 기능:**
  - 음성 텍스트 변환
    - Custom Speech(AM(음향 모델) 및 LM(언어 모델) 적응)
      - [Speech Studio](https://speech.azure.cn/)
  - 텍스트 음성 변환
    - 표준 음성
    - 인공신경망 음성
  - 음성 변환기
- **지원되지 않는 기능:**
  - Custom Voice
- **지원되는 언어:**
  - [여기](language-support.md)에서 지원되는 언어 목록을 참조하세요.

### <a name="endpoint-information"></a>엔드포인트 정보

이 섹션에는 [Speech SDK](speech-sdk.md), [Speech-to-text REST API](rest-speech-to-text.md) 및 [Text-to-speech REST API](rest-text-to-speech.md) 사용에 대한 Speech Services 엔드포인트 정보가 포함되어 있습니다.

#### <a name="speech-services-rest-api"></a>Speech Services REST API

Azure 중국의 Speech Services REST API 엔드포인트 형식은 다음과 같습니다.

|  REST API 형식/작업 | 엔드포인트 형식 |
|--|--|
| 액세스 토큰 | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Speech-to-text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Text-to-Speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

`<REGION_IDENTIFIER>`를 다음 표의 구독 지역과 일치하는 식별자로 바꿉니다.

|                     | 지역 식별자 |
|--|--|
| **중국 동부 2**  | `chinaeast2` |
| **중국 북부 2**  | `chinanorth2` |

#### <a name="speech-sdk"></a>Speech SDK

소버린 클라우드의 Speech SDK의 경우 `SpeechConfig` 클래스의 "호스트에서" 인스턴스화 또는 `--host`Speech CLI[의 ](spx-overview.md) 옵션을 사용해야 합니다. ("엔드포인트에서" 인스턴스화 및 `--endpoint` Speech CLI 옵션을 사용할 수도 있습니다.)

`SpeechConfig` 클래스는 다음과 같이 인스턴스화되어야 합니다.
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Speech CLI는 다음과 같이 사용해야 합니다(`--host` 옵션 참고).
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey`를 Speech 리소스 키로 바꿉니다. `azCnHost`를 필수 서비스 제공 사항 및 다음 표의 구독 지역과 일치하는 식으로 바꿉니다.

|  지역/서비스 제공 사항 | 호스트 식 |
|--|--|
| **중국 동부 2** | |
| 음성 텍스트 변환 | `wss://chinaeast2.stt.speech.azure.cn` |
| Text to Speech | `https://chinaeast2.tts.speech.azure.cn` |
| **중국 북부 2** | |
| 음성 텍스트 변환 | `wss://chinanorth2.stt.speech.azure.cn` |
| Text to Speech | `https://chinanorth2.tts.speech.azure.cn` |