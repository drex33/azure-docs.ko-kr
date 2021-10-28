---
title: '빠른 시작: Form Recognizer C# SDK v3.0 | 미리 보기'
titleSuffix: Azure Applied AI Services
description: Form Recognizer C# 클라이언트 라이브러리 SDK v3.0(미리 보기)을 사용한 양식/문서 처리, 데이터 추출 및 분석
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 50c1cfbcfc79212f03fd67f783afaff110ce1e09
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216735"
---
# <a name="quickstart-form-recognizer-c-client-library-sdks-v30--preview"></a>빠른 시작: Form Recognizer C# 클라이언트 라이브러리 SDK v3.0 | 미리 보기

C# 프로그래밍 언어를 사용하여 Azure Form Recognizer를 시작합니다. Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 [Azure Applied AI Service](../../../applied-ai-services/index.yml) 클라우드 서비스입니다. REST API 또는 SDK를 통해 Form Recognizer를 사용할 수 있습니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

>[!NOTE]
> Form Recognizer v3.0은 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 

[참조 설명서](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet&preserve-view=true ) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

Azure Cognitive Services Form Recognizer는 기계 학습을 사용하여 문서에서 양식 필드, 텍스트 및 테이블을 추출하고 분석하는 클라우드 서비스입니다. 클라이언트 라이브러리 SDK를 워크플로 및 애플리케이션에 통합하여 Form Recognizer 모델을 쉽게 호출할 수 있습니다.

### <a name="form-recognizer-models"></a>Form Recognize 모델

C# SDK에서 지원하는 모델 및 기능은 다음과 같습니다.

* 🆕일반 문서 - 텍스트, 테이블, 구조, 키-값 쌍 및 명명된 엔터티를 분석하고 추출합니다.|
* 레이아웃 - 모델을 학습시킬 필요 없이 양식 문서의 라디오 단추 및 확인란과 같은 테이블, 선, 단어 및 선택 표시를 분석하고 추출합니다.
* 사용자 지정 - 사용자 고유의 양식 유형으로 학습된 모델을 사용하여 사용자 지정 양식에서 양식 필드 및 기타 콘텐츠를 분석하고 추출합니다.
* 청구서 - 미리 학습된 모델을 사용하여 청구서에서 공통 필드를 분석하고 추출합니다.
* 영수증 - 미리 학습된 영수증 모델을 사용하여 영수증에서 공통 필드를 분석하고 추출합니다.
* ID 문서 - 미리 학습된 ID 문서 모델을 사용하여 여권 또는 운전면허증과 같은 ID 문서에서 공통 필드를 분석하고 추출합니다.
* 명함 - 미리 학습된 명함 모델을 사용하여 명함에서 공통 필드를 분석하고 추출합니다.

이 빠른 시작에서는 다음 기능을 사용하여 양식 및 문서에서 데이터와 값을 분석하고 추출합니다.

* [**일반 문서**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**미리 빌드된 청구서**](#try-it-prebuilt-invoice-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* 현재 버전의 [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 [.NET Core](https://dotnet.microsoft.com/download)

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

> [!TIP] 
> 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](../../../active-directory/authentication/overview-authentication.md)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스가 배포되면 **리소스로 이동** 을 클릭합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="set-up"></a>설정

### <a name="option-1-net-command-line-interface-cli"></a>[옵션 1: .NET CLI(명령줄 인터페이스)](#tab/cli)

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `formrecognizer-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs* 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n formrecognizer-quickstart
```

명령줄을 열고 프로젝트 파일이 포함된 디렉터리로 전환합니다. 다음을 사용하여 애플리케이션을 빌드합니다.

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

### <a name="install-the-client-library-with-nuget"></a>NuGet을 사용하여 클라이언트 라이브러리 설치

