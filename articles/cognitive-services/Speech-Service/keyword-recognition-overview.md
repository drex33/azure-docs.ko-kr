---
title: 키워드 인식 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK(소프트웨어 개발 키트)를 사용하는 키워드 인식의 특징, 기능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: d1fd3bd30b0778065101fb1d223c63421b5e8d67
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043683"
---
# <a name="keyword-recognition"></a>키워드 인식

키워드 인식은 지정된 오디오 스트림 내에서 단어 또는 짧은 구의 존재를 인식하는 음성 기술을 나타냅니다. 일반적으로 키워드 스폿이라고도 합니다. 키워드 인식의 가장 일반적인 사용 사례는 가상 도우미의 음성 활성화입니다. 예를 들어, “Hey Cortana”는 Cortana 도우미의 키워드입니다. 키워드를 인식하면 시나리오별 작업이 수행됩니다. 가상 도우미 시나리오의 경우 일반적인 결과 작업은 키워드를 따르는 오디오의 음성 인식입니다.

일반적으로 가상 도우미는 항상 수신 대기합니다. 키워드 인식은 사용자의 개인 정보 보호 경계 역할을 합니다. 키워드 요구 사항은 관련 없는 사용자 오디오가 로컬 디바이스를 클라우드로 통과하지 못하게 하는 게이트 역할을 합니다.

정확도, 대기 시간 및 컴퓨팅 복잡성의 균형을 맞추기 위해 키워드 인식이 다단계 시스템으로 구현됩니다. 첫 번째 단계 이후의 모든 단계에서 오디오는 이전 단계에서 관심 키워드를 인식한 경우에만 처리됩니다.

현재 시스템은 에지 및 클라우드에 걸쳐 여러 단계로 설계되었습니다.

![키워드 인식의 여러 단계는 에지 및 클라우드에 걸쳐 있습니다.](media/custom-keyword/keyword-recognition-multi-stage.png)

키워드 인식의 정확도는 다음 메트릭을 통해 측정됩니다.
* **CA(긍정 허용률)** – 최종 사용자가 키워드를 말할 때 해당 키워드를 인식하는 시스템의 기능을 측정합니다. 이를 진양성 비율이라고도 합니다. 
* **FA(부정 허용률)** – 최종 사용자가 말하는 키워드가 아닌 오디오를 필터링하는 시스템의 기능을 측정합니다. 이를 가양성 비율이라고도 합니다.

목표는 부정 허용률을 최소화하면서 긍정 허용률을 최대화하는 것입니다. 현재 시스템은 짧은 무음이 앞에 오는 키워드 또는 구를 검색하도록 설계되었습니다. 문장 또는 발화 중간에 있는 키워드 검색은 지원되지 않습니다.

## <a name="custom-keyword-for-on-device-models"></a>온디바이스 모델을 위한 사용자 지정 키워드

