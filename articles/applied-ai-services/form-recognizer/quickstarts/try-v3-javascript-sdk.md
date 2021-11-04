---
title: '빠른 시작: Form Recognizer JavaScript SDK v3.0 | 미리 보기'
titleSuffix: Azure Applied AI Services
description: Form Recognizer JavaScript 클라이언트 라이브러리 SDKs v3.0(미리 보기)을 사용한 양식 및 문서 처리, 데이터 추출 및 분석
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 364eea405653ac54032787a14a612fbc57de03db
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030872"
---
# <a name="quickstart-form-recognizer-javascript-client-library-sdks-v30--preview"></a>빠른 시작: Form Recognizer JavaScript 클라이언트 라이브러리 SDKs v3.0 | 미리 보기

>[!NOTE]
> Form Recognizer v3.0은 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다.

[참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/formrecognizer/ai-form-recognizer/src) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_4.0.0-beta.1/sdk/formrecognizer/ai-form-recognizer/README.md)

JavaScript 프로그래밍 언어를 사용하여 Azure Form Recognizer를 시작합니다. Azure Form Recognizer는 기계 학습을 사용하여 문서에서 양식 필드, 텍스트 및 테이블을 추출하고 분석하는 클라우드 기반 Azure Applied AI Service입니다. 클라이언트 라이브러리 SDK를 워크플로 및 애플리케이션에 통합하여 Form Recognizer 모델을 쉽게 호출할 수 있습니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

