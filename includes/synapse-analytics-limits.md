---
title: 포함 파일
description: 포함 파일
services: synapse-analytics
author: jonburchel
ms.service: synapse-analytics
ms.topic: include
ms.date: 09/22/2021
ms.author: jburchel
ms.custom: include file
ms.openlocfilehash: d97a314a5928de17ad878aee5dfc14b5519d0f80
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129095980"
---
Azure Synapse Analytics에는 고객의 구독이 서로의 워크로드로부터 보호되도록 하는 다음과 같은 기본값 한도가 있습니다. 구독에 대한 최대 한도를 높이려면 고객 지원에 문의하세요.

### <a name="synapse-workspace-limits"></a>Synapse 작업 영역 한도

| 리소스 | 기본 제한 | 최대 제한 | 
| -------- | ------------- | ------------- |
| Azure 구독 내 Synapse 작업 영역 | 20 | 20 |

### <a name="synapse-pipeline-limits"></a>Synapse 파이프라인 한도

| 리소스 | 기본 제한 | 최대 제한 |
| -------- | ------------- | ------------- |
| Synapse 작업 영역 내 Synapse 파이프라인 | 800 | 800 |
| 작업 영역 내 총 엔터티(예: 파이프라인, 데이터 세트, 트리거, 연결된 서비스, 프라이빗 엔드포인트 및 통합 런타임) 수 | 5,000 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 한 작업 영역의 Azure-SSIS Integration Runtime에 대한 총 CPU 코어 수 | 256 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 작업 영역 내 모든 파이프라인에서 공유되는 작업 영역당 동시 파이프라인 실행 수 | 10000  | 10000 |
| 작업 영역당 [Azure Integration Runtime 지역](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)별 동시 외부 활동 실행 수<br><small>외부 활동은 통합 런타임에서 관리되지만 Databricks, 저장 프로시저, HDInsight, 웹 등을 포함한 연결된 서비스에서 실행됩니다. 자체 호스팅 IR에는 이 제한이 적용되지 않습니다.</small> | 3,000 | 3,000 |
| 작업 영역당 [Azure Integration Runtime 지역](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)별 동시 파이프라인 활동 실행 수 <br><small>파이프라인 활동은 Lookup, GetMetadata 및 Delete를 포함한 통합 런타임에서 실행됩니다. 자체 호스팅 IR에는 이 제한이 적용되지 않습니다.</small> | 1,000 | 1,000                                                        |
| 작업 영역당 [Azure Integration Runtime 지역](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)별 동시 제작 작업 수<br><small>테스트 연결을 포함하여 폴더 목록과 테이블 목록을 찾아보고 데이터를 미리 봅니다. 자체 호스팅 IR에는 이 제한이 적용되지 않습니다.</small> | 200 | 200                                                          |
| 작업 영역당 [Azure Integration Runtime 지역](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)별 동시 데이터 통합 단위<sup>1</sup> 사용량| 지역 그룹 1<sup>2</sup>: 6,000<br>지역 그룹 2<sup>2</sup>: 3,000<br>지역 그룹 3<sup>2</sup>: 1,500<br>관리형 가상 네트워크<sup>2:</sup>2,400 | 지역 그룹 1<sup>2</sup>: 6,000<br/>지역 그룹 2<sup>2</sup>: 3,000<br/>지역 그룹 3<sup>2</sup>: 1,500<br>관리형 가상 네트워크: [지원 담당자에게 문의하세요.](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| 파이프라인당 최대 활동 수(컨테이너에 대한 내부 활동 포함) | 40 | 40 |
| 단일 자체 호스팅 통합 런타임에 대해 만들 수 있는 연결된 통합 런타임의 최대 수 | 100 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 파이프라인당 최대 매개 변수 수 | 50 | 50 |
| ForEach 항목 | 100,000 | 100,000 |
| ForEach 병렬 처리 | 20 | 50 |
| 파이프라인당 최대 대기 중 실행 수 | 100 | 100 |
| 식당 문자 | 8,192 | 8,192 |
| 최소 연속 창 트리거 간격 | 5분 | 15분 |
| 파이프라인 활동 실행에 대한 최대 시간 제한 | 7 일 | 7 일 |
| 파이프라인 개체에 대한 개체당 바이트 수<sup>3</sup> | 200KB | 200KB |
| 데이터 세트 및 연결된 서비스 개체에 대한 개체당 바이트 수<sup>3</sup> | 100KB | 2,000KB |
| 각 작업 실행에 대한 페이로드당 바이트<sup>4</sup> | 896KB | 896KB |
| 복사 작업 실행당 데이터 통합 단위 수<sup>1</sup> | 256 | 256 |
| API 호출 쓰기 | 1,200/시간 | 1,200/시간<br/><br/> 이 제한은 Azure Synapse Analytics가 아닌 Azure Resource Manager가 부과합니다. |
| API 호출 읽기 | 12,500/시간 | 12,500/시간<br/><br/> 이 제한은 Azure Synapse Analytics가 아닌 Azure Resource Manager가 부과합니다. |
| 분당 모니터링 쿼리 | 1,000 | 1,000 |
| 데이터 흐름 디버그 세션의 최대 시간 | 8시간 | 8시간 |
| 통합 런타임당 동시 데이터 흐름 수 | 50 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 관리 되는 vNet의 통합 런타임 당 동시 데이터 흐름 수| 20 | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| 사용자당 작업 영역별 동시 데이터 흐름 디버그 세션 수 | 3 | 3 |
| 데이터 흐름 Azure IR TTL 제한 | 4시간 |  4시간 |
| 작업 영역 내 메타데이터 엔터티 크기 한도 | 2GB | [고객 지원에 문의하세요](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> DIU(데이터 통합 단위)는 클라우드 간 복사 작업에 사용됩니다. [데이터 통합 단위(버전 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units)에서 자세히 알아보세요. 청구에 대한 자세한 내용은 [Azure Synapse Analytics 가격 책정](https://azure.microsoft.com/pricing/details/synapse-analytics/)을 참조하세요.

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime)은 데이터 규정 준수, 효율성 및 네트워크 송신 비용 절감을 보장하기 위해 [전역적으로 사용](https://azure.microsoft.com/global-infrastructure/services/)할 수 있습니다. 

| 지역 그룹 | 영역 |
| -------- | ------ |
| 지역 그룹 1 | 미국 중부, 미국 동부, 미국 동부 2, 북유럽, 서유럽, 미국 서부, 미국 서부 2 |
| 지역 그룹 2 | 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 인도 중부, 일본 동부, 미국 중북부, 미국 중남부, 동남 아시아, 미국 중서부 |
| 지역 그룹 3 | 다른 지역 |
관리 되는 가상 네트워크를 사용 하는 경우 모든 지역 그룹의 DIU (데이터 통합 단위)는 2400입니다.

<sup>3</sup> 파이프라인, 데이터 세트 및 연결된 서비스 개체에서 워크로드의 논리적 그룹화를 나타냅니다. 이러한 개체에 대한 제한은 Azure Synapse Analytics를 사용하여 이동 및 처리할 수 있는 데이터 양과는 관련이 없습니다. Synapse Analytics는 페타바이트 규모의 데이터를 처리할 수 있도록 설계되었습니다.

<sup>4</sup> 각 활동 실행에 대한 페이로드에는 활동 구성, 관련 데이터 세트 및 연결된 서비스 구성(있는 경우) 및 활동 유형별로 생성된 시스템 속성의 일부가 포함됩니다. 이러한 페이로드 크기에 대한 제한은 Azure Synapse Analytics를 사용하여 이동 및 처리할 수 있는 데이터 양과는 관련이 없습니다. 이 제한에 도달하는 경우 [증상 및 권장 사항](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large)에 대해 알아보세요.

### <a name="dedicated-sql-pool-limits"></a>전용 SQL 풀 한도
Azure Synapse Analytics에서 전용 SQL 풀의 용량 제한에 대한 세부 정보는 [전용 SQL 풀 리소스 한도](../articles/synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)을 참조하세요.

### <a name="web-service-call-limits"></a>웹 서비스 호출 제한
Azure Resource Manager에는 API 호출 제한이 있습니다. [Azure 리소스 관리자 API 제한](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)범위 안의 속도로 API 호출을 수행할 수 있습니다.
