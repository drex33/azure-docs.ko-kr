---
title: Azure Data Explorer와 Azure Synapse Dat Explorer의 차이점(미리 보기)
description: 이 문서에서는 Azure Data Explorer와 Azure Synapse Data Explorer 간의 차이점을 설명 합니다.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: maraheja
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: a6368d0431d2ce388f2d5bed37d125a34b4d5094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029605"
---
# <a name="what-is-the-difference-between-azure-synapse-data-explorer-and-azure-data-explorer-preview"></a>Azure Synapse Data Explorer와 Azure Data Explorer의 차이점은 무엇인가요? (미리 보기)

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 독립형 데이터 탐색 서비스입니다. 서비스를 실행하는 동일한 기본 기술을 Azure Synapse에서 통합 분석 서비스로 사용하여 데이터 웨어하우스 및 데이터 엔지니어링 기계 학습 시나리오를 위해 설계된 기존 SQL 및 Spark 서비스를 보완할 수 있습니다.

데이터 탐색 기능의 네이티브 통합은 분석 로그 및 시계열 워크로드를 Synapse Data Explorer 풀 형태의 Synapse로 가져옵니다. 이러한 풀은 다른 Synapse 기능에 대한 연결을 더욱 간편하게 관리하는 기능을 제공합니다. 모든 컴퓨터 및 사용자 데이터를 한 곳에서 사용하고 액세스할 수 있으며 거의 실시간으로 Data Explorer의 예비 기능을 활용하여 비즈니스 가치를 추구하는 솔루션을 빌드할 수 있습니다.

빅 데이터 및 분석 기능을 결합하는 통합 솔루션을 찾고 있다면 Synapse Data Explorer로 시작하는 것이 좋습니다.

## <a name="capability-support"></a>기능 지원

| 범주 | 기능 | Azure Data Explorer | Synapse Data Explorer |
|--|--|--|--|
| **보안** | VNET | VNet 삽입 및 Azure Private Link 지원 | Synapse 관리형 VNET의 일부로 자동으로 통합되는 Azure Private Link에 대한 지원 |
|  | CMK | ✓ | Synapse 작업 영역 구성에서 자동으로 상속됨 |
|  | 방화벽 | ✗ | Synapse 작업 영역 구성에서 자동으로 상속됨 |
| **비즈니스 연속성** | 가용성 영역 | 선택 사항 | 가용성 영역을 사용할 수 있는 경우 기본적으로 사용하도록 설정됨 |
| **SKU** | 컴퓨팅 옵션 | 22개 이상의 Azure VM SKU 선택 | Synapse 워크로드 유형 SKU로 간소화 |
| **통합** | 기본 제공 수집 파이프라인 | Event Hub, Event Grid, IoT Hub | 관리되지 않는 VNet에 대해 Azure Portal을 통해 지원되는 Event Hub, Event Grid 및 IoT Hub |
|  | Spark 통합 | Azure Data Explorer 연결된 서비스: Azure Active Directory 통과 인증, Synapse 작업 영역 MSI 및 서비스 주체를 지원하는 기본 제공 kusto Spark 통합 | Azure Active Directory 통과 인증, Synapse 작업 영역 MSI 및 서비스 주체를 지원하는 기본 제공 Kusto Spark 커넥터 통합 |
|  | KQL 아티팩트 관리 | ✗ | KQL 쿼리 저장 및 Git와 통합 |
|  | 메타데이터 동기화 | ✗ | 외부 테이블은 다른 Synapse 서비스에서 사용하기 위해 중앙 Synapse 메타 저장소와 동기화됩니다. |
| **기능** | KQL 쿼리 | ✓ | ✓ |
|  | API 및 SDK | ✓ | ✓ |
|  | 커넥터 | ✓ | ✓ |
|  | 쿼리 도구 | ✓ | ✓ |
| **가격** | 비즈니스 모델 | 여러 미터를 사용하는 비용 및 청구 모델: Azure Data Explorer IP 표시, 컴퓨팅, 스토리지 및 네트워킹 | 두 가지 미터의 VCore 청구 모델: VCore 및 스토리지 |
|  | 예약 인스턴스 | ✓ | ✗ |

## <a name="next-steps"></a>다음 단계

[Azure Synapse Analytics의 Data Explorer](data-explorer-overview.md)
