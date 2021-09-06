---
title: Storage Explorer를 사용하여 지식 저장소 보기
titleSuffix: Azure Cognitive Search
description: Azure Portal의 Storage Explorer를 사용하여 Azure Cognitive Search 지식 저장소를 살펴보고 분석합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: ee6e17e6fe52125d75f2782b1fa77215045787a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536330"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer를 사용하여 지식 저장소 보기

[지식 저장소](knowledge-store-concept-intro.md)는 기술 세트에 의해 생성되어 Azure Storage에 저장됩니다. 이 문서에서는 Azure Portal의 Storage Explorer를 사용하여 지식 저장소의 콘텐츠를 보는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure Portal](knowledge-store-create-portal.md) 또는 [Postman 및 REST API](knowledge-store-create-rest.md)에서 지식 저장소를 만듭니다.

+ Azure Portal의 액세스 키와 함께 지식 저장소가 있는 Azure Storage 계정의 이름도 필요합니다.

## <a name="start-storage-explorer"></a>Storage Explorer 시작

1. Azure Portal에서 지식 저장소를 만드는 데 사용한 [스토리지 계정을 엽니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/).

1. 스토리지 계정의 왼쪽 탐색 창에서 **Storage Explorer** 를 클릭합니다.

## <a name="view-edit-and-query-tables"></a>테이블 보기, 편집 및 쿼리

포털 및 REST 연습은 모두 Table Storage에 지식 저장소를 만듭니다.

1. **테이블** 목록을 확장하여 지식 저장소를 만들 때 생성된 Azure 테이블 프로젝션 목록을 표시합니다. 테이블에는 호텔 리뷰와 관련된 콘텐츠가 포함되어야 합니다.

1. 핵심 구와 감정 점수를 비롯한 보강 데이터를 보려면 표를 선택합니다.

   ![Storage Explorer에서 테이블 보기](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer에서 테이블 보기")

1. 테이블 값에 대한 데이터 형식을 변경하거나 테이블의 개별 값을 변경하려면 **편집** 을 클릭합니다. 한 테이블 행의 열에 대한 데이터 형식을 변경하면 모든 행에 적용됩니다.

   ![Storage Explorer에서 테이블 편집](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer에서 테이블 편집")

1. 쿼리를 실행하려면 명령 모음에서 **쿼리** 를 클릭하고 조건을 입력합니다.  

   ![Storage Explorer의 쿼리 테이블](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer의 쿼리 테이블")

## <a name="next-steps"></a>다음 단계

자세한 분석을 위해 이 지식 저장소를 Power BI에 연결하거나 REST API 및 Postman을 통해 코드로 작업하여 다른 지식 저장소를 만듭니다.

> [!div class="nextstepaction"]
> [Power BI와 연결](knowledge-store-connect-power-bi.md)
