---
title: 자산을 보고, 편집하고, 삭제하는 방법
description: 이 방법 안내에서는 자산 세부 정보를 보고 편집할 수 있는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8004ef319efc08610f9c1a5de16b7c430d51d666
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209182"
---
# <a name="view-edit-and-delete-assets-in-purview-catalog"></a>Purview 카탈로그에서 자산 보기, 편집 및 삭제

이 문서에서는 자산 및 관련 세부 정보를 볼 수 있는 방법을 설명합니다. 또한 카탈로그에서 자산을 편집하고 삭제할 수 있는 방법에 대해서도 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 설정하고 자산을 카탈로그로 검사합니다.
- *또는* Purview Atlas API를 사용하여 자산을 카탈로그로 수집합니다. 

## <a name="viewing-asset-details"></a>자산 세부 정보 보기

다음 중 하나를 통해 Purview에서 자산을 검색할 수 있습니다.
- [Azure Purview Data Catalog 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)

찾고 있는 자산을 찾으면 다음 섹션에 설명된 대로 모든 세부 정보를 보거나 편집하거나 삭제할 수 있습니다.

## <a name="asset-details-tabs-explained"></a>자산 세부 정보 탭 설명

:::image type="content" source="media/catalog-asset-details/asset-tabs.png" alt-text="자산 세부 정보 탭":::

- **개요** - 개요 탭에서는 설명, 분류, 계층 구조 및 용어집과 같은 자산의 기본 세부 정보를 다룹니다.
- **속성** - 속성 탭은 자산과 관련된 기본 속성과 고급 속성을 모두 다룹니다.
- **스키마** - 열 이름, 데이터 유형, 열 수준 분류, 용어 및 설명을 포함한 자산의 스키마가 스키마 탭에 표시됩니다.
- **계보** - 이 탭에는 사용 가능한 자산에 대한 계보 그래프 세부 정보가 포함되어 있습니다.
- **연락처** - 모든 자산에는 연락처 탭에서 보고 관리할 수 있는 할당된 소유자와 전문가가 있을 수 있습니다.
- **관련** - 이 탭을 사용하면 현재 보고 있는 자산과 관련된 자산으로 이동할 수 있습니다. 

## <a name="asset-overview"></a>자산 개요
자산 세부 정보의 개요 섹션은 자산에 대한 요약 보기를 제공합니다. 다음 섹션에서는 개요 페이지의 여러 부분에 대해 설명합니다.

### <a name="asset-hierarchy"></a>자산 계층 구조

개요 탭에서 전체 자산 계층 구조를 볼 수 있습니다. 예를 들어 SQL 테이블로 이동하면 스키마, 데이터베이스 및 테이블이 속한 서버를 볼 수 있습니다.

:::image type="content" source="media/catalog-asset-details/asset-hierarchy.png" alt-text="자산 계층 구조":::

### <a name="asset-classifications"></a>자산 분류

자산 분류는 표시되는 데이터의 종류를 식별하고 수동으로 또는 검사 중에 적용됩니다. 예: 주민등록번호 또는 여권 번호가 지원되는 분류입니다. (전체 분류 목록은 [지원되는 분류 페이지](supported-classifications.md)를 참조하세요.) 개요 탭은 적용된 자산 수준 분류와 열 수준 분류를 모두 반영하며 스키마의 일부로 볼 수도 있습니다.

:::image type="content" source="media/catalog-asset-details/asset-classifications.png" alt-text="자산 분류":::

### <a name="asset-description"></a>자산 설명

개요 섹션에서 자산에 대한 설명을 볼 수 있습니다. [자산을 편집](#editing-assets)하여 자산 설명을 추가할 수 있습니다.

:::image type="content" source="media/catalog-asset-details/asset-description.png" alt-text="자산 설명":::

### <a name="asset-glossary-terms"></a>자산 용어집

자산 용어집은 환경 전반에서 자산을 분류하고 관련시키는 데 사용할 수 있는 비즈니스 용어에 대한 관리 어휘입니다. 예를 들어 '고객', '구매자', '비용 센터'와 같은 용어 또는 사용자에게 데이터 컨텍스트를 제공하는 모든 용어입니다. 자세한 내용은 [비즈니스 용어집 페이지](concept-business-glossary.md)를 참조하세요. 개요 섹션에서 자산에 대한 용어집을 볼 수 있으며 [자산을 편집](#editing-assets)하여 자산에 대한 용어집을 추가할 수 있습니다.

:::image type="content" source="media/catalog-asset-details/asset-glossary.png" alt-text="자산 용어집":::

## <a name="editing-assets"></a>자산 편집

자산의 왼쪽 위 모퉁이에 있는 편집 아이콘을 선택 하 여 자산을 편집할 수 있습니다.

:::image type="content" source="media/catalog-asset-details/asset-edit-delete.png" alt-text="자산 편집 및 삭제 단추":::

자산 수준에서, 편집 화면의 개요 탭에서 설명, 분류 또는 용어집을 편집하거나 추가할 수 있습니다.

편집 화면의 스키마 탭으로 이동하여 열 이름, 데이터 유형, 열 수준 분류, 용어 또는 자산 설명을 업데이트할 수 있습니다.

편집 화면의 연락처 탭으로 이동하여 자산의 소유자 및 전문가를 업데이트할 수 있습니다. Azure Active Directory 내에서 사용자의 전체 이름, 이메일 또는 별칭으로 검색할 수 있습니다.

### <a name="edit-behavior-explained"></a>편집 동작 설명

설명, 자산 수준 분류, 용어집 또는 연락처를 자산에 추가하는 것과 같은 자산 수준 업데이트를 수행하면 후속 검사에서 자산 스키마(이후 검사 실행에서 스캐너가 감지한 새 열 및 분류)를 업데이트합니다.

설명, 열 수준 분류, 용어집 추가 또는 데이터 유형 또는 열 이름 업데이트와 같은 열 수준 업데이트를 수행하면 후속 검사는 자산 스키마를 업데이트하지 **않습니다**(새 열 및 분류는 후속 검사 실행에서 스캐너에 의해 **감지되지 않습니다**).

## <a name="deleting-assets"></a>자산 삭제

자산 이름 아래에서 삭제 아이콘을 선택 하 여 자산을 삭제할 수 있습니다.

### <a name="delete-behavior-explained"></a>삭제 동작 설명

삭제 단추를 사용하여 삭제한 모든 자산은 영구적으로 삭제됩니다. 그러나 자산이 카탈로그로 수집된 원본에서 **전체 검사** 를 실행하면 자산이 다시 수집되고 Purview 카탈로그를 사용하여 자산을 검색할 수 있습니다.

원본에 대한 예약된 검사(매주 또는 월간)가 있는 경우 이전 검사 실행 이후 최종 사용자가 자산을 수정하지 않는 한 **삭제된 자산이 카탈로그에 다시 수집되지 않습니다**.   예를 들어 SQL 테이블이 Purview에서 삭제되었지만 테이블이 삭제된 후 사용자가 SQL의 테이블에 새 열을 추가한 경우 다음 검사에서 자산이 다시 검사되어 카탈로그로 수집됩니다.

자산을 삭제하면 해당 자산만 삭제됩니다. Purview는 현재 계단식 삭제를 지원하지 않습니다. 예를 들어 카탈로그에서 스토리지 계정 자산을 삭제하는 경우 해당 자산 내의 컨테이너, 폴더 및 파일은 삭제되지 않습니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
