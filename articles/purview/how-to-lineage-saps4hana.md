---
title: SAP S/4HANA의 메타데이터 및 계보
description: 이 문서에서는 SAP S/4HANA 원본에서 데이터 계보 추출을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 122b7a0b826c43f43b5e43d4ba2cb5e42fd52a16
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422485"
---
# <a name="how-to-get-lineage-from-sap-s4hana-into-azure-purview"></a>SAP S/4HANA에서 Azure Purview로 계보를 가져오는 방법

이 문서에서는 Azure Purview에서 SAP S/4HANA 원본의 데이터 계보 양상을 설명합니다. Purview에서 SAP S/4HANA의 데이터 계보를 확인하기 위한 필수 구성 요소는 [SAP S/4HANA를 검사](../purview/register-scan-saps4hana-source.md)하는 것입니다. 

## <a name="lineage-of-sap-s4hana-artifacts-in-azure-purview"></a>Azure Purview의 SAP S/4HANA 아티팩트 계보

사용자는 SAP S/4HANA 아티팩트를 이름, 설명 또는 다른 세부 정보별로 검색하여 관련 결과를 확인할 수 있습니다. 자산 개요 및 속성 탭에 설명, 속성 및 기타 정보와 같은 기본 세부 정보가 표시됩니다. 계보 탭 아래에서 SAP S/4HANA 테이블 및 뷰 간의 자산 관계가 표시됩니다. 따라서 SAP S/4HANA 뷰는 테이블의 계보 정보를 포함합니다. 

파생된 계보는 열 수준에서도 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview의 데이터 계보에 대해 알아보기](catalog-lineage-user-guide.md)
- ADF를 사용하여 SAP S/4HANA에서 Azure로 데이터를 이동하는 경우 데이터 이동 실행 시간의 일부로 계보를 추적할 수 있습니다. [Azure Data Factory를 연결하여 자동 계보 푸시](how-to-link-azure-data-factory.md)
