---
title: Speech SDK 마이크 배열 권장 사항
titleSuffix: Azure Cognitive Services
description: Speech SDK 마이크 배열 권장 사항 이러한 배열 구조는 Microsoft 오디오 스택과 함께 사용하는 것이 좋습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: c95ec117ee099c3441bff122fdeeb510309e4b6c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053539"
---
# <a name="speech-sdk-microphone-array-recommendations"></a>Speech SDK 마이크 배열 권장 사항

이 문서에서는 Speech SDK용 마이크 배열을 디자인하는 방법을 알아봅니다.

Speech SDK는 마이크 기하 도형 및 구성 요소 선택을 포함하여 다음 지침에 따라 설계된 마이크 배열에서 가장 잘 작동합니다. 통합 및 전기적 고려 사항에 대한 지침도 제공됩니다.

## <a name="microphone-geometry"></a>마이크 기하 구조

Microsoft 오디오 스택과 함께 사용할 경우 다음 배열 기하 구조를 사용하는 것이 좋습니다. 특정 애플리케이션, 사용자 시나리오 및 디바이스 폼 팩터 종속성이 포함된 더 많은 수의 마이크를 사용하여 음원의 위치 및 주변 소음을 제거하는 기능이 개선되었습니다.

| 마이크 및 기하 구조 | 원형 배열 | 원형 배열 | 선형 배열 | 선형 배열 |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| 마이크 \# | 7 | 4 | 4 | 2 |
| 기하 도형 | 바깥쪽 6개, 가운데 1개, 반경 = 42.5mm, 고른 간격 | 바깥쪽 3개, 가운데 1개, 반경 = 42.5mm, 고른 간격 | 길이 = 120mm, 간격 = 40mm | 간격 = 40mm |

마이크 채널은 위의 각 배열에 대해 표시된 번호에 따라 0부터 증가해야 합니다. 에코 제거를 수행하려면 Microsoft 오디오 스택에 오디오 재생의 추가 참조 스트림이 필요합니다.

## <a name="component-selection"></a>구성 요소 선택

노이즈와 왜곡 없는 신호를 정확하게 재현하려면 마이크 구성 요소를 선택해야 합니다.

마이크 선택 시 권장되는 속성은 다음과 같습니다.

| 매개 변수 | 권장 |
| --------- | ----------- |
| SNR | \> = 65dB(1kHz 신호 94dBSPL, A 가중치가 적용된 노이즈) |
| 진폭 일치 | ± 1dB @ 1kHz |
| 단계 일치 | ±2° @ 1kHz |
| AOP(음향 오버로드 지점) | \> = 120dBSPL(THD = 10%) |
| 비트 전송률 | 최소 24비트 |
| 샘플링 레이트 | 최소 16kHz\* |
| 빈도 응답 | ±3dB, 200~8000Hz 부동 마스크\* |
| 안정성 | 스토리지 온도 범위 -40°C~70°C<br />운영 온도 범위 -20°C~55°C |

\*_고품질 통신(VoIP) 애플리케이션에는 더 높은 샘플링 속도 또는 "더 넓은" 주파수 범위가 필요할 수 있습니다._

사용되는 구성 요소의 성능 저하를 방지하려면 좋은 구성 요소 선택과 좋은 전기 음향 통합이 결합되어야 합니다. 고유한 사용 사례에는 추가 요구 사항(예: 작동 온도 범위)이 필요할 수도 있습니다.

## <a name="microphone-array-integration"></a>마이크 배열 통합

디바이스에 통합되는 마이크 배열의 성능은 구성 요소 사양과 다릅니다. 통합 후 마이크가 잘 맞는지 확인하는 것이 중요합니다. 따라서 고정 이득 또는 EQ 후에 측정된 디바이스 성능은 다음 권장 사항을 충족해야 합니다.

| 매개 변수          | 권장                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63dB(1kHz 신호 94dBSPL, A 가중치가 적용된 노이즈) |
| 출력 민감도 | -26dBFS/Pa @ 1kHz(권장)                  |
| 진폭 일치 | ±2dB, 200-8000Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5번째 순서             |
| 빈도 응답 | ±6dB, 200~8000Hz 부동 마스크\*\*              |

\*\*_THD를 측정하려면 왜곡이 낮은 스피커가 필요합니다(예: Neumann KH120)._

\*\*_고품질 통신(VoIP) 애플리케이션에는 "더 넓은" 주파수 범위가 필요할 수 있습니다._

## <a name="speaker-integration-recommendations"></a>스피커 통합 권장 사항

스피커가 포함된 음성 인식 디바이스에는 반향 제거가 필요하므로 스피커 선택 및 통합에 대한 추가 권장 사항이 제공됩니다.

| 매개 변수 | 권장 |
| --------- | ----------- |
| 선형 고려 사항 | 스피커 참조 후 비선형 처리가 없습니다. 그렇지 않으면 하드웨어 기반 루프백 참조 스트림이 필요합니다. |
| 스피커 루프백 | WASAPI, 개인 API, 사용자 지정 ALSA 플러그인(Linux)을 통해 제공되거나 펌웨어 채널을 통해 제공됩니다. |
| THD% | 3차 옥타브 대역 최소 5차, 70dBA 재생 @ 0.8m ≤ 6.3%, 315-500Hz ≤ 5%, 630-5000Hz |
| 마이크에 에코 결합 | \> -10dB TCLw, ITU-T G.122 Annex B.4 방법 사용, 마이크 수준으로 정규화<br />TCLw = TCLwmeasured \+(측정된 수준 - 목표 출력 민감도)<br />TCLw = TCLwmeasured \+(측정된 수준 - (-26)) |

## <a name="integration-design-architecture"></a>통합 디자인 아키텍처

마이크를 디바이스에 통합하는 경우 다음과 같은 아키텍처 지침이 필요합니다.

| 매개 변수 | 권장 |
| --------- | -------------- |
| 마이크 포트 유사성 | 배열에서 모든 마이크 포트의 길이가 동일합니다. |
| 마이크 포트 치수 | 포트 크기 Ø0.8-1.0mm. 포트 길이/포트 지름 \< 2 |
| 마이크 밀봉         | 밀봉 개스킷이 스택업에서 균일하게 구현됩니다. 폼 개스킷의 경우 \> 70%의 압축비가 좋습니다. |
| 마이크 안정성     | 먼지와 유입을 막으려면 메시를 사용해야 합니다(하단 포팅 마이크용 PCB와 밀봉 개스킷/상단 커버 사이). |
| 마이크 격리       | 특히 통합 스피커로 인한 모든 진동 경로를 격리하기 위해 구조를 통해 고무 개스킷 및 진동을 분리합니다. |
| 샘플링 클록      | 디바이스 오디오는 지터를 사용하지 않아야 하고 낮은 드리프트로 드롭 아웃해야 합니다. |
| 레코드 기능   | 디바이스는 개별 채널 원시 스트림을 동시에 기록할 수 있어야 합니다. |
| USB                 | 모든 USB 오디오 입력 디바이스는 [USB 오디오 디바이스 Rev3 사양](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)에 따라 설명자를 설정해야 합니다. |
| 마이크 기하 구조 | 드라이버에서 [마이크 배열 기하 구조 설명자](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry)를 올바르게 구현해야 합니다. |
| 검색 기능     | 디바이스에는 검색할 수 없거나 제어할 수 없는 하드웨어, 펌웨어 또는 디바이스에 대한 타사 소프트웨어 기반 비선형 오디오 처리 알고리즘이 없어야 합니다. |
| 캡처 형식      | 캡처 형식은 16kHz의 최소 샘플링 속도와 권장되는 24비트 깊이를 사용해야 합니다. |

## <a name="electrical-architecture-considerations"></a>전기 아키텍처 고려 사항

해당하는 경우 배열은 USB 호스트(예: Microsoft 오디오 스택을 실행하는 SoC) 및 음성 서비스 또는 기타 애플리케이션에 대한 인터페이스에 연결할 수 있습니다.

PDM에서 TDM으로의 변환과 같은 하드웨어 구성 요소는 마이크의 동적 범위와 SNR이 리샘플러 내에서 유지되도록 해야 합니다.

고속 USB 오디오 클래스 2.0은 더 높은 샘플 속도와 비트 깊이에서 최대 7개 채널에 필요한 대역폭을 제공하기 위해 모든 오디오 MCU 내에서 지원되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오디오 처리에 대한 자세한 정보](audio-processing-overview.md)
