---
title: '빠른 시작: Python용 이미지 분석 클라이언트 라이브러리'
description: 이 빠른 시작을 통해 Python용 이미지 분석 클라이언트 라이브러리 시작
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 3f6425edff78fd74bfa875d9c0978ece281b7237
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520829"
---
<a name="HOLTop"></a>

이미지 분석 클라이언트 라이브러리를 사용하여 태그, 텍스트 설명, 얼굴, 성인 콘텐츠 등에 대한 이미지를 분석합니다.

[참조 설명서](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [패키지(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Python 설치에 [pip](https://pip.pypa.io/en/stable/)가 포함되어야 합니다. 명령줄에서 `pip --version`을 실행하여 pip가 설치되어 있는지 확인할 수 있습니다. 최신 버전의 Python을 설치하여 pip를 받으세요.
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스</a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.

    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Pillow 라이브러리도 설치합니다.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

예를 들어 새 Python 파일(&mdash;*quickstart-file.py*)을 만듭니다. 그런 다음, 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 라이브러리를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_imports)]

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ImageAnalysisQuickstart.py)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

그런 다음, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Computer Vision 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)입니다.

> [!div class="nextstepaction"]
> [변수를 설정함](?success=set-up-client#object-model) [문제가 발생함](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client&product=computer-vision&page=image-analysis-python-sdk)

## <a name="object-model"></a>개체 모델

이미지 분석 Python SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| 이 클래스는 이미지 분석, 텍스트 감지, 썸네일 생성 등의 모든 이미지 작업을 직접 처리합니다.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. **ComputerVisionClientOperationsMixin** 을 구현합니다.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| 이 열거형은 표준 Analyze(분석) 작업에서 수행할 수 있는 다양한 유형의 이미지 분석을 정의합니다. 필요에 따라 **VisualFeatureTypes** 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

이 코드 조각은 Python용 이미지 분석 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 분석](#analyze-an-image)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) 개체를 만든 다음, 엔드포인트에 사용하여 [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#analyze-an-image) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client&product=computer-vision&page=image-analysis-python-sdk)

## <a name="analyze-an-image"></a>이미지 분석

클라이언트 개체를 사용하여 원격 이미지의 시각적 기능을 분석합니다. 먼저 분석하려는 이미지의 URL에 대한 참조를 저장합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_remoteimage)]

> [!TIP]
> 로컬 이미지를 분석할 수도 있습니다. [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) 메서드(예: **analyze_image_in_stream**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ImageAnalysisQuickstart.py)의 샘플 코드를 참조하세요.

### <a name="get-image-description"></a>이미지 설명 가져오기

다음 코드는 이미지에 대해 생성된 캡션 목록을 가져옵니다. 자세한 내용은 [이미지 설명](../../concept-describing-images.md)을 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>이미지 범주 가져오기

다음 코드는 검색된 이미지 범주를 가져옵니다. 자세한 내용은 [이미지 분류](../../concept-categorizing-images.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>이미지 태그 가져오기

다음 코드는 이미지에서 검색된 범주 세트를 가져옵니다. 자세한 내용은 [콘텐츠 태그](../../concept-tagging-images.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>개체 감지

다음 코드는 이미지의 공통 개체를 검색하여 콘솔에 출력합니다. 자세한 내용은 [개체 감지](../../concept-object-detection.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_objects)]        

### <a name="detect-brands"></a>브랜드 감지

다음 코드는 이미지의 회사 브랜드 및 로고를 감지하여 콘솔에 출력합니다. 자세한 내용은 [브랜드 감지](../../concept-brand-detection.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>얼굴 감지

다음 코드는 사각형 좌표를 사용하여 이미지에서 검색된 얼굴을 반환하고 얼굴 특성을 선택합니다. 자세한 내용은 [얼굴 감지](../../concept-detecting-faces.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>성인, 외설 또는 폭력 콘텐츠 검색

다음 코드는 이미지에 있는 성인 콘텐츠의 검색된 상태를 출력합니다. 자세한 내용은 [성인, 외설, 폭력 콘텐츠](../../concept-detecting-adult-content.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>이미지 색 구성표 가져오기

다음 코드는 주조색 및 강조 색과 같이 이미지에서 검색된 색 특성을 출력합니다. 자세한 내용은 [색 구성표](../../concept-detecting-color-schemes.md)를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>도메인 특정 콘텐츠 가져오기

이미지 분석은 특수 모델을 사용하여 이미지에 대한 추가 분석을 수행할 수 있습니다. 자세한 내용은 [도메인 특정 콘텐츠](../../concept-detecting-domain-content.md)를 참조하세요. 

다음 코드는 이미지에서 검색된 유명인에 대한 데이터를 구문 분석합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_celebs)]

다음 코드는 이미지에서 검색된 랜드마크에 대한 데이터를 구문 분석합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>이미지 형식 가져오기

다음 코드는 이미지 형식이 클립 아트인지 아니면 선 그리기인지 여부에 관계없이 이미지 형식에 대한 정보를 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ImageAnalysisQuickstart.py?name=snippet_type)]

> [!div class="nextstepaction"]
> [이미지를 분석했습니다.](?success=analyze-image#run-the-application) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=analyze-image&product=computer-vision&page=image-analysis-python-sdk)



## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [애플리케이션을 실행했습니다.](?success=run-the-application#clean-up-resources) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application&product=computer-vision&page=image-analysis-python-sdk)

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [리소스를 정리했습니다.](?success=clean-up-resources#next-steps) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources&product=computer-vision&page=image-analysis-python-sdk)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Image Analysis 클라이언트 라이브러리를 설치하고 기본 이미지 분석 호출을 수행하는 방법을 알아보았습니다. 다음으로, Analyze API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[Analyze API 호출](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [이미지 분석 개요](../../overview-image-analysis.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ImageAnalysisQuickstart.py)에서 확인할 수 있습니다.
