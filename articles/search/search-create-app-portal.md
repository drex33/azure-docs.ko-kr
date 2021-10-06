---
title: '빠른 시작: Azure Portal에서 데모 앱 만들기'
titleSuffix: Azure Cognitive Search
description: 데모 앱 만들기 \마법사를 실행하여 운영 웹앱에 대한 HTML 페이지와 스크립트를 생성합니다. 이 페이지에는 검색 창, 결과 영역, 사이드바 및 자동 완성 지원이 포함됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/24/2021
ms.openlocfilehash: 129c0af62663fcc37621db0d2f37e5fdee55d05c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209645"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>빠른 시작: 포털에서 데모 앱 만들기(Azure Cognitive Search)

Azure Portal의 **데모 앱 만들기** 마법사를 사용하여 브라우저에서 실행되는 다운로드 가능한 "localhost" 스타일 웹앱을 생성합니다. 구성에 따라 생성된 앱은 처음 사용할 때 원격 인덱스에 대한 라이브 읽기 전용 연결을 사용하여 작동합니다. 기본 앱에는 검색 창, 결과 영역, 사이드바 필터 및 자동 완성 지원이 포함될 수 있습니다.

데모 앱은 클라이언트 앱에서 인덱스가 작동하는 방식을 시각화하는 데 도움을 줄 수 있지만, 프로덕션 시나리오에는 적합하지 않습니다. 클라이언트 앱에는 생성된 HTML 페이지가 제공하지 않는 보안, 오류 처리 및 호스팅 논리가 포함되어야 합니다. 클라이언트 앱을 만들 준비가 되면 다음 단계는 [.NET SDK를 사용하여 첫 번째 검색 앱 만들기](tutorial-csharp-create-first-app.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음이 있어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

+ Azure Cognitive Search 서비스 [서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ [Microsoft Edge(최신 버전)](https://www.microsoft.com/edge) 또는 Google Chrome.

+ 생성된 애플리케이션의 기반으로 사용할 [검색 인덱스](search-what-is-an-index.md)입니다. 

  이 빠른 시작에서는 미리 보기 이미지가 있으므로 기본 제공 부동산 샘플 데이터와 인덱스를 사용합니다(마법사에서 결과 페이지에 이미지 추가를 지원함). 이 연습에 사용되는 인덱스를 만들려면 **데이터 가져오기** 마법사를 실행하여 *realestate-us-sample* 데이터 원본을 선택합니다.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="샘플 데이터에 대한 데이터 원본 페이지" border="false":::

인덱스를 사용할 준비가 되면 다음 단계로 이동합니다.

## <a name="start-the-wizard"></a>마법사 시작

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

1. [검색 서비스를 찾고](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) 개요 페이지의 가운데에 있는 링크에서 **인덱스** 를 선택합니다. 

1. 기존 인덱스 목록에서 *realestate-us-sample-index* 를 선택합니다.

1. 인덱스 페이지의 위쪽에서 **데모 앱 만들기(미리 보기)** 를 선택하여 마법사를 시작합니다.

1. 첫 번째 마법사 페이지에서 **CORS(원본 간 리소스 공유) 사용** 을 선택하여 CORS 지원을 인덱스 정의에 추가합니다. 이 단계는 선택 사항이지만, 이를 수행하지 않으면 로컬 웹앱이 원격 인덱스에 연결되지 않습니다.

## <a name="configure-search-results"></a>검색 결과 구성

마법사는 미리 보기 이미지, 제목 및 설명을 위한 공간이 포함된 기본 레이아웃을 렌더링된 검색 결과에 제공합니다. 이러한 각 요소를 지원하는 것은 데이터를 제공하는 인덱스의 필드입니다. 

1. [미리 보기]의 *realestate-us-sample* 인덱스에서 *thumbnail* 필드를 선택합니다. 이 샘플은 *thumbnail* 이라는 필드에 저장된 URL 주소 이미지 형태의 이미지 미리 보기를 포함하고 있습니다. 인덱스에 이미지가 없으면 이 필드를 비워 둡니다.

1. [제목]에서 각 문서의 고유성을 나타내는 필드를 선택합니다. 이 샘플에서는 목록 ID가 적절한 선택입니다.

1. [설명]에서 다른 사용자가 특정 문서를 클릭할지 여부를 결정하는 데 도움이 될 수 있는 세부 정보를 제공하는 필드를 선택합니다.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="샘플 데이터에 대한 결과 구성" border="false":::

## <a name="add-a-sidebar"></a>사이드바 추가

검색 서비스는 사이드바로 자주 렌더링되는 패싯 탐색을 지원합니다. 패싯은 인덱스 스키마에서 표시한 대로 필터링 가능 및 패싯 가능 필드를 기반으로 합니다.

Azure Cognitive Search에서 패싯 탐색은 누적 필터링 환경입니다. 범주 내에서 여러 필터를 선택하면 결과가 확장됩니다(예: 도시 내에서 시애틀 및 벨뷰 선택). 여러 범주에서 여러 필터를 선택하면 결과가 좁혀집니다.

> [!TIP]
> 전체 인덱스 스키마는 포털에서 볼 수 있습니다. 각 인덱스의 개요 페이지에서 **인덱스 정의(JSON)** 링크를 찾습니다. 패싯 탐색에 적합한 필드에는 "filterable: true" 및 "facetable: true" 특성이 있습니다.

1. 마법사에서 페이지 맨 위에 있는 **사이드바** 탭을 선택합니다. 인덱스에서 필터링 가능하고 패싯 가능한 것으로 특성이 지정된 모든 필드의 목록이 표시됩니다.

1. 현재 선택된 패싯 필드를 그대로 적용하고 다음 페이지로 이동합니다.

## <a name="add-typeahead"></a>자동 완성 추가

자동 완성 및 쿼리 제안 형식으로 미리 구성된 기능을 사용할 수 있습니다. 마법사는 쿼리 제안을 지원합니다. 사용자가 제공하는 키 입력에 따라 검색 서비스에서 입력으로 선택할 수 있는 "완성된" 쿼리 문자열 목록을 반환합니다.

제안은 특정 필드 정의에서 사용하도록 설정됩니다. 마법사는 제안에 포함된 정보의 양을 구성하는 옵션을 제공합니다. 

다음 스크린샷에서는 앱에서 렌더링된 페이지와 함께 마법사의 옵션을 보여 줍니다. 필드 선택이 사용되는 방법 및 "필드 이름 표시"가 제안 내에서 레이블 지정을 포함하거나 제외하는 데 사용되는 방법을 확인할 수 있습니다.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="쿼리 제안 구성":::

## <a name="add-suggestions"></a>제안 추가

제안은 검색 상자에 연결된 자동 쿼리 프롬프트를 나타냅니다. Cognitive Search는 부분적으로 입력된 검색어의 *자동 완성* 과 잠재적으로 일치하는 문서를 기반으로 하는 드롭다운 목록에 대한 *제안* 이라는 두 가지를 지원합니다.

마법사는 제안을 지원하고 제안된 결과를 제공할 수 있는 필드는 인덱스의 [`Suggesters`](index-add-suggesters.md) 구성에서 파생됩니다.

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. 마법사에서 페이지 맨 위에 있는 **제안** 탭을 선택합니다. 인덱스 스키마에서 제안 공급자로 지정된 모든 필드의 목록이 표시됩니다.

1. 현재 선택 항목을 그대로 적용하고 다음 페이지로 이동합니다.

## <a name="create-download-and-execute"></a>만들기, 다운로드 및 실행

1. 페이지 맨 아래에 있는 **데모 앱 만들기** 를 선택하여 HTML 파일을 생성합니다.

1. 메시지가 표시되면 **앱 다운로드** 를 선택하여 파일을 다운로드합니다.

1. 파일을 열고 검색 단추를 클릭합니다. 이 작업은 임의의 결과 집합을 반환하는 빈 쿼리(`*`)일 수 있는 쿼리를 실행합니다. 이 페이지는 다음 스크린샷과 유사해야 합니다. 용어를 입력하고 필터를 사용하여 결과를 좁힙니다. 

기본 인덱스는 문서 간에 중복된 가상의 생성된 데이터로 구성되며, 설명이 이미지와 일치하지 않는 경우도 있습니다. 사용자 고유의 인덱스를 기반으로 하여 앱을 만드는 경우 더 응집력 있는 환경을 사용할 수 있습니다.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="앱 실행":::

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

데모 앱은 JavaScript 또는 프런트 엔드 코드를 작성하지 않고도 최종 사용자 환경을 시뮬레이트할 수 있지만 자체 프로젝트에서 개념 증명에 가까워지면 실제 단어 앱과 더 가까운 엔드투엔드 코드 샘플 중 하나를 검토하기 때문에 프로토타이핑에 유용합니다.

> [!div class="nextstepaction"]
> [웹앱에 검색 추가](tutorial-csharp-overview.md)