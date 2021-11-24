---
title: '빠른 시작: Form Recognizer Studio | 미리 보기'
titleSuffix: Azure Applied AI Services
description: Form Recognizer Studio(미리 보기)를 사용하여 양식/문서 처리, 데이터 추출 및 분석
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021, mode-other
ms.openlocfilehash: ba220ad97011394b2f374b130b2f9f667c0a7ae7
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133040340"
---
# <a name="get-started-form-recognizer-studio--preview"></a>시작: Form Recognizer Studio | 미리 보기

>[!NOTE]
> Form Recognizer Studio는 현재 공개 미리 보기에 있습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 

[Form Recognizer Studio 미리 보기](https://formrecognizer.appliedai.azure.com/)는 애플리케이션에서 Form Recognizer 서비스의 기능을 시각적으로 검색, 이해 및 통합하기 위한 온라인 도구입니다. 샘플 문서 또는 사용자 고유의 문서를 사용하여 미리 학습된 모델 살펴보기를 시작합니다. [Python SDK 미리 보기](try-v3-python-sdk.md) 및 기타 빠른 시작을 사용하여 사용자 지정 양식 모델을 빌드하고 애플리케이션에서 모델을 참조하는 프로젝트를 만듭니다.

## <a name="migrating-from-the-sample-labeling-tool"></a>샘플 레이블 지정 도구에서 마이그레이션

[샘플 레이블 지정 도구](try-sample-label-tool.md)의 이전 사용자인 경우 Studio에서 기존 Azure 계정 및 Form Recognizer 또는 Cognitive Services 리소스를 사용하려면 [**Studio 미리 보기에 로그인**](try-v3-form-recognizer-studio.md#sign-into-the-form-recognizer-studio-preview)하기 위한 한 필수 구성 요소를 건너뜁니다. 

기존 사용자 지정 프로젝트를 Studio로 마이그레이션하려면 [**사용자 지정 모델 시작**](try-v3-form-recognizer-studio.md#custom-model-basics) 섹션으로 이동하여 새 프로젝트를 만들고 Azure에서 액세스할 수 있다고 가정하여 동일한 Azure Blob 스토리지 위치를 가리킵니다. 새 프로젝트가 구성되면 Studio에서 레이블 지정 및 학습을 위해 모든 문서와 중간 파일을 로드합니다.

## <a name="minimum-prerequisites-for-new-users"></a>새 사용자를 위한 최소 필수 구성 요소

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들 수**](https://azure.microsoft.com/free/) 있습니다.
* [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [**Cognitive Services 다중 서비스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 리소스

## <a name="additional-prerequisites-for-custom-projects"></a>사용자 지정 프로젝트에 대한 추가 필수 구성 요소

Azure 계정 및 Form Recognizer 또는 Cognitive Services 리소스 외에도 다음이 필요합니다.

### <a name="azure-blob-storage-container"></a>Azure Blob Storage 컨테이너

**표준 성능** [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)입니다. 스토리지 계정 내에서 Blob 데이터를 저장하고 구성하는 컨테이너를 만듭니다. 컨테이너를 사용하여 Azure 스토리지 계정을 만드는 방법을 모르는 경우 다음 빠른 시작을 따릅니다.

  * [**스토리지 계정 만들기**](../../../storage/common/storage-account-create.md). 스토리지 계정을 만드는 경우 **인스턴스 세부 정보 → 성능** 필드에서 **표준** 성능을 선택해야 합니다.
  * [**컨테이너 만들기**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 컨테이너를 만드는 경우 **새 컨테이너** 창에서 **퍼블릭 액세스 수준** 필드를 **컨테이너**(컨테이너 및 Blob에 대한 익명 읽기 액세스)로 설정합니다.

### <a name="configure-cors"></a>CORS 구성

[CORS(원본 간 리소스 공유)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)는 Form Recognizer Studio에서 액세스할 수 있도록 Azure 스토리지 계정에 구성되어야 합니다. Azure Portal에서 CORS를 구성하려면 스토리지 계정의 CORS 블레이드에 액세스해야 합니다.

:::image type="content" source="../media/quickstarts/cors-updated-image.png" alt-text="스토리지 계정에 대한 CORS 구성을 보여주는 스크린샷.":::

1. 스토리지 계정에 대한 CORS 블레이드를 선택합니다.
2. Blob 서비스에서 새 CORS 항목을 만들어 시작합니다.
3. **허용된 원본** 을 **https://formrecognizer.appliedai.azure.com** 로 설정합니다.
4. **허용된 메서드** 에 사용할 수 있는 8가지 옵션을 모두 선택합니다.
5. 각 필드에 *를 입력하여 **허용된 헤더** 및 **노출된 헤더** 를 모두 승인합니다.
6. **Max Age** 를 120초 또는 허용되는 값으로 설정합니다.
7. 페이지 위쪽에 있는 저장 단추를 클릭하여 변경 내용을 저장합니다.

이제 Form Recognizer Studio의 스토리지 계정을 사용하도록 CORS를 구성해야 합니다.

### <a name="sample-documents-set"></a>샘플 문서 세트

1. [Azure Portal](https://ms.portal.azure.com/#home)로 이동하여 **스토리지 계정** → **데이터 스토리지** → **컨테이너** 로 차례로 이동합니다.

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="스크린샷: Azure Portal의 데이터 스토리지 메뉴":::

1. 목록에서 **컨테이너** 를 선택합니다.

1. 페이지 위쪽의 메뉴에서 **업로드** 를 선택합니다.

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="스크린샷: Azure Portal의 컨테이너 업로드 단추":::

1. **Blob 업로드** 창이 표시됩니다.

1. 업로드할 파일을 선택합니다.

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="스크린샷: Azure Portal의 Blob 업로드 창":::

> [!NOTE]
> 기본적으로 Studio는 컨테이너의 루트에 있는 양식 문서를 사용합니다. 그러나 사용자 지정 양식 프로젝트 만들기 단계에서 지정한 경우 폴더에 구성된 데이터를 사용할 수 있습니다. [**하위 폴더에 데이터 구성**](../build-training-data-set.md#organize-your-data-in-subfolders-optional)을 *참조하세요*.

## <a name="sign-into-the-form-recognizer-studio-preview"></a>Form Recognizer Studio 미리 보기에 로그인

사전 요구 사항이 완료되면 [Form Recognizer Studio 미리 보기](https://formrecognizer.appliedai.azure.com)로 이동합니다.

1. Studio 홈페이지에서 Form Recognizer 서비스 기능을 선택합니다.

1. Azure 구독, 리소스 그룹 및 리소스를 선택합니다. (상단 메뉴의 "설정"에서 언제든지 리소스를 변경할 수 있습니다.)

1. 선택 항목을 검토하고 확인합니다.

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-studio-get-started-v2.gif" alt-text="Form Recognizer Studio 시작 예":::

## <a name="layout"></a>Layout

레이아웃 보기에서 다음을 수행합니다.

1. [분석] 명령을 선택하여 샘플 문서에서 레이아웃 분석을 실행하거나 [추가] 명령을 사용하여 문서를 사용해 봅니다.

1. 강조 표시된 추출된 텍스트, 추출된 테이블 위치를 보여 주는 테이블 아이콘 및 강조 표시된 선택 항목 표시를 관찰합니다.

1. 화면 아래쪽의 컨트롤을 사용하여 문서 보기를 확대/축소하고 회전시킵니다.

1. 텍스트, 테이블 및 선택 표시 계층을 표시하거나 숨겨서 각각에 대해 한 번에 포커스를 맞춥니다.

1. 출력 섹션의 [결과] 탭에서 JSON 출력을 찾아 서비스 응답 형식을 이해합니다. 복사하고 다운로드하여 통합을 빠르게 시작합니다.

:::image border="true" type="content" source="../media/quickstarts/layout-get-started-v2.gif" alt-text="Form Recognizer 레이아웃 예":::

## <a name="prebuilt-models"></a>미리 작성된 모델

몇 가지 미리 빌드된 모델 중에서 선택할 수 있으며, 각 모델에는 지원되는 고유한 필드 집합이 있습니다. 분석 작업에 사용할 모델은 분석할 문서 유형에 따라 달라집니다. 다음은 Form Recognizer 서비스에서 현재 지원되는 미리 빌드된 모델입니다.

* [🆕 **일반 문서**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) - 텍스트, 테이블, 구조, 키-값 쌍 및 명명된 엔터티를 분석하고 추출합니다.
* [**청구서**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice): 청구서에서 텍스트, 선택 표시, 테이블, 키-값 쌍 및 키 정보를 추출합니다.
* [**영수증**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt): 영수증에서 텍스트 및 키 정보를 추출합니다.
* [**ID 문서**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument): 운전 면허증과 국제 여권에서 텍스트 및 키 정보를 추출합니다.
* [**명함**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard): 명함에서 텍스트 및 키 정보를 추출합니다.

미리 빌드된 모델 보기에서 다음을 수행합니다.

1. Studio 홈에서 미리 빌드된 모델 중 하나를 선택합니다. 이 예에서는 청구서 모델을 사용하고 있습니다.

1. [분석] 명령을 선택하여 샘플 문서에 대한 분석을 실행하거나 [추가] 명령을 사용하여 청구서를 사용해 봅니다.

1. 시각화 섹션에서 강조 표시된 필드와 값/송장 항목을 관찰합니다. 추출된 텍스트와 테이블도 모두 표시됩니다.

1. 출력 섹션의 [필드] 탭에서 나열된 필드와 값을 확인하고, 테이블과 같은 형식으로 볼 항목을 선택합니다.

1. 출력 섹션의 [결과] 탭에서 JSON 출력을 찾아 서비스 응답 형식을 이해합니다. 복사하고 다운로드하여 통합을 빠르게 시작합니다.

:::image border="true" type="content" source="../media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Form Recognizer 미리 빌드된 모델 예":::

## <a name="custom-model-basics"></a>사용자 지정 모델 기본 사항

### <a name="getting-started"></a>시작

사용자 지정 모델을 만들려면 프로젝트 구성부터 시작합니다.

1. Studio 홈에서 [사용자 지정 양식 프로젝트](https://formrecognizer.appliedai.azure.com/studio/customform/projects)를 선택하여 사용자 지정 양식 홈페이지를 엽니다.

1. "프로젝트 만들기" 명령을 사용하여 새 프로젝트 구성 마법사를 시작합니다.

1. 프로젝트 세부 정보를 입력하고, Azure 구독 및 리소스, 데이터가 포함된 Azure Blob 스토리지 컨테이너를 선택합니다.

1. 설정을 검토하고 제출하여 프로젝트를 만듭니다.

:::image border="true" type="content" source="../media/quickstarts/1-custom-model-get-started-v2.gif" alt-text="Form Recognizer 사용자 지정 프로젝트 시작 예":::

### <a name="basic-flow"></a>기본 흐름

프로젝트 만들기 단계가 완료되면 사용자 지정 모델 단계에서 다음을 수행합니다.

1. 레이블 지정 보기에서 추출하려는 레이블 및 해당 형식을 정의합니다.

1. 문서의 텍스트를 선택하고 드롭다운 목록 또는 레이블 창에서 레이블을 선택합니다.

1. 레이블을 4개 이상의 문서에 지정하여 레이블을 5개 이상의 문서에 지정합니다.

1. [학습] 명령을 선택하고, 모델 이름과 설명을 입력하여 사용자 지정 모델 학습을 시작합니다.

1. 모델이 준비되면 [테스트] 명령을 사용하여 테스트 문서를 통해 모델의 유효성을 검사하고 결과를 관찰합니다.

:::image border="true" type="content" source="../media/quickstarts/2-custom-model-basic-steps-v2.gif" alt-text="Form Recognizer 사용자 지정 프로젝트 기본 워크플로 예":::

### <a name="other-features"></a>기타 기능

또한 왼쪽의 [모델] 탭을 사용하여 모든 모델을 봅니다. 목록 보기에서 모델을 선택하여 다음 작업을 수행합니다.

1. 목록 보기에서 모델을 테스트합니다.

1. [삭제] 명령을 사용하여 필요하지 않은 모델을 삭제합니다.

1. 오프라인 보기를 위한 모델 세부 정보를 다운로드합니다.

1. 여러 모델을 선택하고, 애플리케이션에서 사용할 새 모델로 구성합니다.

## <a name="labeling-as-tables"></a>테이블로 레이블 지정

사용자 지정 모델을 만드는 동안 문서에서 데이터 컬렉션을 추출해야 할 수 있습니다. 이러한 형식은 몇 가지 형식으로 나타날 수 있습니다. 테이블을 시각적 패턴으로 사용:

* 지정된 필드(열) 세트에 대한 동적 또는 변수 값(행)의 개수

* 지정된 필드(열 및/또는 행) 세트에 대한 특정 값(행)의 컬렉션

### <a name="label-as-dynamic-table"></a>동적 테이블로 레이블 지정

동적 테이블을 사용하여 지정된 필드(열) 세트에 대한 값(행)의 변수 수를 추출합니다.

1. 새 "테이블" 형식 레이블을 추가하고, "동적 테이블" 형식을 선택한 다음, 레이블 이름을 지정합니다.

1. 필요한 열(필드) 및 행(데이터용)의 수를 추가합니다.

1. 페이지에서 텍스트를 선택한 다음 텍스트에 할당할 셀을 선택합니다. 모든 문서의 모든 페이지에 있는 모든 행과 열에 대해 반복합니다.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="Form Recognizer 동적 테이블로 레이블 지정 예":::

### <a name="label-as-fixed-table"></a>고정 테이블로 레이블 지정

고정 테이블을 사용하여 지정된 필드(열 및/또는 행) 세트에 대한 특정 값(행)의 컬렉션을 추출합니다.

1. 새 "테이블" 형식 레이블을 만들고, "고정 테이블" 형식을 선택한 다음, 이름을 지정합니다.

1. 두 필드 세트에 해당하는 필요한 열과 행의 수를 추가합니다.

1. 페이지에서 텍스트를 선택한 다음 텍스트에 할당할 셀을 선택합니다. 다른 문서에 대해 반복합니다.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="Form Recognizer 고정 테이블로 레이블 지정 예":::

## <a name="labeling-for-signature-detection"></a>서명 검색에 대한 레이블 지정

서명 검색에 대한 레이블을 지정하려면 다음을 수행합니다.

1. 레이블 보기에서 새 "서명" 형식 레이블을 만들고, 이름을 지정합니다.

1. [영역] 명령을 사용하여 사각형 영역을 서명의 예상 위치에 만듭니다.

1. 그린 영역을 선택하고 서명 형식 레이블을 선택하여 그린 영역에 할당합니다. 다른 문서에 대해 반복합니다.

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="Form Recognizer 서명 검색에 대한 레이블 지정 예":::

## <a name="next-steps"></a>다음 단계

* 이전 버전의 REST API와의 차이점을 알아보려면 [**Form Recognizer v3.0 마이그레이션 가이드**](../v3-migration-guide.md)를 따르세요.
* 새 SDK를 통해 애플리케이션에서 미리 보기 기능을 사용해 보려면 [**미리 보기 SDK 빠른 시작**](try-v3-python-sdk.md)을 살펴보세요.
* 새 REST API를 통해 미리 보기 기능을 사용해 보려면 [**미리 보기 REST API 빠른 시작**](try-v3-rest-api.md)을 참조하세요.

[Form Recognizer Studio 미리 보기를 시작](https://formrecognizer.appliedai.azure.com)합니다.
