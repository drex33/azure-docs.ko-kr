---
title: '빠른 시작: Azure Portal에서 지식 저장소 만들기'
titleSuffix: Azure Cognitive Search
description: 데이터 가져오기 마법사를 사용하여 보강 콘텐츠를 유지하는 데 사용되는 지식 저장소를 만듭니다. 분석을 목적으로 다른 앱에서 지식 저장소에 연결하거나, 보강 콘텐츠를 다운스트림 프로세스에 보냅니다.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/28/2021
ms.openlocfilehash: 6e9c09cb5407747c325b696570cd3eabdeeb4ed7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563723"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 지식 저장소 만들기

[지식 저장소](knowledge-store-concept-intro.md)는 [AI 보강 파이프라인](cognitive-search-concept-intro.md)의 출력을 허용하고 Azure Storage에서 다운스트림 앱 및 워크로드에 사용할 수 있게 하는 Azure Cognitive Search 기능입니다. 번역된 텍스트, OCR 텍스트, 태그가 지정된 이미지 및 인식된 엔터티와 같이 파이프라인에서 만든 보강은 Azure Storage에 연결하는 앱 또는 워크로드에서 액세스할 수 있는 테이블 또는 Blob에 프로젝션됩니다.

이 빠른 시작에서는 데이터를 설정한 다음, **데이터 가져오기** 마법사를 실행하여 지식 저장소도 생성하는 보강 파이프라인을 만듭니다. 지식 저장소에는 원본에서 가져온 원본 텍스트 콘텐츠(호텔에 대한 고객 리뷰)와 감정 레이블, 핵심 구 추출 및 영어가 아닌 고객 의견의 텍스트 번역이 포함된 AI 생성 콘텐츠가 포함됩니다.

