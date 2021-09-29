---
title: 컴퓨팅 최적화 사용 중지
description: 데이터 흐름 컴퓨팅 최적화 옵션이 사용 중지됨
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: tutorial
ms.date: 06/29/2021
ms.openlocfilehash: 2ef5e68f9629feab42a4417b4e08697038947495
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661833"
---
# <a name="retirement-of-data-flow-compute-optimized-option"></a>데이터 흐름 컴퓨팅 최적화 옵션 사용 중지

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 및 Azure Synapse Analytics 데이터 흐름은 그래픽 디자인 패러다임을 사용하여 대규모 ETL 작업의 데이터를 변환하는 소형 코드 메커니즘을 제공합니다. 데이터 흐름은 Azure Data Factory 및 서버리스 Integration Runtime 장비에서 실행됩니다. Azure Data Factory 및 Azure Synapse Analytics Integration Runtime의 확장성 때문에, 대규모 데이터 흐름의 실행에 사용되는 Azure Databricks Spark 환경에 대해 메모리 최적화, 범용 및 컴퓨팅 최적화라는 세 가지 컴퓨팅 옵션을 사용할 수 있습니다. 메모리 최적화 및 범용 프로덕션 워크로드에 대한 Integration Runtime 사용할 데이터 흐름 컴퓨팅의 권장 클래스입니다. 컴퓨팅 최적화는 데이터 흐름의 일반적인 사용 사례에 충분하지 않은 경우가 많으므로 프로덕션 워크로드에서 범용 또는 메모리 최적화 데이터 흐름을 사용하는 것이 좋습니다.

## <a name="migration-steps"></a>마이그레이션 단계

지금부터 2024년 8월 31일까지 컴퓨팅 최적화 데이터 흐름은 기존 파이프라인에서 계속 작동합니다. 서비스 중단을 방지하려면 2024년 8월 31일 이전에 기존 컴퓨팅 최적화 데이터 흐름을 제거하고 아래 단계에 따라 새 Azure Integration Runtime 및 데이터 흐름 활동을 만드세요. 새 데이터 흐름 활동을 만드는 경우:

1. 컴퓨팅 형식으로 "범용" 또는 "메모리 최적화"를 사용하여 새 Azure Integration Runtime을 만듭니다.
2. 이러한 컴퓨팅 유형 중 하나를 사용하여 데이터 흐름 활동을 설정합니다.

   :::image type="content" source="media/data-flow/compute-types.png" alt-text="컴퓨팅 형식":::

## <a name="comparison-between-different-compute-options"></a>다양한 컴퓨팅 옵션 비교 

| 컴퓨팅 옵션              | 성능                                                  |
| :-------------------- | :----------------------------------------------------------- |
| 범용 데이터 흐름(기본) | 프로덕션 워크로드의 일반적인 사용 사례에 적합 |
| 메모리 최적화 데이터 흐름(표준) | 대규모 데이터 세트 및 많은 계산으로 작업하는 경우의 데이터 흐름에 가장 적합한 런타임 |
| 컴퓨팅 최적화 데이터 흐름(사용되지 않음). | 프로덕션 워크로드에는 권장되지 않습니다. |

* [범용 및 메모리 최적화 데이터 흐름에 사용할 수 있는 업데이트된 최신 가격 책정은 Azure Data Factory 가격 책정 페이지를 방문하세요.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
* [데이터 흐름 FAQ에서 자세한 내용을 확인하세요.](/azure/data-factory/frequently-asked-questions#mapping-data-flows)  
* [Microsoft Q&A에서 데이터 흐름에 대한 질문 게시 및 답변 찾기](https://aka.ms/datafactoryqa)