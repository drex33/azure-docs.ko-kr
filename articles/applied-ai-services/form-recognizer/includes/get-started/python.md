---
title: '시작: Python v2.1용 Form Recognizer 클라이언트 라이브러리'
titleSuffix: Azure Applied AI Services
description: Form Recognizer Python 클라이언트 라이브러리 SDK v2.1을 사용한 양식 및 문서 처리, 데이터 추출, 분석
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: includes
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f323d19c1941837c92a66f18eaf8be88260f058e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030884"
---
> [!IMPORTANT]
>
> 이 빠른 시작은 Azure Form Recognizer REST API **v2.1** 을 대상으로 합니다.
>

[참조 설명서](/python/api/azure-ai-formrecognizer) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [패키지(PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

이 빠른 시작에서는 다음 API를 사용하여 양식과 문서에서 구조화된 데이터를 추출합니다.

* [레이아웃](#try-it-layout-model)

* [청구서](#try-it-prebuilt-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * Python 설치에 [pip](https://pip.pypa.io/en/stable/)가 포함되어야 합니다. 명령줄에서 `pip --version`을 실행하여 pip가 설치되어 있는지 확인할 수 있습니다. 최신 버전의 Python을 설치하여 pip를 받으세요.

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

    > [!TIP]
    > 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](/azure/active-directory/authentication/overview-authentication)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스를 배포한 후 **리소스로 이동** 을 선택합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="set-up"></a>설정

로컬 환경에서 터미널 창을 열고, pip를 사용하여, Python용 Azure Form Recognizer 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-ai-formrecognizer

```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

기본 설정 편집기 또는 IDE에서 **form_recognizer_quickstart.py** 라는 새 Python 애플리케이션을 만듭니다. 그런 다음, 다음 라이브러리를 가져옵니다.

```python
import os
from azure.ai.formrecognizer import FormRecognizerClient
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
> * URI에서 지정된 파일을 분석하려면 `begin_recognize_content_from_url` 메서드를 사용합니다.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>다음 코드를 레이아웃 애플리케이션의 `key` 변수 아래의 줄에 추가

```python

  def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])

 def recognize_content():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    form_recognizer_client = FormRecognizerClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
    form_pages = poller.result()

    for idx, content in enumerate(form_pages):
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                content.width, content.height, content.unit
            )
        )
        for table_idx, table in enumerate(content.tables):
            print(
                "Table # {} has {} rows and {} columns".format(
                    table_idx, table.row_count, table.column_count
                )
            )
            print(
                "Table # {} location on page: {}".format(
                    table_idx, format_bounding_box(table.bounding_box)
                )
            )
            for cell in table.cells:
                print(
                    "...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                        cell.row_index,
                        cell.column_index,
                        cell.text,
                        format_bounding_box(cell.bounding_box),
                    )
                )

        for line_idx, line in enumerate(content.lines):
            print(
                "Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
                    line_idx,
                    len(line.words),
                    line.text,
                    format_bounding_box(line.bounding_box),
                )
            )
            if line.appearance:
                if (
                    line.appearance.style_name == "handwriting"
                    and line.appearance.style_confidence > 0.8
                ):
                    print(
                        "Text line '{}' is handwritten and might be a signature.".format(
                            line.text
                        )
                    )
            for word in line.words:
                print(
                    "...Word '{}' has a confidence of {}".format(
                        word.text, word.confidence
                    )
                )

        for selection_mark in content.selection_marks:
            print(
                "Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )
        print("----------------------------------------")


if __name__ == "__main__":
    recognize_content()
```

## <a name="try-it-prebuilt-model"></a>**체험해 보기**: 미리 빌드된 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정 유형의 일반 문서에 있는 데이터를 분석하는 방법을 보여 줍니다. [**청구서 필드**](../../concept-invoice.md#field-extraction)의 전체 목록은 미리 빌드된 개념 페이지를 *참조하세요*.

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * 파일 상단에 있는 ``formUrl` 변수에 파일 URI 값을 추가했습니다.
> * URI에서 지정된 파일을 분석하려면 ``begin_recognize_invoices_from_url` 메서드를 사용합니다.
> * 간단히 하기 위해 서비스가 반환하는 모든 필드가 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [청구서](../../concept-invoice.md#field-extraction) 개념 페이지를 참조하세요.

### <a name="choose-a-prebuilt-model"></a>미리 빌드된 모델 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델은 다음과 같습니다.

* [**청구서**](../../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 필드 및 키 정보를 추출합니다.
* [**영수증**](../../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](../../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](../../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-key-variable"></a>다음 코드를 미리 빌드된 청구서 애플리케이션의 `key` 변수 아래에 추가

```python

def recognize_invoice():

    invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    form_recognizer_client = FormRecognizerClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = form_recognizer_client.begin_recognize_invoices_from_url(
        invoiceUrl, locale="en-US"
    )
    invoices = poller.result()

    for idx, invoice in enumerate(invoices):
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
    recognize_invoice()

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
> [REST API v2.1 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Form Recognizer Python 참조 라이브러리](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python&preserve-view=true)
