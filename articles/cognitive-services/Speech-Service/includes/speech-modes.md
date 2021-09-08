---
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 74ddfac57de8a45ea24a2ff0901cfa572e2f0021
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537837"
---
**대화형**
- 명령 및 제어 시나리오용입니다.
- X의 구분 시간 제한 값이 있습니다.
- 인식된 발화가 끝나면 서비스가 해당 요청 ID로부터의 오디오 처리를 중지하고 차례를 종료합니다. 연결이 닫혀 있지 않습니다.
- 인식에 대한 최대 제한은 20초입니다.
- 호출할 일반적인 Carbon 호출은 `RecognizeOnceAsync`입니다.

**대화**
- 장기 인식용입니다.
- Y의 구분 시간 제한 값이 있습니다(Y != X).
- 차례를 종료하지 않고 여러 개의 완료된 발화를 처리합니다.
- 침묵이 너무 길어지면 차례를 종료합니다.
- Carbon은 필요에 따라 새 요청 ID와 오디오 재생을 통해 계속 진행합니다.
- 이 서비스는 음성 인식 10분 후에 강제로 연결을 끊습니다.
- Carbon은 승인되지 않은 오디오를 다시 연결하고 재생합니다.
- `StartContinuousRecognition`과 함께 Carbon에서 호출됩니다.

**받아쓰기**
- 사용자가 말을 통해 문장 부호를 지정할 수 있습니다.
- 인식을 시작하는 API 호출과 무관하게 `SpeechConfig` 개체에서 `EnableDictation`을 지정하여 Carbon에서 호출됩니다.
- <sup></sup>첫 번째 파티 클러스터가 중간 결과에 대해 `speech.fragment` 메시지를 반환하고, <sup></sup>세 번째 파티 클러스터는 `speech.hypothesis` 메시지를 반환합니다.