---
title: '빠른 시작: Python용 광학 인식 클라이언트 라이브러리'
description: 이 빠른 시작을 통해 Python용 광학 인식 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 2b76a751fb9005bba9b720a0ae1367591b89d5a8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520290"
---
<a name="HOLTop"></a>

광학 인식 클라이언트 라이브러리를 사용하여 Read API를 통해 인쇄 및 필기 텍스트를 읽을 수 있습니다.

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

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

예를 들어 새 Python 파일(&mdash;*quickstart-file.py*)을 만듭니다. 그런 다음, 선호하는 편집기 또는 IDE에서 엽니다.

### <a name="find-the-subscription-key-and-endpoint"></a>구독 키 및 엔드포인트 찾기

Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Computer Vision 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. 

Computer Vision 구독 키와 엔드포인트에 대한 변수를 만듭니다. 구독 키와 엔드포인트를 표시된 다음 코드에 붙여넣습니다. Computer Vision 엔트포인트의 형식은 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`입니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 완료되면 코드에서 구독 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)입니다.

> [!div class="nextstepaction"]
> [변수를 설정함](?success=set-up-client#object-model) [문제가 발생함](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client&product=computer-vision&page=python-sdk)

## <a name="object-model"></a>개체 모델

OCR Python SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| 이 클래스는 이미지 분석, 텍스트 감지, 썸네일 생성 등의 모든 이미지 작업을 직접 처리합니다.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. **ComputerVisionClientOperationsMixin** 을 구현합니다.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| 이 열거형은 표준 Analyze(분석) 작업에서 수행할 수 있는 다양한 유형의 이미지 분석을 정의합니다. 필요에 따라 **VisualFeatureTypes** 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 OCR 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [인쇄 텍스트 및 필기 텍스트 읽기](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) 개체를 만든 다음, 엔드포인트에 사용하여 [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#read-printed-and-handwritten-text) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client&product=computer-vision&page=python-sdk)

## <a name="read-printed-and-handwritten-text"></a>인쇄 텍스트 및 필기 텍스트 읽기

OCR 서비스는 이미지 속의 시각적 텍스트를 읽고 문자 스트림으로 변환할 수 있습니다. 이 작업을 두 부분에서 수행합니다.

### <a name="call-the-read-api"></a>읽기 API 호출

먼저 다음 코드를 사용하여 지정된 이미지에 대한 **읽기** 메서드를 호출합니다. 그러면 작업 ID가 반환되고 이미지의 콘텐츠를 읽는 비동기 프로세스가 시작됩니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> 로컬 이미지에서 텍스트를 읽을 수도 있습니다. [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin) 메서드(예: **read_in_stream**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)의 샘플 코드를 참조하세요.

### <a name="get-read-results"></a>읽기 결과 가져오기

그런 다음, **읽기** 호출에서 반환된 작업 ID를 가져와서 서비스의 작업 결과를 쿼리합니다. 다음 코드는 결과가 반환될 때까지 1초 간격으로 작업을 검사합니다. 그런 다음, 추출된 텍스트 데이터를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [텍스트를 읽었습니다.](?success=read-printed-handwritten-text#run-the-application) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text&product=computer-vision&page=python-sdk)

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [애플리케이션을 실행했습니다.](?success=run-the-application#clean-up-resources) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application&product=computer-vision&page=python-sdk)

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [리소스를 정리했습니다.](?success=clean-up-resources#next-steps) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources&product=computer-vision&page=python-sdk)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 OCR 클라이언트 라이브러리를 설치하고 Read API를 사용하는 방법을 알아보았습니다. 다음으로, Read API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[읽기 API 호출](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 개요](../../overview-ocr.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py)에서 확인할 수 있습니다.
