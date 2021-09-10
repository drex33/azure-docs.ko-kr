---
title: Teradata의 메타데이터 및 계보
description: 이 문서에서는 Teradata 원본에서 데이터 계보를 추출하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 39965089cdd838e12ac324737e977dbeee17b40b
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537442"
---
# <a name="how-to-get-lineage-from-teradata-into-azure-purview"></a>Teradata에서 Azure Purview로 계보를 가져오는 방법

이 문서에서는 Azure Purview의 Teradata 원본 데이터 계보 측면을 자세히 설명합니다. Purview에서 Teradata 데이터 계보를 확인하기 위한 필수 조건은 [Teradata를 검사](../purview/register-scan-teradata-source.md)하는 것입니다. 

## <a name="lineage-of-teradata-artifacts-in-azure-purview"></a>Azure Purview의 Teradata 아티팩트 계보

사용자는 이름, 설명 또는 기타 세부 정보로 Teradata 아티팩트를 검색하여 관련 결과를 확인할 수 있습니다. 자산 개요 및 속성 탭에 설명, 속성, 기타 정보와 같은 기본 세부 정보가 표시됩니다. 계보 탭 아래에 Teradata 테이블과 저장 프로시저 간 자산 관계 및 Teradata 테이블과 뷰 간 자산 관계가 표시됩니다. 

따라서 저장 프로시저에서 발생하는 데이터 변환과 Teradata 테이블에 만든 뷰의 변환에 대해 계보를 지원합니다. 열 수준에서 파생된 계보도 사용할 수 있습니다.

:::image type="content" source="./media/how-to-lineage-teradata/lineage.png" alt-text="Teradata에 대한 계보 렌더링 방식을 보여 주는 스크린샷" lightbox="./media/how-to-lineage-teradata/lineage.png":::

위 스크린샷에서 **customerView** 는 Teradata 테이블 **test_customer** 에서 만든 Teradata 뷰입니다. 또한 **Return_DataSet** 는 Teradata 테이블 **test_customer** 를 사용하는 저장 프로시저입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- ADF를 사용하여 Teradata에서 Azure로 데이터를 이동하는 경우 데이터 이동 런타임의 일부로 계보를 추적할 수 있습니다. [Azure Data Factory를 연결하여 자동화된 계보 푸시](how-to-link-azure-data-factory.md)
