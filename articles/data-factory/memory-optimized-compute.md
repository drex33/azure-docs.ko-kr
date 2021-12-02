---
title: 데이터 흐름에 대 한 메모리 액세스에 최적화 된 계산 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse의 메모리 액세스에 최적화 된 계산 형식 설정에 대해 알아봅니다.
ms.author: jburchel
author: jonburchel
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 11/12/2021
ms.openlocfilehash: 347b0b6f8c14257a14c122be446a47a9aa479ee4
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487818"
---
# <a name="memory-optimized-compute-type-for-data-flows-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 데이터 흐름에 대 한 메모리 액세스에 최적화 된 계산 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 및 Azure Synapse의 데이터 흐름 작업은 [계산 형식 설정을](control-flow-execute-data-flow-activity.md#type-properties) 지원 하 여 작업의 비용 및 성능에 대 한 클러스터 구성을 최적화 합니다.  설정에 대 한 기본 선택은 **일반** 이며 대부분의 데이터 흐름 워크 로드에서 충분 합니다. 일반적으로 범용 클러스터는 성능 및 비용의 균형을 극대화 합니다.  그러나 **메모리 최적화** 된 설정은 클러스터에 대해 코어 당 사용 가능한 메모리를 최대화 하 여 일부 시나리오에서 성능을 크게 향상 시킬 수 있습니다.

## <a name="when-to-use-the-memory-optimized-compute-type"></a>메모리 액세스에 최적화 된 계산 유형을 사용 하는 경우

데이터 흐름에서 조인과 조회가 많이 발생하는 경우 메모리 최적화 클러스터를 사용하는 것이 좋습니다. 이러한 더 많은 메모리를 많이 사용 하는 작업은 특히 추가 메모리를 사용 하는 경우에 유용 하며, 기본 계산 형식에서 발생 하는 메모리 부족 오류는 최소화 됩니다. **메모리** 액세스에 최적화 된 클러스터는 코어 당 비용이 가장 많이 발생 하지만 메모리를 많이 사용 하는 작업의 경우 파이프라인 오류가 발생할 수 있습니다. 데이터 흐름을 실행할 때 메모리 부족 오류가 발생하면 메모리 최적화 Azure IR 구성으로 전환하세요.

## <a name="next-steps"></a>다음 단계

[데이터 Flow 형식 속성](control-flow-execute-data-flow-activity.md#type-properties)
