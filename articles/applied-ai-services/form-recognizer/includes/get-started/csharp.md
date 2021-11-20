---
title: '시작: .NET v2.1용 Form Recognizer 클라이언트 라이브러리'
description: .NET용 Form Recognizer SDK를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74f9373d6ef108bec6555d5124c01d1a4f371266
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132876660"
---
<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
> 이 빠른 시작은 cURL을 사용하여 REST API 호출을 실행하는 Azure Form Recognizer REST API 버전 **2.1** 을 대상으로 합니다.
>
>* 간단한 말해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다.

[참조 설명서](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

이 빠른 시작에서는 다음 API를 사용하여 양식과 문서에서 구조화된 데이터를 추출합니다.

* [레이아웃](#try-it-layout-model)

* [청구서](#try-it-prebuilt-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* 현재 버전의 [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

    > [!TIP]
    > 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](../../../../active-directory/authentication/overview-authentication.md)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스가 배포되면 **리소스로 이동** 을 클릭합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="set-up"></a>설정

1. Visual Studio 2019를 시작합니다.

1. 시작 페이지에서 새 프로젝트 만들기를 선택합니다.

    :::image type="content" source="../../media/quickstarts/start-window.png" alt-text="스크린샷: Visual Studio 시작 창":::

1. **새 프로젝트 만들기 페이지** 의 검색 상자에서 **콘솔** 을 입력합니다. **콘솔 애플리케이션** 템플릿을 선택하고, **다음** 을 선택합니다.

    :::image type="content" source="../../media/quickstarts/create-new-project.png" alt-text="스크린샷: Visual Studio 새 프로젝트 만들기 페이지":::

1. **새 프로젝트 구성** 대화 상자 창의 [프로젝트 이름] 상자에서 `formRecognizer_quickstart`를 입력합니다. 그런 다음, [다음]을 선택합니다.

    :::image type="content" source="../../media/quickstarts/configure-new-project.png" alt-text="스크린샷: Visual Studio 새 프로젝트 구성 대화 상자 창":::

1. **추가 정보** 대화 상자 창에서 **.NET 5.0(현재)** 을 선택한 다음, **만들기** 를 선택합니다.

    :::image type="content" source="../../media/quickstarts/additional-information.png" alt-text="스크린샷: Visual Studio 추가 정보 대화 상자 창":::

### <a name="install-the-client-library-with-nuget"></a>NuGet을 사용하여 클라이언트 라이브러리 설치

 1. 마우스 오른쪽 단추로 **formRecognizer_quickstart** 를 클릭하고, **NuGet 패키지 관리...** 를 선택합니다.

    :::image type="content" source="../../media/quickstarts/select-nuget-package.png" alt-text="스크린샷: select-nuget-package.png":::

 1. [찾아보기] 탭을 선택하고, Azure.AI.FormRecognizer를 입력합니다.

     :::image type="content" source="../../media/quickstarts/azure-nuget-package.png" alt-text="스크린샷: select-form-recognizer-package.png":::

 1. 드롭다운 메뉴에서 버전 **3.1.1** 을 선택하고, **설치** 를 선택합니다.

## <a name="build-your-application"></a>애플리케이션 빌드

Form Recognizer 서비스와 상호 작용하려면 `FormRecognizerClient` 클래스의 인스턴스를 만들어야 합니다. 이렇게 하려면 apiKey를 사용하여 `AzureKeyCredential`을 만들고, `AzureKeyCredential` 및 Form Recognizer `endpoint`를 사용하여 `FormRecognizerClient` 인스턴스를 만듭니다.

1. **Program.cs** 파일을 엽니다.

1. 다음 using 지시문을 포함합니다.

```csharp
using System;
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using System.Threading.Tasks;
```

1. `endpoint` 및 `apiKey` 환경 변수를 설정하고, `AzureKeyCredential` 및 `FormRecognizerClient` 인스턴스를 만듭니다.

```csharp
private static readonly string endpoint = "your-form-recognizer-endpoint";
private static readonly string apiKey = "your-api-key";
private static readonly AzureKeyCredential credential = new AzureKeyCredential(apiKey);
```

1. `Console.Writeline("Hello World!");`줄을 삭제하고, **사용해 보세요** 코드 샘플 중 하나를 **Program.cs** 파일의 **Main** 메서드에 추가합니다.

    :::image type="content" source="../../media/quickstarts/add-code-here.png" alt-text="스크린샷: Main 메서드에 샘플 코드 추가":::

1. 애플리케이션의 Main 메서드에 복사하여 붙여넣을 코드 샘플을 선택합니다.

    * [**Layout**](#try-it-layout-model)

    * [**미리 빌드된 청구서**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](.(/azure/cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

문서에서 경계 영역 좌표와 함께 텍스트, 선택 표시, 텍스트 스타일 및 테이블 구조를 추출합니다.

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 `formUri` 변수에 추가했습니다.
> * URI에서 지정된 파일에서 레이아웃을 추출하려면 `StartRecognizeContentFromUriAsync` 메서드를 사용합니다.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>다음 코드를 레이아웃 애플리케이션 **Main** 메서드에 추가합니다.

```csharp

FormRecognizerClient recognizerClient = AuthenticateClient();

Task recognizeContent = RecognizeContent(recognizerClient);
Task.WaitAll(recognizeContent);
```

### <a name="add-the-following-code-below-the-main-method"></a>다음 코드를 **Main** 메서드 아래에 추가합니다.

```csharp

private static FormRecognizerClient AuthenticateClient()
            {
                var credential = new AzureKeyCredential(apiKey);
                var client = new FormRecognizerClient(new Uri(endpoint), credential);
                return client;
            }

            private static async Task RecognizeContent(FormRecognizerClient recognizerClient)
        {
            string formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
            FormPageCollection formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(formUrl))
        .WaitForCompletionAsync();

            foreach (FormPage page in formPages)
            {
                Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

                for (int i = 0; i < page.Lines.Count; i++)
                {
                    FormLine line = page.Lines[i];
                    Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
                }

                for (int i = 0; i < page.Tables.Count; i++)
                {
                    FormTable table = page.Tables[i];
                    Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                    foreach (FormTableCell cell in table.Cells)
                    {
                        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
                    }
                }
            }
        }
    }
}

```

## <a name="try-it-prebuilt-model"></a>**체험해 보기**: 미리 빌드된 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정 유형의 일반 문서에 있는 데이터를 분석하는 방법을 보여 줍니다.

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 Main 메서드의 파일 상단에 있는 `invoiceUri` 변수에 추가했습니다.
> * URI에서 지정된 파일을 분석하려면 `StartRecognizeInvoicesFromUriAsync` 메서드를 사용합니다.
> * 간단히 하기 위해 서비스가 반환하는 모든 필드가 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [청구서](../../concept-invoice.md#field-extraction) 개념 페이지를 참조하세요.

### <a name="choose-a-prebuilt-model"></a>미리 빌드된 모델 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델은 다음과 같습니다.

* [**청구서**](../../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 필드 및 키 정보를 추출합니다.
* [**영수증**](../../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](../../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](../../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>다음 코드를 미리 빌드된 청구서 애플리케이션의 **Main** 메서드에 추가

```csharp
FormRecognizerClient recognizerClient = AuthenticateClient();

            Task analyzeinvoice = AnalyzeInvoice(recognizerClient, invoiceUrl);
            Task.WaitAll(analyzeinvoice);
```

### <a name="add-the-following-code-below-the-main-method"></a>다음 코드를 **Main** 메서드 아래에 추가합니다.

```csharp
   private static FormRecognizerClient AuthenticateClient() {
     var credential = new AzureKeyCredential(apiKey);
     var client = new FormRecognizerClient(new Uri(endpoint), credential);
     return client;
   }

   static string invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

   private static async Task AnalyzeInvoice(FormRecognizerClient recognizerClient, string invoiceUrl) {
     var options = new RecognizeInvoicesOptions() {
       Locale = "en-US"
     };
     RecognizedFormCollection invoices = await recognizerClient.StartRecognizeInvoicesFromUriAsync(new Uri(invoiceUrl), options).WaitForCompletionAsync();

     RecognizedForm invoice = invoices[0];

     FormField invoiceIdField;
     if (invoice.Fields.TryGetValue("InvoiceId", out invoiceIdField)) {
       if (invoiceIdField.Value.ValueType == FieldValueType.String) {
         string invoiceId = invoiceIdField.Value.AsString();
         Console.WriteLine($ "    Invoice Id: '{invoiceId}', with confidence {invoiceIdField.Confidence}");
       }
     }

     FormField invoiceDateField;
     if (invoice.Fields.TryGetValue("InvoiceDate", out invoiceDateField)) {
       if (invoiceDateField.Value.ValueType == FieldValueType.Date) {
         DateTime invoiceDate = invoiceDateField.Value.AsDate();
         Console.WriteLine($ "    Invoice Date: '{invoiceDate}', with confidence {invoiceDateField.Confidence}");
       }
     }

     FormField dueDateField;
     if (invoice.Fields.TryGetValue("DueDate", out dueDateField)) {
       if (dueDateField.Value.ValueType == FieldValueType.Date) {
         DateTime dueDate = dueDateField.Value.AsDate();
         Console.WriteLine($ "    Due Date: '{dueDate}', with confidence {dueDateField.Confidence}");
       }
     }

     FormField vendorNameField;
     if (invoice.Fields.TryGetValue("VendorName", out vendorNameField)) {
       if (vendorNameField.Value.ValueType == FieldValueType.String) {
         string vendorName = vendorNameField.Value.AsString();
         Console.WriteLine($ "    Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
       }
     }

     FormField vendorAddressField;
     if (invoice.Fields.TryGetValue("VendorAddress", out vendorAddressField)) {
       if (vendorAddressField.Value.ValueType == FieldValueType.String) {
         string vendorAddress = vendorAddressField.Value.AsString();
         Console.WriteLine($ "    Vendor Address: '{vendorAddress}', with confidence {vendorAddressField.Confidence}");
       }
     }

     FormField customerNameField;
     if (invoice.Fields.TryGetValue("CustomerName", out customerNameField)) {
       if (customerNameField.Value.ValueType == FieldValueType.String) {
         string customerName = customerNameField.Value.AsString();
         Console.WriteLine($ "    Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
       }
     }

     FormField customerAddressField;
     if (invoice.Fields.TryGetValue("CustomerAddress", out customerAddressField)) {
       if (customerAddressField.Value.ValueType == FieldValueType.String) {
         string customerAddress = customerAddressField.Value.AsString();
         Console.WriteLine($ "    Customer Address: '{customerAddress}', with confidence {customerAddressField.Confidence}");
       }
     }

     FormField customerAddressRecipientField;
     if (invoice.Fields.TryGetValue("CustomerAddressRecipient", out customerAddressRecipientField)) {
       if (customerAddressRecipientField.Value.ValueType == FieldValueType.String) {
         string customerAddressRecipient = customerAddressRecipientField.Value.AsString();
         Console.WriteLine($ "    Customer address recipient: '{customerAddressRecipient}', with confidence {customerAddressRecipientField.Confidence}");
       }
     }

     FormField invoiceTotalField;
     if (invoice.Fields.TryGetValue("InvoiceTotal", out invoiceTotalField)) {
       if (invoiceTotalField.Value.ValueType == FieldValueType.Float) {
         float invoiceTotal = invoiceTotalField.Value.AsFloat();
         Console.WriteLine($ "    Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
       }
     }
   }
 }
}
```

## <a name="run-your-application"></a>애플리케이션 실행

formRecognizer_quickstart 옆에 있는 녹색 **시작** 단추를 선택하여 프로그램을 빌드하고 실행하거나 **F5** 키를 누릅니다.

  :::image type="content" source="../../media/quickstarts/run-visual-studio.png" alt-text="스크린샷: Visual Studio 프로그램 실행":::

<!-- --- -->

축하합니다! 이 빠른 시작에서는 Form Recognizer C# SDK를 사용하여 다양한 양식과 문서를 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 탐색하여 Form Recognizer API에 관해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API v2.1 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Form Recognizer C#/.NET 참조 라이브러리](/dotnet/api/overview/azure/AI.FormRecognizer-readme)