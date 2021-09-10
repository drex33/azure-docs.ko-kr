---
title: Face JavaScript 클라이언트 라이브러리 빠른 시작
description: JavaScript용 Face 클라이언트 라이브러리를 사용하여 얼굴을 감지하며, 유사 얼굴을 찾고(이미지별 얼굴 검색), 얼굴을 식별(얼굴 인식 검색)하며, 얼굴 데이터를 마이그레이션합니다.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 56f116b8c7b90f74da86a792242866d5a88ebff4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442467"
---
## <a name="quickstart-face-client-library-for-javascript"></a>빠른 시작: JavaScript용 Face 클라이언트 라이브러리

JavaScript용 Face 클라이언트 라이브러리를 사용하여 얼굴 인식을 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

JavaScript용 Face 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [얼굴 식별](#identify-a-face)
* [유사 얼굴 찾기](#find-similar-faces)

[참조 설명서](/javascript/api/overview/azure/cognitive-services/face-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [패키지(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [샘플](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 최신 버전의 [Node.js](https://nodejs.org/en/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure 구독을 보유한 후에는 Azure Portal에서 [Face 리소스를 만들어](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
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

`ms-rest-azure` 및 `azure-cognitiveservices-face` NPM 패키지를 설치합니다.

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

파일 `index.js`를 만들고 다음 라이브러리를 가져옵니다.

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub]()에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Face 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](../../../cognitive-services-security.md) 문서를 참조하세요.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>개체 모델

Face .NET 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | 이 클래스는 Face 서비스를 사용할 수 있는 권한의 부여를 나타내며 모든 Face 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. |
|[Face](/javascript/api/@azure/cognitiveservices-face/face)|이 클래스는 사람 얼굴을 사용하여 수행할 수 있는 기본 감지 및 인식 작업을 처리합니다. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|이 클래스는 이미지의 한 얼굴에서 감지된 모든 데이터를 나타냅니다. 얼굴에 대한 자세한 정보를 검색하는 데 사용할 수 있습니다.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|이 클래스는 분류된 얼굴 세트를 저장하는 클라우드 저장 **FaceList** 구문을 관리합니다. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| 이 클래스는 한 사람에게 속한 얼굴 세트를 저장하는 클라우드 저장 **Person** 구문을 관리합니다.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| 이 클래스는 분류된 **Person** 개체 세트를 저장하는 클라우드 저장 **PersonGroup** 구문을 관리합니다. |

## <a name="code-examples"></a>코드 예제

아래 코드 조각에서는 .NET용 Face 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [얼굴 식별](#identify-a-face)
* [유사 얼굴 찾기](#find-similar-faces)

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 **[ApiKeyCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials)** 개체를 만들고, 엔드포인트에서 이를 사용하여 **[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient)** 개체를 만듭니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>전역 값 및 도우미 함수 선언

다음 전역 값은 나중에 추가할 몇 가지 Face 작업에 필요합니다.

URL은 샘플 이미지 폴더를 가리킵니다. UUID는 만들 PersonGroup의 이름과 ID로 사용됩니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

다음 함수를 사용하여 PersonGroup의 학습이 완료될 때까지 기다립니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-and-analyze-faces"></a>얼굴 감지 및 분석

얼굴 감지는 얼굴 분석 및 ID 검증의 첫 번째 단계로 필요합니다. 이 섹션에서는 추가 얼굴 특성 데이터를 반환하는 방법을 보여 줍니다. 얼굴 식별 또는 확인을 위해 얼굴만 감지하려면 이후 섹션으로 건너뜁니다.


### <a name="get-detected-face-objects"></a>검색된 얼굴 개체 가져오기

얼굴을 감지하는 새 메서드를 만듭니다. `DetectFaceExtract` 메서드는 지정된 URL에서 세 개의 이미지를 처리하고 **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** 개체의 목록을 프로그램 메모리에 만듭니다. **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** 값 목록은 추출할 기능을 지정합니다. 

그런 다음 `DetectFaceExtract` 메서드는 검색된 각 얼굴에 대한 특성 데이터를 구문 분석하고 출력합니다. 각 특성은 원래 얼굴 검색 API 호출( **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** 목록의)에서 별도로 지정해야 합니다. 다음 코드는 모든 특성을 처리하지만 하나 또는 몇 가지를 사용해야 할 가능성이 높습니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> 로컬 이미지에서 얼굴을 검색할 수도 있습니다. [Face](/javascript/api/@azure/cognitiveservices-face/face) 메서드(예: [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____))를 참조하세요.



## <a name="identify-a-face"></a>얼굴 식별

[식별](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) 작업은 한 사람(또는 여러 사람)의 이미지를 가져와 이미지에서 각 얼굴과 연관되는 저장된 사람 개체를 찾습니다(얼굴 인식 검색). 감지된 각 얼굴을 얼굴 특징이 알려진 다른 [Person](/javascript/api/@azure/cognitiveservices-face/person) 개체의 데이터베이스인 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)과 비교합니다. 식별 작업을 수행하려면 먼저 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)을 만들고 학습해야 합니다.

### <a name="add-faces-to-persongroup"></a>PersonGroup에 얼굴 추가

다음 함수를 만들어 [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)에 얼굴을 추가합니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-persongroup"></a>PersonGroup의 학습 대기

다음 도우미 함수를 만들어 **PersonGroup** 이 학습을 완료할 때까지 기다립니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-persongroup"></a>PersonGroup 만들기

코드는 다음과 같습니다.
- [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup) 만들기
- 이전에 정의한 `AddFacesToPersonGroup`을 호출하여 **PersonGroup** 에 얼굴을 추가합니다.
- **PersonGroup** 을 학습합니다.
- **PersonGroup** 의 얼굴을 식별합니다.

이제 이 **PersonGroup** 및 연결된 해당 **Person** 개체는 Verify(검증), Identify(식별) 또는 Group(그룹) 작업에 사용할 수 있습니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> 로컬 이미지에서 **PersonGroup** 을 만들 수도 있습니다. [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) 메서드(예: [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_))를 참조하세요.

## <a name="find-similar-faces"></a>유사 얼굴 찾기

다음 코드에서는 감지된 하나의 얼굴(원본)을 가져오고, 다른 얼굴(대상) 세트를 검색하여 일치 항목을 찾습니다(이미지별 얼굴 검색). 일치 항목을 찾으면 일치하는 얼굴의 ID를 콘솔에 출력합니다.

### <a name="detect-faces-for-comparison"></a>비교할 얼굴 감지

먼저 두 번째 얼굴 감지 메서드를 정의합니다. 이미지를 비교하기 전에 이미지에서 얼굴을 감지해야 하며, 이 감지 메서드는 비교 작업에 최적화되어 있습니다. 위의 섹션과 같이 자세한 얼굴 특성을 추출하지 않으며 다른 인식 모델을 사용합니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>일치 항목 찾기

다음 메서드는 대상 이미지 세트와 단일 원본 이미지에서 얼굴을 감지합니다. 그런 다음, 이를 비교하여 원본 이미지와 유사한 모든 대상 이미지를 찾습니다. 마지막으로, 일치 항목 세부 정보를 콘솔에 출력합니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="main"></a>주

마지막으로, `main` 함수를 만들고 호출합니다.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 JavaScript용 Face 클라이언트 라이브러리를 사용하여 기본 얼굴 인식 작업을 수행하는 방법을 알아보았습니다. 다음으로, 다양한 얼굴 감지 모델과 사용 사례에 적합한 모델을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [얼굴 감지 모델 버전 지정](../../Face-API-How-to-Topics/specify-detection-model.md)

* [Face 서비스란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js)에서 확인할 수 있습니다.
