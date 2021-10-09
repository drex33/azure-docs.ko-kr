---
title: Form Recognizer의 새로운 기능
titleSuffix: Azure Applied AI Services
description: Form Recognizer API에 대한 최신 변경 내용을 이해합니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: 401c9d08efd6be33d4444d34ba7a2a6c89723a6b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715085"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-azure-form-recognizer"></a>Azure Form Recognizer의 새로운 기능

Form Recognizer 서비스는 지속적으로 업데이트됩니다. 릴리스 정보, 향상된 기능 및 설명서 업데이트를 최신 상태로 유지하려면 이 페이지에 책갈피를 설정합니다.

## <a name="october-2021"></a>2021년 10월

### <a name="form-recognizer-new-preview-release"></a>폼 인식기 새 미리 보기 릴리스

 양식 인식기 새로운 미리 보기 릴리스에는 다음과 같은 몇 가지 새로운 기능 및 기능이 도입 되었습니다.

* [**일반 문서**](concept-general-document.md) 모델은 미리 학습 된 모델을 사용 하 여 폼과 문서에서 텍스트, 테이블, 구조, 키-값 쌍 및 명명 된 엔터티를 추출 하는 새로운 API입니다.
* [**호텔 수령**](concept-receipt.md) 모델이 미리 작성 된 수신 처리에 추가 되었습니다.
* [**Id 문서의 확장 된 필드**](concept-id-document.md) id 모델은 미국 드라이버 라이선스에서 인증, 제한 및 차량 분류 추출을 지원 합니다.
* [**서명 필드**](concept-custom.md) 는 양식 필드에서 서명의 존재를 검색 하기 위한 사용자 지정 양식의 새 필드 형식입니다.

* [**언어 확장**](language-support.md) 122 언어 (인쇄) 및 7 개 언어 (필기)를 지원 합니다. 양식 인식기 레이아웃 및 사용자 지정 양식은 최신 미리 보기를 사용 하 여 [지원 되는 언어](language-support.md) 를 122으로 확장 합니다. 여기에는 러시아어, 불가리아어, 기타 키릴 자모 및 기타 라틴어를 비롯 한 49 새로운 언어의 인쇄 텍스트에 대 한 텍스트 추출이 포함 됩니다. 또한 필기 텍스트를 추출 하면 영어를 포함 하는 7 가지 언어 및 중국어 간체, 프랑스어, 독일어, 이탈리아어, 포르투갈어 및 스페인어 미리 보기가 지원 됩니다.

* **테이블 및 텍스트 추출 기능 향상** 이제 레이아웃에서 키-값 테이블이 라고도 하는 단일 행 테이블을 추출할 수 있습니다. 텍스트 추출의 향상 된 기능에는 일반 성능과 함께 id 문서에서 디지털 Pdf 및 MRZ (컴퓨터 판독 가능 영역) 텍스트가 더 효율적으로 처리 됩니다.

* [**폼 인식기 스튜디오**](https://formrecognizer.appliedai.azure.com) 서비스의 사용을 간소화 하기 위해 이제 폼 인식기 Studio에 액세스 하 여 미리 작성 된 다른 모델 또는 레이블을 테스트 하 고 사용자 지정 모델을 학습 시킬 수 있습니다.

새 [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm), [Python](quickstarts/try-v3-python-sdk.md) 또는 [.net](quickstarts/try-v3-csharp-sdk.md) SDK for v 3.0 preview API를 사용 하 여 시작을 받으세요.

 #### <a name="form-recognizer-model-data-extraction"></a>폼 인식기 모델 데이터 추출

  | **모델**   | **텍스트 추출** |**키-값 쌍** |**선택 표시**   | **테이블**   |**엔터티** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕 일반 문서  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | 청구서  | ✓ | ✓  | ✓  | ✓ ||
  |Receipt  | ✓  |   ✓ |   |  ||
  | ID 문서 | ✓  |   ✓  |   |   ||
  | 명함    | ✓  |   ✓ |   |   ||
  | 사용자 지정             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="september-2021"></a>2021년 9월

* [Azure 메트릭 탐색기 고급 기능은](/azure/azure-monitor/essentials/metrics-charts) Azure Portal의 양식 인식기 리소스 개요 페이지에서 사용할 수 있습니다.

    ### <a name="monitoring-menu"></a>모니터링 메뉴

    :::image type="content" source="media/portal-metrics.png" alt-text="Azure Portal의 모니터링 메뉴를 보여 주는 스크린샷":::

    ### <a name="charts"></a>차트

    :::image type="content" source="media/portal-metrics-charts.png" alt-text="Azure Portal 메트릭 차트의 예를 보여 주는 스크린샷":::

*  **ID 문서** 모델 업데이트: 접미사를 포함 하거나 마침표 (전체 중지)를 포함 하거나 제외 하 고 이름을 지정 하면 성공적으로 처리 됩니다.

    |입력 텍스트 | 업데이트 포함 결과 |
    |------------|-------------------------------------------|
    | William Isaac Kirby Jr. |**FirstName**: William Isaac</br></br>**LastName**: Kirby Jr. |
    | Henry Caleb Ross Sr | **FirstName**: Henry caleb </br></br> **LastName**: Ross Sr |

## <a name="july-2021"></a>2021년 7월

### <a name="system-assigned-managed-identity-support"></a>시스템이 할당한 관리 ID 지원

 이제 시스템이 할당한 관리 ID를 사용하도록 설정하여 VNet(가상 네트워크)이나 방화벽으로 보호되거나 BYOS(Bring Your Own Storage)를 사용하도록 설정한 계정을 비롯한 프라이빗 스토리지 계정에 Form Recognizer 제한된 액세스 권한을 부여할 수 있습니다. 자세한 내용은 [Form Recognizer 리소스의 관리 ID 만들기 및 사용](managed-identity-byos.md)을 ‘참조’하세요.

## <a name="june-2021"></a>2021년 6월

### <a name="form-recognizer-containers-v21-released-in-gated-preview"></a>제어된 미리 보기로 릴리스된 Form Recognizer 컨테이너 v2.1

Form Recognizer 기능은 이제 6개 기능 컨테이너인 **레이아웃**, **명함**,**ID 문서**,  **영수증**, **청구서**, **사용자 지정** 에서 지원됩니다. 이 컨테이너를 사용하려면 [온라인 요청](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)을 제출하고 승인을 받아야 합니다.

[**Form Recognizer의 Docker 컨테이너 설치 및 실행**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout)과 [**Form Recognizer 컨테이너 구성**](containers/form-recognizer-container-configuration.md?branch=main)을 ‘참조’하세요.

### <a name="form-recognizer-connector-released-in-preview"></a>미리 보기로 릴리스된 Form Recognizer 커넥터

  [**Form Recognizer 커넥터**](/connectors/formrecognizer)는 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md), [Microsoft Power Automate](/power-automate/getting-started), [Microsoft Power Apps](/powerapps/powerapps-overview)와 통합됩니다. 커넥터는 사용자 지정 및 미리 빌드된 양식, 청구서, 영수증, 명함, ID 문서 등에서 문서 데이터와 구조를 추출하고 분석하는 워크플로 작업 및 트리거를 지원합니다.

