---
title: 음성 고객 리소스를 백업 및 복구하는 방법
titleSuffix: Azure Cognitive Services
description: Custom Speech 및 Custom Voice 서비스 중단을 준비하는 방법을 알아봅니다.
services: cognitive-services
author: masaki-itagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.custom: ignite-fall-2021
ms.openlocfilehash: aff4093863b1813e22fc6050ff4050cf4133bdd6
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508367"
---
# <a name="back-up-and-recover-speech-customer-resources"></a>음성 고객 리소스 백업 및 복구

Speech Service는 [다양한 지역에서 사용할 수 있습니다.](./regions.md) 서비스 구독 키는 단일 지역에 연결됩니다. 키를 획득하면 데이터, 모델 및 배포가 상주하는 특정 지역을 선택합니다.

사용자 지정된 음성 모델, 사용자 지정 음성 글꼴 및 화자 인식 음성 프로필과 같은 고객이 만든 데이터 자산에 대한 데이터 세트는 **서비스 배포 지역 내에서만 사용할 수 있습니다.** 이러한 자산은 다음과 같습니다.

**Custom Speech**
-   오디오/텍스트 데이터 학습
-   오디오/텍스트 데이터 테스트
-   사용자 지정된 음성 모델
-   로그 데이터

**Custom Voice**
-   오디오/텍스트 데이터 학습
-   오디오/텍스트 데이터 테스트
-   사용자 지정 음성 글꼴

**화자 인식**
- 화자 등록 오디오
- 화자 음성 서명

일부 고객은 기본 엔드포인트를 사용하여 음성 합성을 위해 오디오 또는 표준 음성을 전사하지만, 다른 고객은 사용자 지정을 위한 자산을 만듭니다.

이러한 자산은 리포지토리 자체에 의해 정기적으로 자동으로 백업되므로 지역을 사용할 수 없게 되면 **데이터 손실이 발생하지 않습니다.** 그러나 지역 가동 중단 시 서비스 연속성을 보장하기 위한 단계를 수행해야 합니다.

## <a name="how-to-monitor-service-availability"></a>서비스 가용성을 모니터링하는 방법

기본 엔드포인트를 사용하는 경우 오류를 모니터링하도록 클라이언트 코드를 구성해야 하며, 오류가 지속되면 서비스 구독이 있는 다른 지역으로 다시 연결하도록 준비해야 합니다.

다음 단계에 따라 오류를 모니터링하도록 클라이언트를 구성합니다.

1.  [설명서에서 지역적으로 사용 가능한 엔드포인트 목록을 찾습니다.](./rest-speech-to-text.md)
2.  목록에서 주 지역과 하나 이상의 보조/백업 지역을 선택합니다.
3. Azure Portal 각 지역에 대한 Speech Service 리소스를 만듭니다.
    -  특정 할당량을 설정한 경우 백업 지역에서 동일한 할당량을 설정하는 것도 고려할 수 있습니다. Speech Service 할당량 및 제한에서 세부 정보를 [참조하세요.](./speech-services-quotas-and-limits.md)

4.  각 지역에는 자체 STS 토큰 서비스가 있습니다. 주 지역 및 백업 지역의 경우 클라이언트 구성 파일에서 다음을 알아야 합니다.
    -  지역 음성 서비스 엔드포인트
    -  [지역 구독 키 및 지역 코드](./rest-speech-to-text.md)

