---
title: Storage Explorer를 사용하여 지식 저장소 보기
titleSuffix: Azure Cognitive Search
description: Azure Portal의 Storage Explorer를 사용하여 Azure Cognitive Search 지식 저장소를 살펴보고 분석합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 8be37bfc85b56af67784fb1abb457da95b9565fc
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067044"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer를 사용하여 지식 저장소 보기

[지식 저장소는](knowledge-store-concept-intro.md) Azure Cognitive Search 기술 Azure Storage 저장된 콘텐츠입니다. 이 문서에서는 Azure Portal Storage Explorer 사용하여 지식 저장소의 내용을 보는 방법을 알아봅니다.

Azure Portal 만든 기존 [지식](knowledge-store-create-portal.md) 저장소로 시작하거나 [REST API를 사용하여 시작합니다.](knowledge-store-create-rest.md) 포털 및 REST 연습은 모두 Azure Table Storage 지식 저장소를 만듭니다.

## <a name="start-storage-explorer"></a>Storage Explorer 시작

1. Azure Portal에서 지식 저장소를 만드는 데 사용한 [스토리지 계정을 엽니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/).

1. 스토리지 계정의 왼쪽 탐색 창에서 **Storage Explorer** 선택합니다.

## <a name="view-edit-and-query-tables"></a>테이블 보기, 편집 및 쿼리

1. **테이블** 목록을 확장하여 지식 저장소를 만들 때 생성된 Azure 테이블 프로젝션 목록을 표시합니다. 빠른 시작 또는 REST 문서를 사용하여 지식 저장소를 만든 경우 테이블에는 유럽 호텔 고객 리뷰와 관련된 콘텐츠가 포함됩니다.

1. 목록에서 테이블을 선택합니다.

   ![Storage Explorer에서 테이블 보기](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer에서 테이블 보기")

1. 테이블의 데이터 형식, 속성 이름 또는 개별 데이터 값을 변경하려면 **편집을** 클릭합니다.

   ![Storage Explorer에서 테이블 편집](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer에서 테이블 편집")

1. 쿼리를 실행하려면 명령 모음에서 **쿼리를** 선택하고 조건을 입력합니다.

   ![Storage Explorer의 쿼리 테이블](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer의 쿼리 테이블")

Storage Explorer 지원되는 쿼리 구문을 사용하여 한 번에 하나의 테이블만 [쿼리할](/rest/api/storageservices/Querying-Tables-and-Entities)수 있습니다. 테이블을 쿼리하려면 대신 Power BI 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 지식 저장소를 Power BI 여러 테이블을 포함하는 시각화를 빌드하도록 커넥트.

> [!div class="nextstepaction"]
> [Power BI와 연결](knowledge-store-connect-power-bi.md)
