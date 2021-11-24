---
title: '빠른 시작: 샘플 레이블 지정 도구를 사용하여 양식 레이블 지정, 모델 학습 및 양식 분석 - Form Recognizer'
titleSuffix: Azure Applied AI Services
description: 이 빠른 시작에서는 Form Recognizer 샘플 레이블 지정 도구를 사용하여 레이블을 양식 문서에 수동으로 지정하는 방법을 알아봅니다. 그런 다음, 레이블이 지정된 문서를 사용하여 사용자 지정 문서 처리 모델을 학습시키고, 모델을 사용하여 키/값 쌍을 추출합니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021, ignite-fall-2021, mode-other
keywords: 문서 처리
ms.openlocfilehash: ca58c2e2b80202fd2399a8a238453379c8f55d24
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045127"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-the-sample-labeling-tool"></a>샘플 레이블 지정 도구 시작

Azure Form Recognizer는 기계 학습 모델을 사용하여 문서에서 양식 필드, 텍스트 및 테이블을 추출하고 분석하는 클라우드 기반 Azure Applied AI Service입니다. Form Recognizer를 사용하여 애플리케이션 및 워크플로에서 데이터 처리를 자동화하고, 데이터 기반 전략을 개선하고, 문서 검색 기능을 보강합니다. 

Form Recognizer 샘플 레이블 지정 도구는 다음과 같은 Azure Form Recognizer 및 OCR(광학 인식) 서비스의 최신 기능을 테스트할 수 있는 오픈 소스 도구입니다.