Form Recognizer 기능 및 개발 옵션에 대한 자세한 내용은 [개요](../overview.md#form-recognizer-features-and-development-options) 페이지를 참조하세요.

이 빠른 시작에서는 다음 기능을 사용하여 양식 및 문서에서 데이터와 값을 분석하고 추출합니다.

* [🆕 **일반 문서**](#try-it-general-document-model) - 텍스트, 테이블, 구조, 키-값 쌍 및 명명된 엔터티를 분석하고 추출합니다.

* [**레이아웃**](#try-it-layout-model) - 모델을 학습시킬 필요 없이 테이블, 선, 단어, 선택 표시(양식 문서의 원형 선택 단추 및 확인란)를 분석하고 추출합니다.

* [**미리 빌드된 청구서**](#try-it-prebuilt-model) - 미리 학습된 모델을 사용하여 청구서에서 자주 사용되는 필드를 분석하고 추출합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* 최신 버전의 [Visual Studio Code](https://code.visualstudio.com/) 또는 선호하는 IDE 

* 최신 LTS 버전의 [Node.js](https://nodejs.org/about/releases/)

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

    > [!TIP]
    > 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](/azure/active-directory/authentication/overview-authentication)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스를 배포한 후 **리소스로 이동** 을 선택합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="set-up"></a>설정

1. 새 Node.js 애플리케이션 만들기 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

    ```console
    mkdir form-recognizer-app && cd form-recognizer-app
    ```

1. `package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다.

    ```console
    npm init
    ```

1. `ai-form-recognizer` 클라이언트 라이브러리 npm 패키지를 설치합니다.

    ```console
    npm install @azure/ai-form-recognizer@4.0.0-beta.1 @azure/identity
    ```

    * 종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

1. 이름이 `index.js`인 파일을 만들고, 열고, 다음 라이브러리를 추가합니다.

    ```javascript
   const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");
    ```

1. 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

이 시점에서 JavaScript 애플리케이션에는 다음 코드 줄이 포함되어야 합니다.

```javascript
const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>애플리케이션에 복사하여 붙여넣을 코드 샘플을 선택합니다.

* [**일반 문서**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**미리 빌드된 청구서**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](../../../cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

## <a name="try-it-general-document-model"></a>**사용해 보기**: 일반 문서 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * URI에서 지정된 파일을 분석하려면 `beginExtractGenericDocument` 메서드를 사용합니다.
> * 파일 URI 값을 파일 상단 근처에 있는 `formUrl` 변수에 추가합니다.
> * 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [일반 문서](../concept-general-document.md#named-entity-recognition-ner-categories) 개념 페이지를 참조하세요.

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-apikey-variable"></a>다음 코드를 일반 문서 애플리케이션의 `apiKey` 변수 아래의 줄에 추가

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new DefaultAzureCredential(apiKey));

    const poller = await client.beginExtractGenericDocument(formUrl);

    const {
        keyValuePairs,
        entities
    } = await poller.pollUntilDone();

    if (keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${value?.content ?? "<undefined>"}" (${confidence})`);
        }
    }

    if (entities.length <= 0) {
        console.log("No entities were extracted from the document.");
    } else {
        console.log("Entities:");
        for (const entity of entities) {
            console.log(
                `- "${entity.content}" ${entity.category} - ${entity.subCategory ?? "<none>"} (${
          entity.confidence
        })`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});
```

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 파일 상단 근처에 있는 `formUrl` 변수에 추가합니다.
> * URI에서 지정된 파일을 분석하려면 `beginExtractLayout` 메서드를 사용합니다.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>다음 코드를 레이아웃 애플리케이션의 `apiKey` 변수 아래의 줄에 추가

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginExtractLayout(formUrl);

    const {
        pages,
        tables
    } = await poller.pollUntilDone();

    if (pages.length <= 0) {
        console.log("No pages were extracted from the document.");
    } else {
        console.log("Pages:");
        for (const page of pages) {
            console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
            console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
            console.log(`  ${page.lines.length} lines, ${page.words.length} words`);
        }
    }

    if (tables.length <= 0) {
        console.log("No tables were extracted from the document.");
    } else {
        console.log("Tables:");
        for (const table of tables) {
            console.log(
                `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="try-it-prebuilt-model"></a>**사용해 보기**: 미리 빌드된 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정한 일반 문서 유형에 있는 데이터를 분석하는 방법을 보여 줍니다.

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 파일 위쪽의 `invoiceUrl` 변수에 추가했습니다.
> * URI에서 지정된 파일을 분석하기 위해 `beginAnalyzeDocuments` 메서드를 사용하고 모델 ID로 `PrebuiltModels.Invoice`를 전달합니다. 반환된 값은 제출된 문서에 대한 데이터가 포함된 `result` 개체입니다.
> * 간단히 하기 위해 서비스에서 반환하는 모든 키-값 쌍이 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [청구서](../concept-invoice.md#field-extraction) 개념 페이지를 참조하세요.

### <a name="choose-the-invoice-prebuilt-model-id"></a>청구서가 미리 빌드된 모델 ID 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. 현재 Form Recognizer 서비스에서 지원하는 미리 빌드된 모델에 대한 모델 ID는 다음과 같습니다.

* [**prebuilt-invoice**](../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 키-값 쌍 및 키 정보를 추출합니다.
* [**prebuilt-receipt**](../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**prebuilt-idDocument**](../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**prebuilt-businessCard**](../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>다음 코드를 미리 빌드된 청구서 애플리케이션의 `apiKey` 변수 아래에 추가

```javascript

const {PreBuiltModels} = require("@azure/ai-form-recognizer");

// Use of PrebuiltModels.Receipt above (rather than the raw model ID), adds strong typing of the model's output

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function main() {

    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginAnalyzeDocuments(PrebuiltModels.Invoice, invoiceUrl);

    const {
        documents: [result]
    } = await poller.pollUntilDone();

    if (result) {
        const invoice = result.fields;

        console.log("Vendor Name:", invoice.vendorName?.value);
        console.log("Customer Name:", invoice.customerName?.value);
        console.log("Invoice Date:", invoice.invoiceDate?.value);
        console.log("Due Date:", invoice.dueDate?.value);

        console.log("Items:");
        for (const {
                properties: item
            } of invoice.items?.values ?? []) {
            console.log("-", item.productCode?.value ?? "<no product code>");
            console.log("  Description:", item.description?.value);
            console.log("  Quantity:", item.quantity?.value);
            console.log("  Date:", item.date?.value);
            console.log("  Unit:", item.unit?.value);
            console.log("  Unit Price:", item.unitPrice?.value);
            console.log("  Tax:", item.tax?.value);
            console.log("  Amount:", item.amount?.value);
        }

        console.log("Subtotal:", invoice.subTotal?.value);
        console.log("Previous Unpaid Balance:", invoice.previousUnpaidBalance?.value);
        console.log("Tax:", invoice.totalTax?.value);
        console.log("Amount Due:", invoice.amountDue?.value);
    } else {
        throw new Error("Expected at least one receipt in the result.");
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="run-your-application"></a>애플리케이션 실행

1. Form Recognizer 애플리케이션(form-recognizer-app)이 있는 폴더로 이동합니다.

1. 터미널에 다음 명령을 입력합니다.

```console
node index.js
```

축하합니다! 이 빠른 시작에서는 Form Recognizer JavaScript SDK를 사용하여 다양한 양식을 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 살펴보고서 Form Recognizer v3.0 API에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API v3.0 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Form Recognizer JavaScript 참조 라이브러리](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html)