[Speech Studio의 사용자 지정 키워드 포털](https://speech.microsoft.com/customkeyword)을 사용하면 단어 또는 짧은 구를 지정하여 에지에서 실행되는 키워드 인식 모델을 생성할 수 있습니다. 올바른 발음을 선택하여 키워드 모델을 추가로 개인 설정할 수 있습니다.

### <a name="pricing"></a>가격 책정

기본 모델과 고급 모델을 비롯하여 모델을 생성하기 위해 사용자 지정 키워드를 사용하는 데 비용은 들지 않습니다. 또한 Speech SDK를 사용하여 온디바이스 모델을 실행하는 데에도 비용이 청구되지 않습니다.

### <a name="types-of-models"></a>모델 유형

사용자 지정 키워드를 사용하면 모든 키워드에 대해 다음과 같은 두 가지 유형의 온디바이스 모델을 생성할 수 있습니다.

| 모델 형식 | Description |
| ---------- | ----------- |
| Basic | 데모 또는 신속한 프로토타입 작성에 가장 적합합니다. 모델은 공통 기본 모델로 생성되며 준비되는 데 최대 15분이 걸릴 수 있습니다. 모델에는 최적의 정확도 특성이 포함되지 않을 수 있습니다. |
| 고급 | 제품 통합 용도에 가장 적합합니다. 모델은 시뮬레이션된 학습 데이터를 사용하여 정확도 특성을 개선하는 공통 기본 모델을 적용하여 생성됩니다. 모델을 준비하는 데 최대 48시간이 걸릴 수 있습니다. |

두 모델 유형 모두 학습 데이터를 업로드해야 합니다. 사용자 지정 키워드는 데이터 생성 및 모델 학습을 완전히 처리합니다.

### <a name="pronunciations"></a>발음

새 모델을 만들 때 사용자 지정 키워드는 제공된 키워드의 가능한 발음을 자동으로 생성합니다. 각 발음을 듣고 최종 사용자가 키워드를 말할 것으로 예상되는 방식을 모두 선택할 수 있습니다. 다른 모든 발음은 선택하지 않아야 합니다.

최상의 정확도 특성을 보장하려면 선택한 발음을 신중하게 사용해야 합니다. 예를 들어 필요한 것보다 더 많은 발음을 선택하면 부정 허용률이 높아질 수 있습니다. 예상되는 변형이 모두 적용되지 않는 발음을 너무 적게 선택하면 긍정 허용률이 낮아질 수 있습니다.

### <a name="testing-models"></a>모델 테스트

사용자 지정 키워드에 의해 온디바이스 모델이 생성되면 포털에서 직접 테스트할 수 있습니다. 포털을 사용하면 브라우저에 직접 말하고 키워드 인식 결과를 얻을 수 있습니다.

## <a name="keyword-verification"></a>키워드 확인

키워드 확인은 Azure에서 실행되는 강력한 모델이 있는 온디바이스 모델에서 부정 허용의 영향을 줄이는 클라우드 서비스입니다. 키워드 확인에 키워드가 함께 작동하는 데 튜닝 또는 학습이 필요하지 않습니다. 증분 모델 업데이트는 정확도와 대기 시간을 개선하기 위해 서비스에 지속적으로 배포되며 클라이언트 애플리케이션은 전혀 인식하지 못합니다.

### <a name="pricing"></a>가격 책정

키워드 확인은 항상 음성 텍스트 변환과 함께 사용되며, 키워드 확인을 사용하는 데 음성 텍스트 변환 비용 외에는 비용이 들지 않습니다.

### <a name="keyword-verification-and-speech-to-text"></a>키워드 확인 및 음성 텍스트 변환

키워드 확인이 사용되는 경우 항상 음성 텍스트 변환과 함께 사용됩니다. 두 서비스는 모두 병렬로 실행됩니다. 즉, 오디오가 동시 처리를 위해 두 서비스로 전송됩니다.

![키워드 확인 및 음성 텍스트 변환의 병렬 처리](media/custom-keyword/keyword-verification-parallel-processing.png)

키워드 확인과 음성 텍스트 변환을 병렬로 실행하면 다음과 같은 이점이 있습니다.
* **음성 텍스트 변환 결과에 추가 대기 시간 없음** – 병렬 실행은 키워드 확인이 대기 시간을 추가하지 않으며 클라이언트가 음성 텍스트 변환 결과를 빠르게 수신함을 의미합니다. 키워드 확인에서 키워드가 오디오에 없는 것으로 확인되면 음성 텍스트 변환 처리가 종료되어 불필요한 음성 텍스트 변환 처리로부터 보호됩니다. 그러나 네트워크 및 클라우드 모델 처리는 음성 활성화의 사용자 인식 대기 시간을 증가시킵니다. 자세한 내용은 [권장 사항 및 지침](keyword-recognition-guidelines.md)을 참조 하세요.
* **음성 텍스트 변환 결과에 키워드 접두사 적용** – 음성 텍스트 변환 처리는 클라이언트에 전송된 결과 앞에 키워드가 붙습니다. 이렇게 하면 키워드 뒤에 오는 음성에 대한 음성 텍스트 변환 결과의 정확도가 향상됩니다.
* **음성 텍스트 변환 시간 제한 증가** – 오디오의 시작 부분에 키워드가 필요하기 때문에 음성 텍스트 변환은 음성의 끝을 확인하고 음성 텍스트 변환 처리를 종료하기 전에 키워드 다음에 최대 5초 동안 일시 중지될 수 있습니다. 이렇게 하면 최종 사용자 환경이 준비된 명령( *\<keyword> \<pause> \<command>* ) 및 연결된 명령( *\<keyword> \<command>* )에 대해 올바르게 처리됩니다. 

### <a name="keyword-verification-responses-and-latency-considerations"></a>키워드 확인 응답 및 대기 시간 고려 사항

서비스에 대한 각 요청에 대해 키워드 확인은 수락됨 또는 거부됨의 두 응답 중 하나를 반환합니다. 처리 대기 시간은 키워드의 길이 및 키워드를 포함하는 데 예상되는 오디오 세그먼트의 길이에 따라 달라집니다. 처리 대기 시간에는 클라이언트와 Azure Speech Service 간의 네트워크 비용이 포함되지 않습니다.

| 키워드 확인 응답 | Description |
| ----------------------------- | ----------- |
| 수락됨 | 서비스가 요청의 일부로 제공된 오디오 스트림에 키워드를 표시했음을 나타냅니다. |
| 거부됨 | 서비스가 요청의 일부로 제공된 오디오 스트림에 키워드를 표시하지 않았음을 나타냅니다. |

거부된 사례는 서비스가 수락된 사례보다 더 많은 오디오를 처리할 때 대기 시간이 더 많이 발생합니다. 기본적으로 키워드 확인은 키워드를 검색하는 데 최대 2초의 오디오를 처리합니다. 키워드가 2초 동안 존재하지 않는 것으로 확인되면 서비스는 시간 초과되고 클라이언트에게 거부된 응답을 알립니다.

### <a name="using-keyword-verification-with-on-device-models-from-custom-keyword"></a>사용자 지정 키워드의 온디바이스 모델에서 키워드 확인 사용

Speech SDK를 통해 키워드 확인 및 음성 텍스트 변환과 함께 사용자 지정 키워드를 사용하여 생성된 온디바이스 모델을 원활하게 사용할 수 있습니다. 다음을 투명하게 처리합니다.
* 온디바이스 모델의 결과에 따른 키워드 확인 및 음성 인식에 대한 오디오 게이팅.
* 키워드 확인 서비스에 키워드를 전달.
* 엔드투엔드 시나리오를 오케스트레이션하기 위해 추가 메타데이터를 클라우드에 전달. 

구성 매개 변수를 명시적으로 지정할 필요는 없습니다. 필요한 모든 정보는 사용자 지정 키워드로 생성된 온디바이스 모델에서 자동으로 추출됩니다.

아래에 연결된 샘플 및 자습서는 Speech SDK를 사용하는 방법을 보여 줍니다.
 * [GitHub의 음성 도우미 샘플](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
 * [자습서: C# Speech SDK와 Azure Bot Service를 사용하여 빌드된 도우미를 음성을 지원하도록 설정](./tutorial-voice-enable-your-bot-speech-sdk.md)
 * [자습서: 간단한 음성 명령으로 사용자 지정 명령 애플리케이션 만들기](./how-to-develop-custom-commands-application.md)

## <a name="speech-sdk-integration-and-scenarios"></a>Speech SDK 통합 및 시나리오

Speech SDK를 통해 사용자 지정 키워드 및 키워드 확인 서비스로 생성된 개인화된 온디바이스 키워드 인식 모델을 손쉽게 사용할 수 있습니다. 제품 요구 사항을 충족할 수 있도록 SDK는 다음 두 가지 시나리오를 지원합니다.

| 시나리오 | Description | 샘플 |
| -------- | ----------- | ------- |
| 음성 텍스트 변환으로 엔드투엔드 키워드 인식 | Azure Speech의 키워드 확인 및 음성 텍스트 변환 서비스를 사용하여 사용자 지정 키워드의 사용자 지정된 온디바이스 키워드 모델을 사용하는 제품에 가장 적합합니다. 이는 가장 일반적인 시나리오입니다. | <ul><li>[음성 도우미 샘플 코드.](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)</li><li>[자습서: 음성에서 C# Speech SDK와 Azure Bot Service를 사용하여 빌드한 도우미를 사용하도록 설정.](./tutorial-voice-enable-your-bot-speech-sdk.md)</li><li>[자습서: 간단한 음성 명령으로 사용자 지정 명령 애플리케이션 만들기.](./how-to-develop-custom-commands-application.md)</li></ul> |
| 오프라인 키워드 인식 | 사용자 지정 키워드의 사용자 지정된 온디바이스 키워드 모델을 사용하는 네트워크 연결이 없는 제품에 가장 적합합니다. | <ul><li>[Windows UWP의 C# 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)</li><li>[Android의 Java 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)</li></ul>

## <a name="next-steps"></a>다음 단계

* [사용자 지정 키워드 사용하여 온디바이스 키워드 인식 모델을 생성하는 빠른 시작을 읽어보세요.](custom-keyword-basics.md)
* [음성 도우미에 대해 자세히 알아봅니다.](voice-assistants.md)
