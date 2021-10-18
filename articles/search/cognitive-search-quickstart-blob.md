---
title: '빠른 시작: 텍스트 번역 및 엔터티 인식'
titleSuffix: Azure Cognitive Search
description: 데이터 가져오기 마법사 및 AI 인식 기술을 사용하여 언어를 감지하고, 텍스트를 번역하고, 엔터티를 인식합니다. AI를 통해 만든 새 필드는 Azure Cognitive Search 인덱스로 검색 가능한 텍스트가 됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/07/2021
ms.openlocfilehash: 01024a829c4e4447cfa7dc642469384170bc99f4
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708164"
---
# <a name="quickstart-translate-text-and-recognize-entities-using-the-import-data-wizard"></a>빠른 시작: 데이터 가져오기 마법사를 사용하여 텍스트 번역 및 엔터티 인식

Azure Cognitive Search의 AI 보강에서 언어 감지, 텍스트 번역 및 엔터티 인식을 추가하여 검색 인덱스로 검색 가능한 콘텐츠를 만드는 방법을 알아봅니다. 

이 빠른 시작에서는 **데이터 가져오기** 마법사를 실행하여 스페인에 있는 여러 국립 박물관에 대한 프랑스어 및 스페인어 설명을 분석합니다. 출력은 번역된 텍스트와 엔터티를 포함하는 검색 가능한 인덱스로, [검색 탐색기](search-explorer.md)를 사용하여 포털에서 쿼리할 수 있습니다. 

준비하려면 마법사를 실행하기 전에 몇 가지 리소스를 만들고 샘플 파일을 업로드합니다.

