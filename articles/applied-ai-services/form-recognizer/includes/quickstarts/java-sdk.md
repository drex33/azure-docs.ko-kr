---
title: '빠른 시작: Java용 Form Recognizer 클라이언트 라이브러리'
description: Form Recognizer Java 클라이언트 라이브러리를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/25/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: 4554725a028bc3b70e14b8eba00a636965694819
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652555"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> * 이 빠른 시작에서는 SDK 버전 **3.1.1** 을 사용하고 API 버전 **2.1** 을 대상으로 합니다.
>
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다.

[참조 설명서](/java/api/overview/azure/ai-formrecognizer-readme) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [패키지(Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
  * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다(*sample_data.zip* 다운로드 및 추출).

## <a name="setting-up"></a>설치

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts* 를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

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

새 폴더로 이동하여 *FormRecognizer.java* 라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> 이 빠른 시작의 코드 샘플이 포함된 전체 파일을 보려면 [**GitHub**](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java)에서 찾을 수 있습니다.

애플리케이션의 **FormRecognizer** 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Form Recognizer 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다.
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](../../../../cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

애플리케이션의 **main** 메서드에서 이 빠른 시작에서 사용되는 메서드에 대한 호출을 추가합니다. 나중에 이를 정의합니다. 또한 학습 및 테스트 데이터에 대한 참조를 URL에 추가해야 합니다.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 검색":::
* 테스트할 양식의 URL을 가져오려면 위의 단계를 따라 Blob Storage에 있는 개별 문서의 SAS URL을 가져오면 됩니다. 또는 다른 위치에 있는 문서의 URL을 가져옵니다.
* 위의 방법을 사용하여 영수증 이미지의 URL도 가져올 수 있습니다.
<!-- markdownlint-disable MD024 -->

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

## <a name="object-model"></a>개체 모델

Form Recognizer를 사용하면 두 가지 다른 클라이언트 유형을 만들 수 있습니다. 첫 번째, `FormRecognizerClient`는 인식된 양식 필드 및 콘텐츠에 대한 서비스를 쿼리하는 데 사용됩니다. 두 번째, `FormTrainingClient`는 인식을 향상하기 위해 사용자 지정 모델을 만들고 관리하는 데 사용됩니다.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient`는 다음에 대한 작업을 제공합니다.

* 사용자 지정 양식을 분석하도록 학습된 사용자 지정 모델을 사용하여 양식 필드 및 콘텐츠를 인식합니다.  이러한 값은 `RecognizedForm` 개체의 컬렉션에서 반환됩니다. 예제 [사용자 지정 양식 분석](#analyze-forms-with-a-custom-model)을 참조하세요.
* 모델을 학습하지 않고도 테이블, 줄 및 단어를 비롯한 양식 콘텐츠를 인식합니다.  양식 콘텐츠는 `FormPage` 개체의 컬렉션에서 반환됩니다. 예제 [레이아웃 분석](#analyze-layout)을 참조하세요.
* Form Recognizer 서비스에서 미리 학습된 모델을 사용하여 미국 영수증, 명함, 청구서 및 ID 문서에서 공통 필드를 인식합니다.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient`는 다음에 대한 작업을 제공합니다.

* 사용자 지정 양식에서 발견된 모든 필드와 값을 분석하도록 사용자 지정 모델을 학습시킵니다.  모델이 분석할 양식 유형과 각 양식 유형에 대해 추출할 필드를 나타내는 `CustomFormModel`이 반환됩니다.
* 사용자 지정 양식에 레이블을 지정하여 사용자가 지정한 특정 필드 및 값을 분석하도록 사용자 지정 모델을 학습시킵니다.  모델이 추출하는 필드와 각 필드에 대한 예상 정확도를 나타내는 `CustomFormModel`이 반환됩니다.
* 계정에서 생성된 모델을 관리합니다.
* 하나의 Form Recognizer 리소스에서 다른 리소스로 사용자 지정 모델을 복사합니다.

> [!NOTE]
> [Form Recognizer 레이블 지정 도구](../../label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습할 수도 있습니다.

## <a name="authenticate-the-client"></a>클라이언트 인증

**main** 메서드의 맨 위에 다음 코드를 추가합니다. 여기서는 위에서 정의한 구독 변수를 사용하여 클라이언트 개체 두 개를 인증합니다. **AzureKeyCredential** 개체를 사용하면 필요한 경우 새 클라이언트 개체를 만들지 않고 API 키를 업데이트할 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>레이아웃 분석

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 줄 및 단어를 분석할 수 있습니다. 레이아웃 추출에 대한 자세한 내용은 [레이아웃 개념 가이드](../../concept-layout.md)를 참조하세요.

지정된 URL에서 파일의 콘텐츠를 분석하려면 **beginRecognizeContentFromUrl** 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> 로컬 파일에서 콘텐츠를 가져올 수도 있습니다. [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 메서드(예: **beginRecognizeContent**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)의 샘플 코드를 참조하세요.

그러면 제출된 문서의 각 페이지당 하나씩 **FormPage** 개체 컬렉션이 반환됩니다. 다음 코드는 이러한 개체에서 반복되고 추출된 키/값 쌍 및 테이블 데이터를 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]

### <a name="output"></a>출력

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-receipts"></a>영수증 분석

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 영수증 분석에 대한 자세한 내용은 [영수증 개념 가이드](../../concept-receipts.md)를 참조하세요.

URI를 통해 영수증을 분석하려면 **beginRecognizeReceiptsFromUrl** 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> 로컬 영수증 이미지를 분석할 수도 있습니다. [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 메서드(예: **beginRecognizeReceipts**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)의 샘플 코드를 참조하세요.

그러면 제출된 문서의 각 페이지당 하나씩 **RecognizedReceipt** 개체 컬렉션이 반환됩니다. 다음 코드 블록은 영수증에서 반복되고 콘솔에 세부 정보를 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

다음 코드 블록은 영수증에서 검색된 개별 항목에서 반복되고 콘솔에 세부 정보를 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>출력

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>명함 분석

이 섹션에서는 사전 학습된 모델을 사용하여 영문 명함의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 명함 분석에 대한 자세한 내용은 [명함 개념 가이드](../../concept-business-cards.md)를 참조하세요.

URL에서 명함을 분석하려면 `beginRecognizeBusinessCardsFromUrl` 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> 또한 로컬 명함 이미지를 분석할 수도 있습니다. [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 메서드(예: **beginRecognizeBusinessCards**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)의 샘플 코드를 참조하세요.

반환된 값은 문서의 각 명함에 하나씩 있는 **RecognizedForm** 개체의 컬렉션입니다. 다음 코드는 지정된 URI에서 명함을 처리하고 주요 필드와 값을 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

## <a name="analyze-invoices"></a>송장 분석

이 섹션에서는 사전 학습된 모델을 사용하여 판매 청구서의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 청구서 분석에 대한 자세한 내용은 [청구서 개념 가이드](../../concept-invoices.md)를 참조하세요.

URL에서 청구서를 분석하려면 `beginRecognizeInvoicesFromUrl` 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> 로컬 청구서를 분석할 수도 있습니다. [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 메서드(예:**beginRecognizeInvoices**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)의 샘플 코드를 참조하세요.

반환된 값은 문서의 각 송장에 하나씩 있는 **RecognizedForm** 개체의 컬렉션입니다. 다음 코드는 지정된 URI에서 송장을 처리하고 주요 필드와 값을 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

## <a name="analyze-identity-documents"></a>ID 문서 분석

이 섹션에서는 Form Recognizer에서 미리 빌드된 ID 모델을 사용하여 정부에서 발행한 신분증(예: 전 세계에서의 여권 및 미국 운전 면허증)에서 핵심 정보를 분석하고 추출하는 방법을 보여 줍니다. ID 문서 분석 방법에 대한 자세한 내용은 [미리 빌드된 ID 모델 개념 가이드](../../concept-identification-cards.md)를 참조하세요.

URI에서 ID 문서를 분석하려면 `beginRecognizeIdentityDocumentsFromUrl` 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_id_call)]

> [!TIP]
> 로컬 ID 문서 이미지도 분석할 수 있습니다. [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) 메서드(예:**beginRecognizeIdentityDocuments**)를 참조하세요. 또한 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)의 샘플 코드를 참조하세요.

다음 코드는 지정된 URI에서 ID 문서를 처리하고 주요 필드와 값을 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_id_print)]

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

이 섹션에서는 사용자 고유의 데이터로 모델을 학습시키는 방법을 보여 줍니다. 학습된 모델은 원본 양식 문서의 키/값 관계가 포함된 구조적 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

학습 문서에 수동으로 레이블을 지정하지 않고 사용자 지정 양식에서 발견된 모든 필드와 값을 분석하도록 사용자 지정 모델을 학습시킬 수 있습니다.

다음 메서드는 지정된 문서 세트를 모델에 학습시키고 모델의 상태를 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

반환되는 **CustomFormModel** 개체는 모델에서 분석할 수 있는 양식 유형과 각 양식 유형에서 추출할 수 있는 필드에 대한 정보를 포함합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

마지막으로, 이 메서드는 모델의 고유 ID를 반환합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]

