---
title: Extractive 요약 API를 사용 하 여 텍스트 요약
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 extractive 요약 API를 사용 하 여 텍스트를 요약 하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-summarization, ignite-fall-2021
ms.openlocfilehash: 21be719780b4633456ceea89c0cb6c6295480348
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103615"
---
# <a name="how-to-use-text-summarization-preview"></a>텍스트 요약을 사용 하는 방법 (미리 보기)

> [!IMPORTANT] 
> Extractive 요약 기능은 "있는 그대로" 및 "모든 오류를 포함 하 여" 제공 되는 미리 보기 기능입니다. 따라서 Extractive 요약 (미리 보기)은 프로덕션 환경에서 구현 하거나 배포 해서는 안 됩니다. 고객은 extractive 요약의 사용을 전적으로 책임 집니다. 

일반적으로 자동 텍스트 요약에는 추출 및 추상의 두 가지 접근 방식이 있습니다. 이 API는 extractive 요약을 제공 합니다.

Extractive summary는 원래 콘텐츠 내에서 가장 중요 하거나 관련 된 정보를 통칭 하는 문장을 추출 하 여 요약을 생성 하는 기능입니다.

이 기능은 사용자가 너무 길어서 읽을 수 없다고 간주하는 콘텐츠를 줄이도록 디자인됩니다. 추출 요약은 기사, 보고서 또는 문서를 핵심 문장으로 압축합니다.

API에서 사용하는 AI 모델은 서비스를 통해 제공되며, 분석 용도로만 콘텐츠를 전송해야 합니다.

## <a name="features"></a>기능

> [!TIP]
> 이 기능을 사용하려면 [빠른 시작 문서](../quickstart.md)에 따라 시작합니다. 코드를 작성할 필요 없이 [Language Studio](../../language-studio.md)를 사용하여 예제 요청을 만들 수도 있습니다.

Extractive 요약 API는 자연어 처리 기술을 사용 하 여 구조화 되지 않은 텍스트 문서에서 키 문장을 찾습니다. 이 문장은 문서의 기본 아이디어를 집합적으로 전달합니다.

추출 요약은 추출된 문장, 원본 문서 내 위치와 함께 순위 점수를 시스템 응답의 일부로 반환합니다. 순위 점수는 문장과 문서의 기본 아이디어 간 관련성을 나타내는 표시기입니다. 모델은 각 문장에 대해 0~1(포함) 사이의 점수를 제공하고 요청당 점수가 가장 높은 문장을 반환합니다. 예를 들어, 3개 문장 요약을 요청하는 경우 서비스는 점수가 가장 높은 3개 문장을 반환합니다.

핵심 [정보를 추출할](./../../key-phrase-extraction/how-to/call-api.md)수 있는 언어에 대 한 Azure 인식 서비스의 또 다른 기능이 있습니다. 핵심 구 추출과 추출 요약 중에서 결정하는 경우 다음을 고려합니다.
* 핵심 구 추출은 구를 반환하는 반면, 추출 요약은 문장을 반환합니다.
* 추출 요약은 순위 점수와 함께 문장을 반환합니다. 요청당 순위가 가장 높은 문장이 반환됩니다.

## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-text-summarization-model"></a>텍스트 요약 모델 지정

기본적으로 텍스트 요약은 텍스트에서 사용 가능한 최신 AI 모델을 사용 합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정 하는 모델은 텍스트 요약 작업을 수행 하는 데 사용 됩니다.

| 지원되는 버전 | 최신 버전 |
|--|--|
| `2021-08-01` | `2021-08-01` |

### <a name="input-languages"></a>입력 언어

키 구 추출에서 처리할 문서를 제출 하는 경우 [지원 되는 언어](../language-support.md) 중에서 작성 된 언어를 지정할 수 있습니다. 언어를 지정 하지 않으면 핵심 문구 추출은 기본적으로 영어로 표시 됩니다. API는 다양한 [다국어 및 그림 이모티콘 인코딩](../../concepts/multilingual-emoji-support.md)을 지원하기 위해 응답에 오프셋을 반환할 수 있습니다. 

## <a name="submitting-data"></a>데이터 제출

문서를 텍스트 문자열로 API에 제출 합니다. 요청을 받으면 분석이 수행됩니다. API는 비동기 이므로 API 요청을 보내고 결과를 받는 사이에 지연이 있을 수 있습니다.  분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래 데이터 제한을 참조하세요.

이 기능을 사용 하는 경우 요청이 수집 된 시간부터 24 시간 동안 API 결과를 사용할 수 있으며 응답에 표시 됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.

매개 변수를 사용 하 여 반환 되는 `sentenceCount` 문장의 수를 기본값으로 지정할 수 있습니다 `3` . 범위는 1~20 사이입니다.

매개 변수를 사용 하 여 `sortby` 추출 된 문장이 반환 되는 순서를 지정할 수도 있습니다 `Offset` `Rank` `Offset` . 기본값은입니다. 


|매개 변수 값  |설명  |
|---------|---------|
|순위    | 서비스에서 결정 한 대로 입력 문서에 대 한 관련성에 따라 문장을 정렬 합니다.        |
|Offset    | 입력 문서에서 문장이 나타나는 원래 순서를 유지 합니다.        |

