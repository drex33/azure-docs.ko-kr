---
title: Speech 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: 음성 umbrella helm 차트 구성 옵션을 자세히 설명합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: eur
ms.openlocfilehash: d0c666089a6886737018572cdace21b0690d64dc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500775"
---
### <a name="speech-umbrella-chart"></a>음성(umbrella 차트)

최상위 “파라솔” 차트의 값은 해당 하위 차트 값을 재정의합니다. 따라서 모든 온-프레미스 사용자 지정 값을 여기에 추가해야 합니다.

|매개 변수|Description|기본값|
| -- | -- | -- | -- |
| `speechToText.enabled` | **speech-to-text** 서비스 사용 여부를 나타냅니다. | `true` |
| `speechToText.verification.enabled` | **speech-to-text** 서비스의 `helm test` 기능 사용 여부를 나타냅니다. | `true` |
| `speechToText.verification.image.registry` | `helm test`가 **speech-to-text** 서비스를 테스트하는 데 사용하는 Docker 이미지 리포지토리입니다. Helm은 테스트를 위해 클러스터 내부에 분리된 Pod를 만들고 이 레지스트리에서 *test-use* 이미지를 풀합니다. | `docker.io` |
| `speechToText.verification.image.repository` | `helm test`가 **speech-to-text** 서비스를 테스트하는 데 사용하는 Docker 이미지 리포지토리입니다. Helm 테스트 Pod는 이 리포지토리를 사용하여 *test-use* 이미지를 풀합니다. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | **speech-to-text** 서비스의 `helm test`에서 사용되는 Docker 이미지 태그입니다. Helm 테스트 Pod는 이 태그를 사용하여 *test-use* 이미지를 풀합니다. | `latest` |
| `speechToText.verification.image.pullByHash` | *test-use* Docker 이미지를 해시를 통해 풀할지 여부를 나타냅니다. `true`인 경우 유효한 이미지 해시 값과 함께 `speechToText.verification.image.hash`를 추가해야 합니다. | `false` |
| `speechToText.verification.image.arguments` | *test-use* Docker 이미지를 실행하는 데 사용되는 인수입니다. Helm 테스트 Pod는 `helm test`를 실행할 때 이 인수를 컨테이너에 전달합니다. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **text-to-speech** 서비스 사용 여부를 나타냅니다. | `true` |
| `textToSpeech.verification.enabled` | **speech-to-text** 서비스의 `helm test` 기능 사용 여부를 나타냅니다. | `true` |
| `textToSpeech.verification.image.registry` | `helm test`가 **speech-to-text** 서비스를 테스트하는 데 사용하는 Docker 이미지 리포지토리입니다. Helm은 테스트를 위해 클러스터 내부에 분리된 Pod를 만들고 이 레지스트리에서 *test-use* 이미지를 풀합니다. | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test`가 **speech-to-text** 서비스를 테스트하는 데 사용하는 Docker 이미지 리포지토리입니다. Helm 테스트 Pod는 이 리포지토리를 사용하여 *test-use* 이미지를 풀합니다. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | **speech-to-text** 서비스의 `helm test`에서 사용되는 Docker 이미지 태그입니다. Helm 테스트 Pod는 이 태그를 사용하여 *test-use* 이미지를 풀합니다. | `latest` |
| `textToSpeech.verification.image.pullByHash` | *test-use* Docker 이미지를 해시를 통해 풀할지 여부를 나타냅니다. `true`인 경우 유효한 이미지 해시 값과 함께 `textToSpeech.verification.image.hash`를 추가해야 합니다. | `false` |
| `textToSpeech.verification.image.arguments` | *test-use* Docker 이미지에서 실행할 인수입니다. Helm 테스트 Pod는 `helm test`를 실행할 때 이 인수를 컨테이너에 전달합니다. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |
