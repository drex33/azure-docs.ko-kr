---
title: '빠른 시작: Go용 이미지 분석 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 통해 Go용 이미지 분석 클라이언트 라이브러리 시작
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: fce788e25c967d99e38607e242ba9db12899173e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520613"
---
<a name="HOLTop"></a>

이미지 분석 클라이언트 라이브러리를 사용하여 태그, 텍스트 설명, 얼굴, 성인 콘텐츠 등에 대한 이미지를 분석합니다.

[참조 설명서](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [패키지](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 최신 버전의 [Go](https://golang.org/dl/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-go-project-directory"></a>Go 프로젝트 디렉터리 만들기

콘솔 창(cmd, PowerShell, 터미널, Bash)에서 Go 프로젝트에 대한 새 작업 영역 `my-app`을 만들고 해당 작업 영역으로 이동합니다.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

작업 영역에는 다음 세 개의 폴더가 있습니다.

* **src** - 이 디렉터리에는 소스 코드와 패키지가 포함됩니다. `go get` 명령으로 설치된 모든 패키지는 이 디렉터리로 이동됩니다.
* **pkg** - 이 디렉터리에는 컴파일된 Go 패키지 개체가 포함됩니다. 이러한 파일의 확장명은 모두 `.a`입니다.
* **bin** - 이 디렉터리에는 `go install`을 실행할 때 만들어지는 이진 실행 파일이 포함됩니다.

> [!TIP]
> Go 작업 영역의 구조에 대한 자세한 내용은 [Go 언어 설명서](https://golang.org/doc/code.html#Workspaces)를 참조하세요. 이 가이드에는 `$GOPATH` 및 `$GOROOT` 설정에 대한 정보가 있습니다.

### <a name="install-the-client-library-for-go"></a>Go용 클라이언트 라이브러리 설치

다음으로, Go용 클라이언트 라이브러리를 설치합니다.

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

dep를 사용하는 경우에는 리포지토리 내에서 다음을 실행합니다.

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Go 애플리케이션 빌드 

다음으로, **src** 디렉터리에 `sample-app.go`라는 파일을 만듭니다.

```bash
cd src
touch sample-app.go
```

원하는 IDE 또는 텍스트 편집기에서 `sample-app.go`를 엽니다. 그런 다음, 패키지 이름을 추가하고 다음 라이브러리를 가져옵니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_imports)]

또한 스크립트의 루트에서 컨텍스트를 선언합니다. 대부분의 이미지 분석 함수 호출을 실행하려면 이 개체가 필요합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_context)]

다음으로, 다른 Computer Vision 작업을 수행하는 코드를 추가합니다.

