---
title: '빠른 시작: Form Recognizer Python SDK v3.0 | 미리 보기'
titleSuffix: Azure Applied AI Services
description: Form Recognizer Python 클라이언트 라이브러리 SDK v3.0(미리 보기)을 사용한 양식/문서 처리, 데이터 추출 및 분석
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: ced6bd08895993c18a305418bd68ae8a0557301f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026679"
---
# <a name="quickstart-python-client-library-sdk-v30--preview"></a>빠른 시작: Python 클라이언트 라이브러리 SDK v3.0 | 미리 보기

>[!NOTE]
> Form Recognizer v3.0은 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 

[참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/latest/azure.ai.formrecognizer.html) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [패키지(PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/samples)

 Python 프로그래밍 언어를 사용하여 Azure Form Recognizer를 시작합니다. Azure Form Recognizer는 기계 학습을 사용하여 문서에서 양식 필드, 텍스트 및 테이블을 추출하고 분석하는 클라우드 기반 Azure Applied AI Service입니다. 클라이언트 라이브러리 SDK를 워크플로 및 애플리케이션에 통합하여 Form Recognizer 모델을 쉽게 호출할 수 있습니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

Form Recognizer 기능 및 개발 옵션에 대한 자세한 내용은 [개요](../overview.md#form-recognizer-features-and-development-options) 페이지를 참조하세요.

이 빠른 시작에서는 다음 기능을 사용하여 양식 및 문서에서 데이터와 값을 분석하고 추출합니다.

* [🆕 **일반 문서**](#try-it-general-document-model) - 텍스트, 테이블, 구조, 키-값 쌍 및 명명된 엔터티를 분석하고 추출합니다.

* [**레이아웃**](#try-it-layout-model) - 모델을 학습시킬 필요 없이 테이블, 선, 단어, 선택 표시(양식 문서의 원형 선택 단추 및 확인란)를 분석하고 추출합니다.

* [**미리 빌드된 청구서**](#try-it-prebuilt-model) - 미리 학습된 모델을 사용하여 청구서에서 자주 사용되는 필드를 분석하고 추출합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * Python 설치에 [pip](https://pip.pypa.io/en/stable/)가 포함되어야 합니다. 명령줄에서 `pip --version`을 실행하여 pip가 설치되어 있는지 확인할 수 있습니다. 최신 버전의 Python을 설치하여 pip를 받으세요.

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

> [!TIP]
> 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](../../../active-directory/authentication/overview-authentication.md)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스를 배포한 후 **리소스로 이동** 을 선택합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 빠른 시작 뒷부분에서 코드에 키와 엔드포인트를 붙여넣게 됩니다.

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="set-up"></a>설정

로컬 환경에서 터미널 창을 열고, pip를 사용하여, Python용 Azure Form Recognizer 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-ai-formrecognizer --pre

```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

기본 설정 편집기 또는 IDE에서 **form_recognizer_quickstart.py** 라는 새 Python 애플리케이션을 만듭니다. 그런 다음, 다음 라이브러리를 가져옵니다.

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential
```

### <a name="create-variables-for-your-azure-resource-endpoint-and-key"></a>Azure 리소스 엔드포인트 및 키에 대한 변수 만들기

```python
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

이 시점에서 Python 애플리케이션에는 다음 코드 줄이 포함되어야 합니다.

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"

```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>복사하여 애플리케이션에 붙여넣을 코드 샘플을 선택합니다.

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
> * URI에서 지정된 파일을 분석하기 위해 `begin_analyze_document` 메서드를 사용하고 모델 ID로 `prebuilt-document`를 전달합니다. 반환된 값은 제출된 문서에 대한 데이터가 포함된 `result` 개체입니다.
> * 파일 URI 값을 파일 위쪽 근처에 있는 `formUrl` 변수에 추가했습니다.
> * 간단히 하기 위해 서비스에서 반환하는 모든 엔터티 필드가 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [일반 문서](../concept-general-document.md#named-entity-recognition-ner-categories) 개념 페이지를 참조하세요.

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-key-variable"></a>다음 코드를 일반 문서 애플리케이션의 `key` 변수 아래의 줄에 추가

```python

def format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join("Page #{}: {}".format(region.page_number, format_bounding_box(region.bounding_box)) for region in bounding_regions)

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_general_documents():

    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-document", formUrl)
    result = poller.result()

    for style in result.styles:
        if style.is_handwritten:
            print("Document contains handwritten content: ")
            print(",".join([result.content[span.offset:span.offset + span.length] for span in style.spans]))

    print("----Key-value pairs found in document----")
    for kv_pair in result.key_value_pairs:
        if kv_pair.key:
            print(
                    "Key '{}' found within '{}' bounding regions".format(
                        kv_pair.key.content,
                        format_bounding_region(kv_pair.key.bounding_regions),
                    )
                )
        if kv_pair.value:
            print(
                    "Value '{}' found within '{}' bounding regions\n".format(
                        kv_pair.value.content,
                        format_bounding_region(kv_pair.value.bounding_regions),
                    )
                )

    print("----Entities found in document----")
    for entity in result.entities:
        print("Entity of category '{}' with sub-category '{}'".format(entity.category, entity.sub_category))
        print("...has content '{}'".format(entity.content))
        print("...within '{}' bounding regions".format(format_bounding_region(entity.bounding_regions)))
        print("...with confidence {}\n".format(entity.confidence))

    for page in result.pages:
        print("----Analyzing document from page #{}----".format(page.page_number))
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                page.width, page.height, page.unit
            )
        )

        for line_idx, line in enumerate(page.lines):
            print(
                "...Line # {} has text content '{}' within bounding box '{}'".format(
                    line_idx,
                    line.content,
                    format_bounding_box(line.bounding_box),
                )
            )

        for word in page.words:
            print(
                "...Word '{}' has a confidence of {}".format(
                    word.content, word.confidence
                )
            )

        for selection_mark in page.selection_marks:
            print(
                "...Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )

    for table_idx, table in enumerate(result.tables):
        print(
            "Table # {} has {} rows and {} columns".format(
                table_idx, table.row_count, table.column_count
            )
        )
        for region in table.bounding_regions:
            print(
                "Table # {} location on page: {} is {}".format(
                    table_idx,
                    region.page_number,
                    format_bounding_box(region.bounding_box),
                )
            )
        for cell in table.cells:
            print(
                "...Cell[{}][{}] has content '{}'".format(
                    cell.row_index,
                    cell.column_index,
                    cell.content,
                )
            )
            for region in cell.bounding_regions:
                print(
                    "...content on page {} is within bounding box '{}'\n".format(
                        region.page_number,
                        format_bounding_box(region.bounding_box),
                    )
                )
    print("----------------------------------------")


if __name__ == "__main__":
    analyze_general_documents()
```

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 파일 위쪽 근처에 있는 `formUrl` 변수에 추가했습니다.
> * URI에서 지정된 파일을 분석하기 위해 `begin_analyze_document` 메서드를 사용하고 모델 ID로 `prebuilt-layout`을 전달합니다. 반환된 값은 제출된 문서에 대한 데이터가 포함된 `result` 개체입니다.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>다음 코드를 레이아웃 애플리케이션의 `key` 변수 아래의 줄에 추가

```python

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_layout():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-layout", formUrl)
    result = poller.result()

    for idx, style in enumerate(result.styles):
        print(
            "Document contains {} content".format(
                "handwritten" if style.is_handwritten else "no handwritten"
            )
        )

```

## <a name="try-it-prebuilt-model"></a>**사용해 보기**: 미리 빌드된 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정한 일반 문서 유형에 있는 데이터를 분석하는 방법을 보여 줍니다.

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * 파일 URI 값을 파일 위쪽의 `string fileUri` 변수에 추가했습니다.
> * URI에서 지정된 파일을 분석하기 위해 `begin_analyze_document` 메서드를 사용하고 모델 ID로 `prebuilt-invoice`를 전달합니다. 반환된 값은 제출된 문서에 대한 데이터가 포함된 `result` 개체입니다.
> * 간단히 하기 위해 서비스에서 반환하는 모든 키-값 쌍이 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [청구서](../concept-invoice.md#field-extraction) 개념 페이지를 참조하세요.

### <a name="choose-the-invoice-prebuilt-model-id"></a>청구서가 미리 빌드된 모델 ID 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. 현재 Form Recognizer 서비스에서 지원하는 미리 빌드된 모델에 대한 모델 ID는 다음과 같습니다.

* [**prebuilt-invoice**](../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 키-값 쌍 및 키 정보를 추출합니다.
* [**prebuilt-receipt**](../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**prebuilt-idDocument**](../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**prebuilt-businessCard**](../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-key-variable"></a>다음 코드를 미리 빌드된 청구서 애플리케이션의 `key` 변수 아래에 추가

```python

def format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join("Page #{}: {}".format(region.page_number, format_bounding_box(region.bounding_box)) for region in bounding_regions)

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_invoice():

    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-document", formUrl)
    invoices = poller.result()

    for idx, invoice in enumerate(invoices.documents):
        print("--------Recognizing invoice #{}--------".format(idx + 1))
        vendor_name = invoice.fields.get("VendorName")
        if vendor_name:
            print(
                "Vendor Name: {} has confidence: {}".format(
                    vendor_name.value, vendor_name.confidence
                )
            )
        vendor_address = invoice.fields.get("VendorAddress")
        if vendor_address:
            print(
                "Vendor Address: {} has confidence: {}".format(
                    vendor_address.value, vendor_address.confidence
                )
            )
        vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
        if vendor_address_recipient:
            print(
                "Vendor Address Recipient: {} has confidence: {}".format(
                    vendor_address_recipient.value, vendor_address_recipient.confidence
                )
            )
        customer_name = invoice.fields.get("CustomerName")
        if customer_name:
            print(
                "Customer Name: {} has confidence: {}".format(
                    customer_name.value, customer_name.confidence
                )
            )
        customer_id = invoice.fields.get("CustomerId")
        if customer_id:
            print(
                "Customer Id: {} has confidence: {}".format(
                    customer_id.value, customer_id.confidence
                )
            )
        customer_address = invoice.fields.get("CustomerAddress")
        if customer_address:
            print(
                "Customer Address: {} has confidence: {}".format(
                    customer_address.value, customer_address.confidence
                )
            )
        customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
        if customer_address_recipient:
            print(
                "Customer Address Recipient: {} has confidence: {}".format(
                    customer_address_recipient.value,
                    customer_address_recipient.confidence,
                )
            )
        invoice_id = invoice.fields.get("InvoiceId")
        if invoice_id:
            print(
                "Invoice Id: {} has confidence: {}".format(
                    invoice_id.value, invoice_id.confidence
                )
            )
        invoice_date = invoice.fields.get("InvoiceDate")
        if invoice_date:
            print(
                "Invoice Date: {} has confidence: {}".format(
                    invoice_date.value, invoice_date.confidence
                )
            )
        invoice_total = invoice.fields.get("InvoiceTotal")
        if invoice_total:
            print(
                "Invoice Total: {} has confidence: {}".format(
                    invoice_total.value, invoice_total.confidence
                )
            )
        due_date = invoice.fields.get("DueDate")
        if due_date:
            print(
                "Due Date: {} has confidence: {}".format(
                    due_date.value, due_date.confidence
                )
            )
        purchase_order = invoice.fields.get("PurchaseOrder")
        if purchase_order:
            print(
                "Purchase Order: {} has confidence: {}".format(
                    purchase_order.value, purchase_order.confidence
                )
            )
        billing_address = invoice.fields.get("BillingAddress")
        if billing_address:
            print(
                "Billing Address: {} has confidence: {}".format(
                    billing_address.value, billing_address.confidence
                )
            )
        billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
        if billing_address_recipient:
            print(
                "Billing Address Recipient: {} has confidence: {}".format(
                    billing_address_recipient.value,
                    billing_address_recipient.confidence,
                )
            )
        shipping_address = invoice.fields.get("ShippingAddress")
        if shipping_address:
            print(
                "Shipping Address: {} has confidence: {}".format(
                    shipping_address.value, shipping_address.confidence
                )
            )
        shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
        if shipping_address_recipient:
            print(
                "Shipping Address Recipient: {} has confidence: {}".format(
                    shipping_address_recipient.value,
                    shipping_address_recipient.confidence,
                )
            )
        print("Invoice items:")
        for idx, item in enumerate(invoice.fields.get("Items").value):
            print("...Item #{}".format(idx + 1))
            item_description = item.value.get("Description")
            if item_description:
                print(
                    "......Description: {} has confidence: {}".format(
                        item_description.value, item_description.confidence
                    )
                )
            item_quantity = item.value.get("Quantity")
            if item_quantity:
                print(
                    "......Quantity: {} has confidence: {}".format(
                        item_quantity.value, item_quantity.confidence
                    )
                )
            unit = item.value.get("Unit")
            if unit:
                print(
                    "......Unit: {} has confidence: {}".format(
                        unit.value, unit.confidence
                    )
                )
            unit_price = item.value.get("UnitPrice")
            if unit_price:
                print(
                    "......Unit Price: {} has confidence: {}".format(
                        unit_price.value, unit_price.confidence
                    )
                )
            product_code = item.value.get("ProductCode")
            if product_code:
                print(
                    "......Product Code: {} has confidence: {}".format(
                        product_code.value, product_code.confidence
                    )
                )
            item_date = item.value.get("Date")
            if item_date:
                print(
                    "......Date: {} has confidence: {}".format(
                        item_date.value, item_date.confidence
                    )
                )
            tax = item.value.get("Tax")
            if tax:
                print(
                    "......Tax: {} has confidence: {}".format(tax.value, tax.confidence)
                )
            amount = item.value.get("Amount")
            if amount:
                print(
                    "......Amount: {} has confidence: {}".format(
                        amount.value, amount.confidence
                    )
                )
        subtotal = invoice.fields.get("SubTotal")
        if subtotal:
            print(
                "Subtotal: {} has confidence: {}".format(
                    subtotal.value, subtotal.confidence
                )
            )
        total_tax = invoice.fields.get("TotalTax")
        if total_tax:
            print(
                "Total Tax: {} has confidence: {}".format(
                    total_tax.value, total_tax.confidence
                )
            )
        previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
        if previous_unpaid_balance:
            print(
                "Previous Unpaid Balance: {} has confidence: {}".format(
                    previous_unpaid_balance.value, previous_unpaid_balance.confidence
                )
            )
        amount_due = invoice.fields.get("AmountDue")
        if amount_due:
            print(
                "Amount Due: {} has confidence: {}".format(
                    amount_due.value, amount_due.confidence
                )
            )
        service_start_date = invoice.fields.get("ServiceStartDate")
        if service_start_date:
            print(
                "Service Start Date: {} has confidence: {}".format(
                    service_start_date.value, service_start_date.confidence
                )
            )
        service_end_date = invoice.fields.get("ServiceEndDate")
        if service_end_date:
            print(
                "Service End Date: {} has confidence: {}".format(
                    service_end_date.value, service_end_date.confidence
                )
            )
        service_address = invoice.fields.get("ServiceAddress")
        if service_address:
            print(
                "Service Address: {} has confidence: {}".format(
                    service_address.value, service_address.confidence
                )
            )
        service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
        if service_address_recipient:
            print(
                "Service Address Recipient: {} has confidence: {}".format(
                    service_address_recipient.value,
                    service_address_recipient.confidence,
                )
            )
        remittance_address = invoice.fields.get("RemittanceAddress")
        if remittance_address:
            print(
                "Remittance Address: {} has confidence: {}".format(
                    remittance_address.value, remittance_address.confidence
                )
            )
        remittance_address_recipient = invoice.fields.get("RemittanceAddressRecipient")
        if remittance_address_recipient:
            print(
                "Remittance Address Recipient: {} has confidence: {}".format(
                    remittance_address_recipient.value,
                    remittance_address_recipient.confidence,
                )
            )

if __name__ == "__main__":
    analyze_invoice()
```

## <a name="run-your-application"></a>애플리케이션 실행

1. **form_recognizer_quickstart.py** 파일이 있는 폴더로 이동합니다.

1. 터미널에 다음 명령을 입력합니다.

```console
python form_recognizer_quickstart.py
```

축하합니다! 이 빠른 시작에서는 Form Recognizer Python SDK를 사용하여 다양한 양식을 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 살펴보고서 Form Recognizer v3.0 API에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API v3.0 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Form Recognizer Python 참조 라이브러리](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/3.2.0b1/index.html)
