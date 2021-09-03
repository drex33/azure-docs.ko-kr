---
title: Erwin의 메타데이터 및 계보
description: 이 문서에서는 Erwin 원본에서 데이터 계보 추출을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: d1821ca94d8f5329ca4c08f65438a3d0ba6e4d48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567442"
---
# <a name="how-to-get-lineage-from-erwin-into-azure-purview"></a>Erwin에서 Azure Purview로 계보를 가져오는 방법

이 문서에서는 Azure Purview에서 Erwin 원본의 데이터 계보 양상을 설명합니다. Purview에서 Erwin의 데이터 계보를 확인하기 위한 필수 요소는 [Erwin을 검사](../purview/register-scan-erwin-source.md)하는 것입니다. 

## <a name="lineage-of-erwin-artifacts-in-azure-purview"></a>Azure Purview의 Erwin 아티팩트 계보

사용자는 Erwin 아티팩트를 이름, 설명 또는 다른 세부 정보별로 검색하여 관련 결과를 확인할 수 있습니다. 자산 개요 및 속성 탭에 설명, 속성 및 기타 정보와 같은 기본 세부 정보가 표시됩니다. 계보 탭 아래에는 Erwin 테이블과 뷰 간의 자산 관계가 표시됩니다. 따라서 Erwin 뷰에는 테이블의 계보 정보가 있습니다. 

:::image type="content" source="./media/how-to-lineage-erwin/lineage.png" alt-text="계보가 Erwin에 렌더링되는 방식을 보여주는 스크린샷" lightbox="./media/how-to-lineage-erwin/lineage.png":::


## <a name="next-steps"></a>다음 단계

- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- [Azure Data Factory를 연결하여 자동화된 계보 푸시](how-to-link-azure-data-factory.md)