> [!div class="nextstepaction"]
> [변수를 설정함](?success=set-up-client#object-model) [문제가 발생함](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client&product=computer-vision&page=image-analysis-go-sdk)

## <a name="object-model"></a>개체 모델

이미지 분석 Go SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | 이 클래스는 이미지 분석이나 텍스트 읽기 같은 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 이 클래스를 인스턴스화한 다음, 대부분의 이미지 작업에 사용합니다.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| 이 형식에는 **AnalyzeImage** 함수 호출의 결과가 포함됩니다. 각 범주 관련 함수에도 비슷한 형식이 있습니다.|
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| 이 형식은 표준 분석 작업에서 수행할 수 있는 다양한 종류의 이미지 분석을 정의합니다. 필요에 따라 VisualFeatureTypes 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

이 코드 조각은 Go용 이미지 분석 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 분석](#analyze-an-image)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 단계에서는 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 Computer Vision 키와 엔드포인트에 대한 [환경 변수를 만들었다](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)고 가정합니다.

클라이언트를 엔드포인트 및 키로 인스턴스화하도록 `main` 함수를 만들고 다음 코드를 이 함수에 추가합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#analyze-an-image) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client&product=computer-vision&page=image-analysis-go-sdk)

## <a name="analyze-an-image"></a>이미지 분석

다음 코드는 클라이언트 개체를 사용하여 원격 이미지를 분석하고 그 결과를 콘솔에 출력합니다. 텍스트 설명, 분류, 태그 목록, 감지된 개체, 감지된 브랜드, 감지된 얼굴, 성인 콘텐츠 플래그, 기본 색 및 이미지 형식을 얻을 수 있습니다.

### <a name="set-up-test-image"></a>테스트 이미지 설정

먼저 분석하려는 이미지의 참조 URL을 저장합니다. 이 URL을 `main` 함수 내에 배치합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> 로컬 이미지를 분석할 수도 있습니다. [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) 메서드(예: **AnalyzeImageInStream**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ImageAnalysisQuickstart.go)의 샘플 코드를 참조하세요.

### <a name="specify-visual-features"></a>시각적 기능 지정

다음 함수 호출은 샘플 이미지에서 다른 시각적 기능을 추출합니다. 다음 섹션에서는 이러한 함수를 정의합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>이미지 설명 가져오기

다음 함수는 이미지에 대해 생성된 자막 목록을 가져옵니다. 이미지 설명에 대한 자세한 내용은 [이미지 설명](../../concept-describing-images.md)을 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>이미지 범주 가져오기

다음 함수는 검색된 이미지 범주를 가져옵니다. 자세한 내용은 [범주 이미지](../../concept-categorizing-images.md)를 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>이미지 태그 가져오기

다음 함수는 이미지에서 감지된 태그 세트를 가져옵니다. 자세한 내용은 [콘텐츠 태그](../../concept-tagging-images.md)를 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>개체 감지

다음 함수는 이미지의 공통 개체를 감지하여 콘솔에 출력합니다. 자세한 내용은 [개체 감지](../../concept-object-detection.md)를 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>브랜드 감지

다음 코드는 이미지의 회사 브랜드 및 로고를 감지하여 콘솔에 출력합니다. 자세한 내용은 [브랜드 감지](../../concept-brand-detection.md)를 참조하세요.

먼저 `main` 함수 내에서 새 이미지에 대한 참조를 선언합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_brand_url)]

다음 코드는 브랜드 감지 함수를 정의합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>얼굴 감지

다음 함수는 사각형 좌표와 특정 얼굴 특성을 사용하여 이미지에서 감지된 얼굴을 반환합니다. 자세한 내용은 [얼굴 감지](../../concept-detecting-faces.md)를 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>성인, 외설 또는 폭력 콘텐츠 검색

다음 함수는 이미지에서 감지된 성인 콘텐츠를 출력합니다. 자세한 내용은 [성인, 외설, 폭력 콘텐츠](../../concept-detecting-adult-content.md)를 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>이미지 색 구성표 가져오기

다음 함수는 주조색이나 강조 색처럼 이미지에서 감지된 색 특성을 출력합니다. 자세한 내용은 [색 구성표](../../concept-detecting-color-schemes.md)를 참조하세요.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>도메인 특정 콘텐츠 가져오기

이미지 분석은 특수 모델을 사용하여 이미지에 대한 추가 분석을 수행할 수 있습니다. 자세한 내용은 [도메인 특정 콘텐츠](../../concept-detecting-domain-content.md)를 참조하세요. 

다음 코드는 이미지에서 검색된 유명인에 대한 데이터를 구문 분석합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_celebs)]

다음 코드는 이미지에서 검색된 랜드마크에 대한 데이터를 구문 분석합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>이미지 형식 가져오기

다음 함수는 이미지 형식이 클립 아트인지 아니면 선 그리기인지 여부에 관계없이 이미지&mdash; 형식에 대한 정보를 출력합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ImageAnalysisQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [이미지를 분석했습니다.](?success=analyze-image#run-the-application) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=analyze-image&product=computer-vision&page=image-analysis-go-sdk)


## <a name="run-the-application"></a>애플리케이션 실행

`go run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [애플리케이션을 실행했습니다.](?success=run-the-application#clean-up-resources) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application&product=computer-vision&page=image-analysis-go-sdk)

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [리소스를 정리했습니다.](?success=clean-up-resources#next-steps) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources&product=computer-vision&page=image-analysis-go-sdk)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Image Analysis 클라이언트 라이브러리를 설치하고 기본 이미지 분석 호출을 수행하는 방법을 알아보았습니다. 다음으로, Analyze API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[Analyze API 호출](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [이미지 분석 개요](../../overview-image-analysis.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ImageAnalysisQuickstart.go)에서 확인할 수 있습니다.

