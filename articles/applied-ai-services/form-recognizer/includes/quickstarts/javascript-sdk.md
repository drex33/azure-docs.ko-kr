---
title: '빠른 시작: JavaScript용 Form Recognizer 클라이언트 라이브러리'
description: JavaScript용 Form Recognizer 클라이언트 라이브러리를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/12/2021
ms.author: lajanuar
ms.custom: devx-track-js
ms.openlocfilehash: b1b830ae8e6e7a203bc075ca94f8a87767ba3423
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288279"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> * 이 빠른 시작에서는 SDK 버전 **3.1.1** 을 사용하고 API 버전 **2.1** 을 대상으로 합니다.
>
>* 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 아래의 참조 설명서를 참조하세요.

[참조 설명서](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [패키지(npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [Node.js](https://nodejs.org/)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터 세트를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 세트 빌드](../../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다(*sample_data.zip* 다운로드 및 추출).
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스 만들기"  target="_blank">Form Recognizer 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.
  * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다.

```console
npm init
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`ai-form-recognizer` NPM 패키지를 설치합니다.

```console
npm install @azure/ai-form-recognizer
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

이름이 `index.js`인 파일을 만들어 열고 다음 라이브러리를 가져옵니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Form Recognizer 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다.
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 Cognitive Services [보안](../../../../cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

## <a name="object-model"></a>개체 모델

Form Recognizer를 사용하면 두 가지 다른 클라이언트 유형을 만들 수 있습니다. 첫 번째, `FormRecognizerClient`는 인식된 양식 필드 및 콘텐츠에 대한 서비스를 쿼리하는 데 사용됩니다. 두 번째, `FormTrainingClient`는 인식을 향상하기 위해 사용자 지정 모델을 만들고 관리하는 데 사용됩니다.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient`는 다음에 대한 작업을 제공합니다.

* 사용자 지정 양식을 분석하도록 학습된 사용자 지정 모델을 사용하여 양식 필드 및 콘텐츠를 인식합니다. 이러한 값은 `RecognizedForm` 개체의 컬렉션에서 반환됩니다.
* 모델을 학습하지 않고도 테이블, 줄 및 단어를 비롯한 양식 콘텐츠를 인식합니다. 양식 콘텐츠는 `FormPage` 개체의 컬렉션에서 반환됩니다.
* Form Recognizer 서비스에서 미리 학습된 모델을 사용하여 미국 영수증, 명함, 청구서 및 ID 문서에서 공통 필드를 인식합니다.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient`는 다음에 대한 작업을 제공합니다.

* 사용자 지정 양식에서 발견된 모든 필드와 값을 분석하도록 사용자 지정 모델을 학습시킵니다. 모델이 분석할 양식 유형과 각 양식 유형에 대해 추출할 필드를 나타내는 `CustomFormModel`이 반환됩니다. 자세한 내용은 [레이블이 없는 모델 학습에 대한 서비스의 문서](#train-a-model-without-labels)를 _참조_ 하세요.
* 사용자 지정 양식에 레이블을 지정하여 사용자가 지정한 특정 필드 및 값을 분석하도록 사용자 지정 모델을 학습시킵니다. 모델이 추출하는 필드와 각 필드에 대한 예상 정확도를 나타내는 `CustomFormModel`이 반환됩니다. 학습 데이터 세트에 레이블을 적용하는 방법에 대한 자세한 설명은 [레이블이 지정된 모델 학습에 대한 서비스의 설명서](#train-a-model-with-labels)를 참조하세요.
* 계정에서 생성된 모델을 관리합니다.
* 하나의 Form Recognizer 리소스에서 다른 리소스로 사용자 지정 모델을 복사합니다.

> [!NOTE]
> [Form Recognizer 레이블 지정 도구](../../label-tool.md)와 같은 그래픽 사용자 인터페이스를 사용하여 모델을 학습할 수도 있습니다.

## <a name="authenticate-the-client"></a>클라이언트 인증

정의한 구독 변수를 사용하여 클라이언트 개체를 인증합니다. `AzureKeyCredential` 개체를 사용하면 필요한 경우 새 클라이언트 개체를 만들지 않고 API 키를 업데이트할 수 있습니다. 또한 학습 클라이언트 개체를 만듭니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>테스트용 자산 가져오기

또한 학습 및 테스트 데이터에 대한 참조를 URL에 추가해야 합니다.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL 검색":::

* 아래 샘플에 포함된 샘플 및 영수증 이미지([GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/assets)에서도 사용 가능)를 사용하거나 위의 단계를 사용하여 Blob 스토리지에 있는 개별 문서의 SAS URL을 가져올 수 있습니다.

## <a name="analyze-layout"></a>레이아웃 분석

Form Recognizer를 사용하면 모델을 학습시킬 필요 없이 문서의 표, 줄 및 단어를 분석할 수 있습니다. 레이아웃 추출에 대한 자세한 내용은 [레이아웃 개념 가이드](../../concept-layout.md)를 참조하세요. 지정된 URI에서 파일의 콘텐츠를 분석하려면 `beginRecognizeContentFromUrl` 메서드를 사용합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 메서드(예: **beginRecognizeContent**)를 사용하여 로컬 파일에서 콘텐츠를 가져올 수도 있습니다. 

### <a name="output"></a>출력

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>영수증 분석

이 섹션에서는 사전 학습된 영수증 모델을 사용하여 미국 영수증의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 영수증 분석에 대한 자세한 내용은 [영수증 개념 가이드](../../concept-receipt.md)를 참조하세요.

URI에서 영수증을 분석하려면 `beginRecognizeReceiptsFromUrl` 메서드를 사용합니다. 다음 코드는 지정된 URI에서 영수증을 처리하고 주요 필드와 값을 콘솔에 출력합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 메서드(예: **beginRecognizeReceipts**)를 사용하여 로컬 수신 이미지를 분석할 수도 있습니다. 

### <a name="output"></a>출력

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>명함 분석

이 섹션에서는 사전 학습된 모델을 사용하여 영어 명함에서 공통 필드를 분석하고 추출하는 방법을 보여줍니다. 명함 분석에 대한 자세한 내용은 [명함 개념 가이드](../../concept-business-card.md)를 참조하세요.

URL에서 명함을 분석하려면 `beginRecognizeBusinessCardsFromURL` 메서드를 사용합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_bc)]

> [!TIP]
 > [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 메서드(예: **beginRecognizeBusinessCards**)를 사용하여 로컬 명함 이미지를 분석할 수도 있습니다. 

## <a name="analyze-invoices"></a>송장 분석

이 섹션에서는 사전 학습된 모델을 사용하여 판매 청구서의 공통 필드를 분석하고 추출하는 방법을 보여 줍니다. 청구서 분석에 대한 자세한 내용은 [청구서 개념 가이드](../../concept-invoice.md)를 참조하세요.

URL에서 청구서를 분석하려면 `beginRecognizeInvoicesFromUrl` 메서드를 사용합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_invoice)]

> [!TIP]
> [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 메서드(예: **beginRecognizeInvoices**)를 사용하여 로컬 수신 이미지를 분석할 수도 있습니다. 

## <a name="analyze-id-documents"></a>ID 문서 분석

이 섹션에서는 Form Recognizer에서 미리 빌드된 ID 모델을 사용하여 정부에서 발행한 신분증(예: 전 세계에서의 여권 및 미국 운전 면허증)에서 핵심 정보를 분석하고 추출하는 방법을 보여 줍니다. ID 문서 분석 방법에 대한 자세한 내용은 [미리 빌드된 ID 모델 개념 가이드](../../concept-id-document.md)를 참조하세요.

URL에서 ID 문서를 분석하려면 `beginRecognizeIdDocumentsFromUrl` 메서드를 사용합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_id)]

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

이 섹션에서는 사용자 고유의 데이터로 모델을 학습시키는 방법을 보여 줍니다. 학습된 모델은 원본 양식 문서의 키/값 관계가 포함된 구조적 데이터를 출력할 수 있습니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

> [!NOTE]
> [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)와 같은 GUI(그래픽 사용자 인터페이스)를 사용하여 모델을 학습시킬 수도 있습니다.

### <a name="train-a-model-without-labels"></a>레이블 없이 모델 학습

학습 문서에 수동으로 레이블을 지정하지 않고 사용자 지정 양식에서 발견된 모든 필드와 값을 분석하도록 사용자 지정 모델을 학습시킬 수 있습니다.

다음 함수는 지정된 문서 세트를 모델에 학습시키고 모델의 상태를 콘솔에 출력합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>출력

[JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer)에서 사용할 수 있는 학습 데이터로 학습된 모델의 출력입니다. 가독성을 위해 이 샘플 출력이 잘렸습니다.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>레이블을 사용하여 모델 학습

학습 문서에 레이블을 수동으로 지정하여 사용자 지정 모델을 학습시킬 수도 있습니다. 일부 시나리오에서는 레이블을 사용하여 학습시키면 성능이 향상됩니다. 레이블을 사용하여 학습하려면 학습 문서와 별도로 Blob 스토리지 컨테이너에 특별한 레이블 정보 파일(`\<filename\>.pdf.labels.json`)이 있어야 합니다. [Form Recognizer 샘플 레이블 지정 도구](../../label-tool.md)는 이러한 레이블 파일을 만드는 데 도움이 되는 UI를 제공합니다. 레이블 파일이 있으면 `true`로 설정된 `uselabels` 매개 변수를 사용하여 `beginTraining` 메서드를 호출할 수 있습니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>출력

[JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)에서 사용할 수 있는 학습 데이터로 학습된 모델의 출력입니다. 가독성을 위해 이 샘플 출력이 잘렸습니다.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

이 섹션에서는 사용자 고유의 양식으로 학습시킨 모델을 사용하여 사용자 지정 양식 유형에서 키/값 정보 및 기타 콘텐츠를 추출하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 시나리오를 구현하려면 해당 ID를 아래 메서드에 전달할 수 있도록 모델을 이미 학습시킨 상태여야 합니다. [모델 학습](#train-a-model-without-labels) 섹션을 참조하세요.

`beginRecognizeCustomFormsFromUrl` 메서드를 사용합니다. 그러면 제출된 문서의 각 페이지당 하나씩 `RecognizedForm` 개체 컬렉션이 반환됩니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) 메서드(예: **beginRecognizeCustomForms**)를 사용하여 로컬 파일을 분석할 수도 있습니다. 

### <a name="output"></a>출력

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-custom-models"></a>사용자 지정 모델 관리

이 섹션에서는 계정에 저장된 사용자 지정 모델을 관리하는 방법을 보여 줍니다. 다음 코드는 단일 메서드의 모든 모델 관리 작업을 수행하는 예를 보여 줍니다.

### <a name="get-number-of-models"></a>모델 수 가져오기

다음 코드 블록은 현재 계정에 있는 모델 수를 가져옵니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>계정의 모델 목록 가져오기

다음 코드 블록은 모델 생성 시기 및 현재 상태에 대한 정보를 포함하여 계정에서 사용 가능한 모델의 전체 목록을 제공합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>출력

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>페이지별로 모델 ID 목록 가져오기

이 코드 블록은 페이지가 매겨진 모델과 모델 ID 목록을 제공합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>출력

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>ID별로 모델 가져오기

다음 함수는 모델 ID를 사용하여 일치하는 모델 개체를 가져옵니다. 이 함수는 기본적으로 호출되지 않습니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>리소스 계정에서 모델 삭제

해당 ID를 참조하여 계정에서 모델을 삭제할 수도 있습니다. 이 함수는 지정된 ID로 모델을 삭제합니다. 이 함수는 기본적으로 호출되지 않습니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>출력

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>문제 해결

### <a name="enable-logs"></a>로그 사용 설정

다음 환경 변수를 설정하여 이 라이브러리를 사용할 때 디버그 로그를 볼 수 있습니다.

```console
export DEBUG=azure*
```

로그를 사용하는 방법에 대한 자세한 내용은 [@azure/logger 패키지 문서](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer JavaScript 클라이언트 라이브러리를 사용하여 다양한 방식으로 모델을 학습하고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](../../build-training-data-set.md)

## <a name="see-also"></a>참조

* [Form Recognizer란?](../../overview.md)
* 이 가이드의 샘플 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)에서 찾을 수 있습니다.