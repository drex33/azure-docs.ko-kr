---
title: 기술 자료 저장소 보기
titleSuffix: Azure Cognitive Search
description: Azure Portal에서 Storage 브라우저를 사용 하 여 기술 자료 저장소를 봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: d3a66bacfe746b73d269cd8a36fedf0c6b821cdd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563666"
---
# <a name="view-a-knowledge-store-with-storage-browser"></a>Storage 브라우저를 사용 하 여 기술 자료 저장소 보기

[기술 자료 저장소](knowledge-store-concept-intro.md) 는 Azure Cognitive Search 기술에서 작성 되 고 Azure Storage에 저장 된 콘텐츠입니다. 이 문서에서는 Azure Portal에서 Storage 브라우저를 사용 하 여 기술 자료 저장소의 내용을 보는 방법에 대해 알아봅니다.

[Azure Portal](knowledge-store-create-portal.md) 또는 [REST api](knowledge-store-create-rest.md)를 사용 하 여 만든 기존 기술 자료 저장소로 시작 합니다. 포털 및 REST 연습은 Azure Table Storage에서 기술 자료 저장소를 만듭니다.

## <a name="start-storage-browser"></a>Storage 브라우저 시작

1. Azure Portal에서 지식 저장소를 만드는 데 사용한 [스토리지 계정을 엽니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/).

1. 저장소 계정의 왼쪽 탐색 창에서 **Storage Browser** 를 선택 합니다.

## <a name="view-and-edit-tables"></a>테이블 보기 및 편집

1. **테이블** 을 확장 하 여 기술 자료 저장소의 테이블 프로젝션을 찾습니다. 퀵 스타트 또는 REST 문서를 사용 하 여 기술 자료 저장소를 만든 경우이 테이블에는 유럽 호텔 고객 리뷰와 관련 된 콘텐츠가 포함 됩니다.

   :::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Storage Browser의 스크린샷" border="true":::

1. 목록에서 내용을 볼 테이블을 선택 합니다.

1. 열 순서를 다시 정렬 하거나 열을 삭제 하려면 페이지 맨 위에서 **열 편집** 을 선택 합니다.

Storage 브라우저에서 [지원 되는 쿼리 구문을](/rest/api/storageservices/Querying-Tables-and-Entities)사용 하 여 한 번에 하나의 테이블만 쿼리할 수 있습니다. 테이블에 대해 쿼리하려면 대신 Power BI를 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 기술 자료 저장소를 커넥트 하 여 여러 테이블을 포함 하는 시각화를 빌드할 Power BI.

> [!div class="nextstepaction"]
> [Power BI와 연결](knowledge-store-connect-power-bi.md)