### <a name="form-recognizer-sdk-v310-patched-to-v311-for-c-java-and-python"></a>C#, Java, Python의 경우 Form Recognizer SDK v3.1.0이 v3.1.1로 패치됨

이 패치는 `Text`가 있지만 `BoundingBox` 또는 `Page` 정보가 없는 `FormField`와 같은 하위 품목 필드를 포함하지 않는 청구서를 처리합니다.

### <a name="c"></a>[**C#**](#tab/csharp)

| [참조 설명서](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet 패키지 버전 3.1.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

### <a name="java"></a>[**Java**](#tab/java)

 | [참조 설명서](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven 아티팩트 패키지 종속성 버전 3.1.1](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer/3.1.1) |

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

> [!NOTE]
> JavaScript SDK v3.1.0에 대한 업데이트는 없습니다.

| [참조 설명서](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm 패키지 종속성 form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer) |

### <a name="python"></a>[**Python**](#tab/python)

| [참조 설명서](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.1](https://pypi.org/project/azure-ai-formrecognizer/) |

---

## <a name="may-2021"></a>2021년 5월

### <a name="form-recognizer-21-api-generally-available-ga-release"></a>Form Recognizer 2.1 API GA(일반 공급) 릴리스

* Form Recognizer 2.1은 일반 공급됩니다. GA(일반 공급) 릴리스는 이전 2.1 미리 보기 패키지 버전에 도입된 변경 내용의 안정성을 표시합니다. 이 릴리스를 사용하면 다음에서 정보 및 데이터를 검색하고 추출할 수 있습니다.

* [문서](concept-layout.md)
* [Receipts](concept-receipts.md)
* [명함](concept-business-cards.md)
* [송장](concept-invoices.md)
* [ID 문서](concept-identification-cards.md)
* [사용자 지정 양식](concept-custom.md)

#### <a name="get-started"></a>시작

[Form Recognizer 샘플 도구](https://fott-2-1.azurewebsites.net/)로 이동하고 [빠른 시작](quickstarts/get-started-with-form-recognizer.md)을 따릅니다.

### <a name="layout-adds-table-headers"></a>레이아웃에 테이블 머리글이 추가됨

업데이트된 레이아웃 API 테이블 기능은 여러 행에 걸쳐 있을 수 있는 열 머리글을 사용하여 머리글 인식을 추가합니다. 각 테이블 셀에는 머리글의 일부인지 여부를 나타내는 특성이 있습니다. 테이블 머리글을 구성하는 행을 식별하는 데 사용할 수 있습니다.

#### <a name="sdk-updates"></a>SDK 업데이트

### <a name="c"></a>[**C#**](#tab/csharp)

| [참조 설명서](/dotnet/api/azure.ai.formrecognizer?view=azure-dotnet&preserve-view=true) | [NuGet 패키지 버전 3.0.1](https://www.nuget.org/packages/Azure.AI.FormRecognizer) |

#### <a name="non-breaking-changes"></a>**호환성이 손상되지 않는 변경**

* 이제 **FormRecognizerModelFactory** 클래스는 **TextAppearance** 및 **ReadingOrder** 모델의 업데이트와 **TextStyle** 모델의 제거를 지원합니다. [호환성이 손상되는 변경](#breaking-changes-may)을 참조하세요.

#### <a name="breaking-changes-may"></a>**호환성이 손상되는 변경(5월)**

* 클라이언트는 기본적으로 현재 지원되는 최신 서비스 버전 v2.1로 설정됩니다. **FormRecognizerClientOptions** 개체의 **Version** 속성에서 버전 2.0을 지정할 수 있습니다.

* **StartRecognizeIdentityDocuments**. 모든 관련 ID 문서 인식 API 기능의 _Id_ 키워드를 바꾸기 위해 **ID** 를 사용하는 메서드와 메서드 매개 변수의 이름을 바꾸었습니다.

* **FormReadingOrder**. *ReadingOrder* 이름이 **FormReadingOrder** 로 바뀌었습니다.

* **AsCountryRegion**. *AsCountryCode* 이름이 **AsCountryRegion** 으로 바뀌었습니다.

* 이제 **TextAppearance** 에는 **StyleName** 및 **StyleConfidence** 속성(이전 **TextStyle** 개체의 일부)이 포함됩니다.

* **FieldValueType**.  **Gender** 값이 모델에서 제거되었습니다.

* **TextStyle** 모델이 제거되었습니다.

* **FieldValueGender** 형식이 제거되었습니다.

### <a name="java"></a>[**Java**](#tab/java)

  | [참조 설명서](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [Maven 아티팩트 패키지 종속성 버전 3.1.0](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) |

#### <a name="non-breaking-changes"></a>**호환성이 손상되지 않는 변경**

* **FormRecognizerClientBuilder** 및 **FormTrainingClientBuilder** . **clientOptions** 및 **getDefaultLogOptions** 메서드를 추가했습니다.

* **FormRecognizerLanguage**.  언어 필드를 더 추가했습니다.

#### <a name="breaking-changes-may"></a>**호환성이 손상되는 변경(5월)**

* 클라이언트는 기본적으로 현재 지원되는 최신 서비스 버전 v2.1로 설정됩니다. **FormRecognizerClientBuilder** 개체의 **serviceVersion** 속성에서 버전 2.0을 지정할 수 있습니다.

* v2.1-preview.1 및 v2.1-preview.2 지원을 제거했습니다.

* **beginRecognizeIdentityDocuments**.  모든 관련 ID 문서 인식 API 기능의 _Id_ 키워드를 바꾸기 위해 **ID** 를 사용하는 메서드와 메서드 매개 변수의 이름을 바꾸었습니다.

* **FormReadingOrder**. *ReadingOrder* 이름이 **FormReadingOrder** 로 바뀌었고 클래스를 확장 가능한 문자열 클래스로 리팩터링합니다.

* **asCountryRegion**. *asCountry* 이름이 **asCountryRegion** 메서드로 바뀌었습니다.

* **FieldValueType**. 필드 값 *COUNTRY* 이름이 **COUNTRY_REGION** 으로 바뀌었습니다.

* 이제 **TextAppearance** 클래스에는 **styleName** 및 **styleConfidence** 속성(이전 **TextStyle** 개체의 일부)이 포함됩니다.

* **FieldValueType**. *Gender* 값이 모델에서 제거되었습니다.

* **TextStyle** 모델이 제거되었습니다.

* **FieldValueGender** 클래스 형식이 제거되었습니다.

* **pollInterval**. **RecognizeBusinessCardsOptions**, **RecognizeContentOptions**, **RecognizeCustomFormsOptions**, **RecognizeIdentityDocumentOptions**, **RecognizeInvoicesOptions**, **RecognizeReceiptsOptions** 클래스에서 pollInterval 메서드를 제거했습니다. 폴링 간격은 Azure Core [**SyncPoller setPollInterval**](/java/api/com.azure.core.util.polling.syncpoller.setpollinterval?view=azure-java-stable&preserve-view=true) 또는 [**PollerFlux setPollInterval**](/java/api/com.azure.core.util.polling.pollerflux.setpollinterval?view=azure-java-stable&preserve-view=true) 메서드를 사용하여 각각 동기적이나 비동기적으로 업데이트할 수 있습니다.

* **FormLine**, **FormPage**, **FormTable**, **FormSelectionMark**, **TextAppearance**, **CustomFormModel**, **CustomFormModelInfo**, **CustomFormModelProperties**, **CustomFormSubmodel**, **TrainingDocumentInfo** 는 이제 변경할 수 없는 모델 클래스입니다.

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

| [참조 설명서](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest&preserve-view=true)| [npm 패키지 종속성 form-recognizer 3.1.0](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |

#### <a name="non-breaking-changes"></a>**호환성이 손상되지 않는 변경**

* 모든 REST API 호출은 v2.1 엔드포인트로 마이그레이션됩니다.

* 양식 로캘의 가능한 값에 액세스하기 위해 **KnownFormLocale** 열거형이 추가되었습니다.

* **beginRecognizeIdDocuments...** . 모든 관련 ID 문서 인식 API 기능의 _Id_ 키워드를 바꾸기 위해 **ID** 를 사용하는 메서드와 메서드 매개 변수의 이름을 바꾸었습니다.

* **FormReadingOrder**, **FormLanguage**. *ReadingOrder* 이름이 *FormReadingOrder* 로 바뀌었습니다. *Language* 이름이 **FormLanguage** 로 바뀌었습니다.

* **FormCountryRegionField**, **countryRegion**. *FormCountryField* 형식 이름이 **FormCountryRegionField** 로 바뀌고, valueType *country* 이름이 **countryRegion** 으로 바뀌었습니다.

* 이제 **TextAppearance** 인터페이스에는 **styleName** 및 **styleConfidence** 속성(**TextStyle** 인터페이스의 이전 name 및 confidence 속성)이 포함됩니다.

* **KnownStyleName**, **KnownSelectionMarkState** 및 **KnownKeyValueType** 열거형이 제거되었습니다.

* **FormGenderField** 형식이 제거되었습니다. 이전에 _FormGenderField_ 로 생성된 인식된 값은 이제 FormStringField 형식으로 반환되고 값은 동일하게 유지됩니다.

* **TextStyle** 형식이 제거되었습니다.

#### <a name="breaking-changes-may"></a>**호환성이 손상되는 변경(5월)**

**주요 변경 내용이 없습니다.**

### <a name="python"></a>[**Python**](#tab/python)

| [Reference documentation](/java/api/com.azure.ai.formrecognizer.models?view=azure-java-stable&preserve-view=true)| [PyPi azure-ai-formrecognizer 3.1.0](https://pypi.org/project/azure-ai-formrecognizer/) |

#### <a name="non-breaking-changes"></a>**호환성이 손상되지 않는 변경**

* **to_dict** 및 **from_dict** 메서드가 모든 모델에 추가되었습니다.

#### <a name="breaking-changes-may"></a>**호환성이 손상되는 변경(5월)**

* **begin_recognize_identity_documents** 및 **begin_recognize_identity_documents_from_url**. _Id_ 키워드를 바꾸기 위해 **ID** 사용하는 메서드와 메서드 매개 변수의 이름을 바꾸었습니다.

* **FieldValueType**. 값 형식 *country* 이름을 **countryRegion** 으로 바꾸었습니다.  값 형식 *gender* 를 제거했습니다.

* 이제 **TextAppearance** 모델에는 **style_name** 및 **style_confidence** 속성(**TextStyle** 개체의 이전 name 및 confidence 속성)이 포함됩니다.

* **TextStyle** 모델이 제거되었습니다.

---

## <a name="april-2021"></a>2021년 4월
<!-- markdownlint-disable MD029 -->

### <a name="sdk-preview-updates-for-api--version-21-preview3"></a>API 버전 2.1-preview.3에 대한 SDK 미리 보기 업데이트

### <a name="c"></a>[**C#**](#tab/csharp)

NuGet 패키지 버전 3.1.0-beta.4

* **ID 문서의 데이터를 분석하는 새로운 방법**:

   **StartRecognizeIdDocumentsFromUriAsync**

   **StartRecognizeIdDocumentsAsync**

   필드 값 목록은 Form Recognizer 설명서에서 [추출된 필드](concept-identification-cards.md#fields-extracted)를 _참조하세요_.

* **[StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** 메서드에 제공할 수 있는 문서 언어 세트가 확장되었습니다.

* **다음 클래스에서 지원하는 새 속성 `Pages`** :

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   `Pages` 속성을 사용하면 다중 페이지 PDF 및 TIFF 문서에 대한 개별 또는 페이지 범위를 선택할 수 있습니다. 개별 페이지의 경우 페이지 번호(예: `3`)를 입력합니다. 페이지 범위(예: 2페이지 및 5-7페이지)의 경우 페이지 번호 및 쉼표로 구분한 페이지 범위를 입력합니다(`2, 5-7`).

* **다음 클래스에 대해 지원되는 새 속성 `ReadingOrder`** :

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  `ReadingOrder` 속성은 `basic` 또는 `natural`의 읽기 순서 알고리즘 중에서 텍스트 요소의 추출 순서에 적용할 알고리즘을 지정할 수 있는 선택적 매개 변수입니다. 지정하지 않으면 기본값 `basic`입니다.

#### <a name="breaking-changes-april"></a>호환성이 손상되는 변경(4월)

* 클라이언트는 기본적으로 지원되는 최신 서비스 버전(현재 **2.1-preview.3)** 으로 설정됩니다.

* 이제 **[StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** 메서드는 잘못된 파일이 전달될 때 `RequestFailedException()`을 throw합니다.

### <a name="java"></a>[**Java**](#tab/java)

Maven 아티팩트 패키지 종속성 버전 3.1.0-beta.3

* **ID 문서의 데이터를 분석하는 새로운 방법**:

  **[beginRecognizeIdDocumentsFromUrl]**

  **[beginRecognizeIdDocuments]**

   필드 값 목록은 Form Recognizer 설명서에서 [추출된 필드](concept-identification-cards.md#fields-extracted)를 _참조하세요_.

* **`FormContentType` 열거형의 사용자 지정 양식 및 학습 메서드에 대한 비트맵 이미지 파일(.bmp) 지원**:

* `image/bmp`

* **다음 클래스에서 지원하는 새 속성 `Pages`** :

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  `Pages` 속성을 사용하면 다중 페이지 PDF 및 TIFF 문서에 대한 개별 또는 페이지 범위를 선택할 수 있습니다. 개별 페이지의 경우 페이지 번호(예: `3`)를 입력합니다. 페이지 범위(예: 2페이지 및 5-7페이지)의 경우 페이지 번호 및 쉼표로 구분한 페이지 범위를 입력합니다(`2, 5-7`).

* **[Formcontenttype](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields) 필드의 사용자 지정 양식 및 학습 메서드에 대한 비트맵 이미지 파일(.bmp) 지원**:

  `image/bmp`

* **다음 메서드에 대해 지원되는 새 키워드 인수 `ReadingOrder`** :

* **[beginRecognizeContent](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?preserve-view=true&view=azure-java-preview)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   `ReadingOrder` 키워드 인수는 `basic` 또는 `natural`의 읽기 순서 알고리즘 중에서 텍스트 요소의 추출 순서에 적용할 알고리즘을 지정할 수 있는 선택적 매개 변수입니다. 지정하지 않으면 기본값 `basic`입니다.

* 클라이언트는 기본적으로 지원되는 최신 서비스 버전(현재 **2.1-preview.3)** 으로 설정됩니다.

### <a name="javascript"></a>[**JavaScript**](#tab/javascript)

npm 패키지 버전 3.1.0-beta.3

* **ID 문서의 데이터를 분석하는 새로운 방법**:

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    필드 값 목록은 Form Recognizer 설명서에서 [추출된 필드](concept-identification-cards.md#fields-extracted)를 _참조하세요_.

* **FieldValue 인터페이스에 추가된 새 필드 값**:

    `gender`—가능한 값은 `M`, `F` 또는 `X`입니다.</br>
   `country`—가능한 값은 [ISO alpha-3](https://www.iso.org/obp/ui/#search) 3문자 국가 코드 문자열을 따릅니다.

* 모든 양식 인식 방법(사용자 지정 양식 및 미리 작성된 모든 모델)이 지원하는 새 옵션 `pages`. 이 인수를 사용하면 다중 페이지 PDF 및 TIFF 문서에 대한 개별 또는 페이지 범위를 선택할 수 있습니다. 개별 페이지의 경우 페이지 번호(예: `3`)를 입력합니다. 페이지 범위(예: 2페이지 및 5-7페이지)의 경우 페이지 번호 및 쉼표로 구분한 페이지 범위를 입력합니다(`2, 5-7`).

* 콘텐츠 인식 메서드에 **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/formreadingorder?view=azure-node-latest&preserve-view=true to the URL)** 형식에 대한 지원이 추가되었습니다. 이 옵션을 사용하면 서비스에서 인식되는 텍스트 줄을 정렬하는 방법을 결정하는 데 사용하는 알고리즘을 제어할 수 있습니다. 텍스트 요소의 추출 순서를 지정하기 위해 적용해야 하는 읽기 순서 알고리즘을 `basic` 또는 `natural` 중에서 지정할 수 있습니다. 지정하지 않으면 기본값 `basic`입니다.

* **[FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** 형식을 여러 다른 인터페이스로 분할합니다. 이 업데이트는 특정 에지 사례(정의되지 않은 valueType)를 제외하고 API 호환성 문제를 일으키지 않습니다.

* 모든 REST API 호출에 대한 **2.1-preview.3** Form Recognizer 서비스 엔드포인트로 마이그레이션되었습니다.

### <a name="python"></a>[**Python**](#tab/python)

pip 패키지 버전 3.1.0b4

* **ID 문서의 데이터를 분석하는 새로운 방법**:

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  필드 값 목록은 Form Recognizer 설명서에서 [추출된 필드](concept-identification-cards.md#fields-extracted)를 _참조하세요_.

* **[FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true) 열거형에 추가된 새 필드 값**:

   gender—가능한 값은 `M`, `F` 또는 `X`입니다.

  country—가능한 값은 [ISO alpha-3 국가 코드](https://www.iso.org/obp/ui/#search)를 따릅니다.

* **[Formcontenttype](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true) 열거형의 사용자 지정 양식 및 학습 메서드에 대한 비트맵 이미지 파일(.bmp) 지원**:

    image/bmp

* **다음 메서드에서 지원하는 새 키워드 인수 `pages`** :

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   `pages` 키워드 인수를 사용하면 다중 페이지 PDF 및 TIFF 문서에 대한 개별 또는 페이지 범위를 선택할 수 있습니다. 개별 페이지의 경우 페이지 번호(예: `3`)를 입력합니다. 페이지 범위(예: 2페이지 및 5-7페이지)의 경우 페이지 번호 및 쉼표로 구분한 페이지 범위를 입력합니다(`2, 5-7`).

* **다음 메서드에 대해 지원되는 새 키워드 인수 `readingOrder`** :

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   `readingOrder` 키워드 인수는 `basic` 또는 `natural`의 읽기 순서 알고리즘 중에서 텍스트 요소의 추출 순서에 적용할 알고리즘을 지정할 수 있는 선택적 매개 변수입니다. 지정하지 않으면 기본값 `basic`입니다.

---

## <a name="march-2021"></a>2021년 3월

**이제 Form Recognizer v2.1 공개 미리 보기 3을 사용할 수 있습니다.** 다음 기능을 포함한 v2.1-preview.3이 릴리스되었습니다.

* **새롭게 미리 빌드된 ID 모델** 새롭게 미리 빌드된 ID 모델로 고객은 ID를 가져오고 구조화된 데이터를 반환하여 처리를 자동화할 수 있습니다. 강력한 OCR(광학 인식) 기능을 ID 해석 모델과 결합하여 여권 및 미국 운전 면허증에서 이름, 생년월일, 발급일, 만료일 등의 주요 정보를 추출합니다.

  [미리 빌드된 ID 모델에 대한 자세한 정보](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="여권 예" lightbox="./media/id-canada-passport-example.png":::

* **송장 모델에 대 한 줄 항목 추출** -이제 미리 작성 한 송장 모델에서 줄 항목 추출을 지원 합니다. 이제 전체 항목과 해당 부분 (설명, 수량, 수량, 제품 ID, 날짜 등)을 추출 합니다. 간단한 API/SDK 호출로 청구서에서 유용한 데이터(텍스트, 표, 키-값 쌍, 품목)를 추출할 수 있습니다.

   [송장 모델에 대 한 자세한 정보](concept-invoices.md)

* **감독된 표 레이블 지정 및 학습, 빈 값 레이블 지정** - Form Recognizer의 [최신 딥 러닝 자동 표 추출 기능](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011) 외에도 이제는 고객이 표에 대해 레이블을 지정하고 학습시킬 수 있습니다. 이 새 릴리스에는 품목/표(동적 및 고정)에 대해 레이블을 지정하고 학습시키는 기능과 키-값 쌍 및 품목을 추출하는 사용자 지정 모델을 학습시키는 기능이 포함됩니다. 모델을 학습시키고 나면 모델은 documentResults 섹션에서 JSON 출력의 일부로 품목을 추출합니다.

    :::image type="content" source="./media/table-labeling.png" alt-text="표 레이블 지정" lightbox="./media/table-labeling.png":::

    표에 레이블을 지정하는 것 외에도 이제 비어 있는 값 및 영역에 레이블을 지정할 수 있습니다. 학습 세트의 일부 문서에 특정 필드 값이 없는 경우 레이블을 지정하여 분석 대상 문서에서 올바르게 값을 추출하는 방법을 모델이 알게 할 수 있습니다.

* **66개의 새 언어 지원** - Form Recognizer의 Layout API 및 Custom Models는 이제 73개의 언어를 지원합니다.

  [Form Recognizer의 언어 지원에 대한 자세한 정보](language-support.md)

* **자연 스러운 읽기 순서, 필기 분류 및 페이지 선택** -이 업데이트를 사용 하면 기본 왼쪽에서 오른쪽 방향으로, 위쪽에서 아래쪽으로 정렬 하는 대신 기본 읽기 순서로 텍스트 줄 출력을 가져오도록 선택할 수 있습니다. 새 readingOrder 쿼리 매개 변수를 사용하고 사용자에게 더 친숙한 읽기 순서로 출력되도록 "natural" 값으로 설정합니다. 또한 라틴어의 경우 Form Recognizer가 텍스트 줄을 필기 스타일인지 아닌지로 분류하고 신뢰도 점수를 제공합니다.

* **미리 빌드된 영수증 모델 품질 개선 사항** 이 업데이트에는 특히 품목 추출과 관련하여 미리 빌드된 영수증 모델에 대한 많은 품질 개선 사항이 포함되어 있습니다.

## <a name="november-2020"></a>2020년 11월

### <a name="new-features"></a>새로운 기능

**이제 Form Recognizer v2.1 공개 미리 보기 2를 사용할 수 있습니다.** 다음 기능을 포함한 v2.1-preview.2가 릴리스되었습니다.

* **새롭게 미리 빌드된 청구서 모델** - 새롭게 미리 빌드된 청구서 모델로 고객은 다양한 형식의 청구서를 가져오고 구조화된 데이터를 반환하여 청구서 처리를 자동화할 수 있습니다. 강력한 OCR(광학 인식) 기능을 청구서 해석 딥 러닝 모델과 결합하여 영어로 된 청구서에서 주요 정보를 추출합니다. 키 텍스트, 표 및 고객, 공급업체, 청구서 ID, 청구서 기한, 총액, 지불액, 세금, 배송지, 청구지 등과 같은 정보를 추출합니다.

  > [미리 빌드된 청구서 모델에 대한 자세한 정보](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="청구서 예" lightbox="./media/invoice-example.jpg":::

* **향상된 표 추출** - 이제 Form Recognizer는 강력한 OCR(광학 인식) 기능을 딥 러닝 표 추출 모델과 결합하는 향상된 표 추출 기능을 제공합니다. Form Recognizer는 병합된 열, 행, 테두리 없음 등을 포함하는 복잡한 표를 포함하여 표에서 데이터를 추출할 수 있습니다.

  :::image type="content" source="./media/tables-example.jpg" alt-text="표 예" lightbox="./media/tables-example.jpg":::

  > [레이아웃 추출에 대한 자세한 정보](concept-layout.md)

* **클라이언트 라이브러리 업데이트** - .Net, Python, Java, JavaScript용 [클라이언트 라이브러리](quickstarts/client-library.md)의 최신 버전은 Form Recognizer 2.1 API를 지원합니다.
* **지원되는 새 언어: 일본어** - 이제 다음 새 언어가 지원됩니다. `AnalyzeLayout` 및 `AnalyzeCustomForm`에 대해 일본어(`ja`)가 지원됩니다. [언어 지원](language-support.md)
* **텍스트 줄 스타일 표시(필기/기타)(라틴어에만 해당)** - 이제 Form Recognizer에서 각 텍스트 줄이 필기 스타일인지 아닌지를 분류하는 `appearance` 개체를 신뢰도 점수와 함께 출력합니다. 이 기능은 라틴어에 대해서만 지원됩니다.
* **품질 개선 사항** - 한 자릿수 추출 개선 사항을 포함하여 추출 기능이 개선되었습니다.
* **Form Recognizer 샘플 및 레이블 지정 도구의 새로운 실습 기능** - Form Recognizer 샘플 레이블 지정 도구를 사용하여 미리 빌드된 청구서, 영수증, 명함 모델 및 레이아웃 API를 사용해볼 수 있는 기능입니다. 코드를 작성하지 않고 데이터를 추출하는 방법을 참조하세요.

  [**Form Recognizer 샘플 레이블 지정 도구 사용해 보기**](https://fott-2-1.azurewebsites.net)

  ![스크린샷: 샘플 레이블 지정 도구](./media/ui-preview.jpg)

* **피드백 반복** - 샘플 레이블 지정 도구를 통해 파일을 분석할 때 이제 학습 세트에 이를 추가할 수도 있고 필요한 경우 레이블을 조정하고 모델 개선을 위해 학습시킬 수도 있습니다.
* **문서 자동 레이블 지정** - 프로젝트에서 이전에 레이블이 지정된 문서를 기준으로 추가 문서에 자동으로 레이블을 지정합니다.

## <a name="august-2020"></a>2020년 8월

### <a name="new-features"></a>새로운 기능

**이제 Form Recognizer v2.1 공개 미리 보기를 사용할 수 있습니다.** 다음 기능을 포함한 V2.1-preview.1이 릴리스되었습니다.

* **REST API 참조 사용 가능** - [v2.1-preview.1 참조](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)를 살펴봅니다.
* **영어 외에 지원되는 새 언어**, 이제 다음 [언어](language-support.md)가 지원됩니다. `Layout` 및 `Train Custom Model`에 대해 영어(`en`), 중국어(간체)(`zh-Hans`), 네덜란드어(`nl`), 프랑스어(`fr`), 독일어(`de`), 이탈리아어(`it`), 포르투갈어(`pt`), 스페인어(`es`)가 지원됩니다.
* **확인란/선택 표시 검색** – Form Recognizer는 확인란 및 라디오 단추와 같은 선택 표시에 대한 검색 및 추출을 지원합니다. 선택 표시는 `Layout`에서 추출되며 이제 `Train Custom Model` - _레이블을 사용하여 학습_ 에서 레이블을 지정하고 학습시켜 선택 표시에 대한 키 값 쌍을 추출할 수도 있습니다.
* **모델 작성** - 단일 모델 ID로 여러 개의 모델을 작성하고 호출할 수 있습니다. 작성된 모델 ID로 분석하기 위해 문서를 제출하면 먼저 분류 단계를 수행하여 올바른 사용자 지정 모델로 라우팅합니다. 모델 작성 기능은 `Train Custom Model` - _레이블을 사용하여 학습_ 에서 사용할 수 있습니다.
* **모델 이름** - 더 쉽게 관리하고 추적할 수 있도록 사용자 지정 모델에 식별 이름을 추가합니다.
* **[새롭게 미리 빌드된 명함용 모델](concept-business-cards.md)** 영어 명함에서 일반 필드를 추출하는 데 사용됩니다.
* **[미리 빌드된 영수증용 새로운 로캘](concept-receipts.md)** EN-US 외에 이제 EN-AU, EN-CA, EN-GB, EN-IN에 대한 지원이 제공됩니다.
* **품질 개선 사항** `Layout`, `Train Custom Model` - _레이블 없이 학습_ 및 _레이블을 사용하여 학습_.

**v2.0** 에는 다음 업데이트가 포함됩니다.

* .NET, Python, Java, JavaScript용 [클라이언트 라이브러리](quickstarts/client-library.md)가 일반 공급에 들어갔습니다.

**새 샘플** 은 GitHub에서 사용할 수 있습니다.

* [지식 추출 레시피 - Forms 플레이북](https://github.com/microsoft/knowledge-extraction-recipes-forms)은 실제 Form Recognizer 고객 참여의 모범 사례를 수집하고, 이러한 프로젝트를 개발하는 데 사용되는 유용한 코드 샘플, 검사 목록, 샘플 파이프라인을 제공합니다.
* [샘플 레이블 지정 도구](https://github.com/microsoft/OCR-Form-Tools)는 새로운 v2.1 기능을 지원하도록 업데이트되었습니다. 도구를 시작하려면 이 [빠른 시작](label-tool.md)을 참조하세요.
* [지능형 키오스크](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Form Recognizer 샘플은 `Analyze Receipt`와 `Train Custom Model` - _레이블 없이 학습_ 을 통합하는 방법을 보여 줍니다.

## <a name="july-2020"></a>2020년 7월

### <a name="new-features"></a>새로운 기능
<!-- markdownlint-disable MD004 -->
* **v2.0 참조 사용 가능** - [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme), [JavaScript](/javascript/api/overview/azure/)용 [v2.0 API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) 및 업데이트된 SDK를 살펴봅니다.
* **표 기능 향상 및 추출 기능 향상** - 정확도 개선 및 표 추출 기능 향상, 특히 _레이블 없이 사용자 지정 학습_ 에서 표 헤더와 구조를 학습하는 기능을 포함합니다.

* **통화 지원** - 전세계 통화 기호의 검색 및 추출을 지원합니다.
* **Azure Gov** - 이제 Form Recognizer를 Azure Gov에서도 사용할 수 있습니다.
* **강화된 보안 기능**:
  * **Bring Your Own Key** - Form Recognizer는 데이터가 클라우드에 보관될 때 이를 자동으로 암호화하여 데이터를 보호하고 사용자가 조직의 보안 및 규정 준수 약정을 충족할 수 있도록 합니다. 기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 이제 사용자 고유의 암호화 키로 구독을 관리할 수도 있습니다. [고객 관리 키(BYOK(Bring Your Own Key)라고도 함)](./encrypt-data-at-rest.md)를 사용하여 훨씬 더 유연하게 액세스 제어를 만들고, 회전하고, 해제하고, 취소할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.
  * **프라이빗 엔드포인트** – 가상 네트워크에서 [프라이빗 링크를 통해 데이터에 안전하게 액세스](../../private-link/private-link-overview.md)할 수 있게 합니다.

## <a name="june-2020"></a>2020년 6월

### <a name="new-features"></a>새로운 기능

* **클라이언트 SDK에 추가된 CopyModel API** - 이제 클라이언트 SDK를 사용하여 한 구독에서 다른 구독으로 모델을 복사할 수 있습니다. 이 기능에 대한 일반 정보는 [모델 백업 및 복구](./disaster-recovery.md)를 참조하세요.
* **Azure Active Directory 통합** - 이제 Azure AD 자격 증명을 사용하여 SDK에서 Form Recognizer 클라이언트 개체를 인증할 수 있습니다.
* **SDK 관련 변경 내용** - 이러한 변경에는 사소한 기능 추가와 주요 변경 내용이 모두 포함됩니다. 자세한 내용은 SDK 변경 로그를 참조하세요.
  * [C# SDK 미리 보기 3 변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK 미리 보기 3 변경 로그](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK 미리 보기 3 변경 로그](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK 미리 보기 3 변경 로그](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020년 4월

### <a name="new-features"></a>새로운 기능

* **Form Recognizer API v2.0 공개 미리 보기에 대한 SDK 지원**  - 이번 달에는 Form Recognizer v2.0(미리 보기) 릴리스용 미리 보기 SDK를 포함하도록 서비스 지원을 확장했습니다. 아래 링크를 사용하여 선택한 언어로 시작합니다.
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  새 SDK는 Form Recognizer용 v2.0 REST API의 모든 기능을 지원합니다. 예를 들어 레이블을 사용하거나 사용하지 않고 모델을 학습시키고, 양식에서 텍스트, 키 값 쌍, 표를 추출하고, 미리 빌드된 영수증 서비스로 영수증에서 데이터를 추출하고, 문서에서 레이아웃 서비스로 텍스트와 표를 추출할 수 있습니다. [SDK 피드백 양식](https://aka.ms/FR_SDK_v1_feedback)을 통해 SDK에 대한 사용자의 피드백을 공유할 수 있습니다.

* **사용자 지정 모델 복사** 이제 새로운 사용자 지정 모델 복사 기능을 사용하여 지역과 구독 간에 모델을 복사할 수 있습니다. 사용자 지정 모델 복사 API를 호출하기 전에 먼저 대상 리소스 엔드포인트에 대해 복사 권한 부여 작업을 호출하여 대상 리소스로 복사할 권한 부여를 가져와야 합니다.

  * [복사 권한 부여 생성](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [사용자 지정 모델 복사](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>향상된 보안 기능

* 이제 FormRecognizer에서 고객 관리형 키를 사용할 수 있습니다. 자세한 내용은 [Form Recognizer에 대한 미사용 데이터 암호화](./encrypt-data-at-rest.md)를 참조하세요.
* Azure Active Directory로 Azure 리소스에 액세스하기 위해 관리 ID를 사용합니다. 자세한 내용은 [관리 ID에 대한 액세스 권한 부여](../../cognitive-services/authentication.md#authorize-access-to-managed-identities)를 참조하세요.

## <a name="march-2020"></a>2020년 3월

### <a name="new-features"></a>새로운 기능

* **레이블 지정을 위한 값 형식** 이제 Form Recognizer 샘플 레이블 지정 도구로 레이블을 지정할 값의 형식을 지정할 수 있습니다. 현재 지원되는 값 형식 및 변형은 다음과 같습니다.
  * `string`
    * 기본값, `no-whitespaces`, `alphanumeric`
  * `number`
    * 기본값, `currency`
  * `date`
    * 기본값, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  이 기능을 사용하는 방법을 알아보려면 [샘플 레이블 지정 도구](label-tool.md#specify-tag-value-types) 가이드를 참조하세요.

* **표 시각화** 이제 샘플 레이블 지정 도구는 문서에서 인식된 표를 표시합니다. 레이블 지정 및 분석 전에 이 기능을 사용하여 문서에서 인식 및 추출된 표를 볼 수 있습니다. 이 기능은 레이어 옵션을 사용하여 켜거나 끌 수 있습니다.

  다음 이미지는 표를 인식하고 추출하는 방법의 예입니다.

  > [!div class="mx-imgBorder"]
  > ![샘플 레이블 지정 도구를 사용한 표 시각화](./media/whats-new/table-viz.png)

    추출된 표는 `"pageResults"` 아래의 JSON 출력에서 사용할 수 있습니다.

  > [!IMPORTANT]
  > 표에 레이블을 지정하는 기능은 지원되지 않습니다. 표가 자동으로 인식 및 추출되지 않으면 키/값 쌍으로만 레이블을 지정할 수 있습니다. 표에 키/값 쌍으로 레이블을 지정할 때 각 셀에 고유한 값으로 레이블을 지정합니다.

### <a name="extraction-enhancements"></a>추출 기능 향상

이 릴리스에는 추출 기능 향상 및 정확성 개선, 특히 동일한 텍스트 줄에서 여러 개의 키/값 쌍을 레이블 지정하고 추출하는 기능이 포함되어 있습니다.

### <a name="sample-labeling-tool-is-now-open-source"></a>이제 오픈 소스로 제공되는 샘플 레이블 지정 도구

이제 Form Recognizer 샘플 레이블 지정 도구를 오픈 소스 프로젝트로 사용할 수 있습니다. 솔루션 내에서 통합하고 필요에 맞게 고객 전용 변경을 수행할 수 있습니다.

Form Recognizer 샘플 레이블 지정 도구에 대한 자세한 내용은 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)에서 제공되는 설명서를 검토합니다.

### <a name="tls-12-enforcement"></a>TLS 1.2 적용

이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../../cognitive-services/cognitive-services-security.md)을 참조하세요.

## <a name="january-2020"></a>2020년 1월

이 릴리스에서는 Form Recognizer 2.0(미리 보기)을 도입했습니다. 아래 섹션에서 새로운 기능, 기능 향상, 변경 내용에 대한 자세한 정보를 확인할 수 있습니다.

### <a name="new-features"></a>새로운 기능

* **사용자 지정 모델**
  * **레이블을 사용하여 학습** 이제 수동으로 레이블이 지정된 데이터로 사용자 지정 모델을 학습시킬 수 있습니다. 이 방법을 통해 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.
  * **비동기 API** 비동기 API 호출을 사용하여 대량의 데이터 세트 및 파일을 학습하고 분석할 수 있습니다.
  * **TIFF 파일 지원** 이제 TIFF 문서로 학습시키고 이 문서의 데이터를 추출할 수 있습니다.
  * **추출 정확도 개선**

* **미리 빌드된 영수증 모델**
  * **팁 금액** 이제 팁 금액과 기타 필기 값을 추출할 수 있습니다.
  * **품목 추출** 영수증에서 품목 값을 추출할 수 있습니다.
  * **신뢰도 값** 추출된 각 값에 대한 모델의 신뢰도를 볼 수 있습니다.
  * **추출 정확도 개선**

* **레이아웃 추출** 이제 레이아웃 API를 사용하여 양식에서 텍스트 데이터 및 표 데이터를 추출할 수 있습니다.

### <a name="custom-model-api-changes"></a>사용자 지정 모델 API 변경 내용

사용자 지정 모델을 학습시키고 사용하기 위한 모든 API의 이름이 변경되었으며, 일부 동기식 메서드는 이제 비동기식입니다. 다음은 주요 변경 내용입니다.

* 모델을 학습시키는 프로세스는 이제 비동기식입니다. **/custom/models** API 호출을 통해 학습을 시작합니다. 이 호출은 **custom/models/{modelID}** 에 전달하여 학습 결과를 반환할 수 있는 작업 ID를 반환합니다.
* 이제 키/값 추출이 **/custom/models/{modelID}/analyze** API 호출에 의해 시작됩니다. 이 호출은 **custom/models/{modelID}/analyzeResults/{resultID}** 에 전달하여 추출 결과를 반환할 수 있는 작업 ID를 반환합니다.
* 이제 학습 작업을 위한 작업 ID가 **Operation-Location** 헤더가 아니라 HTTP 응답의 **Location** 헤더에 있습니다.

### <a name="receipt-api-changes"></a>영수증 API 변경 내용

판매 영수증을 읽기 위한 API의 이름이 변경 되었습니다.

* 이제 영수증 데이터 추출이 **/prebuilt/receipt/analyze** API 호출에 의해 시작됩니다. 이 호출은 **/prebuilt/receipt/analyzeResults/{resultID}** 에 전달하여 추출 결과를 반환할 수 있는 작업 ID를 반환합니다.

### <a name="output-format-changes"></a>출력 형식 변경 내용

이제 API 호출 모두에 대한 JSON 응답에서 새로운 형식을 사용합니다. 일부 키 및 값이 추가되거나, 제거되거나, 이름이 변경되었습니다. 현재의 JSON 형식 예에 대해서는 빠른 시작을 참조하세요.

## <a name="next-steps"></a>다음 단계

사용자가 선택한 개발 언어로 Form Recognizer를 사용하여 양식 처리 앱 작성을 시작하려면 [빠른 시작](quickstarts/client-library.md)을 완료하세요.

## <a name="see-also"></a>참조

* [Form Recognizer란?](./overview.md)
