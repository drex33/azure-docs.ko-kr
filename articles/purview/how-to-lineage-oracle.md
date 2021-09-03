---
title: Oracle의 메타데이터 및 계보
description: 이 문서에서는 Oracle 원본에서의 데이터 계보 추출을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: cf415250868c74aa95e22b12d8980987f0c9dc48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529492"
---
# <a name="how-to-get-lineage-from-oracle-into-azure-purview"></a>Oracle의 계보를 Azure Purview로 가져오는 방법

이 문서에서는 Azure Purview에서 Oracle 원본의 데이터 계보 양상을 설명합니다. Purview에서 Oracle의 데이터 계보를 확인하기 위한 필수 요소는 [Oracle 검사](../purview/register-scan-oracle-source.md)입니다. 

## <a name="lineage-of-oracle-artifacts-in-azure-purview"></a>Azure Purview에서 Oracle 아티팩트의 계보

사용자는 Oracle 아티팩트를 이름, 설명 또는 다른 세부 정보별로 검색하여 관련 결과를 확인할 수 있습니다. 자산 개요 및 속성 탭에 설명, 분류 및 기타 정보와 같은 기본 세부 정보가 표시됩니다. 계보 탭 아래에는 Oracle 테이블과 저장 프로시저, 뷰, 함수 간의 자산 관계가 표시됩니다. 

따라서 Oracle 뷰는 테이블의 계보 정보를 포함하게 되지만, 함수와 저장 프로시저는 parameter_dataset 및 stored_procedure_result_set를 사용하여 테이블/뷰 간에 계보를 생성하게 됩니다. 파생된 계보는 열 수준에서도 사용할 수 있습니다.

:::image type="content" source="./media/how-to-lineage-oracle/lineage.png" alt-text="계보가 Oracle에 렌더링되는 방식을 보여 주는 스크린샷" lightbox="./media/how-to-lineage-oracle/lineage.png":::


## <a name="next-steps"></a>다음 단계

- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- [Azure Data Factory를 연결하여 자동화된 계보 푸시](how-to-link-azure-data-factory.md)
