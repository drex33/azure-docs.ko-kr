---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: e2b5a03fdcd82e8a8246a0b9cb29303c276ec81c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910501"
---
이 가이드는 Node.js용 Custom Vision 클라이언트 라이브러리를 사용하여 이미지 분류 모델을 빌드하는 데 유용한 지침과 샘플 코드를 제공합니다. 프로젝트를 만들고, 태그를 추가하고, 프로젝트를 학습시키고, 프로젝트의 예측 엔드포인트 URL을 사용하여 프로그래밍 방식으로 테스트합니다. 자체 이미지 인식 앱을 빌드하기 위한 템플릿으로 이 예제를 사용할 수 있습니다.

> [!NOTE]
> 코드를 작성하지 _않고_ 분류 모델을 빌드하고 학습시키려면 [브라우저 기반 지침](../../getting-started-build-a-classifier.md)을 대신 참조하세요.

.NET용 Custom Vision 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 새 Custom Vision 프로젝트 만들기
* 프로젝트에 태그 추가
* 이미지 업로드 및 태그 지정
* 프로젝트 학습
* 현재 반복 게시
* 예측 엔드포인트 테스트

참조 설명서 [(학습)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(예측)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | 라이브러리 소스 코드 [(학습)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(예측)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | 패키지(npm) [(학습)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(예측)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [샘플](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision 리소스를 만들어"  target="_blank">Custom Vision 리소스 만들기 </a> 학습 및 예측 리소스를 만들고 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Custom Vision에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Node.js용 Custom Vision을 사용하여 이미지 분석 앱을 작성하려면 Custom Vision NPM 패키지가 필요합니다. 이를 설치하려면 PowerShell에서 다음 명령을 실행합니다.

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

파일 `index.js`를 만들고 다음 라이브러리를 가져옵니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_imports)]


> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Custom Vision Training 리소스가 성공적으로 배포되면 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
>Azure Portal에 있는 리소스의 **속성** 탭에서 **리소스 ID** 로 나열된 예측 리소스 ID를 찾을 수 있습니다.
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 [Cognitive Services 보안](../../../cognitive-services-security.md) 문서를 참조하세요.

또한 프로젝트 이름의 필드와 비동기 호출에 대한 시간 제한 매개 변수를 추가합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_vars)]


## <a name="object-model"></a>개체 모델

|Name|Description|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | 이 클래스는 모델의 생성, 학습 및 게시를 처리합니다. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| 이 클래스는 이미지 분류 예측에 대한 모델의 쿼리를 처리합니다.|
|[예측](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| 이 인터페이스는 단일 이미지에 대한 단일 예측을 정의합니다. 여기에는 개체 ID 및 이름에 대한 속성과 신뢰도 점수가 포함됩니다.|

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 JavaScript용 Custom Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [새 Custom Vision 프로젝트 만들기](#create-a-new-custom-vision-project)
* [프로젝트에 태그 추가](#add-tags-to-the-project)
* [이미지 업로드 및 태그 지정](#upload-and-tag-images)
* [프로젝트 학습](#train-the-project)
* [현재 반복 게시](#publish-the-current-iteration)
* [예측 엔드포인트 테스트](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>클라이언트 인증

클라이언트 개체를 엔드포인트와 키로 인스턴스화합니다. 키를 사용하여 **ApiKeyCredentials** 개체를 만들고 엔드포인트와 함께 사용하여 [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) 및 [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) 개체를 만듭니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>새 Custom Vision 프로젝트 만들기

모든 Custom Vision 함수 호출을 포함하도록 새 함수를 시작합니다. 다음 코드를 추가하여 새 Custom Vision 서비스 프로젝트를 만듭니다.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>프로젝트에 태그 추가

프로젝트에 분류 태그를 만들려면 함수에 다음 코드를 추가합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

먼저 이 프로젝트에 대한 샘플 이미지를 다운로드합니다. [샘플 이미지 폴더](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)의 내용을 로컬 디바이스에 저장합니다.

> [!NOTE]
> 학습을 완료하려면 보다 광범위한 이미지 세트가 필요한가요? Microsoft Garage 프로젝트인 Trove를 사용하면 학습 목적으로 이미지 세트를 수집하고 구매할 수 있습니다. 이미지를 수집한 후에는 해당 이미지를 다운로드한 다음, 일반적인 방법으로 Custom Vision 프로젝트로 가져올 수 있습니다. 자세한 내용은 [Trove 페이지](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3)를 참조하세요.

프로젝트에 샘플 이미지를 추가하려면 태그를 만든 후 다음 코드를 삽입합니다. 이 코드는 해당 태그를 사용하여 각 이미지를 업로드합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_upload)]

> [!IMPORTANT]
> Cognitive Services Python SDK 샘플 리포지토리를 다운로드한 위치에 따라 이미지(`sampleDataRoot`) 경로를 변경해야 합니다.

## <a name="train-the-project"></a>프로젝트 학습

이 코드는 예측 모델의 첫 번째 반복을 만듭니다. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_train)]

## <a name="publish-the-current-iteration"></a>현재 반복 게시

이 코드는 학습된 반복을 예측 엔드포인트에 게시합니다. 게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>예측 엔드포인트 테스트

예측 엔드포인트에 이미지를 보내고 예측을 검색하려면 함수에 다음 코드를 추가합니다. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_test)]

그런 다음, Custom Vision 함수를 닫고 호출합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_function_close)]


## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

애플리케이션의 출력은 다음 텍스트와 비슷할 것입니다.

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

그러면 테스트 이미지( **\<sampleDataRoot\>/Test/** 에 있음)에 태그가 적절하게 지정되는지 확인할 수 있습니다. [Custom Vision 웹 사이트](https://customvision.ai)로 돌아가서 새로 만든 프로젝트의 현재 상태를 살펴볼 수도 있습니다.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>다음 단계

지금까지 개체 검색 프로세스의 모든 단계를 코드로 수행하는 방법을 살펴보았습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)

* Custom Vision이란?
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)에서 확인할 수 있습니다.
* [SDK 참조 설명서(학습)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK 참조 설명서(예측)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