5.  연결 오류(일반적으로 연결 시간 제한 및 서비스 사용 불가 오류)를 모니터링하도록 코드를 구성합니다. 다음은 C#의 샘플 코드입니다. [GitHub: 지역 전환에 사용할 수 있는 후보를 보여 주기 위한 샘플 추가.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965)

    1.  네트워크에서 일시적인 오류가 발생하기 때문에 단일 연결 문제가 발생하는 경우 다시 시도하도록 제안합니다.
    2.  지속성을 위해 트래픽을 새 STS 토큰 서비스 및 Speech Service 엔드포인트로 리디렉션합니다. (텍스트 음성 번역의 경우 샘플 코드: [GitHub: TTS 공용 음성 전환 지역 을](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880)참조하세요.

이 사용 유형에 대한 지역별 오류 복구는 즉각적이고 매우 저렴한 비용일 수 있습니다. 클라이언트 쪽에서 이 기능을 개발하기만 하면됩니다. 오디오 스트림의 백업이 최소화되지 않는다고 가정할 경우 발생하는 데이터 손실입니다.

## <a name="custom-endpoint-recovery"></a>사용자 지정 엔드포인트 복구

한 지역의 데이터 자산, 모델 또는 배포를 다른 지역에서 표시하거나 액세스할 수 없습니다.

기본 엔드포인트에 사용되는 것과 동일한 단계를 수행하여 주 지역과 보조 지역 모두에서 Speech Service 리소스를 만들어야 합니다.

### <a name="custom-speech"></a>Custom Speech

Custom Speech 서비스는 자동 장애 조치(failover)를 지원하지 않습니다. 클라이언트 코드에서 구현된 수동 또는 자동 장애 조치(failover)를 준비하기 위해 다음 단계를 권장합니다. 이러한 단계에서는 보조 지역에서 사용자 지정 모델을 복제합니다. 이 준비를 통해 주 지역이 실패하면 클라이언트 코드가 보조 지역으로 전환할 수 있습니다.

1.  하나의 주 지역(기본)에서 사용자 지정 모델을 만듭니다.
2.  모델 [복사 API를](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) 실행하여 준비된 모든 지역에 사용자 지정 모델을 복제합니다(보조).
3.  Speech Studio 이동하여 복사된 모델을 로드하고 보조 지역에 새 엔드포인트를 만듭니다. Custom Speech 모델 학습 및 [배포에서](./how-to-custom-speech-train-model.md)새 모델을 배포하는 방법을 참조하세요.
    -  특정 할당량을 설정한 경우 백업 지역에서 동일한 할당량을 설정하는 것도 좋습니다. Speech Service 할당량 및 제한에서 세부 정보를 [참조하세요.](./speech-services-quotas-and-limits.md)
4.  기본 엔드포인트 사용과 마찬가지로 영구 오류에서 장애 조치(fail over)하려면 클라이언트를 구성합니다.

클라이언트 코드는 주 지역에 배포된 모델의 가용성을 모니터링하고, 주 지역에 장애가 발생하면 해당 오디오 트래픽을 보조 지역으로 리디렉션할 수 있습니다. 실시간 장애 조치(failover)가 필요하지 않은 경우에도 다음 단계에 따라 수동 장애 조치(failover)를 준비할 수 있습니다.

#### <a name="offline-failover"></a>오프라인 장애 조치(failover)

실시간 장애 조치(failover)가 필요하지 않은 경우 나중에 이러한 작업을 완료하는 데 시간이 소요된다는 것을 이해하여 데이터를 가져오고 보조 지역에 모델을 만들고 배포하도록 결정할 수 있습니다.

#### <a name="failover-time-requirements"></a>장애 조치(failover) 시간 요구 사항

이 섹션에서는 타이밍에 대한 일반적인 지침을 제공합니다. [대표적인 테스트 데이터 집합](https://github.com/microsoft/Cognitive-Custom-Speech-Service)를 사용하여 오프라인 장애 조치(failover)를 예상하기 위해 시간이 기록되었습니다.

-   새 지역에 데이터 업로드: **15분**
-   음향/언어 모델 만들기: **6시간(데이터 볼륨에 따라 다름)**
-   모델 평가: **30분**
-   엔드포인트 배포: **10분**
-   모델 복사 API 호출: **10분**
-   클라이언트 코드 재구성 및 배포: **클라이언트 시스템에 따라 다름**

그럼에도 불구하고 실시간 요구 사항이 있는 프로덕션 모델의 기본 및 보조 지역에 대한 키를 만드는 것이 좋습니다.

### <a name="custom-voice"></a>Custom Voice

Custom Voice 자동 장애 조치(failover)를 지원하지 않습니다. 이러한 두 가지 옵션을 통해 실시간 합성 실패를 처리합니다.

**옵션 1: 동일한 지역의 공용 음성으로 장애 조치(fail over)합니다.**

사용자 지정 음성 실시간 합성이 실패하면 공용 음성으로 장애 조치(failover)합니다(클라이언트 샘플 코드: [GitHub: 공용 음성으로 사용자 지정 음성 장애 조치(failover)).](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899)

사용 [가능한 공용 음성을 확인합니다.](./language-support.md#neural-voices) 다른 음성 또는 다른 지역으로 장애 조치(fail over)하려는 경우 위의 샘플 코드를 변경할 수도 있습니다.

**옵션 2: 다른 지역의 사용자 지정 음성으로 장애 조치(fail over)합니다.**

1.  하나의 주 지역(기본)에서 사용자 지정 음성을 만들고 배포합니다.
2.  사용자 지정 음성 모델을 [Speech Studio](https://speech.microsoft.com)다른 지역(보조 지역)에 복사합니다.
3.  Speech Studio 이동하여 보조 지역의 Speech 리소스로 전환합니다. 복사한 모델을 로드하고 새 엔드포인트를 만듭니다.
    -   음성 모델 배포는 일반적으로 **3분 안에** 완료됩니다.
    -   참고: 추가 엔드포인트는 추가 요금이 부과됩니다. [여기에서 모델 호스팅에 대한 가격 책정을 확인합니다.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

4.  보조 지역으로 장애 조치하도록 클라이언트를 구성합니다. C#: [GitHub: 보조 지역으로 사용자 지정 음성 장애 조치(failover)의](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920)샘플 코드를 참조하세요.

### <a name="speaker-recognition"></a>Speaker Recognition

Speaker Recognition [Azure 쌍을 이루는 지역을](/azure/best-practices-availability-paired-regions) 사용하여 자동으로 장애 조치(failover) 작업을 수행합니다. 화자 등록 및 음성 서명은 데이터 손실을 방지하고 가동 중단 시 사용할 수 있도록 정기적으로 백업됩니다.

가동 중단 시 Speaker Recognition 서비스는 쌍을 이루는 지역으로 자동으로 장애 조치(failover)하고 백업된 데이터를 사용하여 주 지역이 다시 온라인이 될 때까지 요청을 계속 처리합니다.
