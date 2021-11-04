---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: eric-urban
ms.openlocfilehash: 7600bae5191c5230764b4f75cb93f20049c4b963
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506869"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 다른 언어로 번역하는 기능입니다. 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성 번역을 수행하는 방법을 알아봅니다. 이 빠른 시작은 마이크의 음성을 다른 언어로 된 텍스트로 변환합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>원본 및 대상 언어 설정

이 명령은 Speech CLI를 호출하여 마이크의 음성을 이탈리아어에서 프랑스어로 번역합니다.

```shell
 spx translate --microphone --source it-IT --target fr
```
