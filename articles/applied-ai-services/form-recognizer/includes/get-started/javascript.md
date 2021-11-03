---
title: '빠른 시작: Form Recognizer JavaScript SDK v2.1'
titleSuffix: Azure Applied AI Services
description: Form Recognizer JavaScript 클라이언트 라이브러리 v2.1을 사용한 양식 및 문서 처리, 데이터 추출 및 분석
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f978333bdb10433f19831b5255010dacef7c81aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030879"
---
> [!IMPORTANT]
>
> 이 빠른 시작은 Azure Form Recognizer REST API **v2.1** 을 대상으로 합니다.

[참조 설명서](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [패키지(npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

이 빠른 시작에서는 다음 API를 사용하여 양식과 문서에서 구조화된 데이터를 추출합니다.

* [레이아웃](#try-it-layout-model)

* [청구서](#try-it-prebuilt-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* 최신 버전의 [Visual Studio Code](https://code.visualstudio.com/) 또는 선호하는 IDE

* 최신 LTS 버전의 [Node.js](https://nodejs.org/about/releases/)

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

    > [!TIP]
    > 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](/azure/active-directory/authentication/overview-authentication)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스를 배포한 후 **리소스로 이동** 을 선택합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

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
    npm install @azure/ai-form-recognizer
    ```

    종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

1. 이름이 `index.js`인 파일을 만들어 열고 다음 라이브러리를 가져옵니다.

    ```javascript
    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
    ```

1. 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

1. 이 시점에서 JavaScript 애플리케이션에는 다음 코드 줄이 포함되어야 합니다.

    ```javascript

    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");

    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    ```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>애플리케이션에 복사하여 붙여넣을 코드 샘플을 선택합니다.

* [**Layout**](#try-it-layout-model)

* [**미리 빌드된 청구서**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](/azure/cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 파일 상단 근처에 있는 `formUrl` 변수에 추가합니다.
> * URI에서 지정된 파일을 분석하려면 `beginRecognizeContent` 메서드를 사용합니다.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>다음 코드를 레이아웃 애플리케이션의 `apiKey` 변수 아래의 줄에 추가

```javascript
const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeContent() {
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

## <a name="try-it-prebuilt-model"></a>**체험해 보기**: 미리 빌드된 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정 유형의 일반 문서에 있는 데이터를 분석하는 방법을 보여 줍니다. [**청구서 필드**](../../concept-invoice.md#field-extraction)의 전체 목록은 미리 빌드된 개념 페이지를 *참조하세요*.

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 파일 상단에 있는 `invoiceUrl` 변수에 추가합니다.
> * URI에서 지정된 파일을 분석하려면 `beginRecognizeInvoices` 메서드를 사용합니다.
> * 간단히 하기 위해 서비스가 반환하는 모든 필드가 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [청구서](../../concept-invoice.md#field-extraction) 개념 페이지를 참조하세요.

### <a name="choose-a-prebuilt-model"></a>미리 빌드된 모델 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델은 다음과 같습니다.

* [**청구서**](../../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 필드 및 키 정보를 추출합니다.
* [**영수증**](../../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](../../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](../../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>다음 코드를 미리 빌드된 청구서 애플리케이션의 `apiKey` 변수 아래에 추가

```javascript

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeInvoices() {

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginRecognizeInvoicesFromUrl(invoiceUrl);
    const [invoice] = await poller.pollUntilDone();

    if (invoice === undefined) {
        throw new Error("Failed to extract data from at least one invoice.");
    }

    /**
     * This is a helper function for printing a simple field with an elemental type.
     */
    function fieldToString(field) {
        const {
            name,
            valueType,
            value,
            confidence
        } = field;
        return `${name} (${valueType}): '${value}' with confidence ${confidence}'`;
    }

    console.log("Invoice fields:");

    /**
     * Invoices contain a lot of optional fields, but they are all of elemental types
     * such as strings, numbers, and dates, so we will just enumerate them all.
     */
    for (const [name, field] of Object.entries(invoice.fields)) {
        if (field.valueType !== "array" && field.valueType !== "object") {
            console.log(`- ${name} ${fieldToString(field)}`);
        }
    }

    // Invoices also support nested line items, so we can iterate over them.
    let idx = 0;

    console.log("- Items:");

    const items = invoice.fields["Items"]?.value;
    for (const item of items ?? []) {
        const value = item.value;

        // Each item has several subfields that are nested within the item. We'll
        // map over this list of the subfields and filter out any fields that
        // weren't found. Not all fields will be returned every time, only those
        // that the service identified for the particular document in question.

        const subFields = [
                "Description",
                "Quantity",
                "Unit",
                "UnitPrice",
                "ProductCode",
                "Date",
                "Tax",
                "Amount"
            ]
            .map((fieldName) => value[fieldName])
            .filter((field) => field !== undefined);

        console.log(
            [
                `  - Item #${idx}`,
                // Now we will convert those fields into strings to display
                ...subFields.map((field) => `    - ${fieldToString(field)}`)
            ].join("\n")
        );
    }
}

recognizeInvoices().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

축하합니다! 이 빠른 시작에서는 Form Recognizer JavaScript SDK를 사용하여 다양한 양식을 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 살펴보고서 Form Recognizer v3.0 API에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API v2.1 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Form Recognizer JavaScript 참조 라이브러리](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true)
