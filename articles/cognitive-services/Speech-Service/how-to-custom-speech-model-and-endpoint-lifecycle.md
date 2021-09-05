---
title: Custom Speech의 모델 및 엔드포인트 수명 주기 - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech는 적응을 위한 기본 모델을 제공하고 데이터에서 사용자 지정 모델을 만들 수 있습니다. 이 문서에서는 모델 및 이러한 모델을 사용하는 엔드포인트에 대한 타임라인을 설명합니다.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: 701a16779a7a485f33e5af44ec30d48801b7c1fd
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111440811"
---
# <a name="model-and-endpoint-lifecycle"></a>모델 및 엔드포인트 수명 주기

표준(사용자 지정되지 않음) 음성은 기본 모델을 호출하는 AI 모델을 기반으로 합니다. 대부분의 경우 지원되는 각 음성 언어에 대해 다른 기본 모델을 학습합니다.  정확도와 품질을 개선하기 위해 몇 개월마다 새 기본 모델로 음성 서비스를 업데이트합니다.  
Custom Speech를 사용하면 선택한 기본 모델을 특정 고객 시나리오의 데이터로 조정하여 사용자 지정 모델을 만들 수 있습니다. 사용자 지정 모델을 만든 후에는 해당 모델이 적용된 해당 기본 모델이 표준 음성 서비스에서 업데이트되더라도 해당 모델이 업데이트되거나 변경되지 않습니다.  
이 정책을 사용하면 요구 사항을 충족하는 사용자 지정 모델을 만든 후에 특정 사용자 지정 모델을 오랫동안 계속 사용할 수 있습니다.  하지만 향상된 정확도와 품질을 활용하기 위해 최신 기본 모델에서 조정할 수 있도록 사용자 지정 모델을 주기적으로 다시 만드는 것이 좋습니다.

모델 수명 주기와 관련된 다른 주요 용어는 다음과 같습니다.

* **적응**: 기본 모델을 사용하고 텍스트 데이터 및/또는 오디오 데이터를 사용하여 도메인/시나리오로 사용자 지정합니다.
* **디코딩**: 모델을 사용하고 음성 인식을 수행합니다(오디오를 텍스트로 디코딩).
* **엔드포인트**: 지정된 사용자 *만* 액세스할 수 있는 기본 모델 또는 사용자 지정 모델의 사용자별 배포입니다.

### <a name="expiration-timeline"></a>만료 타임라인

새 모델 및 새로운 기능을 사용할 수 있게 되고 기존의 정확도가 낮은 모델을 사용할 수 없게 되면 모델 및 엔드포인트 만료에 대해 다음 타임라인을 참조하세요.

**기본 모델** 

* 적응: 1년 동안 사용할 수 있습니다. 모델을 가져온 후에는 1년 동안 사용자 지정 모델을 만드는 데 사용할 수 있습니다. 1년이 지난 후 새로운 기본 모델 버전에서 새 사용자 지정 모델을 만들어야 합니다.  
* 디코딩: 가져온 후 2년 동안 사용할 수 있습니다. 따라서 이 모델에서 엔드포인트를 만들고 2년 동안 일괄 처리 전사를 사용할 수 있습니다. 
* 엔드포인트: 디코딩과 동일한 타임라인에서 사용할 수 있습니다.

**사용자 지정 모델**

* 디코딩: 모델을 만든 후 2년 동안 사용할 수 있습니다. 따라서 사용자 지정 모델을 만든 후 2년 동안(일괄 처리/실시간/테스트) 사용할 수 있습니다. 2년 후에는 기본 모델이 일반적으로 적응에 사용되지 않으므로 *모델을 다시 학습시켜야 합니다.*  
* 엔드포인트: 디코딩과 동일한 타임라인에서 사용할 수 있습니다.

기본 모델 또는 사용자 지정 모델이 만료되면 항상 *최신 기본 모델 버전* 으로 돌아갑니다. 따라서 구현은 중단되지 않지만 사용자 지정 모델이 만료에 도달하면 *특정 데이터* 에 대해 정확도가 낮아질 수 있습니다. Speech Studio의 Custom Speech 영역에서 모델의 만료를 확인할 수 있습니다.

* 모델 학습 요약
* 모델 학습 세부 정보
* 배포 요약
* 배포 세부 정보

다음은 모델 학습 요약의 예제입니다.

![모델 학습 요약 ](media/custom-speech/custom-speech-model-training-with-expiry.png) 및 모델 학습 세부 정보 페이지에서:

![모델 학습 세부 정보](media/custom-speech/custom-speech-model-details-with-expiry.png)

JSON 응답의 `deprecationDates` 속성에서 [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) 및 [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) 사용자 지정 음성 API를 통해 만료 날짜를 확인할 수도 있습니다.

다음은 GetModel API 호출의 만료 데이터의 예제입니다. **DEPRECATIONDATES** 는 모델이 만료될 때를 표시합니다. 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Speech Studio 배포 섹션의 엔드포인트에서 사용하는 모델을 변경하거나 사용자 지정 음성 API를 통해 가동 중지 시간 없이 사용자 지정 음성 엔드포인트에서 모델을 업그레이드할 수 있습니다.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>모델이 만료될 때 발생하는 작업 및 업데이트하는 방법
모델이 만료될 때 발생하는 작업과 모델을 업데이트하는 방법은 어떻게 사용되는지에 따라 달라집니다.
### <a name="batch-transcription"></a>일괄 처리 기록
[일괄 처리 기록](batch-transcription.md)에 사용되는 모델이 만료된 경우 기록 요청은 4xx 오류로 인해 실패합니다. 이를 방지하려면 **기록 만들기** 요청 본문에서 보낸 JSON의 `model` 매개 변수가 최신 기본 모델 또는 최신 사용자 지정 모델을 가리키도록 합니다. JSON에서 `model` 항목을 제거하여 항상 최신 기본 모델을 사용할 수도 있습니다.
### <a name="custom-speech-endpoint"></a>사용자 지정 음성 엔드포인트
[사용자 지정 음성 엔드포인트](how-to-custom-speech-train-model.md)에서 사용되는 모델이 만료되면 서비스는 사용 중인 언어에 대한 최신 기본 모델을 자동으로 대체합니다. 사용 중인 모델을 업데이트하기 위해 페이지 맨 위에 있는 **Custom Speech** 메뉴에서 **배포** 를 선택한 다음, 엔드포인트 이름을 클릭하여 세부 정보를 볼 수 있습니다. 세부 정보 페이지의 맨 위에는 이 엔드포인트에서 사용하는 모델을 가동 중지 시간 없이 원활하게 업데이트할 수 있는 **모델 업데이트** 단추가 표시됩니다. [**모델 업데이트**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) Rest API를 사용하여 프로그래밍 방식으로 이 변경을 수행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [모델 학습 및 배포](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)