### <a name="output"></a>출력

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

학습 문서에 레이블을 수동으로 지정하여 사용자 지정 모델을 학습시킬 수도 있습니다. 일부 시나리오에서는 레이블을 사용하여 학습시키면 성능이 향상됩니다. 레이블을 사용하여 학습시키려면 학습 문서와 별도로 Blob Storage 컨테이너에 특별한 레이블 정보 파일( *\<filename\>.pdf.labels.json*)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 레이블 파일이 있으면 `true`로 설정된 *useTrainingLabels* 매개 변수를 사용하여 **beginTraining** 메서드를 호출할 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]

반환된 **CustomFormModel** 은 각 필드의 예상 정확도와 함께 모델이 추출할 수 있는 필드를 표시합니다. 다음 코드 블록은 이 정보를 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]

### <a name="output"></a>출력

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

이 섹션에서는 사용자 고유의 양식으로 학습시킨 모델을 사용하여 사용자 지정 양식 유형에서 키/값 정보 및 기타 콘텐츠를 추출하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 시나리오를 구현하려면 해당 ID를 아래 메서드에 전달할 수 있도록 모델을 이미 학습시킨 상태여야 합니다. [모델 학습](#train-a-model-without-labels) 섹션을 참조하세요.

**beginRecognizeCustomFormsFromUrl** 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> 로컬 파일을 분석할 수도 있습니다. [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) 메서드(예: **beginRecognizeCustomForms**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)의 샘플 코드를 참조하세요.

반환된 값은 제출된 문서의 각 페이지마다 하나씩 **RecognizedForm** 개체 컬렉션이 됩니다. 다음 코드는 분석 결과를 콘솔에 출력합니다. 인식된 각 필드와 해당 값을 신뢰도 점수와 함께 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]

