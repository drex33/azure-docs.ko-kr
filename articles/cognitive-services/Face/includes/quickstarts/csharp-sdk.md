---
title: Face .NET 클라이언트 라이브러리 빠른 시작
description: .NET용 Face 클라이언트 라이브러리를 사용하여 얼굴을 감지하고, 유사 얼굴을 찾고(이미지별 얼굴 검색), 얼굴을 식별하고(얼굴 인식 검색), 얼굴 데이터를 마이그레이션합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 5d795debd6701c2d2d579a5558fc0631b732e66a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442533"
---
.NET용 Face 클라이언트 라이브러리를 사용하여 얼굴 인식을 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

.NET용 Face 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [얼굴 식별](#identify-a-face)
* [유사 얼굴 찾기](#find-similar-faces)

[참조 설명서](/dotnet/api/overview/azure/cognitiveservices/face-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.7.0-preview.1) | [샘플](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>필수 구성 요소


* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio를 사용하여 새 .NET Core 애플리케이션을 만듭니다. 

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

새 프로젝트를 만든 후 **솔루션 탐색기** 에서 프로젝트 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기** 를 선택하고, **시험판 포함** 을 선택하고, `Microsoft.Azure.CognitiveServices.Vision.Face`를 검색합니다. `2.7.0-preview.1` 버전, **설치** 를 차례로 선택합니다. 

#### <a name="cli"></a>[CLI](#tab/cli)

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `face-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs* 라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n face-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Face 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.7.0-preview.1
```

---

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.


프로젝트 디렉터리에서 *program.cs* 파일을 열고 `using` 지시문을 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

애플리케이션의 **Program** 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.


> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Face 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](../../../cognitive-services-security.md) 문서를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

애플리케이션의 **Main** 메서드에서 이 빠른 시작에 사용된 메서드에 대한 호출을 추가합니다. 이러한 기능은 나중에 구현합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>개체 모델

Face .NET 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | 이 클래스는 Face 서비스를 사용할 수 있는 권한의 부여를 나타내며 모든 Face 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|이 클래스는 사람 얼굴을 사용하여 수행할 수 있는 기본 감지 및 인식 작업을 처리합니다. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|이 클래스는 이미지의 한 얼굴에서 감지된 모든 데이터를 나타냅니다. 얼굴에 대한 자세한 정보를 검색하는 데 사용할 수 있습니다.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|이 클래스는 분류된 얼굴 세트를 저장하는 클라우드 저장 **FaceList** 구문을 관리합니다. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| 이 클래스는 한 사람에게 속한 얼굴 세트를 저장하는 클라우드 저장 **Person** 구문을 관리합니다.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| 이 클래스는 분류된 **Person** 개체 세트를 저장하는 클라우드 저장 **PersonGroup** 구문을 관리합니다. |

## <a name="code-examples"></a>코드 예제

아래 코드 조각에서는 .NET용 Face 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [얼굴 식별](#identify-a-face)
* [유사 얼굴 찾기](#find-similar-faces)

## <a name="authenticate-the-client"></a>클라이언트 인증

새 메서드에서 엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** 개체를 만들고, 엔드포인트에서 이를 사용하여 **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** 개체를 만듭니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>도우미 필드 선언

다음 필드는 나중에 추가할 몇 가지 얼굴 작업에 필요합니다. **Program** 클래스의 루트에서 다음 URL 문자열을 정의합니다. 이 URL은 샘플 이미지 폴더를 가리킵니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

**Main** 메서드에서 다른 인식 모델 유형을 가리키는 문자열을 정의합니다. 나중에 얼굴 감지에 사용할 인식 모델을 지정할 수 있습니다. 이러한 옵션에 대한 자세한 내용은 [인식 모델 지정](../../Face-API-How-to-Topics/specify-recognition-model.md)을 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-and-analyze-faces"></a>얼굴 감지 및 분석
얼굴 감지는 다른 모든 시나리오에서 첫 번째 단계로 필요합니다. 이 섹션에서는 추가 얼굴 특성 데이터를 반환하는 방법을 보여 줍니다. 얼굴 식별 또는 확인을 위해 얼굴만 감지하려면 이후 섹션으로 건너뜁니다.

### <a name="get-detected-face-objects"></a>검색된 얼굴 개체 가져오기

얼굴을 감지하는 새 메서드를 만듭니다. `DetectFaceExtract` 메서드는 지정된 URL에서 세 개의 이미지를 처리하고 **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** 개체의 목록을 프로그램 메모리에 만듭니다. **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** 값 목록은 추출할 기능을 지정합니다. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> 로컬 이미지에서 얼굴을 검색할 수도 있습니다. [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) 메서드(예: **DetectWithStreamAsync**)를 참조하세요.

### <a name="display-detected-face-data"></a>검색된 얼굴 데이터 표시

나머지 `DetectFaceExtract` 메서드는 검색된 각 얼굴에 대한 특성 데이터를 구문 분석하고 출력합니다. 각 특성은 원래 얼굴 검색 API 호출( **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** 목록의)에서 별도로 지정해야 합니다. 다음 코드는 모든 특성을 처리하지만 하나 또는 몇 가지를 사용해야 할 가능성이 높습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]



## <a name="identify-a-face"></a>얼굴 식별

식별 작업은 한 사람(또는 여러 사람)의 이미지를 가져와 이미지에서 각 얼굴과 연관되는 저장된 사람 개체를 찾습니다(얼굴 인식 검색). 감지된 각 얼굴을 얼굴 데이터가 알려진 다른 **Person** 개체의 데이터베이스인 **PersonGroup** 과 비교합니다. 식별 작업을 수행하려면 먼저 **PersonGroup** 을 만들고 학습해야 합니다.

### <a name="create-a-persongroup"></a>PersonGroup 만들기

다음 코드에서는 6개의 서로 다른 **Person** 개체를 사용하여 **PersonGroup** 을 만듭니다. 각 **Person** 을 예제 이미지 세트와 연결한 다음, 해당 얼굴 특성을 통해 각 사람을 인식하도록 학습시킵니다. **Person** 및 **PersonGroup** 개체는 Verify(검증), Identify(식별) 및 Group(그룹) 작업에 사용됩니다.

**PersonGroup** 의 ID를 나타내도록 클래스의 루트에서 문자열 변수를 선언합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

새 메서드에서 다음 코드를 추가합니다. 이 메서드는 식별 작업을 수행합니다. 첫 번째 코드 블록은 사람의 이름을 해당 예제 이미지와 연결합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

이 코드는 `sourceImageFileName` 변수를 정의합니다. 이 변수는 식별할 사람이 포함된 원본 이미지에 해당합니다.

다음으로, 다음 코드를 추가하여 Dictionary(사전)의 각 사람에 대한 **Person** 개체를 만들고 적절한 이미지에서 얼굴 데이터를 추가합니다. 각 **Person** 개체는 고유 ID 문자열을 통해 동일한 **PersonGroup** 과 연결됩니다. `client`, `url` 및 `RECOGNITION_MODEL1` 변수를 이 메서드에 전달해야 합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> 로컬 이미지에서 **PersonGroup** 을 만들 수도 있습니다. [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) 메서드(예: **AddFaceFromStreamAsync**)를 참조하세요.

### <a name="train-the-persongroup"></a>PersonGroup 학습

이미지에서 얼굴 데이터를 추출하여 다른 **Person** 개체에 정렬했으면 각 **Person**  개체와 연결된 시각적 기능을 식별하도록 **PersonGroup** 을 학습시켜야 합니다. 다음 코드에서는 비동기 **train** 메서드를 호출하고, 결과를 폴링하여 상태를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Face API는 기본적으로 정적인 사전 빌드된 모델 세트에서 실행됩니다(서비스가 실행될 때 모델의 성능이 저하되거나 향상되지 않음). Microsoft에서 완전히 새로운 모델 버전으로 마이그레이션하지 않고 모델의 백엔드를 업데이트하면 모델이 생성하는 결과가 변경될 수 있습니다. 최신 버전의 모델을 사용하려면 **PersonGroup** 을 동일한 등록 이미지를 가진 매개 변수로 지정하여 다시 학습할 수 있습니다.

이제 이 **Person** 그룹 및 연결된 해당 **Person** 개체는 Verify(검증), Identify(식별) 또는 Group(그룹) 작업에 사용할 수 있습니다.

### <a name="identify-faces"></a>얼굴 식별

다음 코드에서는 원본 이미지를 가져와서 이미지에서 감지된 모든 얼굴의 목록을 만듭니다. 이러한 얼굴은 **PersonGroup** 과 비교하여 식별되는 얼굴입니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

다음 코드 조각에서는 **IdentifyAsync** 작업을 호출하고 결과를 콘솔에 출력합니다. 여기서는 서비스에서 원본 이미지의 각 얼굴을 지정된 **PersonGroup** 의 **Person** 과 일치시키려고 합니다. 이렇게 하면 Identify 메서드가 종료됩니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]


## <a name="find-similar-faces"></a>유사 얼굴 찾기

다음 코드에서는 감지된 하나의 얼굴(원본)을 가져오고, 다른 얼굴(대상) 세트를 검색하여 일치 항목을 찾습니다(이미지별 얼굴 검색). 일치 항목을 찾으면 일치하는 얼굴의 ID를 콘솔에 출력합니다.

### <a name="detect-faces-for-comparison"></a>비교할 얼굴 감지

먼저 두 번째 얼굴 감지 메서드를 정의합니다. 이미지를 비교하기 전에 이미지에서 얼굴을 감지해야 하며, 이 감지 메서드는 비교 작업에 최적화되어 있습니다. 위의 섹션과 같이 자세한 얼굴 특성을 추출하지 않으며 다른 인식 모델을 사용합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>일치 항목 찾기

다음 메서드는 대상 이미지 세트와 단일 원본 이미지에서 얼굴을 감지합니다. 그런 다음, 이를 비교하여 원본 이미지와 유사한 모든 대상 이미지를 찾습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>일치 항목 출력

다음 코드에서는 일치 항목 세부 정보를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]


## <a name="run-the-application"></a>애플리케이션 실행

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE 창의 위쪽에서 **디버그** 단추를 클릭하여 애플리케이션을 실행합니다.

#### <a name="cli"></a>[CLI](#tab/cli)

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

이 빠른 시작에서는 **PersonGroup** 을 만들었으며, 이를 삭제하려면 프로그램에서 다음 코드를 실행합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

다음 코드를 사용하여 삭제 메서드를 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 .NET용 Face 클라이언트 라이브러리를 사용하여 기본 얼굴 인식 작업을 수행하는 방법을 알아보았습니다. 다음으로, 다양한 얼굴 감지 모델과 사용 사례에 적합한 모델을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [얼굴 감지 모델 버전 지정](../../Face-API-How-to-Topics/specify-detection-model.md)

* [Face 서비스란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)에서 확인할 수 있습니다.
