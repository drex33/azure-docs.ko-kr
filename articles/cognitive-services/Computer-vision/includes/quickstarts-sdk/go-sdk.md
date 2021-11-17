---
title: '빠른 시작: Go용 광학 인식 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 통해 Go용 광학 인식 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: a6dbc2b54c8fac9f2b1bdb219784f873d99432f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521000"
---
<a name="HOLTop"></a>

OCR 클라이언트 라이브러리를 사용하여 이미지에서 인쇄 및 필기 텍스트를 읽습니다.

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

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

원하는 IDE 또는 텍스트 편집기에서 `sample-app.go`를 엽니다.

스크립트의 루트에서 컨텍스트를 선언합니다. 대부분의 Computer Vision 함수 호출을 실행하려면 이 개체가 필요합니다.

### <a name="find-the-subscription-key-and-endpoint"></a>구독 키 및 엔드포인트 찾기

Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Computer Vision 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. 

Computer Vision 구독 키와 엔드포인트에 대한 변수를 만듭니다. 구독 키와 엔드포인트를 표시된 다음 코드에 붙여넣습니다. Computer Vision 엔트포인트의 형식은 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`입니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> 완료되면 코드에서 구독 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)입니다.

다음으로, 다른 OCR 작업을 수행하는 코드를 추가합니다.

> [!div class="nextstepaction"]
> [변수를 설정함](?success=set-up-client#object-model) [문제가 발생함](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client&product=computer-vision&page=go-sdk)

## <a name="object-model"></a>개체 모델

OCR Go SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | 이 클래스는 이미지 분석이나 텍스트 읽기 같은 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 이 클래스를 인스턴스화한 다음, 대부분의 이미지 작업에 사용합니다.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| 이 형식에는 일괄 읽기 작업의 결과가 포함됩니다. |

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Go용 OCR 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [인쇄 텍스트 및 필기 텍스트 읽기](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 단계에서는 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 Computer Vision 키와 엔드포인트에 대한 [환경 변수를 만들었다](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)고 가정합니다.

클라이언트를 엔드포인트 및 키로 인스턴스화하도록 `main` 함수를 만들고 다음 코드를 이 함수에 추가합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#read-printed-and-handwritten-text) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client&product=computer-vision&page=go-sdk)



## <a name="read-printed-and-handwritten-text"></a>인쇄 텍스트 및 필기 텍스트 읽기

OCR 서비스는 이미지 속의 시각적 텍스트를 읽고 문자 스트림으로 변환할 수 있습니다. 이 단원의 코드는 클라이언트 개체를 사용하여 이미지의 인쇄 텍스트 또는 필기 텍스트를 감지하고 추출하는 `RecognizeTextReadAPIRemoteImage` 함수를 정의합니다.

`main` 함수에 샘플 이미지 참조 및 함수 호출을 추가합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> 로컬 이미지에서 텍스트를 추출할 수도 있습니다. [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) 메서드(예: **BatchReadFileInStream**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)의 샘플 코드를 참조하세요.

### <a name="call-the-read-api"></a>읽기 API 호출

텍스트를 읽기 위한 새 함수 `RecognizeTextReadAPIRemoteImage`를 정의합니다. 지정된 이미지에 대해 **BatchReadFile** 메서드를 호출하는 아래 코드를 추가합니다. 이 메서드는 작업 ID를 반환하고, 이미지의 콘텐츠를 읽는 비동기 프로세스를 시작합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>읽기 결과 가져오기

다음으로, **BatchReadFile** 호출에서 반환된 작업 ID를 가져와 **GetReadOperationResult** 메서드에 사용하여 서비스의 작업 결과를 쿼리합니다. 다음 코드는 결과가 반환될 때까지 1초 간격으로 작업을 검사합니다. 그런 다음, 추출된 텍스트 데이터를 콘솔에 출력합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>읽기 결과 표시

검색된 텍스트 데이터를 구문 분석하고, 표시하고, 함수 정의를 완료하는 다음 코드를 추가합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [텍스트를 읽었습니다.](?success=read-printed-handwritten-text#run-the-application) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text&product=computer-vision&page=go-sdk)

## <a name="run-the-application"></a>애플리케이션 실행

`go run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [애플리케이션을 실행했습니다.](?success=run-the-application#clean-up-resources) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application&product=computer-vision&page=go-sdk)

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [리소스를 정리했습니다.](?success=clean-up-resources#next-steps) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources&product=computer-vision&page=go-sdk)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 OCR 클라이언트 라이브러리를 설치하고 Read API를 사용하는 방법을 알아보았습니다. 다음으로, Read API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[읽기 API 호출](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 개요](../../overview-ocr.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)에서 확인할 수 있습니다.
