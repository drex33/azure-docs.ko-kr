---
title: 다중 클라우드 데이터 소스를 관리 하는 방법
description: Azure 부서의 범위에서 새 데이터 원본을 등록 하 고, 데이터 원본의 컬렉션을 관리 하 고, 소스를 보는 방법에 대해 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c7ef1849859dca48f5d2489f2ac8668c3cd0fc31
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215139"
---
# <a name="manage-data-sources-in-azure-purview"></a>Azure Purview에서 데이터 원본 관리

이 문서에서는 Azure 부서의 범위에서 새 데이터 원본을 등록 하 고, 데이터 원본의 컬렉션을 관리 하 고, 소스를 보는 방법에 대해 알아봅니다.

## <a name="register-a-new-source"></a>새 원본 등록

다음 단계에 따라 새 원본을 등록합니다.

1. [부서의 범위 Studio](https://web.purview.azure.com/resource/)를 열고, **데이터 맵**, **원본** 으로 이동 하 고, **등록** 을 선택 합니다.

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure Purview Studio":::

1. 원본 유형을 선택 합니다. 이 예제에서는 Azure Blob Storage를 사용합니다. **계속** 을 선택합니다.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="원본 등록 페이지에서 데이터 원본 형식을 선택합니다.":::

2. **원본 등록** 페이지의 양식을 작성합니다. 원본 이름을 선택하고 관련 정보를 입력합니다. 계정 선택 방법으로 **Azure 구독에서** 를 선택하는 경우 구독의 원본이 드롭다운 목록에 표시됩니다.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="데이터 원본 정보 양식":::

3. **등록** 을 선택합니다.

## <a name="view-sources"></a>원본 보기

Azure Purview Studio의 **데이터 맵** 탭에서 등록된 모든 원본을 볼 수 있습니다. 지도 보기와 목록 보기의 두 가지 보기 형식이 있습니다.

### <a name="map-view"></a>맵 보기

지도 보기에서 모든 원본 및 컬렉션을 볼 수 있습니다. 다음 이미지에는 Azure Blob Storage 원본이 하나 있습니다. 각 원본 타일에서 소스를 편집하거나, 새 검색을 시작하거나, 원본 정보를 볼 수 있습니다.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Azure Purview 데이터 원본 지도 보기":::

### <a name="table-view"></a>테이블 보기

테이블 뷰에서 정렬 가능한 원본 목록을 볼 수 있습니다. 편집, 새 검사 시작 또는 삭제 옵션을 보려면 원본을 마우스로 가리킵니다.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Azure Purview 데이터 원본 목록 보기":::

## <a name="manage-collections"></a>컬렉션 관리

데이터 원본을 컬렉션으로 그룹화할 수 있습니다. 새 컬렉션을 만들려면 Azure Purview Studio의 *원본* 페이지에서 **+ 새 컬렉션** 을 선택합니다. 컬렉션 이름을 지정하고 부모로 *없음* 을 선택합니다. 새 컬렉션이 맵 보기에 나타납니다.

컬렉션에 원본을 추가하려면 원본에서 **편집** 연필을 선택하고 **컬렉션 선택** 드롭다운 메뉴에서 컬렉션을 선택합니다.

컬렉션의 계층 구조를 만들려면 상위 수준 컬렉션을 하위 수준 컬렉션에 부모로 할당합니다. 다음 이미지에서 *Fabrikam* 은 Azure Blob Storage 데이터 원본을 포함하는 *Finance* 컬렉션의 부모입니다. 수준 사이에 화살표에 연결 된 원을 선택 하 여 컬렉션을 축소 하거나 확장할 수 있습니다.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Azure Purview Studio의 컬렉션 계층 구조":::

부모에 대해 *없음* 을 선택하여 계층에서 원본을 제거할 수 있습니다. 부모가 없는 원본은 부모에 연결하는 화살표 없이 맵 보기에서 점선 상자로 그룹화됩니다.

## <a name="next-steps"></a>다음 단계

다양한 데이터 원본을 등록하고 검사하는 방법을 알아봅니다.

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI 테넌트](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