## <a name="getting-text-summarization-results"></a>텍스트 요약 결과 가져오기

언어 감지에서 결과를 가져올 때 애플리케이션에 결과를 스트리밍하거나 로컬 시스템의 파일에 출력을 저장할 수 있습니다.

다음은 통합 AI에 대 한 [전체적인 표현](https://www.microsoft.com/research/blog/a-holistic-representation-toward-integrative-ai/)으로 Microsoft 블로그 문서를 사용 하 여 추출 된 요약에 제출할 수 있는 콘텐츠의 예입니다. 이 문서는 한 예에 불과합니다. API는 훨씬 긴 입력 텍스트를 허용할 수 있습니다. 자세한 내용은 데이터 제한 섹션을 참조 하세요.
 
*"Microsoft에서는 학습 하 고 이해 하는 데 보다 전체적이 고 이해 하는 방법을 사용 하 여 기존 기술을 벗어나는 AI를 제공 하 고 있습니다. Azure AI Cognitive Services의 최고 기술 책임자는이 퀘스트를 현실로 전환 하기 위해 놀라운 과학자 및 엔지니어 팀과 협력 하 고 있습니다. 내 역할에서 사용자는 cognition의 세 가지 특성, 즉 monolingual text (X), audio 또는 visual 토대로 신호, (Y) 및 다국어 (Z) 간의 관계를 볼 때 고유한 큐브 뷰를 활용 합니다. 세 가지를 모두 사용할 경우에는 시각적으로 말할 수 있습니다. 그림 1에 나와 있는 바와 같이 XYZ-코드를 호출 하는 것은 사람에 게 더 나은 기능을 제공 하 고, 듣고, 보고, 이해할 수 있는 강력한 AI를 만드는 공동 표현입니다. XYZ-코드를 통해 장기적인 비전 (도메인 간 전송 학습, 소프트웨어나 및 언어 확장)을 수행할 수 있습니다. 목표는 오늘날에는 대부분의 다운스트림 AI 작업을 지원 하기 위해 표현을 공동으로 학습할 수 있는 미리 학습 된 모델을 보유 하는 것입니다. 지난 5 년간는 대화형 음성 인식, 기계 번역, 대화형 질문 답변, 기계 판독값 이해 및 이미지 자막의 벤치 마크에 대 한 인간 성과를 달성 했습니다. 이러한 5 가지 돌파구는 AI 기능에서 도약을 생성 하 고, 사람이 학습 하 고 이해 하는 방법에 더 가까운 multisensory 및 다국어 학습을 달성 하는 원대한 위해서라도에 대 한 강력한 신호를 제공 했습니다. 다운스트림 AI 작업에서 외부 기술 자료를 사용 하 여 접지 한 경우에는이 위해서라도의 기본 구성 요소입니다.*

Extractive 요약 API는 API 백 엔드에 대 한 작업을 만들어 요청이 수신 될 때 수행 됩니다. 작업이 성공 하면 API의 출력이 반환 됩니다. 출력은 24 시간 동안 검색에 사용할 수 있습니다. 이 시간이 지나면 출력이 제거 됩니다. 다국어 지원 및 emoji 지원으로 인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 자세한 내용은 [오프셋 처리 방법](../../concepts/multilingual-emoji-support.md)을 참조하세요.

위의 예제를 사용 하 여 API는 다음과 같은 요약 된 문장을 반환할 수 있습니다.

*"Microsoft는 사용자가 학습 하 고 이해 하는 데 보다 전체적이 고 이해 하는 방법을 사용 하 여 기존 기술을 벗어나는 AI를 제공 하 고 있습니다."*

*"내 역할에서 cognition의 세 가지 특성, 즉 monolingual text (X), audio 또는 visual 토대로 신호, (Y) 및 다국어 (Z)의 세 가지 특성 간 관계를 볼 때 고유한 큐브 뷰를 이용 합니다.*

*"세는 모두 교차로 표시 됩니다. 그림 1에 나와 있는 바와 같이 XYZ-코드를 호출 하는 것은 사람에 게 더 나은 기능을 제공 하 고, 듣고, 보고, 이해할 수 있는 강력한 AI를 만드는 공동 표현입니다."*


## <a name="data-limits"></a>데이터 제한

이 섹션에서는 크기에 대 한 제한과 텍스트 요약 API에 데이터를 보낼 수 있는 속도에 대해 설명 합니다. 가격 책정은 데이터 제한 또는 속도 제한의 영향을 받지 않습니다. 가격 책정에는 언어 리소스 [가격 정보가](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)적용 됩니다.

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 요청당 최대 문자 수  | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정되는 모든 제출된 문서에서 125K 문자를 계산합니다. |
| 요청당 최대 문서 수 | 25 |

문서가 제한을 초과 하는 경우 API는 전체 요청을 거부 하 고 `400 bad request` 그 안에 포함 된 문서가 최대 크기를 초과 하는 경우 오류를 반환 합니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>참고 항목

* [텍스트 요약 개요](../overview.md)