프로젝트가 포함된 디렉터리에서 다음 명령을 사용하여 .NET용 Form Recognizer 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.AI.FormRecognizer
```

이 버전의 클라이언트 라이브러리는 기본적으로 서비스의 2021-09-30-preview 버전으로 설정됩니다.

### <a name="option-2-visual-studio"></a>[옵션 2: Visual Studio](#tab/vs)

1. Visual Studio 2019를 시작합니다.

1. 시작 페이지에서 새 프로젝트 만들기를 선택합니다.

    :::image type="content" source="../media/quickstarts/start-window.png" alt-text="스크린샷: Visual Studio 시작 창":::

1. **새 프로젝트 만들기 페이지** 의 검색 상자에서 **콘솔** 을 입력합니다. **콘솔 애플리케이션** 템플릿을 선택하고, **다음** 을 선택합니다.

    :::image type="content" source="../media/quickstarts/create-new-project.png" alt-text="스크린샷: Visual Studio 새 프로젝트 만들기 페이지":::

1. **새 프로젝트 구성** 대화 상자 창의 [프로젝트 이름] 상자에서 `formRecognizer_quickstart`를 입력합니다. 그런 다음, [다음]을 선택합니다.

    :::image type="content" source="../media/quickstarts/configure-new-project.png" alt-text="스크린샷: Visual Studio 새 프로젝트 구성 대화 상자 창":::

1. **추가 정보** 대화 상자 창에서 **.NET 5.0(현재)** 을 선택한 다음, **만들기** 를 선택합니다.

    :::image type="content" source="../media/quickstarts/additional-information.png" alt-text="스크린샷: Visual Studio 추가 정보 대화 상자 창":::

### <a name="install-the-client-library-with-nuget"></a>NuGet을 사용하여 클라이언트 라이브러리 설치

 1. 마우스 오른쪽 단추로 **formRecognizer_quickstart** 를 클릭하고, **NuGet 패키지 관리...** 를 선택합니다.

    :::image type="content" source="../media/quickstarts/select-nuget-package.png" alt-text="스크린샷: select-nuget-package.png":::

 1. [찾아보기] 탭을 선택하고, Azure.AI.FormRecognizer를 입력합니다.

     :::image type="content" source="../media/quickstarts/azure-nuget-package.png" alt-text="스크린샷: select-form-recognizer-package.png":::

 1. 드롭다운 메뉴에서 원하는 버전을 선택하고, **설치** 를 선택합니다.

     이 버전의 클라이언트 라이브러리는 기본적으로 서비스의 2021-09-30-preview 버전으로 설정됩니다.

---

## <a name="build-your-application"></a>애플리케이션 빌드

Form Recognizer 서비스와 상호 작용하려면 `DocumentAnalysisClient` 클래스의 인스턴스를 만들어야 합니다. 이렇게 하려면 apiKey를 사용하여 `AzureKeyCredential`을 만들고, `AzureKeyCredential` 및 Form Recognizer `endpoint`를 사용하여 `DocumentAnalysisClient` 인스턴스를 만듭니다.

1. **Program.cs** 파일을 엽니다.

1. 다음 using 지시문을 포함합니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.AI.FormRecognizer.DocumentAnalysis;
    ```

1. `endpoint` 및 `apiKey` 환경 변수를 설정하고, `AzureKeyCredential` 및 `DocumentAnalysisClient` 인스턴스를 만듭니다.

    ```csharp
    string endpoint = "<your-endpoint>";
    string apiKey = "<your-apiKey>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), credential);
    ```

1. `Console.Writeline("Hello World!");` 줄을 삭제하고, **사용해 보세요** 코드를 **Program.cs** 파일의 **Main** 메서드에 추가합니다.

    :::image type="content" source="../media/quickstarts/add-code-here.png" alt-text="스크린샷: Main 메서드에 샘플 코드 추가":::

## <a name="try-it-general-document-model"></a>**사용해 보기**: 일반 문서 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 Main 메서드의 파일 위쪽에 있는 `string fileUri` 변수에 추가합니다.
> * URI에서 지정된 파일을 분석하려면 `StartAnalyzeDocumentFromUri` 메서드를 사용합니다. 반환된 값은 제출된 문서에 대한 데이터가 포함된 `AnalyzeResult` 개체입니다.

### <a name="add-the-following-code-to-your-general-document-application-main-method"></a>다음 코드를 일반 문서 애플리케이션 **Main** 메서드에 추가

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-document", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

Console.WriteLine("Detected entities:");

foreach (DocumentEntity entity in result.Entities)
{
    if (entity.SubCategory == null)
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}'.");
    }
    else
    {
        Console.WriteLine($"  Found entity '{entity.Content}' with category '{entity.Category}' and sub-category '{entity.SubCategory}'.");
    }
}

Console.WriteLine("Detected key-value pairs:");

