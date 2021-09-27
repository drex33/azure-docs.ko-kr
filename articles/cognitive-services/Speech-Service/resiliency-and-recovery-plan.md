---
title: 음성 사용자 지정 리소스를 백업 하 고 복구 하는 방법
titleSuffix: Azure Cognitive Services
description: Custom Speech 및 사용자 지정 음성으로 서비스 중단을 준비 하는 방법을 알아봅니다.
services: cognitive-services
author: masakiitagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.openlocfilehash: 0f540025561b6e452371a74093133bf3e1183b1b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744122"
---
# <a name="back-up-and-recover-speech-customization-resources"></a>음성 사용자 지정 리소스 백업 및 복구

음성 서비스는 [다양 한 지역에서 사용할 수](./regions.md)있습니다. 서비스 구독 키는 단일 지역에 연결 됩니다. 키를 획득 하는 경우 데이터, 모델 및 배포가 상주 하는 특정 지역을 선택 합니다.

사용자 지정 음성 모델 및 사용자 지정 음성 글꼴과 같은 고객이 만든 데이터 자산에 대 한 데이터 집합은 **서비스 배포 지역 내 에서만 사용할 수** 있습니다. 이러한 자산은 다음과 같습니다.

**Custom Speech**
-   오디오/텍스트 데이터 학습
-   오디오/텍스트 데이터 테스트
-   사용자 지정 된 음성 모델
-   로그 데이터

**Custom Voice**
-   오디오/텍스트 데이터 학습
-   오디오/텍스트 데이터 테스트
-   사용자 지정 음성 글꼴

일부 고객은 기본 끝점을 사용 하 여 음성 합성을 위한 오디오 또는 표준 음성을 높여줄 다른 고객은 사용자 지정을 위한 자산을 만듭니다.

이러한 자산은 리포지토리 자체에서 정기적으로 자동으로 백업 되므로 지역을 사용할 수 없게 되 면 **데이터가 손실 되지** 않습니다. 그러나 지역 가동 중단이 발생 하는 경우 서비스 연속성을 보장 하는 단계를 수행 해야 합니다.

## <a name="how-to-monitor-service-availability"></a>서비스 가용성을 모니터링 하는 방법

기본 끝점을 사용 하는 경우 오류를 모니터링 하도록 클라이언트 코드를 구성 해야 합니다. 오류가 지속 되 면 서비스 구독이 있는 다른 지역으로 다시 이동할 수 있도록 준비 해야 합니다.

오류를 모니터링 하도록 클라이언트를 구성 하려면 다음 단계를 수행 합니다.

1.  [설명서에서 사용 가능한 지역적으로 끝점 목록을](./rest-speech-to-text.md)찾습니다.
2.  목록에서 기본 및 하나 이상의 보조/백업 지역을 선택 합니다.
3. Azure Portal에서 각 지역에 대 한 음성 서비스 리소스를 만듭니다.
    -  특정 할당량을 설정한 경우 백업 지역에서 동일한 할당량을 설정 하는 것을 고려할 수도 있습니다. [음성 서비스 할당량 및 제한](./speech-services-quotas-and-limits.md)에서 세부 정보를 참조 하세요.

4.  각 지역에는 자체 STS 토큰 서비스가 있습니다. 주 지역 및 모든 백업 지역의 경우 클라이언트 구성 파일에 다음이 있어야 합니다.
    -  국가별 음성 서비스 끝점
    -  [지역 구독 키 및 지역 코드](./rest-speech-to-text.md)

5.  연결 오류 (일반적으로 연결 시간 제한 및 서비스를 사용할 수 없음 오류)를 모니터링 하도록 코드를 구성 합니다. 다음은 c #의 샘플 코드입니다. [GitHub: 지역을 전환 하는 데 사용할 수 있는 샘플을 추가](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965)합니다.

    1.  네트워크에서 일시적인 오류가 발생 하므로 단일 연결 문제가 발생 하는 경우에는 다시 시도 하는 것이 좋습니다.
    2.  새 STS 토큰 서비스 및 음성 서비스 끝점에 대 한 지 속성 리디렉션 트래픽 텍스트 음성 변환의 경우 참조 샘플 코드: [GitHub: TTS 공용 음성 전환 지역](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880)입니다.

이 사용 유형에 대 한 지역별 실패에서의 복구는 즉각적 이며 매우 저렴 한 비용으로 발생할 수 있습니다. 클라이언트 쪽에서는이 기능을 개발 해야 합니다. 오디오 스트림의 백업이 없는 경우에 발생 하는 데이터 손실은 최소화 됩니다.

## <a name="custom-endpoint-recovery"></a>사용자 지정 끝점 복구

한 지역의 데이터 자산, 모델 또는 배포는 다른 지역에서 표시 하거나 액세스할 수 없습니다.

기본 끝점에 사용 되는 것과 동일한 단계를 수행 하 여 주 지역과 보조 지역 모두에서 음성 서비스 리소스를 만들어야 합니다.

### <a name="custom-speech"></a>Custom Speech

