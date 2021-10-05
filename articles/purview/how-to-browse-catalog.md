---
title: '방법: Data Catalog 찾아보기'
description: 이 문서에서는 자산 유형별로 Azure Purview Data Catalog를 찾아보는 방법에 대한 개요를 제공합니다
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: ab5cb2856343d4fdcfcbd6c3c88d45889c191d2d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456838"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Azure Purview 데이터 카탈로그 찾아보기

데이터 소비자가 원하는 것을 알고 있지만 데이터 자산이 어떻게 구성되었는지 정확하게 알지 못하는 경우가 많으면 데이터 카탈로그 검색은 데이터 검색에 적합한 도구입니다. Azure Purview 데이터 카탈로그는 사용자가 카탈로그에 있는 각 데이터 원본의 계층 구조를 수집하거나 트래버스하여 사용할 수 있는 데이터를 탐색할 수 있는 찾아보기 환경을 제공합니다.

찾아보기 경험에 액세스하려면 데이터 카탈로그 홈 페이지에서 "자산 찾아보기"를 선택합니다.

:::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview 홈페이지" border="true":::

## <a name="browse-by-collection"></a>컬렉션별 찾아보기

컬렉션별 찾아보기를 사용하면 데이터 판독기 또는 모음인 다양한 컬렉션을 탐색할 수 있습니다.

> [!NOTE]
> 액세스할 수 있는 컬렉션만 표시됩니다. 자세한 내용은 [컬렉션 만들기 및 관리를 참조하세요.](how-to-create-and-manage-collections.md)

:::image type="content" source="media/how-to-browse-catalog/browse-by-collection.png" alt-text="컬렉션별 찾아보기 페이지를 보여주는 스크린샷" border="true":::

컬렉션을 선택하면 검색에서 사용할 수 있는 피싯 및 필터를 포함하는 해당 컬렉션의 자산 목록이 표시됩니다. 컬렉션에 수천 개의 자산이 있을 수 있기 때문에 찾아보기는 Purview 검색 관련성 엔진을 사용하여 가장 중요한 자산을 위쪽으로 높입니다.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-results.png" alt-text="컬렉션 결과별 찾아보기를 보여주는 스크린샷" border="true":::

특정 주석의 경우 줄임표를 클릭하여 AND 조건 또는 OR 조건 중에서 선택할 수 있습니다. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="및 AND 또는 OR 조건 중에서 선택하는 방법을 보여주는 스크린샷" border="true":::

선택한 컬렉션에 찾고 있는 데이터가 없는 경우 관련 컬렉션으로 쉽게 이동하거나 돌아가 전체 컬렉션 트리를 볼 수 있습니다.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-navigation.png" alt-text="컬렉션 간을 탐색하는 방법을 보여주는 스크린샷" border="true":::

원하는 자산을 찾으면 해당 자산을 선택하여 스키마, 계보 및 자세한 분류 목록과 같은 추가 세부 정보를 볼 수 있습니다. 자산 세부 정보 페이지에 대한 자세한 내용은 [카탈로그 자산 관리를 참조하세요.](catalog-asset-details.md)

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="자산 정보 페이지를 보여 주는 스크린샷" border="true":::

## <a name="browse-by-source-type"></a>원본 유형별 찾아보기

원본 유형별 찾아보기를 사용하면 데이터 소비자가 탐색기 보기를 사용하여 데이터 원본의 계층 구조를 탐색할 수 있습니다. 검색된 원본 목록을 보려면 원본 유형을 선택합니다.

예를 들어, Azure Data Lake Storage Gen 2의 *Dimensions* 이라는 폴더 아래에서 *DateDimension* 이라는 데이터 세트를 쉽게 찾을 수 있습니다. '원본 유형별 찾아보기' 환경을 사용하여 ADLS Gen 2 스토리지 계정으로 이동한 다음, 서비스 > 컨테이너 > 폴더로 이동하여 특정 *Dimensions* 폴더에 도달한 다음 *DateDimension* 테이블을 볼 수 있습니다.

계층 구조 네임스페이스가 있는 네이티브 검색 환경은 해당하는 각 데이터 원본에 대해 제공됩니다.

> [!NOTE]
> 범위가 성공적으로 검사된 후 새로 검색된 자산이 찾아보기 환경으로 표시되기까지 지연이 있을 수 있습니다.


1. 원본 **유형별 찾아보기** 페이지에서 타일은 데이터 원본별로 분류됩니다. 각 데이터 원본의 자산을 추가로 탐색하려면 해당 타일을 선택합니다.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.png" alt-text="자산 유형 찾아보기 페이지" border="true":::

   > [!TIP]
   > 특정 타일은 데이터 원본의 컬렉션을 그룹화한 것입니다. 예를 들어, Azure Storage 계정 타일에는 모든 Azure Blob Storage 및 Azure Data Lake Storage Gen2 계정이 포함되어 있습니다. Azure SQL Server 타일에는 Azure SQL Database 및 수집된 Azure Dedicated SQL Pool 인스턴스를 포함하는 Azure SQL Server 자산이 카탈로그에 표시됩니다. 

1. 다음 페이지에서는 선택한 데이터 형식 아래에 최상위 자산이 나열됩니다. 자산 중 하나를 선택해 내용을 추가로 탐색합니다. 예를 들어 "Azure SQL Database"을 선택하면 데이터 카탈로그에 자산이 있는 데이터베이스 목록이 표시됩니다.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.png" alt-text="Azure SQL Database 찾아보기 페이지" border="true":::

1. 탐색기 보기가 열립니다. 왼쪽 패널에서 자산을 선택하여 검색을 시작합니다. 하위 자산은 페이지의 오른쪽 패널에 나열됩니다.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.png" alt-text="탐색기 보기" border="true":::

1. 자산의 세부 정보를 보려면 맨 오른쪽에 있는 이름 또는 줄임표 단추를 선택합니다.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.png" alt-text="자산 세부 정보 페이지를 보려면 줄임표 단추를 선택합니다." border="true":::

## <a name="next-steps"></a>다음 단계

- [용어집 용어를 만들고, 가져오며, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대한 용어 템플릿을 관리하는 방법](how-to-manage-term-templates.md)
- [Purview 데이터 카탈로그를 검색하는 방법](how-to-search-catalog.md)