foreach (DocumentKeyValuePair kvp in result.KeyValuePairs)
{
    if (kvp.Value.Content == null)
    {
        Console.WriteLine($"  Found key with no value: '{kvp.Key.Content}'");
    }
    else
    {
        Console.WriteLine($"  Found key-value pair: '{kvp.Key.Content}' and '{kvp.Value.Content}'");
    }
}

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

문서에서 경계 영역 좌표와 함께 텍스트, 선택 표시, 텍스트 스타일 및 테이블 구조를 추출합니다.

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 Main 메서드의 파일 위쪽에 있는 `string fileUri` 변수에 추가합니다.
> * URI의 지정된 파일에서 레이아웃을 추출하려면 `StartAnalyzeDocumentFromUri` 메서드를 사용하고 `prebuilt-layout`을 모델 ID로 전달합니다. 반환된 값은 제출된 문서의 데이터가 포함된 `AnalyzeResult` 개체입니다.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>다음 코드를 레이아웃 애플리케이션 **Main** 메서드에 추가

```csharp
// sample form document
string fileUri = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentFromUriAsync("prebuilt-layout", fileUri);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

foreach (DocumentPage page in result.Pages)
{
    Console.WriteLine($"Document Page {page.PageNumber} has {page.Lines.Count} line(s), {page.Words.Count} word(s),");
    Console.WriteLine($"and {page.SelectionMarks.Count} selection mark(s).");

    for (int i = 0; i < page.Lines.Count; i++)
    {
        DocumentLine line = page.Lines[i];
        Console.WriteLine($"  Line {i} has content: '{line.Content}'.");

        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {line.BoundingBox[0].X}, Y= {line.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {line.BoundingBox[1].X}, Y= {line.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {line.BoundingBox[2].X}, Y= {line.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {line.BoundingBox[3].X}, Y= {line.BoundingBox[3].Y}");
    }

    for (int i = 0; i < page.SelectionMarks.Count; i++)
    {
        DocumentSelectionMark selectionMark = page.SelectionMarks[i];

        Console.WriteLine($"  Selection Mark {i} is {selectionMark.State}.");
        Console.WriteLine($"    Its bounding box is:");
        Console.WriteLine($"      Upper left => X: {selectionMark.BoundingBox[0].X}, Y= {selectionMark.BoundingBox[0].Y}");
        Console.WriteLine($"      Upper right => X: {selectionMark.BoundingBox[1].X}, Y= {selectionMark.BoundingBox[1].Y}");
        Console.WriteLine($"      Lower right => X: {selectionMark.BoundingBox[2].X}, Y= {selectionMark.BoundingBox[2].Y}");
        Console.WriteLine($"      Lower left => X: {selectionMark.BoundingBox[3].X}, Y= {selectionMark.BoundingBox[3].Y}");
    }
}

foreach (DocumentStyle style in result.Styles)
{
    // Check the style and style confidence to see if text is handwritten.
    // Note that value '0.8' is used as an example.

    bool isHandwritten = style.IsHandwritten.HasValue && style.IsHandwritten == true;

    if (isHandwritten && style.Confidence > 0.8)
    {
        Console.WriteLine($"Handwritten content found:");

        foreach (DocumentSpan span in style.Spans)
        {
            Console.WriteLine($"  Content: {result.Content.Substring(span.Offset, span.Length)}");
        }
    }
}

Console.WriteLine("The following tables were extracted:");

for (int i = 0; i < result.Tables.Count; i++)
{
    DocumentTable table = result.Tables[i];
    Console.WriteLine($"  Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");

    foreach (DocumentTableCell cell in table.Cells)
    {
        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) has kind '{cell.Kind}' and content: '{cell.Content}'.");
    }
}

```

## <a name="try-it-prebuilt-invoice-model"></a>**사용해 보기**: 미리 빌드된 청구서 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정 유형의 일반 문서에 있는 데이터를 분석하는 방법을 보여 줍니다.

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 청구서 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 Main 메서드의 파일 위쪽에 있는 `string fileUri` 변수에 추가합니다.
> * URI에서 지정된 파일을 분석하려면 `StartAnalyzeDocumentFromUri` 메서드를 사용하고 `prebuilt-invoice`를 모델 ID로 전달합니다. 반환된 값은 제출된 문서의 데이터가 포함된 `AnalyzeResult` 개체입니다.

### <a name="choose-the-invoice-prebuilt-model-id"></a>청구서가 미리 빌드된 모델 ID 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. 현재 Form Recognizer 서비스에서 지원하는 미리 빌드된 모델에 대한 모델 ID는 다음과 같습니다.

