---
title: Power BI를 사용하여 지식 저장소에 연결
titleSuffix: Azure Cognitive Search
description: 분석 및 검색을 위해 Power BI를 사용하여 Azure Cognitive Search 지식 저장소에 연결합니다.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 97a0025437b7f055f9fcf2e7860e926e9693cde6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766950"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Power BI를 사용하여 지식 저장소 연결

이 문서에서는 Power BI Desktop 앱에서 파워 쿼리를 사용하여 지식 저장소에 연결하고 검색하는 방법을 알아봅니다. 템플릿을 사용해서 빠르게 시작하거나 사용자 지정 대시보드를 처음부터 만들 수 있습니다.

Azure Storage 테이블로 구성된 지식 저장소는 Power BI 가장 적합합니다. 테이블에 동일한 기술 및 프로젝션 그룹의 프로젝션이 포함된 경우 쉽게 "조인"하여 관련 테이블의 필드를 포함하는 테이블 시각화를 작성할 수 있습니다.

Azure Portal 만든 샘플 데이터 및 지식 저장소를 사용하거나 [Postman 및 REST](knowledge-store-create-rest.md) [API를](knowledge-store-create-portal.md) 통해 이 문서의 단계를 따릅니다. 

## <a name="connect-to-azure-storage"></a>Azure Storage에 연결

1. [Power BI Desktop](https://powerbi.microsoft.com/downloads/) 시작하고 **데이터 받기를** 선택합니다. 

1. **데이터 가져오기** 에서 **Azure**, **Azure Table Storage** 를 차례로 선택합니다.

1. **연결** 을 선택합니다.

1. **계정 이름 또는 URL** 에서 Azure Storage 계정 이름을 입력합니다(전체 URL이 자동으로 생성됨).

1. 스토리지 계정 키를 입력하라는 메시지가 표시되면 입력합니다.

## <a name="set-up-tables"></a>테이블 설정

1. 동일한 기술에서 만든 모든 테이블 옆에 있는 확인란을 **선택한** 다음 로드를 선택합니다.

   ![테이블 로드](media/knowledge-store-connect-power-bi/power-bi-load-tables.png "테이블 로드")

1. 위쪽 리본에서 **데이터 변환을** 선택하여 **Power Query 편집기** 엽니다.

   ![파워 쿼리 열기](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "파워 쿼리 열기")

1. *hotelReviewsSsDocument* 를 선택한 다음, *PartitionKey*, *RowKey* 및 *Timestamp* 열을 제거합니다. 

   ![테이블 편집](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "테이블 편집")

1. 테이블의 오른쪽 위에서 반대 방향 화살표가 있는 아이콘을 클릭하여 *콘텐츠* 를 확장합니다. 열 목록이 표시되면 모든 열을 선택한 다음, 'metadata'로 시작하는 열을 선택 취소합니다. **확인** 을 클릭하여 선택한 열을 표시합니다.

   ![콘텐츠 확장](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "콘텐츠 확장")

1. 열의 왼쪽 위에 있는 ABC-123 아이콘을 클릭하여 다음 열의 데이터 형식을 변경합니다.

   + *content.latitude* 및 *Content.longitude* 의 경우 **10진수** 를 선택합니다.
   + *Content.reviews_date* 및 *Content.reviews_dateAdded* 의 경우 **날짜/시간** 을 선택합니다.

   ![데이터 형식 변경](media/knowledge-store-connect-power-bi/powerbi-change-type.png "데이터 형식 변경")

1. *hotelReviewsSsPages* 를 선택한 다음, 9단계와 10단계를 반복하여 열을 삭제하고 *콘텐츠* 를 확장합니다.

1. *hotelReviewsSsKeyPhrases* 를 선택한 다음, 9단계와 10단계를 반복하여 열을 삭제하고 *콘텐츠* 를 확장합니다. 이 테이블의 데이터 형식은 수정할 필요가 없습니다.

1. 명령 모음에서 **닫기 및 적용** 을 클릭합니다.

## <a name="check-table-relationships"></a>테이블 관계 확인

1. 왼쪽 탐색 창에서 모델 타일을 클릭하고 Power BI가 세 테이블 간의 관계를 표시하는지 확인합니다.

   ![관계 유효성 검사](media/knowledge-store-connect-power-bi/powerbi-relationships.png "관계 유효성 검사")

1. 각 관계를 두 번 클릭하고 **교차 필터 방향** 이 **둘 다** 로 설정되었는지 확인합니다.  이렇게 하면 필터가 적용될 때 시각적 개체를 새로 고칠 수 있습니다.

## <a name="build-a-report"></a>보고서 작성

1. 시각화를 통해 데이터를 검색하려면 왼쪽 탐색 창의 보고서 타일을 클릭합니다. 텍스트 필드의 경우 테이블과 카드는 유용한 시각화입니다.

1. 테이블 또는 카드를 채울 세 개의 테이블 각각에서 필드를 선택합니다.

   ![테이블 보고서 작성](media/knowledge-store-connect-power-bi/power-bi-table-report.png "테이블 보고서 작성")

## <a name="sample-power-bi-template---azure-portal-only"></a>Power BI 템플릿 샘플 - Azure Portal만

[Azure Portal을 사용하여 지식 저장소](knowledge-store-create-portal.md)를 만들 때 **데이터 가져오기** 마법사의 두 번째 페이지에서 [Power BI 템플릿](https://github.com/Azure-Samples/cognitive-search-templates)을 다운로드하는 옵션이 있습니다. 이 템플릿은 텍스트 기반 콘텐츠에 대한 WordCloud 및 Network Navigator와 같은 몇 가지 시각화를 제공합니다. 

**인지 기술 추가** 페이지에서 **Power BI 템플릿 가져오기** 를 클릭하여 해당하는 공용 GitHub 위치에서 템플릿을 검색하고 다운로드합니다. 마법사는 마법사에 지정된 지식 저장소 프로젝션에 캡처된 것처럼 데이터의 모양을 수용하도록 템플릿을 수정합니다. 이러한 이유로, 다운로드하는 템플릿은 다른 데이터 입력과 기술 선택을 가정하여 마법사를 실행할 때마다 달라집니다.

![Azure Cognitive Search Power BI 템플릿 샘플](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Power BI 템플릿 샘플")

> [!NOTE]
> 마법사가 중간에 있는 동안 템플릿이 다운로드됩니다. 지식 저장소를 사용하려면 Azure Table Storage 지식 저장소가 실제로 만들어질 때까지 기다려야 합니다.

## <a name="video-introduction"></a>비디오 소개

지식 저장소에서 Power BI 사용하는 데모는 다음 비디오를 시청하세요.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Power BI 보고서 및 대시보드의 테이블](/power-bi/visuals/power-bi-visualization-tables)