* [레이아웃 API를 사용하여 문서 분석](#analyze-layout) 레이아웃 API를 사용하여 문서에서 텍스트, 테이블, 선택 표시 및 구조를 추출해 보세요.

* [미리 빌드된 모델을 사용하여 문서 분석](#analyze-using-a-prebuilt-model) 청구서, 영수증, ID 문서 또는 명함에서 데이터를 추출하기 위해 미리 빌드된 모델로 시작합니다.

* [사용자 지정 양식 학습 및 분석](#train-a-custom-form-model) 사용자 지정 모델을 사용하여 고유한 비즈니스 데이터 및 사용 사례와 관련한 문서에서 데이터를 추출합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure 구독 – [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)

* Cognitive Services 또는 Form Recognizer 리소스. Azure 구독이 있으면 Azure Portal에서 [단일 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [다중 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer 리소스를 만들어 키와 엔드포인트를 가져옵니다. 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

    > [!TIP]
    > 단일 엔드포인트/키에서 여러 Cognitive Services에 액세스하려는 경우 Cognitive Services 리소스를 만듭니다. Form Recognizer 리소스를 Form Recognizer 액세스 전용으로 만듭니다. [Azure Active Directory 인증](../../../active-directory/authentication/overview-authentication.md)을 사용하려는 경우 단일 서비스 리소스가 필요합니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

 :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal의 키 및 엔드포인트 위치.":::

## <a name="analyze-using-a-prebuilt-model"></a>미리 빌드된 모델을 사용하여 분석

Form Recognizer에서는 선택할 수 있는 몇 가지 미리 빌드된 모델을 제공합니다. 각 모델에는 지원되는 고유한 필드 집합이 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델은 다음과 같습니다.

* [**청구서**](../concept-invoice.md): 청구서에서 텍스트, 선택 표시, 테이블, 키-값 쌍 및 키 정보를 추출합니다.
* [**영수증**](../concept-receipt.md): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](../concept-id-document.md): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](../concept-business-card.md): 명함에서 텍스트 및 키 정보를 추출합니다.

1. [Form Recognizer 샘플 도구](https://fott-2-1.azurewebsites.net/)로 이동합니다.

1. 샘플 도구 홈페이지에서 **미리 빌드된 모델을 사용하여 데이터 가져오기** 를 선택합니다.

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Form Recognizer 레이아웃의 분석 결과":::

1. 드롭다운 창에서 분석하려는 **양식 유형** 을 선택합니다.

1. 아래 옵션에서 분석하려는 파일의 URL을 선택합니다.

    * [**샘플 청구서 문서**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf)
    * [**샘플 ID 문서**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png)
    * [**샘플 영수증 이미지**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)
    * [**샘플 명함 이미지**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg)

1. **원본: URL** 필드에 선택한 URL을 붙여넣고 **페치** 단추를 선택합니다.

1. **Form Recognizer 서비스 엔드포인트** 필드에 Form Recognizer 구독을 통해 얻은 엔드포인트를 붙여넣습니다.

1. **API 키** 필드에 Form Recognizer 리소스에서 얻은 구독 키를 붙여넣습니다.

    :::image type="content" source="../media/fott-select-form-type.png" alt-text="스크린샷: 양식 유형 선택 드롭다운 창":::

1. **분석 실행** 을 선택합니다. Form Recognizer 샘플 레이블 지정 도구는 미리 빌드된 분석 API를 호출하고 문서를 분석합니다.

1. 결과 보기 - 추출된 키 값 쌍, 개별 항목, 추출되어 있는 강조 표시된 텍스트 및 검색된 테이블을 참조하세요.

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Form Recognizer 청구서 모델의 분석 결과":::

1. 자세한 결과를 보려면 JSON 출력 파일을 다운로드 합니다.

    * ‘readResults’ 노드에는 페이지의 각 경계 상자 배치가 있는 모든 텍스트 줄이 포함되어 있습니다.
    * ‘selectionMarks’ 노드는 모든 선택 표시(확인란, 라디오 표시) 및 상태가 ‘선택됨’ 또는 ‘선택되지 않음’인지 여부를 표시합니다.
    * ‘pageResults’ 섹션에는 추출된 표가 포함됩니다. 각 표에서 텍스트, 행 및 열 인덱스, 행 및 열 스패닝, 경계 상자 등이 추출됩니다.
    * ‘documentResults’ 필드에는 문서에서 가장 관련성이 큰 부분에 대한 키/값 쌍 정보 및 개별 항목 정보가 포함됩니다.

## <a name="analyze-layout"></a>레이아웃 분석

Azure Form Recognizer 레이아웃 API는 문서(PDF, TIFF) 및 이미지(JPG, PNG, BMP)에서 텍스트, 테이블, 선택 표시 및 구조 정보를 추출합니다.

1. [Form Recognizer 샘플 도구](https://fott-2-1.azurewebsites.net/)로 이동합니다.

1. 샘플 도구 홈페이지에서 **레이아웃을 사용하여 텍스트, 테이블 및 선택 표시 가져오기** 를 선택합니다.

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="레이아웃 Form Recognizer 도구의 연결 설정":::

1. **Form Recognizer 서비스 엔드포인트** 필드에 Form Recognizer 구독을 통해 얻은 엔드포인트를 붙여넣습니다.

1. **API 키** 필드에 Form Recognizer 리소스에서 얻은 구독 키를 붙여넣습니다.

1. **원본: URL** 필드에 다음 URL `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg`을 붙여넣고 **페치** 단추를 선택합니다.

1. **레이아웃 실행** 을 선택합니다. Form Recognizer 샘플 레이블 지정 도구는 레이아웃 분석 API를 호출하고 문서를 분석합니다.

    :::image type="content" source="../media/fott-layout.png" alt-text="스크린샷: 레이아웃 드롭다운 창":::

1. 결과 보기 - 추출되어 있는 강조 표시된 텍스트, 검색된 선택 표시 및 검색된 테이블을 참조하세요.

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Form Recognizer 도구의 연결 설정":::

1. 자세한 레이아웃 결과를 보려면 JSON 출력 파일을 다운로드 합니다.
     * `readResults` 노드에는 페이지의 각 경계 상자 배치가 있는 모든 텍스트 줄이 포함되어 있습니다.
     * `selectionMarks` 노드는 모든 선택 표시(확인란, 라디오 표시) 및 상태가 `selected` 또는 `unselected`인지 여부를 표시합니다.
     * `pageResults` 섹션에는 추출된 표가 포함됩니다. 각 표에서 텍스트, 행 및 열 인덱스, 행 및 열 스패닝, 경계 상자 등이 추출됩니다.

## <a name="train-a-custom-form-model"></a>사용자 지정 양식 모델 학습

사용자 지정 모델을 학습하면 비즈니스와 관련된 양식 및 문서의 데이터를 분석하고 추출할 수 있습니다. API는 고객의 고유한 콘텐츠 내의 양식 필드를 인식하고 키-값 쌍 및 테이블 데이터를 추출하도록 학습된 기계 학습 프로그램입니다. 최소 5가지의 동일한 양식 유형 예제가 있으면 시작할 수 있으며, 사용자 지정 모델은 레이블이 지정된 데이터 세트를 사용하여 또는 사용하지 않고 학습시킬 수 있습니다.

### <a name="prerequisites-for-training-a-custom-form-model"></a>사용자 지정 양식 모델 학습을 위한 필수 조건

* 학습 데이터 세트가 포함된 Azure Storage BLOB 컨테이너입니다. 모든 학습 문서가 동일한 형식인지 확인합니다. 여러 형식의 양식이 있으면 공용 형식에 따라 하위 폴더로 구성합니다. 이 프로젝트의 경우 [샘플 데이터 세트](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip)를 사용할 수 있습니다.

* CORS 구성

    [CORS(원본 간 리소스 공유)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)는 Form Recognizer Studio에서 액세스할 수 있도록 Azure 스토리지 계정에 구성되어야 합니다. Azure Portal에서 CORS를 구성하려면 스토리지 계정의 CORS 블레이드에 액세스해야 합니다.

    :::image type="content" source="../media/quickstarts/storage-cors-example.png" alt-text="스토리지 계정에 대한 CORS 구성을 보여주는 스크린샷.":::

    1. 스토리지 계정에 대한 CORS 블레이드를 선택합니다.

    1. Blob 서비스에서 새 CORS 항목을 만들어 시작합니다.

    1. **허용된 원본** 을 **https://formrecognizer.appliedai.azure.com** 로 설정합니다.

    1. **허용된 메서드** 에 사용할 수 있는 8가지 옵션을 모두 선택합니다.

    1. 각 필드에 *를 입력하여 **허용된 헤더** 및 **노출된 헤더** 를 모두 승인합니다.

    1. **Max Age** 를 120초 또는 허용되는 값으로 설정합니다.

    1. 페이지 위쪽에 있는 저장 단추를 클릭하여 변경 내용을 저장합니다.

    이제 Form Recognizer Studio의 스토리지 계정을 사용하도록 CORS를 구성해야 합니다.

### <a name="use-the-sample-labeling-tool"></a>샘플 레이블 지정 도구 사용

1. [Form Recognizer 샘플 도구](https://fott-2-1.azurewebsites.net/)로 이동합니다.

1. 샘플 도구 홈페이지에서 **사용자 지정 양식을 사용하여 레이블이 있는 모델을 학습시키고 키 값 쌍 얻기** 를 선택합니다.

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="사용자 지정 모델 학습":::

1. **새 프로젝트** 선택

    :::image type="content" source="../media/fott-new-project.png" alt-text="스크린샷: 새 프로젝트 선택 프롬프트":::

#### <a name="create-a-new-project"></a>새 프로젝트 만들기

다음 값을 사용해 **프로젝트 설정** 필드를 구성합니다.

1. **표시 이름** 프로젝트 이름을 지정합니다.

1. **보안 토큰** 각 프로젝트는 중요한 프로젝트 설정을 암호화/암호 해독하는 데 사용할 수 있는 보안 토큰을 자동으로 생성합니다. 왼쪽 탐색 모음 아래쪽에 있는 기어 아이콘을 선택하여 [애플리케이션 설정]에서 보안 토큰을 찾을 수 있습니다.

1. **원본 연결** 샘플 레이블 지정 도구는 원본(원래 업로드된 양식) 및 대상(만든 레이블 및 출력 데이터)에 연결됩니다. 프로젝트 간에 연결을 설정하고 공유할 수 있습니다. 확장 가능한 공급자 모델을 사용하므로 새 원본/대상 공급자를 쉽게 추가할 수 있습니다.

    * 새 연결을 만들고 **연결 추가** 단추를 클릭합니다. 다음 값으로 필드를 완성합니다.

    > [!div class="checklist"]
    >
    > * **표시 이름** 연결 이름을 지정합니다.
    > * **설명**. 간단한 설명을 추가합니다.
    > * **SAS URL** Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL을 붙여넣습니다.

    * 사용자 지정 모델 학습 데이터의 SAS URL을 검색하려면 Azure Portal의 스토리지 리소스로 이동하고 **Storage Explorer** 탭을 선택합니다. 컨테이너로 이동하고 마우스 오른쪽 단추를 클릭한 후 **공유 액세스 서명 가져오기** 를 선택합니다. 스토리지 계정 자체가 아니라 컨테이너에 대한 SAS를 가져오는 것이 중요합니다. **읽기**, **쓰기**, **삭제** 및 **목록 보기** 권한이 선택되어 있는지 확인하고 **만들기** 를 클릭합니다. 그런 다음, **URL** 섹션의 값을 임시 위치에 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.

       :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS 위치":::

1. **폴더 경로**(옵션)  원본 양식이 BLOB 컨테이너의 폴더 내에 있는 경우 폴더 이름을 지정합니다.

1. **Form Recognizer 서비스 URI** - Form Recognizer 엔드포인트 URL

1. **API 키** Form Recognizer 구독 키입니다.

1. **API 버전** v2.1(기본값) 값을 유지합니다.

1. **설명**(옵션) - 프로젝트를 설명합니다.

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="연결 설정":::

#### <a name="label-your-forms"></a>양식 레이블 지정

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="새 프로젝트 페이지":::

프로젝트를 만들거나 열면 주 태그 편집기 창이 열립니다. 텍스트 편집기는 다음 세 부분으로 구성되어 있습니다.

* 미리 보기 창(크기 조정 가능) - 원본 연결에서 스크롤 가능한 양식 목록을 포함합니다.
* 주 편집기 창 - 태그를 적용하는 데 사용할 수 있습니다.
* 태그 편집기 창 - 사용자가 태그에 대한 수정, 잠금, 다시 정렬 및 삭제를 수행할 수 있습니다.

##### <a name="identify-text-and-tables"></a>텍스트 및 테이블 식별

왼쪽 창에서 **모든 파일에 대해 OCR 실행** 을 선택하여 각 문서에 대한 텍스트 및 테이블 레이아웃 정보를 가져옵니다. 레이블 지정 도구는 각 텍스트 요소 주위에 경계 상자를 그립니다.

또한 레이블 지정 도구는 자동으로 추출된 테이블을 표시합니다. 문서의 왼쪽에 있는 테이블/그리드 아이콘을 선택하여 추출된 테이블을 확인합니다. 테이블 콘텐츠가 자동으로 추출되므로 테이블 콘텐츠의 레이블을 지정하지 않고 자동화된 추출을 사용합니다.

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="샘플 레이블 지정 도구의 테이블 시각화":::

##### <a name="apply-labels-to-text"></a>텍스트에 레이블 적용

다음으로, 태그(레이블)를 만들어 모델에서 분석할 텍스트 요소에 적용합니다. 샘플 레이블 데이터 세트에는 다른 필드에 추가할 레이블 지정된 필드가 이미 포함되어 있습니다.

태그 편집기 창을 사용하여 식별할 새 태그를 만듭니다.

1. 더하기 기호 **+** 를 선택하여 새 태그를 만듭니다.

1. 태그 "Total" 이름을 입력합니다.

1. **Enter** 를 선택하여 태그를 저장합니다.

1. 주 편집기에서 강조 표시된 텍스트 요소에서 전체 값을 선택합니다.

1. 값에 적용하려는 Total 태그를 선택하거나 해당 키보드 키를 누릅니다. 숫자 키는 처음 10개 태그에 대한 바로 가기 키로 할당됩니다. 태그 편집기 창에서 위쪽 및 아래쪽 화살표 아이콘을 사용하여 태그를 다시 정렬할 수 있습니다.

    > [!Tip]
    > 양식에 레이블을 지정할 때 다음 팁을 참조하세요.
    >
    > * 선택한 텍스트 요소마다 태그를 하나만 적용할 수 있습니다.
    >
    > * 각 태그는 페이지당 한 번만 적용할 수 있습니다. 동일한 양식에 값이 여러 번 표시되는 경우 각 인스턴스마다 서로 다른 태그를 만듭니다. 예: "invoice# 1", "invoice# 2" 등.
    > * 태그는 여러 페이지에 걸쳐 있을 수 없습니다.
    > * 양식에 나타나는 대로 값에 레이블을 지정합니다. 두 개의 다른 태그를 사용하여 값을 두 부분으로 분할하지 마십시오. 예를 들어, 주소 필드가 여러 줄에 걸쳐 있더라도 단일 태그로 레이블을 지정해야 합니다.
    > * 태그가 지정된 필드에 키를 포함하지 말고&mdash;값만 포함합니다.
    > * 테이블 데이터는 자동으로 검색되어야 하며 최종 출력 JSON 파일('pageResults' 섹션 안)에서 사용이 가능합니다. 그러나 모델이 모든 테이블 데이터를 검색하지 못하는 경우에는 모델의 레이블 지정과 학습을 통해 테이블을 검색할 수 있습니다. [사용자 지정 모델 학습 | 양식 레이블 지정](../label-tool.md#label-your-forms)을 참조하세요.
    > * **+** 의 오른쪽에 있는 단추를 사용하여 태그를 검색하고, 이름을 바꾸고, 순서를 변경하고, 삭제합니다.
    > * 태그 자체를 삭제하지 않고 적용된 태그를 제거하려면 문서 보기에서 태그가 지정된 사각형을 선택하고 delete 키를 누릅니다.
    >

1. 계속해서 위의 단계를 따라 샘플 데이터 세트에서 5가지 양식 모두의 레이블을 지정합니다.

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="예제 레이블 지정":::

#### <a name="train-a-custom-model"></a>사용자 지정 모델 학습

왼쪽 창에서 [학습] 아이콘을 선택하여 [학습] 페이지를 엽니다. 그런 다음, **학습** 단추를 선택하여 모델 학습을 시작합니다. 학습 프로세스가 완료되면 다음 정보가 표시됩니다.

* **모델 ID** - 만들어지고 학습된 모델의 ID입니다. 각 학습 호출은 고유한 ID를 사용하여 새 모델을 만듭니다. [REST API](./try-sdk-rest-api.md?pivots=programming-language-rest-api) 또는 [클라이언트 라이브러리](./try-sdk-rest-api.md)를 통해 예측 호출을 수행하려면 이 문자열이 필요하므로 안전한 위치에 복사합니다.

* **평균 정확도** - 모델의 평균 정확도입니다. 레이블을 추가 양식에 지정하고 새 모델을 만들기 위해 다시 학습시키면 모델 정확도를 향상시킬 수 있습니다. 먼저 결과를 분석하고 테스트하는 5개의 양식에 레이블을 지정한 후 필요하다면 그에 따라 양식을 더 추가하는 것이 좋습니다.
* 태그 목록 및 태그당 예상 정확도

    :::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="학습 보기 도구":::

#### <a name="analyze-a-custom-form"></a>사용자 지정 양식 분석

1. 왼쪽에 있는 **분석**(전구) 아이콘을 선택하여 모델을 테스트합니다. 

1. 원본 **로컬 파일** 을 선택하고 테스트 폴더에서 압축을 푼 샘플 데이터 세트에서 선택할 파일을 찾습니다. 

1. **분석 실행** 단추를 선택하여 양식의 키/값 쌍, 텍스트 및 테이블 예측을 가져옵니다. 이 도구는 태그를 경계 상자에 적용하고, 각 태그의 신뢰도를 보고합니다.

   :::image type="content" source="../media/analyze.png" alt-text="학습 보기.":::

정말 간단하죠. 미리 빌드된 Form Recognizer, 레이아웃 및 사용자 지정 모델에 Form Recognizer 샘플 도구를 사용하는 방법을 배웠습니다. 또한 수동으로 레이블이 지정되는 데이터를 사용하여 사용자 지정 양식을 분석하는 방법을 배웠습니다. 이제 Form Recognizer 클라이언트 라이브러리 SDK 또는 REST API를 사용해 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Form Recognizer 클라이언트 라이브러리 SDK 및 REST API 빠른 시작 살펴보기](../quickstarts/get-started-sdk-rest-api.md)
