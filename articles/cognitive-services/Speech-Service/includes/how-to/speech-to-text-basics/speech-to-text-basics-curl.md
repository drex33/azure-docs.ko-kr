---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: edb41875e75dd8de51d7f444d8259345c8bbc395
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501903"
---
이 빠른 시작에서는 Speech Service 및 cURL을 사용하여 음성을 텍스트로 변환하는 방법을 알아봅니다.

음성 텍스트 변환 개념에 대한 간략한 설명은 [개요](../../../speech-to-text.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>음성을 텍스트로 변환

명령 프롬프트에서 다음 명령을 실행합니다. 명령에 다음 값을 삽입해야 합니다.
- Speech Service 구독 키입니다.
- Speech Service 지역입니다.
- 입력 오디오 파일 경로입니다. [텍스트 음성 변환](../../../get-started-text-to-speech.md)을 사용하여 오디오 파일을 생성할 수 있습니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

다음과 같은 응답을 받게 됩니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

자세한 내용은 [음성 텍스트 변환 REST API 참조](../../../rest-speech-to-text.md)를 참조하세요.