코드로 시작하겠습니까? 대신 [.NET 자습서](cognitive-search-tutorial-blob-dotnet.md), [Python 자습서](cognitive-search-tutorial-blob-python.md) 또는 [REST 자습서](cognitive-search-tutorial-blob-dotnet.md)를 사용해 보세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음과 같은 필수 구성 요소를 갖추어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search 서비스. [서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ Blob Storage가 있는 Azure Storage 계정. [스토리지 계정을 만들](../storage/common/storage-account-create.md?tabs=azure-portal)거나 [기존 스토리지 계정을 찾습니다.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 

  + 마법사에서 스토리지 계정을 찾고 연결을 설정하려면 동일한 구독을 선택합니다.
  + 대역폭 요금이 부과되지 않도록 Azure Cognitive Search와 동일한 지역을 선택합니다.
  + StorageV2(범용 V2)를 선택합니다.

> [!NOTE]
> 또한 이 빠른 시작은 AI에 대한 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 사용합니다. 워크로드가 너무 작으므로 Cognitive Services는 최대 20개의 트랜잭션을 무료로 처리하기 위해 백그라운드에서 탭으로 처리됩니다. 즉, 추가 Cognitive Services 리소스를 만들지 않고도 이 연습을 완료할 수 있습니다.

## <a name="set-up-your-data"></a>데이터를 설정합니다.

다음 단계에서는 다른 유형의 콘텐츠 파일을 저장할 Azure Storage의 blob 컨테이너를 설정합니다.

1. GitHub에서 [샘플 데이터를 다운로드](https://github.com/Azure-Samples/azure-search-sample-data)합니다. 여러 데이터 세트가 있습니다. 이 빠른 시작의 경우 **spanish-museums** 폴더의 파일을 사용합니다.

1. 샘플 데이터를 Blob 컨테이너에 업로드합니다.

   1. [Azure Portal](https://portal.azure.com/)에 로그인하고 스토리지 계정을 찾습니다.
   1. 왼쪽 탐색 창에서 **컨테이너** 를 선택합니다.
   1. "spanish-museums"이라는 [컨테이너를 만듭니다](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 기본 공용 액세스 수준을 사용합니다.
   1. "spanish-museums" 컨테이너에서 **업로드** 를 선택하여 로컬 **spanish-museums** 폴더에서 파일을 업로드합니다.

스페인에 있는 국립 박물관에 대한 프랑스어 및 스페인어 설명이 포함된 10개의 파일이 있어야 합니다.

   :::image type="content" source="media/cognitive-search-quickstart-blob/museums-container.png" alt-text="Blob 컨테이너의 docx 파일 목록" border="true":::

이제 데이터 가져오기 마법사로 이동할 준비가 되었습니다.

## <a name="run-the-import-data-wizard"></a>데이터 가져오기 마법사 실행

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

1. [검색 서비스를 찾고](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 개요 페이지의 명령 바에서 **데이터 가져오기** 를 클릭하여 4단계의 통해 인지 보강을 설정합니다.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="데이터 가져오기 명령의 스크린샷" border="true":::

### <a name="step-1---create-a-data-source"></a>1단계: 데이터 원본 만들기

1. **데이터에 연결** 에서 **Azure Blob 스토리지** 를 선택합니다. 사용자가 만든 스토리지 계정 및 컨테이너에 대한 기존 연결을 선택합니다. 데이터 원본의 이름을 지정하고, 나머지는 기본값을 사용합니다. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/connect-to-spanish-museums.png" alt-text="Azure Blob 구성" border="true":::

### <a name="step-2---add-cognitive-skills"></a>2단계: 인식 기술 추가

다음으로, 언어 감지, 텍스트 번역 및 엔터티 인식을 호출하도록 AI 보강을 구성합니다. 

1. 이 빠른 시작에서는 **체험** Cognitive Services 리소스를 사용합니다. 샘플 데이터는 10개의 파일로 구성되어 있으므로 이 빠른 시작에서는 Cognitive Services에서 매일 인덱서당 20개의 트랜잭션을 무료로 할당하는 것으로 충분합니다. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/free-enrichments.png" alt-text="무료 Cognitive Services 처리 연결" border="true":::

1. 같은 페이지에서 **보강 추가** 를 확장하고 5가지 항목을 추가합니다.

   엔터티 인식(사람, 조직, 위치) 선택

   언어 감지 및 텍스트 번역 선택

   :::image type="content" source="media/cognitive-search-quickstart-blob/select-entity-lang-enrichments.png" alt-text="기술 세트에 대한 Cognitive Services 선택 서비스 연결" border="true":::

   Blob에서 "콘텐츠" 필드에는 파일의 콘텐츠가 포함됩니다. 샘플 데이터에서 콘텐츠는 프랑스어 또는 스페인어로 지정된 박물관에 대한 여러 단락입니다. "세분성"은 필드 자체입니다. 일부 기술은 더 작은 텍스트 청크에서 더 잘 작동하지만 이 빠른 시작의 기술에는 필드 세분성으로 충분합니다.

### <a name="step-3---configure-the-index"></a>3단계 - 인덱스 구성

인덱스에는 검색 가능한 콘텐츠가 포함되어 있으며, **데이터 가져오기** 마법사는 일반적으로 데이터를 샘플링하여 스키마를 유출할 수 있습니다. 이 단계에서는 생성된 스키마를 검토하고, 잠재적으로 설정을 수정할 수 있습니다. 데모 데이터 세트에 대해 만들어진 기본 스키마는 다음과 같습니다.

이 빠른 시작에서 마법사는 기본값을 적절하게 설정합니다.  

+ 기본 필드는 기존 Blob의 속성과 보강 출력을 포함하는 새 필드(예: `people`, `organizations`, `locations`)를 기반으로 합니다. 데이터 형식은 메타데이터 및 데이터 샘플링에서 유추됩니다.

+ 기본 문서 키는 *metadata_storage_path* 입니다(필드에 고유 값이 포함되어 있으므로 선택됨).

+ 기본 특성은 **조회 가능** 하고 **검색 가능** 합니다. **검색 가능** 은 필드의 전체 텍스트 검색을 허용합니다. **조회 가능** 은 필드 값을 결과에 반환할 수 있다는 뜻입니다. 마법사는 사용자가 기술 세트를 통해 필드를 생성했기 때문에 이 필드를 조회 및 검색 가능하도록 하려고 한다고 가정합니다.

+ "언어"에 대해 필터링 가능한 확인란을 선택합니다. 마법사는 폴더를 설정하지 않지만 언어별로 필터링하는 기능은 여러 언어가 있는 경우 이 데모에서 유용합니다.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields-lang-entities.png" alt-text="인덱스 필드" border="true":::

필드를 **조회 가능** 으로 표시한다고 해서 필드가 검색 결과에 *나타나야* 하는 것은 아닙니다. **$select** 쿼리 매개 변수로 포함할 필드를 지정하여 검색 결과의 구성을 정확하게 제어할 수 있습니다. `content`와 같이 텍스트가 많은 필드의 경우 **$select** 매개 변수는 애플리케이션의 사용자에게 관리 가능한 검색 결과를 구체화하는 솔루션으로, 클라이언트 코드에서 **조회 가능** 특성을 통해 필요한 모든 정보에 액세스할 수 있도록 해줍니다.

### <a name="step-4---configure-the-indexer"></a>4단계 - 인덱서 구성

인덱서는 인덱싱 프로세스를 구동하는 상위 수준 리소스입니다. 인덱서는 데이터 원본 이름, 대상 인덱스 및 실행 빈도를 지정합니다. **데이터 가져오기** 마법사는 여러 개체를 만들고, 항상 이러한 개체 중에서 반복적으로 실행할 수 있는 인덱서입니다.

1. **인덱서** 페이지에서 기본 이름을 적용하고, **한 번** 일정 옵션을 클릭하여 즉시 실행할 수 있습니다. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-spanish-museum.png" alt-text="인덱서 정의" border="true":::

1. **제출** 을 클릭하여 인덱서를 만드는 동시에 실행합니다.

## <a name="monitor-status"></a>상태 모니터링

인지 기술 인덱싱은 일반적인 텍스트 기반 인덱싱보다 완료하는 데 더 오래 걸립니다. 진행 상황을 모니터링하려면 [개요] 페이지로 이동하여 페이지 중간에 있는 **인덱서** 탭을 선택합니다.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-status-spanish-museums.png" alt-text="인덱서 상태" border="true":::

실행 상태에 대한 세부 정보를 확인하려면 목록에서 인덱서를 선택합니다.

## <a name="query-in-search-explorer"></a>Search 탐색기에서 쿼리

인덱스가 생성된 후에는 쿼리를 실행하여 결과를 반환할 수 있습니다. 이 작업의 경우 포털에서 **검색 탐색기** 를 사용합니다. 

1. 검색 서비스 대시보드 페이지의 명령 모음에서 **Search 탐색기** 를 클릭합니다.

1. 맨 위에서 **인덱스 변경** 을 선택하고 앞에서 만든 인덱스를 선택합니다.

1. 쿼리 문자열에 `search="picasso museum" &$select=people,organizations,locations,language,translated_text &$count=true &$filter=language eq 'fr'`과 같은 인덱스를 쿼리할 검색 문자열을 입력한 다음, **검색** 을 선택합니다.

   :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-query-string-spanish-museums.png" alt-text="검색 탐색기의 쿼리 문자열" border="true":::

결과는 자세한 정보 표시가 가능하고 읽기 어려울 수 있는 JSON으로 반환되며, Azure Blob에서 오는 대용량 문서인 경우에 특히 그렇습니다. 이 도구를 검색하는 몇 가지 팁은 다음과 같습니다.

+ `$select`를 추가하여 결과에 포함할 필드를 지정합니다. 
+ JSON 내에서 특정 속성 또는 용어를 검색하려면 CTRL-F를 사용합니다.

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-results-spanish-museums.png" alt-text="Search 탐색기 예제" border="true":::

쿼리 문자열은 대/소문자를 구분하므로 "알 수 없는 필드" 메시지가 표시되면 **필드** 또는 **인덱스 정의(JSON)** 를 검사하여 이름과 대/소문자를 확인합니다. 

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Cognitive Search에는 데이터 가져오기 마법사에서 연습할 수 있는 다른 기본 제공 기술이 있습니다. 다음 단계로 OCR 및 이미지 분석 기술을 사용하여 이미지 파일에서 텍스트 검색 가능한 콘텐츠를 만듭니다.

> [!div class="nextstepaction"]
> [빠른 시작: OCR 및 이미지 분석을 사용하여 검색 가능한 콘텐츠 만들기](cognitive-search-quickstart-ocr.md)