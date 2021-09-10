---
title: Face Python 클라이언트 라이브러리 빠른 시작
description: Python용 Face 클라이언트 라이브러리를 사용하여 얼굴을 감지하고, 유사 얼굴을 찾고(이미지별 얼굴 검색), 얼굴을 식별(얼굴 인식 검색)합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/10/2020
ms.author: pafarley
ms.openlocfilehash: da96179707be6d41c8191b5debff3154fe1e1151
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442457"
---
Python용 Face 클라이언트 라이브러리를 사용하여 얼굴 인식을 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

Python용 Face 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [얼굴 식별](#identify-a-face)
* [얼굴 확인](#verify-faces)
* [유사 얼굴 찾기](#find-similar-faces)

[참조 설명서](/python/api/overview/azure/cognitiveservices/face-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [패키지(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [샘플](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python 설치에 [pip](https://pip.pypa.io/en/stable/)가 포함되어야 합니다. 명령줄에서 `pip --version`을 실행하여 pip가 설치되어 있는지 확인할 수 있습니다. 최신 버전의 Python을 설치하여 pip를 받으세요.
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure 구독이 있으면 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스를 만들어</a> 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치
 
### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

예를 들어 새 Python 스크립트(*quickstart-file.py*)를 만듭니다. 그런 다음, 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 라이브러리를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

그런 다음, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Face 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)입니다.

## <a name="object-model"></a>개체 모델

Face Python 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) | 이 클래스는 Face 서비스를 사용할 수 있는 권한의 부여를 나타내며 모든 Face 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. |
|[FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations)|이 클래스는 사람 얼굴을 사용하여 수행할 수 있는 기본 감지 및 인식 작업을 처리합니다. |
|[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface)|이 클래스는 이미지의 한 얼굴에서 감지된 모든 데이터를 나타냅니다. 얼굴에 대한 자세한 정보를 검색하는 데 사용할 수 있습니다.|
|[FaceListOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations)|이 클래스는 분류된 얼굴 세트를 저장하는 클라우드 저장 **FaceList** 구문을 관리합니다. |
|[PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations)| 이 클래스는 한 사람에게 속한 얼굴 세트를 저장하는 클라우드 저장 **Person** 구문을 관리합니다.|
|[PersonGroupOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations)| 이 클래스는 분류된 **Person** 개체 세트를 저장하는 클라우드 저장 **PersonGroup** 구문을 관리합니다. |
|[ShapshotOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations)|이 클래스는 스냅샷 기능을 관리합니다. 이를 사용하여 모든 클라우드 기반 얼굴 데이터를 임시로 저장하고 해당 데이터를 새 Azure 구독으로 마이그레이션할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Face 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [얼굴 식별](#identify-a-face)
* [얼굴 확인](#verify-faces)
* [유사 얼굴 찾기](#find-similar-faces)


## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) 개체를 만들고, 엔드포인트에서 이를 사용하여 [FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-and-analyze-faces"></a>얼굴 감지 및 분석

얼굴 감지는 얼굴 분석 및 ID 검증에 필요합니다. 이 섹션에서는 추가 얼굴 특성 데이터를 반환하는 방법을 보여 줍니다. 얼굴 식별 또는 확인을 위해 얼굴만 감지하려면 이후 섹션으로 건너뜁니다.


다음 코드에서는 원격 이미지에서 얼굴을 감지합니다. 감지된 얼굴의 ID를 콘솔에 출력하고, 프로그램 메모리에 저장합니다. 그런 다음, 여러 사람이 있는 이미지에서도 얼굴을 감지하고, 해당 ID를 콘솔에 출력합니다. [detect_with_url](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) 메서드의 매개 변수를 변경하면 각 [DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface) 개체와 관련된 다른 정보를 반환할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> 로컬 이미지에서 얼굴을 검색할 수도 있습니다. [FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations) 메서드(예: **detect_with_stream**)를 참조하세요.

### <a name="display-and-frame-faces"></a>표시 및 프레임 면

다음 코드는 DetectedFace.faceRectangle 속성을 사용하여 지정된 이미지를 디스플레이에 출력하고 얼굴 주위에 사각형을 그립니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![얼굴 주위에 빨간색 사각형이 그려진 젊은 여자](../../images/face-rectangle-result.png)




## <a name="identify-a-face"></a>얼굴 식별

Identify(식별) 작업은 사람(또는 여러 사람)의 이미지를 가져와서 이미지에서 각 얼굴의 ID를 찾습니다(얼굴 인식 검색). 감지된 각 얼굴을 얼굴 특징이 알려진 다른 **Person** 개체의 데이터베이스인 **PersonGroup** 과 비교합니다.

### <a name="create-a-persongroup"></a>PersonGroup 만들기

다음 코드에서는 서로 다른 세 개의 **Person** 개체를 사용하여 **PersonGroup** 을 만듭니다. 각 **Person** 을 예제 이미지 세트와 연결한 다음, 각 사용자를 인식할 수 있도록 학습시킵니다. 

이 시나리오를 단계별로 실행하려면 https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 의 이미지를 프로젝트의 루트 디렉터리에 저장해야 합니다.

이 이미지 그룹에는 서로 다른 세 명의 사람에 해당하는 세 개의 얼굴 이미지 세트가 포함되어 있습니다. 코드에서는 세 개의 **Person** 개체를 정의하고, `woman`, `man` 및 `child`로 시작하는 이미지 파일에 연결합니다.

이미지가 설정되면 만들 **PersonGroup** 개체에 대한 레이블을 스크립트의 위쪽에 정의합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

그런 다음, 다음 코드를 Python 스크립트의 아래쪽에 추가합니다. 이 코드는 **PersonGroup** 및 세 개의 **Person** 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Person에 얼굴 할당

다음 코드는 접두사를 기준으로 이미지를 정렬하고, 얼굴을 감지하며, 해당 얼굴을 각 **Person** 개체에 할당합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

> [!TIP]
> URL에서 참조하는 원격 이미지에서 **PersonGroup** 을 만들 수도 있습니다. [PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations) 메서드(예: **add_face_from_url**)를 참조하세요.

### <a name="train-the-persongroup"></a>PersonGroup 학습

얼굴이 할당되면 각 **Person** 개체와 관련된 시각적 기능을 식별할 수 있도록 **PersonGroup** 을 학습시켜야 합니다. 다음 코드는 비동기 **train** 메서드를 호출하고, 결과를 폴링하여 상태를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

> [!TIP]
> Face API는 기본적으로 정적인 사전 빌드된 모델 세트에서 실행됩니다(서비스가 실행될 때 모델의 성능이 저하되거나 향상되지 않음). Microsoft에서 완전히 새로운 모델 버전으로 마이그레이션하지 않고 모델의 백엔드를 업데이트하면 모델이 생성하는 결과가 변경될 수 있습니다. 최신 버전의 모델을 사용하려면 **PersonGroup** 을 동일한 등록 이미지를 가진 매개 변수로 지정하여 다시 학습할 수 있습니다.

### <a name="get-a-test-image"></a>테스트 이미지 가져오기

다음 코드는 프로젝트 루트에서 _test-image-person-group.jpg_ 이미지를 살펴보고, 이미지에서 얼굴을 감지합니다. 이 이미지는 **PersonGroup** 관리에 사용된 이미지(https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images )에서 찾을 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="output-identified-face-ids"></a>식별된 얼굴 ID 출력

**identify** 메서드는 감지된 얼굴의 배열을 사용하여 **PersonGroup** 과 비교합니다. 감지된 얼굴을 **Person** 과 일치시킬 수 있으면 결과가 저장됩니다. 이 코드는 자세한 일치 항목 결과를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>얼굴 확인

확인 작업은 얼굴 ID와 다른 얼굴 ID 또는 **Person** 개체를 가져와서 동일한 사람에게 속하는지 여부를 확인합니다. 확인을 통해 식별 작업에서 반환된 얼굴 일치를 다시 확인할 수 있습니다.

다음 코드는 두 개의 원본 이미지에서 얼굴을 감지한 다음, 대상 이미지에서 검색된 얼굴에 대해 확인합니다.

### <a name="get-test-images"></a>테스트 이미지 가져오기

다음 코드 블록은 확인 작업을 위한 소스 및 대상 이미지를 가리키는 변수를 선언합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>확인을 위한 얼굴 감지

다음 코드는 소스 및 대상 이미지에서 얼굴을 감지하고 변수에 저장합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>확인 결과 가져오기

다음 코드는 각 원본 이미지를 대상 이미지와 비교하고 동일한 사람에 속하는지 여부를 나타내는 메시지를 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="find-similar-faces"></a>유사 얼굴 찾기

다음 코드에서는 감지된 하나의 얼굴(원본)을 가져오고, 다른 얼굴(대상) 세트를 검색하여 일치 항목을 찾습니다(이미지별 얼굴 검색). 일치 항목을 찾으면 일치하는 얼굴의 ID를 콘솔에 출력합니다.

### <a name="find-matches"></a>일치 항목 찾기

먼저 위의 섹션([얼굴 감지 및 분석](#detect-and-analyze-faces))에서 코드를 실행하여 단일 얼굴에 대한 참조를 저장합니다. 그런 다음, 다음 코드를 실행하여 그룹 이미지에서 여러 얼굴에 대한 참조를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

그런 다음, 다음 코드 블록을 추가하여 그룹에서 첫 번째 얼굴의 인스턴스를 찾습니다. 이 동작을 수정하는 방법에 대한 자세한 내용은 [find_simply](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) 메서드를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>일치 항목 출력

다음 코드를 사용하여 일치 항목 세부 정보를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="run-the-application"></a>애플리케이션 실행

`python` 명령을 사용하여 애플리케이션 디렉터리에서 얼굴 인식 앱을 실행합니다.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

이 빠른 시작에서는 **PersonGroup** 을 만들었으며, 이를 삭제하려면 스크립트에서 다음 코드를 실행합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python용 Face 클라이언트 라이브러리를 사용하여 기본 얼굴 인식 작업을 수행하는 방법을 알아보았습니다. 다음으로, 다양한 얼굴 감지 모델과 사용 사례에 적합한 모델을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [얼굴 감지 모델 버전 지정](../../Face-API-How-to-Topics/specify-detection-model.md)

* [Face 서비스란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)에서 확인할 수 있습니다.
