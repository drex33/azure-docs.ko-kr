---
title: Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: Speech CLI는 코드를 작성하지 않고 Speech Service를 사용하기 위한 명령줄 도구입니다. Speech CLI는 최소한의 설정만 필요하며, 사용 사례를 충족할 수 있는지 확인하기 위해 Speech Service의 주요 기능을 쉽게 즉시 실험할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: pafarley
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0764a6d9d3cf1539237e552d6a3324c5f6412c3
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538564"
---
# <a name="what-is-the-speech-cli"></a>Speech CLI란?

Speech CLI는 코드를 작성하지 않고 Speech Service를 사용하기 위한 명령줄 도구입니다. Speech CLI는 최소한의 설정만 필요하며, 사용 사례를 충족할 수 있는지 확인하기 위해 Speech Service의 주요 기능을 쉽게 즉시 실험할 수 있습니다. 몇 분 안에 파일 디렉터리의 일괄 처리 음성 인식 또는 파일의 문자열 모음에 대한 텍스트 음성 변환과 같은 간단한 테스트 워크플로를 실행할 수 있습니다. 단순한 워크플로를 넘어서 Speech CLI는 생산 준비가 되어 있으며 자동화된 `.bat` 또는 셸 스크립트를 사용하여 더 큰 프로세스를 실행하도록 확장할 수 있습니다.

Speech SDK의 대부분의 기능은 Speech CLI에서 사용할 수 있으며 일부 고급 기능 및 사용자 지정은 Speech CLI에서 단순화됩니다. 다음 지침을 고려하여 Speech CLI 또는 Speech SDK를 사용할 시기를 결정합니다.

다음 경우에 Speech CLI를 사용합니다.
* 최소한의 설정으로 코드 없이 Speech Service 기능을 실험하려고 합니다.
* Speech Service를 사용하는 프로덕션 애플리케이션에 대한 요구 사항은 비교적 간단합니다.

다음과 같은 경우 Speech SDK를 사용합니다.
* 특정 언어 또는 플랫폼(예: C#, Python, C++) 내에서 Speech Service 기능을 통합하려는 경우
* 고급 서비스 요청이 필요하거나 응답 스트리밍을 포함한 사용자 지정 동작을 개발해야 하는 복잡한 요구 사항이 있는 경우

## <a name="core-features"></a>핵심 기능

* 음성 인식 - 오디오 파일 또는 마이크에서 직접 음성을 텍스트로 변환하거나 녹음된 대화를 전사합니다.

* 음성 합성 - 텍스트 파일에서 입력하거나 명령줄에서 직접 입력하여 텍스트를 음성으로 변환합니다. [SSML 구성](speech-synthesis-markup.md)과 [표준 또는 인공신경망 음성](speech-synthesis-markup.md#neural-and-custom-voices)을 사용하여 음성 출력 특성을 사용자 지정합니다.

* 음성 번역 - 소스 언어의 오디오를 대상 언어의 텍스트 또는 오디오로 번역합니다.

* Azure 컴퓨팅 리소스에서 실행 - `spx webjob`을 사용하여 Azure 원격 컴퓨팅 리소스에서 실행할 Speech CLI 명령을 보냅니다.

## <a name="get-started"></a>시작

Speech CLI를 시작하려면 [빠른 시작](spx-basics.md)을 참조하세요. 이 문서에서는 몇 가지 기본 명령을 실행하는 방법을 보여 주고 음성 텍스트 변환 및 텍스트 음성 변환의 일괄 처리 작업을 실행하기 위한 고급 명령도 보여 줍니다. 기본 문서를 읽은 후에는 일부 사용자 지정 명령을 작성하거나 간단한 음성 서비스 작업을 자동화하기 위한 구문을 충분히 이해해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Speech CLI 빠른 시작](spx-basics.md) 시작하기
- [데이터 저장소 구성](./spx-data-store-configuration.md)
- [Speech CLI로 일괄 처리 작업 실행](./spx-batch-operations.md)에 대한 자세한 정보
