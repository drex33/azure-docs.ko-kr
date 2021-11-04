---
title: Speech Devices SDK 설명서
titleSuffix: Azure Cognitive Services
description: 이 릴리스 노트에서는 Speech Devices SDK의 업데이트, 개선 사항, 버그 수정 사항 및 변경 사항에 대한 로그를 제공합니다. 이 문서는 Speech Devices SDK의 각 릴리스에 따라 업데이트됩니다.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: ac7009cd1b7d2135bcc71dfba097639018bfdcfb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507875"
---
# <a name="release-notes-speech-devices-sdk"></a>릴리스 정보: Speech Devices SDK

다음 섹션에는 최신 릴리스의 변경 내용이 나와 있습니다.

## <a name="speech-devices-sdk-1160"></a>Speech Devices SDK 1.16.0:

- [Github 문제 #22](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues/22)를 해결했습니다.
- [Speech SDK](./speech-sdk.md) 구성 요소를 1.16.0 버전으로 업데이트했습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-1150"></a>Speech Devices SDK 1.15.0:

- 향상된 빔 포밍 및 음성 노이즈 감소 기능을 갖춘 새로운 MAS(Microsoft Audio Stack)로 업그레이드되었습니다.
- 대상에 따라 이진 크기를 최대 70% 줄였습니다.
- [이진 릴리스](https://aka.ms/sdsdk-download-APAudio)의 [Azure Percept Audio](../../azure-percept/overview-azure-percept-audio.md)를 지원합니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.15.0. 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-1110"></a>Speech Devices SDK 1.11.0:

- [임의 마이크 배열 구조](how-to-devices-microphone-array-configuration.md) 및 [구성 파일](https://aka.ms/sdsdk-micarray-json)을 통한 작업 각도 설정을 지원합니다.
- [Urbetter DDK](http://www.urbetter.com/products_56/278.html)를 지원합니다.
- [음성 도우미 샘플](https://aka.ms/sdsdk-speaker)에 사용된 [GGEC 스피커](https://aka.ms/sdsdk-download-speaker)용 이진이 릴리스되었습니다.
- Rasperry Pi 및 이와 유사한 디바이스를 위한 [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) 및 [Linux ARM64](https://aka.ms/sdsdk-download-linux-arm64)용 이진이 릴리스되었습니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.11.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-190"></a>Speech Devices SDK 1.9.0:

- [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)(Linux ARM64)에 대한 초기 이진 파일이 제공됩니다.
- 이제 Roobo v1은 Speech SDK용으로 Maven을 사용합니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.9.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-170"></a>Speech Devices SDK 1.7.0:

- 이제 Linux ARM이 지원됩니다.
- [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2)용 초기 이진 파일이 제공됩니다(Linux ARM64).
- Windows 사용자는 `AudioConfig.fromDefaultMicrophoneInput()` 또는 `AudioConfig.fromMicrophoneInput(deviceName)`을 사용하여 사용할 마이크를 지정할 수 있습니다.
- 라이브러리 크기가 최적화되었습니다.
- 동일한 음성/의도 인식기 개체를 사용하여 멀티 턴 인식을 지원합니다.
- 인식을 중지하는 동안 가끔 프로세스가 응답을 중지하는 문제를 해결했습니다.
- 이제 샘플 앱에는 파일 형식을 보여 주기 위한 샘플 participants.properties 파일이 포함됩니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.7.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

- 일반적인 [샘플 애플리케이션](./speech-devices-sdk.md)을 사용하여 Windows 및 Linux에서 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)를 지원합니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.6.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

- 샘플 앱에 [대화 전사](./conversation-transcription.md)가 포함되었습니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.5.1 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0: 2019년 5월 릴리스

- 이제 Speech Devices SDK는 일반 공급되어 더 이상 제어된 미리 보기가 아닙니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 1.5.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 새로운 키워드 기술은 상당한 품질 향상을 가져왔습니다. 자세한 내용은 주요 변경 사항을 참조하세요.
- 원거리 인식을 개선하기 위한 새로운 오디오 처리 파이프라인이 추가되었습니다.

**주요 변경 내용**

- 새로운 키워드 기술 덕분에 개선된 키워드 포털에서 모든 키워드를 다시 만들어야 합니다. 디바이스에서 이전 키워드를 완전히 제거하려면 이전 앱을 제거합니다.
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0: 2019년 4월 릴리스

- [Speech SDK](./speech-sdk.md) 구성 요소가 1.4.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1: 2019년 3월 릴리스

- [Speech SDK](./speech-sdk.md) 구성 요소가 1.3.1 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 키워드 처리가 업데이트되었습니다. 주요 변경 사항을 참조하세요.
- 샘플 애플리케이션에 음성 인식과 번역을 위한 언어 선택 기능이 추가되었습니다.

**주요 변경 내용**

- [키워드 설치](./custom-keyword-basics.md)가 간소화되어 이제 앱의 일부가 되었으므로 디바이스에 별도로 설치하지 않아도 됩니다.
- 키워드 인식이 변경되어 두 개의 이벤트가 지원됩니다.
  - `RecognizingKeyword,`는 음성 결과에 (확인되지 않은) 키워드 텍스트가 포함되어 있음을 나타냅니다.
  - `RecognizedKeyword`는 지정된 키워드 인식이 완료되었음을 나타냅니다.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0: 2018년 11월 릴리스

- [Speech SDK](./speech-sdk.md) 구성 요소가 1.1.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 향상된 오디오 처리 알고리즘으로 원거리 음성 인식 정확도가 향상되었습니다.
- 샘플 애플리케이션에 중국어 음성 인식 지원이 추가되었습니다.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1: 2018년 10월 릴리스

- [Speech SDK](./speech-sdk.md) 구성 요소가 1.0.1 버전으로 업데이트되었습니다. 해당 [릴리스 정보](./releasenotes.md)를 참조하세요.
- 향상된 오디오 처리 알고리즘을 사용하여 음성 인식 정확도가 향상됩니다.
- 연속 인식 오디오 세션 버그 하나가 해결되었습니다.

**주요 변경 내용**

- 이 릴리스에는 몇 가지 주요한 변경 내용이 도입되었습니다. API와 관련된 자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0)를 확인하세요.
- 키워드 인식 모델 파일은 Speech Devices SDK 1.0.1과 호환되지 않습니다. 새로운 키워드 파일이 디바이스에 기록되면 기존의 키워드 파일이 삭제됩니다.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0: 2018년 8월 릴리스

- 오디오 처리 코드에서 버그를 수정하여 음성 인식 정확도를 향상했습니다.
- [Speech SDK](./speech-sdk.md) 구성 요소가 0.5.0 버전으로 업데이트되었습니다. 해당 [릴리스 정보](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)를 참조하세요.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733: 2018년 5월 릴리스

Cognitive Services Speech Devices SDK의 첫 번째 공개 미리 보기 릴리스입니다.