Custom Speech Service는 자동 장애 조치 (failover)를 지원 하지 않습니다. 클라이언트 코드에서 구현 된 수동 또는 자동 장애 조치 (failover)를 준비 하려면 다음 단계를 수행 하는 것이 좋습니다. 이러한 단계에서는 보조 지역의 사용자 지정 모델을 복제 합니다. 이러한 준비를 통해 주 지역이 실패 하는 경우 클라이언트 코드를 보조 지역으로 전환할 수 있습니다.

1.  단일 주 지역 (기본)에서 사용자 지정 모델을 만듭니다.
2.  [모델 복사 API](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) 를 실행 하 여 사용자 지정 모델을 모든 준비 된 지역 (보조)에 복제 합니다.
3.  Speech Studio로 이동 하 여 복사 된 모델을 로드 하 고 보조 지역에 새 끝점을 만듭니다. [Custom Speech 모델 학습 및 배포](./how-to-custom-speech-train-model.md)에서 새 모델을 배포 하는 방법을 참조 하세요.
    -  특정 할당량을 설정한 경우 백업 지역에 동일한 할당량을 설정 하는 것도 좋습니다. [음성 서비스 할당량 및 제한](./speech-services-quotas-and-limits.md)에서 세부 정보를 참조 하세요.
4.  기본 끝점 사용과 같이 영구 오류 시 장애 조치 (failover) 하도록 클라이언트를 구성 합니다.

클라이언트 코드는 주 지역에서 배포 된 모델의 가용성을 모니터링 하 고 주 복제본에 장애가 발생 하면 해당 오디오 트래픽을 보조 지역으로 리디렉션할 수 있습니다. 실시간 장애 조치 (failover)가 필요 하지 않은 경우에도이 단계를 수행 하 여 수동 장애 조치를 준비할 수 있습니다.

#### <a name="offline-failover"></a>오프 라인 장애 조치

실시간 장애 조치 (failover)가 필요 하지 않은 경우 데이터를 가져오도록 결정 하 고, 나중에 보조 지역에서 모델을 만들고 배포 하 여 이러한 작업을 완료 하는 데 시간이 걸릴 수 있다는 것을 이해할 수 있습니다.

#### <a name="failover-time-requirements"></a>장애 조치 시간 요구 사항

이 섹션에서는 타이밍에 대 한 일반적인 지침을 제공 합니다. [담당자 테스트 데이터 집합](https://github.com/microsoft/Cognitive-Custom-Speech-Service)을 사용 하 여 오프 라인 장애 조치 (failover)를 예상 하기 위해 시간이 기록 되었습니다.

-   새 지역에 데이터 업로드: **15mins**
-   음향/언어 모델 만들기: **6 시간 (데이터 볼륨에 따라 다름)**
-   모델 평가: **30 분**
-   끝점 배포: **10 분**
-   모델 복사 API 호출: **10 분**
-   클라이언트 코드 재구성 및 배포: **클라이언트 시스템에 따라 다름**

그러나 실시간 요구 사항을 사용 하 여 프로덕션 모델에 대 한 기본 및 보조 지역에 대 한 키를 만드는 것이 좋습니다.

### <a name="custom-voice"></a>Custom Voice

사용자 지정 음성은 자동 장애 조치 (failover)를 지원 하지 않습니다. 이러한 두 옵션을 사용 하 여 실시간 합성 오류를 처리 합니다.

**옵션 1: 같은 지역에서 공용 음성으로 장애 조치 (failover) 합니다.**

사용자 지정 음성 실시간 합성이 실패 하면 공용 음성으로 장애 조치 (failover) 합니다 (클라이언트 샘플 코드: [GitHub: 공용 음성으로의 사용자 지정 음성 장애 조치](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899)).

[사용 가능한 공용 음성을](./language-support.md#neural-voices)확인 합니다. 다른 음성 또는 다른 지역으로 장애 조치 (failover) 하려는 경우 위의 샘플 코드를 변경할 수도 있습니다.

**옵션 2: 다른 지역의 사용자 지정 음성으로 장애 조치 (failover)**

1.  단일 주 지역 (기본)에서 사용자 지정 음성을 만들고 배포 합니다.
2.  사용자 지정 음성 모델을 [Speech Studio](https://speech.microsoft.com)의 다른 지역 (보조 지역)에 복사 합니다.
3.  Speech Studio로 이동 하 여 보조 지역의 음성 리소스로 전환 합니다. 복사 된 모델을 로드 하 고 새 끝점을 만듭니다.
    -   음성 모델 배포는 일반적으로 **3 분 안에** 완료 됩니다.
    -   참고: 추가 요금은 주관적인 끝점입니다. [여기에서 모델 호스팅을 위한 가격 책정을 확인](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)하세요.

4.  보조 지역으로 장애 조치 (failover) 하도록 클라이언트를 구성 합니다. c #의 샘플 코드: [GitHub: 보조 지역으로 사용자 지정 음성 장애 조치 (failover)를](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920)참조 하세요.