### <a name="output"></a>출력

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```

## <a name="manage-custom-models"></a>사용자 지정 모델 관리

이 섹션에서는 계정에 저장된 사용자 지정 모델을 관리하는 방법을 보여 줍니다. 다음 코드는 단일 메서드의 모든 모델 관리 작업을 수행하는 예를 보여 줍니다. 우선 아래의 메서드 시그니처를 복사하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer 리소스 계정의 모델 수 확인

다음 코드 블록은 Form Recognizer 계정에 저장된 모델 수를 확인하고 이를 계정 제한과 비교합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]

#### <a name="output"></a>출력

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>리소스 계정에 현재 저장된 모델 나열

다음 코드 블록은 계정의 현재 모델을 나열하고 해당 세부 정보를 콘솔에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]

#### <a name="output"></a>출력

이 응답은 가독성을 위해 잘렸습니다.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>애플리케이션 실행

주 프로젝트 디렉터리로 다시 이동합니다. 그런 후, 다음 명령을 사용하여 앱을 빌드합니다.

```console
gradle build
```

`run` 목표를 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>문제 해결

From Recognizer 클라이언트에서 `ErrorResponseException` 예외가 발생합니다. 예를 들어 잘못된 파일 원본 URL을 제공하려고 하면 오류가 발생한 원인을 나타내는 오류 메시지와 함께 `ErrorResponseException`이 발생합니다. 다음 코드 조각에서 오류는 예외를 catch하고 오류에 대한 추가 정보를 표시하여 적절히 처리됩니다.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>클라이언트 로깅 사용

Java용 Azure SDK는 애플리케이션 오류를 빠르게 해결하는 데 도움이 되는 일관된 로깅 사례를 제공합니다. 생성된 로그는 터미널 상태에 도달하기 전에 애플리케이션의 흐름을 캡처하여 근본 문제를 찾는 데 도움이 됩니다. 로깅 사용에 대한 지침은 [로깅 wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer Java 클라이언트 라이브러리를 사용하여 다양한 방식으로 모델을 학습시키고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](../../build-training-data-set.md)

* [Form Recognizer란?](../../overview.md)
* [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples)에서 이 가이드의 샘플 코드 등을 확인할 수 있습니다.
