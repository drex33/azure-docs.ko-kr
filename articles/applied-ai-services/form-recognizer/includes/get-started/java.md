---
title: '시작: Java v2.1용 Form Recognizer 클라이언트 라이브러리'
description: Java용 Form Recognizer SDK를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e93e5b157cc7bb17eb2e66a97770f4af0d915c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030891"
---
> [!IMPORTANT]
>
> 이 빠른 시작은 Azure Form Recognizer 버전 **2.1** 을 대상으로 합니다.

[참조 설명서](/java/api/overview/azure/ai-formrecognizer-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [패키지(Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

이 빠른 시작에서는 다음 API를 사용하여 양식과 문서에서 구조화된 데이터를 추출합니다.

* [레이아웃](#try-it-layout-model)

* [청구서](#try-it-prebuilt-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
), 버전 8 이상
* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

> [!TIP]
> 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](/azure/active-directory/authentication/overview-authentication)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

* 리소스가 배포되면 **리소스로 이동** 을 클릭합니다. 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 키와 엔드포인트는 이 빠른 시작의 뒷부분에서 코드에 붙여넣습니다.

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="set-up"></a>설정

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 **form-recognizer-app** 이라는 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir form-recognizer-app && form-recognizer-app
```

1. 작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts* 를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

    ```console
    gradle init --type basic
    ```

1. **DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

1. 기본 프로젝트 이름(form-recognizer-app)을 적용합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. 다른 종속성 관리자에 대한 클라이언트 라이브러리 및 정보는 [Maven 중앙 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)에서 찾을 수 있습니다.

프로젝트의 *build.gradle.kts* 파일에서 필요한 플러그 인 및 설정과 함께 클라이언트 라이브러리를 `implementation` 문으로 포함합니다.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.1")
}
```

### <a name="create-a-java-file"></a>Java 파일 만들기

작업 디렉터리에서 다음 명령을 실행합니다.

```console
mkdir -p src/main/java
```

다음과 같은 디렉터리 구조를 만듭니다.

:::image type="content" source="../../media/quickstarts/java-directories.png" alt-text="스크린샷: Java 디렉터리 구조":::

java 디렉터리로 이동하여 *FormRecognizer.java* 라는 파일을 만듭니다.  원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 패키지 선언 및 `import` 문을 추가합니다.

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>애플리케이션의 Main 메서드에 복사하여 붙여넣을 코드 샘플을 선택합니다.

* [**Layout**](#try-it-layout-model)

* [**미리 빌드된 청구서**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](/azure/cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

## <a name="try-it-layout-model"></a>**사용해 보기**: 레이아웃 모델

문서에서 경계 영역 좌표와 함께 텍스트, 선택 표시, 텍스트 스타일 및 테이블 구조를 추출합니다.

> [!div class="checklist"]
>
> * 이 예제에서는 **URI에 있는 양식 문서 파일** 이 필요합니다. 이 빠른 시작에는 [샘플 양식 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)를 사용할 수 있습니다.
> * URI에서 지정된 파일을 분석하려면 `beginRecognizeContentFromUrl` 메서드를 사용합니다.
> * Main 메서드의 `formUrl` 변수에 파일 URI 값을 추가했습니다.

다음 코드를 사용하여 애플리케이션의 **FormRecognizer** 클래스를 업데이트합니다(Azure Portal에서 Form Recognizer 인스턴스의 값으로 키 및 엔드포인트 변수를 업데이트해야 함).

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
                .credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

    System.out.println("Get form content...");
        GetContent(recognizerClient, formUrl);
  }
    private static void GetContent(FormRecognizerClient recognizerClient, String invoiceUri) {
        String analyzeFilePath = invoiceUri;
        SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller = recognizerClient
                .beginRecognizeContentFromUrl(analyzeFilePath);

        List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
        // </snippet_getcontent_call>
        // <snippet_getcontent_print>
        contentResult.forEach(formPage -> {
            // Table information
            System.out.println("----Recognizing content ----");
            System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
                    formPage.getHeight(), formPage.getUnit());
            formPage.getTables().forEach(formTable -> {
                System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                        formTable.getColumnCount());
                formTable.getCells().forEach(formTableCell -> {
                    System.out.printf("Cell has text %s.%n", formTableCell.getText());
                });
                System.out.println();
            });
        });
    }

```

## <a name="try-it-prebuilt-model"></a>**체험해 보기**: 미리 빌드된 모델

이 샘플에서는 청구서를 예로 사용하여 미리 학습된 모델에서 특정 유형의 일반 문서에 있는 데이터를 분석하는 방법을 보여 줍니다.

> [!div class="checklist"]
>
> * 이 예제에서는 미리 빌드된 모델을 사용하여 청구서 문서를 분석합니다. 이 빠른 시작에는 [샘플 청구서 문서](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)를 사용할 수 있습니다.
> * URI에서 지정된 파일을 분석하려면 `beginRecognizeInvoicesFromUrl`을 사용합니다.
> * Main 메서드의 `invoiceUrl` 변수에 파일 URI 값을 추가했습니다.
> * 간단히 하기 위해 서비스가 반환하는 모든 필드가 여기에 표시되지는 않습니다. 지원되는 모든 필드 및 해당 형식의 목록을 보려면 [청구서](../../concept-invoice.md#field-extraction) 개념 페이지를 참조하세요.

### <a name="choose-a-prebuilt-model"></a>미리 빌드된 모델 선택

청구서에 국한되지 않습니다. 몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 자체의 지원 필드 세트가 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델은 다음과 같습니다.

* [**청구서**](../../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 필드 및 키 정보를 추출합니다.
* [**영수증**](../../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](../../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](../../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

다음 코드를 사용하여 애플리케이션의 **FormRecognizer** 클래스를 업데이트합니다(Azure Portal에서 Form Recognizer 인스턴스의 값으로 키 및 엔드포인트 변수를 업데이트해야 함).

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder().credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

    System.out.println("Analyze invoice...");
        AnalyzeInvoice(recognizerClient, invoiceUrl);
  }
    private static void AnalyzeInvoice(FormRecognizerClient recognizerClient, String invoiceUrl) {
      SyncPoller < FormRecognizerOperationResult,
        List < RecognizedForm >> recognizeInvoicesPoller = recognizerClient.beginRecognizeInvoicesFromUrl(invoiceUrl);
      List < RecognizedForm > recognizedInvoices = recognizeInvoicesPoller.getFinalResult();

      for (int i = 0; i < recognizedInvoices.size(); i++) {
        RecognizedForm recognizedInvoice = recognizedInvoices.get(i);
        Map < String,
        FormField > recognizedFields = recognizedInvoice.getFields();
        System.out.printf("----------- Recognized invoice info for page %d -----------%n", i);
        FormField vendorNameField = recognizedFields.get("VendorName");
        if (vendorNameField != null) {
            if (FieldValueType.STRING == vendorNameField.getValue().getValueType()) {
                String merchantName = vendorNameField.getValue().asString();
                System.out.printf("Vendor Name: %s, confidence: %.2f%n", merchantName, vendorNameField.getConfidence());
            }
        }

        FormField vendorAddressField = recognizedFields.get("VendorAddress");
        if (vendorAddressField != null) {
            if (FieldValueType.STRING == vendorAddressField.getValue().getValueType()) {
                String merchantAddress = vendorAddressField.getValue().asString();
                System.out.printf("Vendor address: %s, confidence: %.2f%n", merchantAddress, vendorAddressField.getConfidence());
            }
        }

        FormField customerNameField = recognizedFields.get("CustomerName");
        if (customerNameField != null) {
            if (FieldValueType.STRING == customerNameField.getValue().getValueType()) {
                String merchantAddress = customerNameField.getValue().asString();
                System.out.printf("Customer Name: %s, confidence: %.2f%n", merchantAddress, customerNameField.getConfidence());
            }
        }

        FormField customerAddressRecipientField = recognizedFields.get("CustomerAddressRecipient");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.STRING == customerAddressRecipientField.getValue().getValueType()) {
                String customerAddr = customerAddressRecipientField.getValue().asString();
                System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n", customerAddr, customerAddressRecipientField.getConfidence());
            }
        }

        FormField invoiceIdField = recognizedFields.get("InvoiceId");
        if (invoiceIdField != null) {
            if (FieldValueType.STRING == invoiceIdField.getValue().getValueType()) {
                String invoiceId = invoiceIdField.getValue().asString();
                System.out.printf("Invoice Id: %s, confidence: %.2f%n", invoiceId, invoiceIdField.getConfidence());
            }
        }

        FormField invoiceDateField = recognizedFields.get("InvoiceDate");
        if (customerNameField != null) {
            if (FieldValueType.DATE == invoiceDateField.getValue().getValueType()) {
                LocalDate invoiceDate = invoiceDateField.getValue().asDate();
                System.out.printf("Invoice Date: %s, confidence: %.2f%n", invoiceDate, invoiceDateField.getConfidence());
            }
        }

        FormField invoiceTotalField = recognizedFields.get("InvoiceTotal");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.FLOAT == invoiceTotalField.getValue().getValueType()) {
                Float invoiceTotal = invoiceTotalField.getValue().asFloat();
                System.out.printf("Invoice Total: %.2f, confidence: %.2f%n", invoiceTotal, invoiceTotalField.getConfidence());
            }
        }
    }
}

```

## <a name="build-and-run-your-application"></a>응용 프로그램 빌드 및 실행

기본 프로젝트 디렉터리 **form-recognizer-app** 으로 돌아갑니다.

1. `build` 명령을 사용하여 애플리케이션을 빌드합니다.

```console
gradle build
```

1. `run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

축하합니다! 이 빠른 시작에서는 Form Recognizer Java SDK를 사용하여 다양한 양식과 문서를 다양한 방식으로 분석했습니다. 다음으로, 참조 설명서를 탐색하여 Form Recognizer API에 관해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API v2.1 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Form Recognizer Java 라이브러리 참조](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-stable&preserve-view=true)