* **prebuilt-invoice**: 청구서에서 텍스트, 선택 표시, 테이블, 키-값 쌍 및 키 정보를 추출합니다.
* **prebuilt-businessCard**: 명함에서 텍스트 및 키 정보를 추출합니다.
* **prebuilt-idDocument**: 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* **prebuilt-receipt**: 영수증에서 텍스트 및 키 정보를 추출합니다.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>다음 코드를 미리 빌드된 청구서 애플리케이션의 **Main** 메서드에 추가

```csharp
// sample invoice document
string filePath = "(https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

using var stream = new FileStream(filePath, FileMode.Open);

AnalyzeDocumentOperation operation = await client.StartAnalyzeDocumentAsync("prebuilt-invoice", stream);

await operation.WaitForCompletionAsync();

AnalyzeResult result = operation.Value;

for (int i = 0; i < result.Documents.Count; i++)
{
    Console.WriteLine($"Document {i}:");

    AnalyzedDocument document = result.Documents[i];

    if (document.Fields.TryGetValue("VendorName", out DocumentField vendorNameField))
    {
        if (vendorNameField.ValueType == DocumentFieldType.String)
        {
            string vendorName = vendorNameField.AsString();
            Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("CustomerName", out DocumentField customerNameField))
    {
        if (customerNameField.ValueType == DocumentFieldType.String)
        {
            string customerName = customerNameField.AsString();
            Console.WriteLine($"Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("Items", out DocumentField itemsField))
    {
        if (itemsField.ValueType == DocumentFieldType.List)
        {
            foreach (DocumentField itemField in itemsField.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.ValueType == DocumentFieldType.Dictionary)
                {
                    IReadOnlyDictionary<string, DocumentField> itemFields = itemField.AsDictionary();

                    if (itemFields.TryGetValue("Description", out DocumentField itemDescriptionField))
                    {
                        if (itemDescriptionField.ValueType == DocumentFieldType.String)
                        {
                            string itemDescription = itemDescriptionField.AsString();

                            Console.WriteLine($"  Description: '{itemDescription}', with confidence {itemDescriptionField.Confidence}");
                        }
                    }

                    if (itemFields.TryGetValue("Amount", out DocumentField itemAmountField))
                    {
                        if (itemAmountField.ValueType == DocumentFieldType.Double)
                        {
                            double itemAmount = itemAmountField.AsDouble();

                            Console.WriteLine($"  Amount: '{itemAmount}', with confidence {itemAmountField.Confidence}");
                        }
                    }
                }
            }
        }
    }

    if (document.Fields.TryGetValue("SubTotal", out DocumentField subTotalField))
    {
        if (subTotalField.ValueType == DocumentFieldType.Double)
        {
            double subTotal = subTotalField.AsDouble();
            Console.WriteLine($"Sub Total: '{subTotal}', with confidence {subTotalField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("TotalTax", out DocumentField totalTaxField))
    {
        if (totalTaxField.ValueType == DocumentFieldType.Double)
        {
            double totalTax = totalTaxField.AsDouble();
            Console.WriteLine($"Total Tax: '{totalTax}', with confidence {totalTaxField.Confidence}");
        }
    }

    if (document.Fields.TryGetValue("InvoiceTotal", out DocumentField invoiceTotalField))
    {
        if (invoiceTotalField.ValueType == DocumentFieldType.Double)
        {
            double invoiceTotal = invoiceTotalField.AsDouble();
            Console.WriteLine($"Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
        }
    }
}

```

## <a name="run-your-application"></a>애플리케이션 실행

### <a name="net-command-line-interface-cli"></a>[.NET CLI(명령줄 인터페이스)](#tab/cli)

명령 프롬프트를 열고, 프로젝트가 포함된 디렉터리로 이동하여 다음을 입력합니다.

```console
dotnet run formrecognizer-quickstart.dll
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

formRecognizer_quickstart 옆에 있는 녹색 **시작** 단추를 선택하여 프로그램을 빌드하고 실행하거나 **F5** 키를 누릅니다.

  :::image type="content" source="../media/quickstarts/run-visual-studio.png" alt-text="스크린샷: Visual Studio 프로그램 실행":::

---

축하합니다! 이 빠른 시작에서는 Form Recognizer C# SDK를 사용하여 다양한 양식과 문서를 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 탐색하여 Form Recognizer API에 관해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API v3.0 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)