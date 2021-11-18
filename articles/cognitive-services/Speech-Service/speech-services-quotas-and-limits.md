---
title: Speech Service 할당량 및 제한
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Speech Service 할당량 및 한도에 대한 빠른 참조, 자세한 설명 및 모범 사례
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: alexeyo
ms.openlocfilehash: 2a660375e8ffa3fd77e58d963fbc5c7e3fa1849c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706519"
---
# <a name="speech-service-quotas-and-limits"></a>Speech Service 할당량 및 제한

이 문서에는 모든 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)에서의 Azure Cognitive Speech Service 할당량 및 한도에 대한 빠른 참조 및 **자세한 설명** 이 포함되어 있습니다. 또한 요청 제한을 방지하기 위한 몇 가지 모범 사례가 포함되어 있습니다.

## <a name="quotas-and-limits-quick-reference"></a>할당량 및 한도 빠른 참조
[텍스트 음성 변환 할당량 및 한도](#text-to-speech-quotas-and-limits-per-speech-resource)로 이동
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>음성 리소스당 음성 텍스트 변환 할당량 및 한도
아래 표에서 "조정 가능" 행이 없는 매개 변수는 모든 가격 계층에서 조정할 수 **없습니다**.

#### <a name="online-transcription"></a>온라인 대화 내용 기록
[Speech SDK](speech-sdk.md) 및/또는 [짧은 오디오용 Speech-to-Text REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)와 함께 사용합니다.

| 할당량 | 무료(F0)<sup>1</sup> | Standard(S0) |
|--|--|--|
| **동시 요청 제한 -기본 모델 엔드포인트** | 1 | 100(기본값) |
| 조정 가능 | 아니요<sup>2</sup> | 예<sup>2</sup> |
| **동시 요청 제한 - 사용자 지정 엔드포인트** | 1 | 100(기본값) |
| 조정 가능 | 아니요<sup>2</sup> | 예<sup>2</sup> |

#### <a name="batch-transcription"></a>일괄 처리 기록
| 할당량 | 무료(F0)<sup>1</sup> | Standard(S0) |
|--|--|--|
| [Speech-to-Text REST API V2.0 및 v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 제한 | F0에 대한 일괄 처리 대화 내용 기록을 사용할 수 없음 | 분당 요청 300개 |
| 최대 오디오 입력 파일 크기 | 해당 없음 | 1GB |
| 최대 입력 Blob 크기(예: 하나의 zip 보관 파일에 두 개 이상의 파일을 포함할 수 있음, 위의 파일 크기 한도에 주의해야 함) | 해당 없음 | 2.5GB |
| 최대 Blob 컨테이너 크기 | 해당 없음 | 5GB |
| 컨테이너당 최대 Blob 수 | 해당 없음 | 10000 |
| 대화 내용 기록 요청당 최대 파일 수(여러 콘텐츠 URL을 입력으로 사용하는 경우) | 해당 없음 | 1000  |

#### <a name="model-customization"></a>모델 사용자 지정
| 할당량 | 무료(F0)<sup>1</sup> | Standard(S0) |
|--|--|--|
| REST API 제한 | 분당 요청 300개 | 분당 요청 300개 |
| 최대 음성 데이터 세트 수 | 2 | 500 |
| 데이터 가져오기에 대한 최대 음향 데이터 세트 파일 크기 | 2GB | 2GB |
| 데이터 가져오기에 대한 최대 언어 데이터 세트 파일 크기 | 200MB | 1.5GB |
| 데이터 가져오기에 대한 최대 발음 데이터 세트 파일 크기 | 1KB | 1MB |
| [Create Model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API 요청에서 `text` 매개 변수를 사용하는 경우 최대 텍스트 크기 | 200KB | 500KB |

<sup>1</sup> **무료(F0)** 가격 책정 계층의 경우 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)의 월간 허용량도 참조하세요.<br/>
<sup>2</sup> [추가 설명](#detailed-description-quota-adjustment-and-best-practices), [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling) 및 [조정 지침](#speech-to-text-increasing-online-transcription-concurrent-request-limit)을 참조하세요.<br/>

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>음성 리소스당 텍스트 음성 변환 할당량 및 한도
아래 표에서 "조정 가능" 행이 없는 매개 변수는 모든 가격 계층에서 조정할 수 **없습니다**.

#### <a name="general"></a>일반

| 할당량 | 무료(F0)<sup>3</sup> | Standard(S0) |
|--|--|--|
| **음성 리소스별 최대 TPS(초당 트랜잭션) 수** |  |  |
| 실시간 데이터 API 표준, 신경망, 사용자 지정 및 사용자 지정 신경망 음성 | 200<sup>4</sup> | 200<sup>4</sup> |
| 조정 가능 | 아니요<sup>4</sup> | 예<sup>4</sup> |
| **HTTP 관련 할당량** |  |  |
| 요청당 생성되는 최대 오디오 길이 | 10분 | 10분 |
| SSML의 최대 고유 `<voice>` 및 `<audio>` 태그 수 | 50 | 50 |
| **웹소켓 특정 할당량** |  |  |
| 턴당 생성되는 최대 오디오 길이 | 10분 | 10분 |
| SSML의 최대 고유 `<voice>` 및 `<audio>` 태그 수 | 50 | 50 |
| 턴당 최대 SSML 메시지 크기 | 64KB | 64KB |

#### <a name="long-audio-api"></a>긴 오디오 API

| 할당량 | 무료(F0)<sup>3</sup> | Standard(S0) |
|--|--|--|
| 최소 텍스트 길이 | 해당 없음 | 일반 텍스트의 경우 400자, SSML의 [청구 가능 문자](text-to-speech.md#pricing-note)의 경우 400자 |
| 최대 텍스트 길이 | 해당 없음 | 10000개 단락 |
| 시작 시간 | 해당 없음 | 10개 태스크 또는 10000자 누적 |

#### <a name="custom-neural-voice-and-custom-voicesup6sup"></a>사용자 지정 신경망 음성 및 사용자 지정 음성<sup>6</sup>

| 할당량 | 무료(F0)<sup>3</sup> | Standard(S0) |
|--|--|--|
| 음성 리소스별 최대 TPS(초당 트랜잭션) 수 | [위의 내용 참조](#general) | [위의 내용 참조](#general) |
| 음성 리소스별 최대 데이터 세트 수 | 10 | 500 |
| 음성 리소스별 최대 동시 데이터 세트 업로드 수 | 2 | 5 |
| 데이터 세트당 데이터 가져오기의 최대 데이터 파일 크기 | 2GB | 2GB |
| 긴 오디오 또는 스크립트가 없는 오디오 업로드 | 아니요 | 예 |
| 음성 리소스별 최대 동시 모델 학습 수 | 1 (사용자 지정<sup>6</sup>만 해당) | 3 |
| 음성 리소스별 최대 사용자 지정 엔드포인트 수 | 1 (사용자 지정<sup>6</sup>만 해당) | 50 |
| **사용자 지정 신경망 음성에 대한 동시 요청 제한** |  |  |
| 기본값 | 해당 없음 | 10 |
| 조정 가능 | 해당 없음 | 예<sup>5</sup> |
| **사용자 지정 음성에 대한 동시 요청 제한 <sup></sup>6** |  |  |
| 기본값 | 10 | 10 |
| 조정 가능 | 아니요<sup>5</sup> | 예<sup>5</sup> |

<sup>3</sup> **무료(F0)** 가격 책정 계층의 경우 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)의 월간 허용량도 참조하세요.<br/>
<sup>4</sup> [추가 설명](#detailed-description-quota-adjustment-and-best-practices) 및 [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)를 참조하세요.<br/>
<sup>5</sup> [추가 설명](#detailed-description-quota-adjustment-and-best-practices), [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling) 및 [조정 지침](#text-to-speech-increasing-concurrent-request-limit-for-custom-neural-and-custom-voices)을 참조하세요.<br/>
<sup>6</sup> 사용자 지정 음성은 더 이상 사용되지 않으며 새로 생성된 음성 리소스에 사용할 수 없습니다. [추가 정보](how-to-custom-voice.md#migrate-to-custom-neural-voice)를 참조하세요.<br/>

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>자세한 설명, 할당량 조정 및 모범 사례
할당량 증가를 요청하기 전에(해당하는 경우) 필요한지 확인합니다. Speech Service는 자동 크기 조정 기술을 사용하여 필요한 계산 리소스를 “주문형” 모드로 가져오는 동시에 과도한 양의 하드웨어 용량을 유지 관리하지 않음으로써 고객의 비용을 낮게 유지합니다. 워크로드가 정의된 한도 내에 있는 동안([할당량 및 한도 빠른 참조](#quotas-and-limits-quick-reference) 참고) 애플리케이션이 응답 코드 429(“요청이 너무 많음”)를 수신할 때마다 가장 가능성이 높은 설명은 서비스가 수요에 맞게 확장되지만 아직 필요한 규모에는 도달하지 못했으므로 즉시 요청을 처리할 리소스가 충분하지 않다는 것입니다. 이 상태는 일반적으로 일시적이며 오래 지속되지 않아야 합니다.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>자동 크기 조정 중 제한을 완화하기 위한 일반적인 모범 사례
제한과 관련된 문제(응답 코드 429)를 최소화하려면 다음 방법을 사용하는 것이 좋습니다.
- 응용 프로그램에서 다시 시도 논리를 구현하세요.
- 워크로드가 급격히 변경되지 않도록 합니다. 워크로드를 점진적으로 늘립니다. <br/>
*예제.* 애플리케이션이 텍스트 음성 변환을 사용하고 있으며 현재 워크로드는 5TPS(초당 트랜잭션 수)입니다. 그 다음 1초에 로드를 20TPS(즉, 4배)로 늘립니다. 서비스는 새 로드를 충족하기 위해 즉시 확장을 시작하지만 1초 이내에 수행할 수 없으므로 일부 요청에 응답 코드 429가 수신됩니다.
- 다양한 로드 증가 패턴 테스트
  - [워크로드 패턴 예제](#example-of-a-workload-pattern-best-practice) 참조
- 동일하거나 다른 지역에 추가 음성 리소스를 만들고 “라운드 로빈” 방식으로 워크로드를 분산합니다. 이는 음성 리소스당 200으로 설정되고 조정할 수 없는 **텍스트 음성 변환 TPS(초당 트랜잭션 수)** 매개 변수에 특히 중요합니다.

다음 섹션에서는 할당량 조정의 특정 사례에 대해 설명합니다.<br/>
[텍스트 음성 변환: 사용자 지정 신경망 및 사용자 지정 음성에 대한 동시 요청 제한 증가](#text-to-speech-increasing-concurrent-request-limit-for-custom-neural-and-custom-voices)로 이동

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>음성 텍스트 변환: 온라인 대화 내용 기록 동시 요청 제한 증가
기본적으로 동시 요청 수는 음성 리소스(기본 모델)당 100개 및 사용자 지정 엔드포인트(사용자 지정 모델)당 100개로 제한됩니다. 표준 가격 책정 계층의 경우 이 크기를 늘릴 수 있습니다. 요청을 제출하기 전에 [이 섹션](#detailed-description-quota-adjustment-and-best-practices)의 자료를 숙지하고 [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)에 대해 알고 있는지 확인하세요.

>[!NOTE]
> 사용자 지정 모델을 사용하는 경우 하나의 음성 리소스가 여러 사용자 지정 모델 배포를 호스팅하는 여러 사용자 지정 엔드포인트와 연결될 수 있습니다. 각 사용자 지정 엔드포인트에는 생성 시 설정된 기본 동시 요청 제한 수(100)가 있습니다. 조정해야 하는 경우 각 사용자 지정 엔드포인트를 **별도** 로 조정해야 합니다. 또한 음성 리소스의 기본 모델에 대한 동시 요청 수 제한 값은 이 리소스와 연결된 사용자 지정 엔드포인트에 영향을 주지 **않습니다**.


동시 요청 제한을 늘려도 비용에 직접적인 영향을 주지 **않습니다**. Speech Service는 "사용한 만큼만 요금 지불" 모델을 사용합니다. 이 제한은 서비스에서 요청을 제한하기 전에 확장할 수 있는 한도를 정의합니다.

**기본** 및 **사용자 지정** 모델의 동시 요청 제한은 **개별적으로** 조정해야 합니다.

동시 요청 제한 매개 변수의 기존 값은 Azure Portal, 명령줄 도구 또는 API 요청을 통해 표시되지 **않습니다**. 기존 값을 확인하려면 Azure 지원 요청을 만듭니다.

>[!NOTE]
>컨테이너는 호스트되는 하드웨어의 CPU에 따라서만 제한되므로 [음성 컨테이너](speech-container-howto.md)는 동시 요청 제한을 늘릴 필요가 없습니다. 그러나 음성 컨테이너에는 고려해야 할 자체 용량 제한이 있습니다. *"온-프레미스 음성 텍스트 변환 컨테이너의 용량 계획과 비용 예측에 도움이 될 수 있나요?"* 질문( [음성 컨테이너 FAQ](./speech-container-howto.md))을 참조하세요.

#### <a name="have-the-required-information-ready"></a>필요한 정보를 준비합니다.
- **기본 모델** 의 경우:
  - 음성 리소스 ID
  - 지역
- **사용자 지정 모델** 의 경우:
  - 지역
  - 사용자 지정 엔드포인트 ID

- **정보를 가져오는 방법(기본 모델)** :
  - [Azure Portal](https://portal.azure.com/)로 이동합니다.
  - 동시 요청 제한을 늘리려는 음성 리소스 선택
  - *속성* 선택(*리소스 관리* 그룹)
  - 다음 필드의 값을 복사하고 저장합니다.
    - **리소스 ID**
    - **위치**(엔드포인트 영역)

- **정보를 가져오는 방법(사용자 지정 모델)** :
  - [Speech Studio](https://speech.microsoft.com/) 포털로 이동
  - 필요한 경우 로그인
  - Custom Speech로 이동
  - 프로젝트 선택
  - *배포* 로 이동
  - 필요한 엔드포인트 선택
  - 다음 필드의 값을 복사하고 저장합니다.
    - **서비스 지역**(엔드포인트 지역)
    - **엔드포인트 ID**

#### <a name="create-and-submit-support-request"></a>지원 요청 만들기 및 제출
리소스에 대한 동시 요청 제한을 늘리기 시작하거나 필요한 경우 지원 요청을 제출하여 오늘의 제한을 확인합니다.

- [필요한 정보](#have-the-required-information-ready)가 있는지 확인
- [Azure Portal](https://portal.azure.com/)로 이동합니다.
- 동시 요청 제한을 늘리려는(또는 확인하려는) 음성 리소스 선택
- *새 지원 요청*(*지원 + 문제 해결* 그룹) 선택
- Azure 구독 및 Azure 리소스에 대한 정보가 자동으로 채워진 새 창이 표시됩니다.
- *요약* 입력(예: "STT 동시 요청 제한 증가")
- *문제 유형* 에서 "할당량 또는 구독 문제" 선택
- 표시되는 *문제 하위 유형* 에서 다음을 선택합니다.
  - "할당량 또는 동시 요청 증가" - 증가 요청의 경우
  - 기존 제한을 확인하기 위한 "할당량 또는 사용량 유효성 검사"
- *다음: 솔루션* 클릭
- 요청 만들기 계속 진행
- *세부 정보* 탭에서 *설명* 필드에 다음을 입력합니다.
  - 참고: 이 요청은 **음성 텍스트 변환** 할당량에 대한 것입니다.
  - **기본** 또는 **사용자 지정** 모델
  - [이전에 수집한](#have-the-required-information-ready) Azure 리소스 정보
  - 필요한 정보 입력을 완료하고 *검토 + 만들기* 탭에서 *만들기* 단추를 클릭합니다.
  - Azure Portal 알림에서 지원 요청 번호를 확인합니다. 추가 처리를 위해 곧 연락을 드릴 것입니다.

### <a name="example-of-a-workload-pattern-best-practice"></a>워크로드 패턴 모범 사례의 예
이 예에서는 [진행 중인 자동 크기 조정](#detailed-description-quota-adjustment-and-best-practices)으로 인해 가능한 요청 제한을 완화하기 위해 다음과 같은 권장되는 접근 방식을 제시합니다. 이는 "정확한 해결 방법"이 아니라 우리가 필요에 맞게 따르고 조정하도록 초대하는 템플릿일 뿐입니다.

음성 리소스의 동시 요청 제한이 300으로 설정되어 있다고 가정해 보겠습니다. 20개의 동시 연결에서 워크로드를 시작하고 1.5-2분마다 20개의 동시 연결로 로드를 늘립니다. 서비스 응답을 제어하고 응답 코드 429가 너무 많이 발생할 경우 대체(로드 감소) 논리를 구현합니다. 그런 다음, 1-2-4-4분 패턴으로 다시 시도합니다. (즉, 1분 안에 로드 증가를 다시 시도하고 그래도 작동하지 않으면 2분 후에 다시 시도합니다.)

일반적으로 프로덕션으로 이동하기 전에 워크로드 및 워크로드 패턴을 테스트하는 것이 좋습니다.

### <a name="text-to-speech-increasing-concurrent-request-limit-for-custom-neural-and-custom-voices"></a>텍스트 음성 변환: 사용자 지정 신경망 및 사용자 지정 음성에 대한 동시 요청 제한 증가
기본적으로 사용자 지정 신경망 및 사용자 지정 음성 엔드포인트에 대한 동시 요청 수는 10으로 제한됩니다. 표준 가격 책정 계층의 경우 이 크기를 늘릴 수 있습니다. 요청을 제출하기 전에 [이 섹션](#detailed-description-quota-adjustment-and-best-practices)의 자료를 숙지하고 [모범 사례](#general-best-practices-to-mitigate-throttling-during-autoscaling)에 대해 알고 있는지 확인하세요.

동시 요청 제한을 늘려도 비용에 직접적인 영향을 주지 **않습니다**. Speech Service는 "사용한 만큼만 요금 지불" 모델을 사용합니다. 이 제한은 서비스에서 요청을 제한하기 전에 확장할 수 있는 한도를 정의합니다.

동시 요청 제한 매개 변수의 기존 값은 Azure Portal, 명령줄 도구 또는 API 요청을 통해 표시되지 **않습니다**. 기존 값을 확인하려면 Azure 지원 요청을 만듭니다.

>[!NOTE]
>컨테이너는 호스트되는 하드웨어의 CPU에 따라서만 제한되므로 [음성 컨테이너](speech-container-howto.md)는 동시 요청 제한을 늘릴 필요가 없습니다.

#### <a name="prepare-the-required-information"></a>필요한 정보를 준비합니다.
증가 요청을 만들려면 배포 지역과 사용자 지정 엔드포인트 ID를 제공해야 합니다. 이 정보를 얻으려면 다음 작업을 수행합니다.

- [Speech Studio](https://speech.microsoft.com/) 포털로 이동
- 필요한 경우 로그인
- *Custom Voice* 로 이동
- 프로젝트 선택
- *배포* 로 이동
- 필요한 엔드포인트 선택
- 다음 필드의 값을 복사하고 저장합니다.
    - **서비스 지역**(엔드포인트 지역)
    - **엔드포인트 ID**

#### <a name="create-and-submit-support-request"></a>지원 요청 만들기 및 제출
리소스에 대한 동시 요청 제한을 늘리기 시작하거나 필요한 경우 지원 요청을 제출하여 오늘의 제한을 확인합니다.

- [필요한 정보](#prepare-the-required-information)가 있는지 확인
- [Azure Portal](https://portal.azure.com/)로 이동합니다.
- 동시 요청 제한을 늘리려는(또는 확인하려는) 음성 리소스 선택
- *새 지원 요청*(*지원 + 문제 해결* 그룹) 선택
- Azure 구독 및 Azure 리소스에 대한 정보가 자동으로 채워진 새 창이 표시됩니다.
- *요약* 입력(예: "TTS 사용자 지정 엔드포인트 동시 요청 제한 증가")
- *문제 유형* 에서 "할당량 또는 구독 문제" 선택
- 표시되는 *문제 하위 유형* 에서 다음을 선택합니다.
  - "할당량 또는 동시 요청 증가" - 증가 요청의 경우
  - 기존 제한을 확인하기 위한 "할당량 또는 사용량 유효성 검사"
- *다음: 솔루션* 클릭
- 요청 만들기 계속 진행
- *세부 정보* 탭에서 *설명* 필드에 다음을 입력합니다.
  - 참고: 이 요청은 **텍스트 음성 변환** 할당량에 대한 것입니다.
  - [이전에 수집한](#prepare-the-required-information) Azure 리소스 정보
  - 필요한 정보 입력을 완료하고 *검토 + 만들기* 탭에서 *만들기* 단추를 클릭합니다.
  - Azure Portal 알림에서 지원 요청 번호를 확인합니다. 추가 처리를 위해 곧 연락을 드릴 것입니다.
