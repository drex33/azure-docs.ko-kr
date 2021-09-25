---
title: Azure Data Factory와의 차이점
description: Azure Synapse Analytics의 데이터 통합 기능이 Azure Data Factory의 데이터 통합 기능과 어떻게 다른지 알아봅니다.
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: d8320c6356f310584d24c51a384446dfdcb22afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128608169"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics와 Azure Data Factory의 데이터 통합

Azure Synapse Analytics에서 Synapse 파이프라인 및 데이터 흐름과 같은 데이터 통합 기능은 Azure Data Factory의 기능을 기반으로 합니다. 자세한 내용은 [Azure Data Factory](../../data-factory/introduction.md)를 참조하세요.


## <a name="available-features-in-adf--azure-synapse-analytics"></a>ADF 및 Azure Synapse Analytics에서 사용할 수 있는 기능

사용할 수 있는 기능은 다음 표를 참조하세요.

| 범주                 | 기능    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **통합 런타임**  | SSIS 및 SSIS 통합 런타임 사용 | ✓ | ✗ |
|                          | 지역 간 통합 런타임 지원(데이터 흐름) | ✓ | ✗ |
|                          | 통합 런타임 공유 | ✓<br><small>*여러 데이터 팩터리에서 공유 가능* | ✗ |
| **파이프라인 작업** | SSIS 패키지 작업 | ✓ | ✗ |
|                          | 파워 쿼리 작업 지원 | ✓ | ✗ |
| **템플릿 갤러리 및 지식 센터** | 솔루션 템플릿 | ✓<br><small>*Azure Data Factory 템플릿 갤러리* | ✓<br><small>*Synapse 작업 영역 지식 센터* |
| **GIT 리포지토리 통합** | Git 통합. | ✓ | ✓ |
| **Monitoring**           | 데이터 흐름에 대한 Spark 작업 모니터링 | ✗ | ✓<br><small>*Synapse Spark 풀 활용* |

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Storage gen2 계정으로 데이터를 수집](data-integration-data-lake.md)하는 방법을 학습하여 Synapse 작업 영역에서 데이터 통합을 시작합니다.