> [!NOTE]
> 이 빠른 시작에서는 Azure Storage에서 완성된 지식 저장소로 가는 가장 빠른 경로를 보여 줍니다. 각 단계에 대한 자세한 설명은 [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)를 대신 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서는 다음 서비스를 사용합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search. 계정에서 [서비스를 만들거나](search-create-service-portal.md) [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ Azure Storage. [계정을 만들거나](../storage/common/storage-account-create.md) [기존 스토리지 계정을 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 계정 유형은 **StorageV2(범용 V2)** 여야 합니다.

+ 데이터 샘플링 이 빠른 시작은 CSV 파일로 저장된 호텔 리뷰 데이터(Kaggle.com에서 가져온 데이터)를 사용하고 단일 호텔에 대한 19개 고객 피드백을 포함하고 있습니다.

  [HotelReviews_Free.csv를 다운로드](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)한 다음, Azure Storage의 [Blob 컨테이너에 업로드합니다](../storage/blobs/storage-quickstart-blobs-portal.md).

또한 이 빠른 시작에서는 AI 보강을 위해 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 사용합니다. 워크로드가 너무 작으므로 Cognitive Services는 최대 20개의 트랜잭션을 무료로 처리하기 위해 백그라운드에서 탭으로 처리됩니다. 즉, 추가 Cognitive Services 리소스를 만들지 않고도 이 연습을 완료할 수 있습니다.

## <a name="start-the-wizard"></a>마법사 시작

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

1. [검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 이후 개요 페이지의 명령 모음에서 **데이터 가져오기** 를 클릭하여 다음 네 단계에 따라 지식 저장소를 만듭니다.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="데이터 가져오기 명령의 스크린샷" border="true":::

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

데이터는 한 CSV 파일에 있는 여러 행이므로 각 행에 대해 하나의 검색 문서를 가져오도록 *구문 분석 모드* 를 설정합니다.

1. **데이터에 연결** 에서 **Azure Blob Storage** 를 선택하고, 만든 계정 및 컨테이너를 선택합니다. 

1. **이름** 에 "hotel-reviews-ds"를 입력합니다.

1. **구문 분석 모드** 에 대해 **분리된 텍스트** 를 선택한 다음, **첫 줄에 헤더 포함** 확인란을 선택합니다. **구분 기호 문자** 가 쉼표(,)인지 확인합니다.

1. **연결 문자열** 에서 연결 문자열을 Azure Storage 계정에 붙여넣습니다. 

   연결 문자열의 형식은 `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`입니다.

1. **컨테이너** 에서 데이터를 보유하는 Blob 컨테이너의 이름("hotel-reviews")을 입력합니다.

    이 페이지는 다음 스크린샷과 비슷합니다.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="데이터 원본 정의의 스크린샷" border="true":::

1. 다음 페이지를 계속합니다.

### <a name="step-2-add-skills"></a>2단계: 기술 추가

이 마법사 단계에서는 AI 보강 기술을 추가합니다. 원본 데이터는 영어와 프랑스어로 된 고객 리뷰로 구성됩니다. 이 데이터 세트와 관련된 기술에는 핵심 구 추출, 감정 감지 및 텍스트 번역이 포함됩니다. 이후 단계에서 이러한 보강은 지식 저장소에 Azure 테이블로 "프로젝션"됩니다.

1. **Cognitive Services 연결** 섹션을 펼칩니다. **무료(제한적 보강)** 가 기본적으로 선택되어 있습니다. HotelReviews-Free.csv의 레코드 수가 19개이고 이 무료 리소스에서 하루 최대 20개의 트랜잭션을 허용하므로 이 리소스를 사용할 수 있습니다.

1. **보강 추가** 를 확장합니다.

1. **기술 세트 이름** 으로 "hotel-reviews-ss"를 입력합니다.

1. **원본 데이터 필드** 에 대해 **reviews_text** 를 선택합니다.

1. **보강 세분성 수준** 에 대해 **페이지(5,000자 청크)** 를 선택합니다.

1. **텍스트 인지 기술** 에 대해 다음 기술을 선택합니다.

    + **핵심 구 추출**
    + **텍스트 번역**
    + **언어 감지**
    + **감정 감지**

   페이지는 다음 스크린샷과 같습니다.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ss.png" alt-text="기술 세트 정의의 스크린샷" border="true":::

1. 아래로 스크롤하여 **지식 저장소에 보강 저장** 을 펼칩니다.

1. **기존 연결 선택** 을 선택한 다음, Azure Storage 계정을 선택합니다. 컨테이너 페이지가 표시되므로 프로젝션을 위한 컨테이너를 만들 수 있습니다. 원본 콘텐츠와 기술 자료 저장소 콘텐츠 간을 구분하기 위해 "kstore-hotel-reviews"와 같은 접두사 명명 규칙을 채택하는 것이 좋습니다.

1. 데이터 가져오기 마법사로 돌아가서 다음 **Azure 테이블 프로젝션** 을 선택합니다. 마법사는 항상 **문서** 프로젝션을 제공합니다. 선택한 기술(예: **핵심 구**) 또는 보강 세분성(**페이지**)에 따라 다른 프로젝션이 제공됩니다.

    + **문서**
    + **페이지**
    + **핵심 구**

   다음 스크린샷은 마법사의 테이블 프로젝션 선택 항목을 보여 줍니다.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="지식 저장소 정의의 스크린샷" border="true":::

1. 다음 페이지를 계속합니다.

### <a name="step-3-configure-the-index"></a>3단계: 인덱스 구성

이 마법사 단계에서는 선택적 전체 텍스트 검색 쿼리에 대한 인덱스를 구성합니다. 마법사는 데이터 원본을 샘플링하여 필드와 데이터 형식을 유추합니다. 원하는 동작의 특성만 선택하면 됩니다. 예를 들어 **조회 가능** 특성을 사용하면 검색 서비스에서 필드 값을 반환할 수 있지만, **검색 가능** 을 사용하면 필드에서 전체 텍스트를 검색할 수 있습니다.

1. **인덱스 이름** 으로 "hotel-reviews-idx"를 입력합니다.

1. 특성의 경우 기본 선택 항목을 적용합니다. 파이프라인에서 만드는 새 필드에 대해 **조회 가능** 및 **검색 가능** 입니다.

    인덱스는 다음 이미지와 비슷합니다. 긴 목록이므로 일부 필드가 이미지에 표시되지 않습니다.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="인덱스 정의의 스크린샷" border="true":::

1. 다음 페이지를 계속합니다.

### <a name="step-4-configure-and-run-the-indexer"></a>4단계: 인덱서 구성 및 실행

이 마법사 단계에서는 이전 마법사 단계에서 정의한 데이터 원본, 기술 세트 및 인덱스를 모두 가져오는 인덱서를 구성합니다.

1. **이름** 으로 "hotel-reviews-idxr"을 입력합니다.

1. **일정** 에 대해 **한 번**(기본값)을 유지합니다.

1. **제출** 을 선택하여 인덱서를 실행합니다. 데이터 추출, 인덱싱, 인지 기술 적용은 모두 이 단계에서 수행됩니다.

### <a name="step-5-check-status"></a>5단계: 상태 확인

**개요** 페이지에서 페이지 중간에 있는 **인덱서** 탭을 연 다음, **hotels-reviews-idxr** 을 선택합니다. 상태가 1~2분 내에 오류 및 경고 없이 "진행 중"에서 "성공"으로 진행되어야 합니다.

## <a name="check-tables-in-storage-browser"></a>Storage Browser에서 테이블 확인

Azure Portal에서 Azure Storage 계정으로 전환하고, **Storage Browser** 를 사용하여 새 테이블을 봅니다. "보강 추가" 페이지의 "보강 저장" 섹션에 제공된 각 프로젝션에 대해 하나씩 세 개의 테이블이 표시됩니다.

+ "hotelReviewssDocuments"는 컬렉션이 아닌 문서 보강 트리의 모든 첫 번째 수준 노드를 포함합니다. 

+ "hotelReviewssKeyPhrases"는 모든 리뷰에서 추출한 핵심 구의 긴 목록을 포함합니다. 핵심 구 및 엔터티와 같은 컬렉션(배열)을 출력하는 기술에는 독립 실행형 테이블에 보내는 출력이 있습니다.

+ "hotelReviewssPages"는 문서에서 분할된 각 페이지에 대해 만들어진 보강 필드를 포함합니다. 이 기술 세트 및 데이터 원본에서는 감정 레이블 및 번역된 텍스트로 구성된 페이지 수준 보강이 있습니다. 기술 세트 정의에서 "페이지" 세분성을 선택하는 경우 페이지 테이블(또는 특정 세분성 수준을 지정하는 경우 문장 테이블)이 만들어집니다. 

이러한 테이블은 모두 다른 도구 및 앱에서 테이블 관계를 지원하기 위한 ID 열을 포함합니다. 테이블을 열 때 이러한 필드를 지나면서 스크롤하여 파이프라인에서 추가한 콘텐츠 필드를 봅니다.

이 빠른 시작에서 "hotelReviewssPages"에 대한 테이블은 다음 스크린샷과 비슷해야 합니다.

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="에서 생성된 테이블의 스크린샷" border="true":::

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기에서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다.

> [!TIP]
> 이 연습을 반복하거나 다른 AI 보강 연습을 시도하려면 **hotel-reviews-idxr** 인덱서 및 관련 개체를 삭제하여 다시 만드세요. 인덱서를 삭제하면 사용 가능한 일별 트랜잭션 카운터가 0으로 다시 설정됩니다.

## <a name="next-steps"></a>다음 단계

이제 지식 저장소를 소개했으므로 REST API 연습으로 전환하여 각 단계를 자세히 살펴봅니다. 마법사에서 내부적으로 처리한 작업은 REST 연습에서 설명합니다.

> [!div class="nextstepaction"]
> [REST 및 Postman을 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)
