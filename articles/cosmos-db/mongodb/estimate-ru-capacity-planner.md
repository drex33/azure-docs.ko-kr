---
title: Azure Cosmos DB Capacity Planner를 사용하여 비용 예상 - Mongo DB용 API
description: Azure Cosmos DB Capacity Planner를 사용하여 워크로드에 필요한 처리량(RU/초)과 비용을 예상할 수 있습니다. 이 문서에서는 MongoDB용 Azure Cosmos DB API를 사용하는 경우 필요한 처리량과 비용을 Capacity Planner를 사용하여 예상하는 방법을 설명합니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: dech
ms.openlocfilehash: a6c430462185ebac38c196d5aa80fc0d676f4911
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030359"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner---azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB Capacity Planner를 사용하여 RU/초 예상 - MongoDB용 Azure Cosmos DB API
[!INCLUDE[appliesto-sql-api](../includes/appliesto-mongodb-api.md)]

> [!NOTE]
> Azure Cosmos DB로 데이터 마이그레이션을 계획 중이고 기존 분할 및 복제된 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해서도 읽어보세요. 
>

비용과 성능을 최적화하려면 워크로드에 적합한 크기의 프로비저닝된 처리량 또는 [요청 단위(RU/초)](../request-units.md)를 사용하는 데이터베이스 및 컬렉션을 구성해야 합니다. 이 문서에서는 Azure Cosmos DB [Capacity Planner](https://cosmos.azure.com/capacitycalculator/)를 통해 MongoDB용 Azure Cosmos DB API를 사용하는 경우 워크로드에 필요한 RU/초 및 비용을 예상하는 방법을 설명합니다. SQL API를 사용하는 경우 [SQL API에서 용량 계산기를 사용](../estimate-ru-with-capacity-planner.md)하는 방법 문서를 참조하세요.

[!INCLUDE [capacity planner modes](../includes/capacity-planner-modes.md)]

## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a><a id="basic-mode"></a>기본 모드를 사용하여 프로비저닝된 처리량 및 비용 예상
기본 모드를 사용하여 워크로드에 대한 예상 값을 빠르게 알아보려면 [Capacity Planner](https://cosmos.azure.com/capacitycalculator/)로 이동합니다. 워크로드에 따라 다음 매개 변수를 입력합니다.

|**Input**  |**설명**  |
|---------|---------|
| API |MongoDB API를 선택합니다. |
|지역 수|MongoDB용 Azure Cosmos DB API는 모든 Azure 지역에서 사용할 수 있습니다. 워크로드에 필요한 지역 수를 선택하세요. 지역 수를 계정과 연결할 수 있습니다. 자세한 내용은 [전역 배포](../distribute-data-globally.md)를 참조하세요.|
|다중 지역 쓰기|[다중 지역 쓰기](../distribute-data-globally.md#key-benefits-of-global-distribution)를 사용하도록 설정하면 애플리케이션이 모든 Azure 지역을 읽고 쓸 수 있습니다. 다중 지역 쓰기를 사용하지 않도록 설정하면 애플리케이션이 데이터를 단일 지역에 쓸 수 있습니다. <br/><br/> 여러 다른 지역에서 대기 시간이 짧은 쓰기가 필요한 활성-활성 워크로드가 있어야 하는 경우 다중 지역 쓰기를 사용하도록 설정합니다. 예를 들어, 여러 다른 지역에서 대용량 데이터베이스에 데이터를 쓰는 IOT 워크로드입니다. <br/><br/> 다중 지역 쓰기는 99.999% 읽기 및 쓰기 가용성을 보장합니다. 다중 지역 쓰기에는 단일 쓰기 지역보다 더 많은 처리량이 필요합니다. 자세한 내용은 [단일 쓰기 지역과 다중 쓰기 지역의 RU 차이점](../optimize-cost-regions.md) 문서를 참조하세요.|
|트랜잭션 저장소에 저장된 총 데이터 |단일 지역의 트랜잭션 저장소에 저장된 총 예상 데이터(GB)입니다.|
|분석 저장소 사용| [Synapse 분석 저장소](../synapse-link.md)를 사용하려면 **설정** 을 선택합니다. **분석 저장소에 저장되는 총 데이터** 를 입력합니다. 이 값은 단일 지역의 분석 저장소에 저장되는 예상 데이터(GB)를 나타냅니다.  |
|항목 크기|문서의 예상 크기로, 1KB에서 2MB 사이입니다. |
|찾기 수/초 |지역별로 1초당 실행되는 예상 찾기 작업 수입니다. |
|Inserts/sec |지역별로 1초당 실행되는 예상 삽입 작업 수입니다. |
|업데이트 수/초 |지역별로 예상되는 초당 업데이트 작업 수입니다. 자동 인덱싱을 선택하는 경우 업데이트 작업의 예상 RU/s가 업데이트별로 변경되는 하나의 속성으로 계산됩니다. |
|삭제 수/초 |지역별로 예상되는 초당 삭제 작업 수입니다. |

필요한 세부 정보를 채운 다음, **계산** 을 선택합니다. **비용 추정** 탭은 스토리지와 프로비저닝된 처리량의 총비용을 보여 줍니다. 이 탭에서 **세부 정보 표시** 링크를 확장하여 다양한 CRUD 및 쿼리 요청에 필요한 처리량 내역을 확인할 수 있습니다. **필드의 값을 변경할 때마다 계산을 선택하여 예상 비용을 다시 계산합니다.**

:::image type="content" source="./media/estimate-ru-capacity-planner/basic-mode-mongodb-api.png" alt-text="Capacity Planner 기본 모드" border="true":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a><a id="advanced-mode"></a>고급 모드를 사용하여 프로비저닝된 처리량과 비용 추정

고급 모드를 사용하면 RU/s 추정에 영향을 주는 추가 설정을 제공할 수 있습니다. 이 옵션을 사용하려면 [용량 플래너](https://cosmos.azure.com/capacitycalculator/)로 이동하고 Azure에 사용하는 계정으로 도구에 **로그인** 합니다. 로그인 옵션은 오른쪽 모서리에서 사용할 수 있습니다.

로그인한 후에는 기본 모드보다 더 많은 필드를 볼 수 있습니다. 워크로드에 따라 다른 매개 변수를 입력합니다.

|**Input**  |**설명**  |
|---------|---------|
|API|Azure Cosmos DB는 다중 모델, 다중 API 서비스입니다. MongoDB API를 선택합니다. |
|지역 수|MongoDB용 Azure Cosmos DB API는 모든 Azure 지역에서 사용할 수 있습니다. 워크로드에 필요한 지역 수를 선택하세요. 지역 수를 Cosmos 계정과 연결할 수 있습니다. 자세한 내용은 [전역 배포](../distribute-data-globally.md)를 참조하세요.|
|다중 지역 쓰기|[다중 지역 쓰기](../distribute-data-globally.md#key-benefits-of-global-distribution)를 사용하도록 설정하면 애플리케이션이 모든 Azure 지역을 읽고 쓸 수 있습니다. 다중 지역 쓰기를 사용하지 않도록 설정하면 애플리케이션이 데이터를 단일 지역에 쓸 수 있습니다. <br/><br/> 여러 다른 지역에서 대기 시간이 짧은 쓰기가 필요한 활성-활성 워크로드가 있어야 하는 경우 다중 지역 쓰기를 사용하도록 설정합니다. 예를 들어, 여러 다른 지역에서 대용량 데이터베이스에 데이터를 쓰는 IOT 워크로드입니다. <br/><br/> 다중 지역 쓰기는 99.999% 읽기 및 쓰기 가용성을 보장합니다. 다중 지역 쓰기에는 단일 쓰기 지역보다 더 많은 처리량이 필요합니다. 자세한 내용은 [단일 쓰기 지역과 다중 쓰기 지역의 RU 차이점](../optimize-cost-regions.md) 문서를 참조하세요.|
|기본 일관성|MongoDB용 Azure Cosmos DB API에서는 5가지 일관성 수준을 지원하므로, 개발자가 일관성, 가용성, 대기 시간 간의 균형을 맞출 수 있습니다. 자세한 내용은 [일관성 수준](../consistency-levels.md) 문서를 참조하세요. <br/><br/> 기본적으로 MongoDB용 API에서는 세션 일관성을 사용하여 세션에서 고유 쓰기를 읽을 수 있는 기능을 보장합니다. <br/><br/> 강력한 부실 또는 제한된 부실을 선택하면 세션, 일관된 접두사, 최종 일관성과 비교할 때 읽기에 필요한 RU/초보다 두 배의 용량이 필요합니다. 다중 지역 쓰기와의 강력한 일관성은 지원되지 않으며 강력한 일관성이 있는 단일 지역 쓰기로 자동으로 기본 설정됩니다. |
|인덱싱 정책| **해제** 옵션을 선택하면 어떤 속성도 인덱싱되지 않습니다. 이 경우 쓰기의 RU 크기가 가장 낮습니다. 모든 쿼리에서 _id 필드 및 분할 키만 사용하여 쿼리하려면(쿼리마다 둘 다 사용) 인덱싱 정책을 해제하세요.<br/><br/> **자동** 옵션을 선택하면 3.6 이상 버전의 MongoDB용 API에서는 자동으로 _id 필드를 인덱싱합니다. 자동 인덱싱을 선택하는 경우 와일드 카드 인덱스를 설정하는 것과 동일합니다(모든 속성이 자동 인덱싱됨). 유연하고 효율적인 쿼리를 위해 모든 필드에서 와일드카드 인덱스를 사용하세요.<br/><br/>**사용자 지정** 옵션을 선택하는 경우 다중 키 인덱스 또는 복합 인덱스를 사용하여 인덱싱되는 속성 수를 설정할 수 있습니다. 나중에 양식에서 인덱싱되는 속성 수를 입력할 수 있습니다. 자세한 내용은 MongoDB용 API의 [인덱스 관리](../mongodb-indexing.md)를 참조하세요.|
|트랜잭션 저장소에 저장되는 총 데이터 |단일 지역의 트랜잭션 저장소에 저장되는 총 예상 데이터(GB)입니다.|
|분석 저장소 사용| [Synapse 분석 저장소](../synapse-link.md)를 사용하려면 **설정** 을 선택합니다. **분석 저장소에 저장되는 총 데이터** 를 입력합니다. 이 값은 단일 지역의 분석 저장소에 저장되는 예상 데이터(GB)를 나타냅니다.  |
|워크로드 모드|워크로드 볼륨이 일정한 경우 **일정** 옵션을 선택합니다. <br/><br/> 시간이 지남에 따라 워크로드 볼륨이 변경되는 경우 **가변** 옵션을 선택합니다.  예를 들어 특정 날짜 또는 한 달 동안입니다. 가변 워크로드 옵션을 선택하는 경우 다음 설정을 사용할 수 있습니다.<ul><li>최대 시간 비율: 워크로드에 최대(가장 높은) 처리량이 필요한 한 달의 시간 백분율입니다. </li></ul> <br/><br/> 예를 들어, 평일 오전 9시부터 오후 6시까지의 업무 시간 동안 작업이 많은 워크로드가 있는 경우 최대 시간의 백분율은 최대 45시간/730시간/월 = ~6%입니다.<br/><br/>사용량 최대 간격과 사용량이 적은 간격을 사용하면 [프로비저닝된 처리량을 프로그래매틱 방식으로 스케일링](../set-throughput.md#update-throughput-on-a-database-or-a-container) 업하고 다운로드하여 비용을 최적화할 수 있습니다.|
|항목 크기|문서의 크기로, 1KB에서 2MB 사이입니다. 여러 샘플 항목에 대한 예상을 추가할 수 있습니다. <br/><br/>더 정확한 추정을 위해 **샘플 업로드(JSON)**  문서도 작성할 수 있습니다.<br/><br/>워크로드에 같은 컨테이너에 있는 여러 형식의 항목(다른 JSON 콘텐츠 포함)이 있으면 여러 JSON 문서를 업로드하고 추정치를 가져올 수 있습니다. **새 항목 추가** 단추를 사용하여 여러 샘플 JSON 문서를 추가하세요.|
| 작업 유형 | **찾기**, **집계**, **수정** 등의 작업 유형입니다.  |
| 호출당 RU(요청 단위) 크기 | 선택한 작업 유형 실행에 예상되는 RU/초 크기입니다. |
| 지역별 호출 수/초 | 지역별로 1초당 실행되는 선택한 작업 유형 수입니다. |

**예상 값 저장** 단추를 사용하여 현재 예상 값이 포함된 CSV 파일을 다운로드할 수도 있습니다.

:::image type="content" source="./media/estimate-ru-capacity-planner/advanced-mode-mongodb-api.png" alt-text="Capacity Planner 고급 모드" border="true":::

Capacity Planner에 표시된 가격은 처리량과 스토리지에 대한 퍼블릭 가격 책정 요율을 기반으로 한 예상 값입니다. 모든 가격은 미국 달러로 표시됩니다. 지역별 모든 요금을 보려면 [Azure Cosmos DB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.  

## <a name="next-steps"></a>다음 단계

* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* [Azure Cosmos DB 가격 책정 모델](../how-pricing-works.md)에 관해 자세히 알아봅니다.
* 새 [Cosmos 계정, 데이터베이스 및 컨테이너](../create-cosmosdb-resources-portal.md)를 만듭니다.
* [프로비저닝된 처리량 비용을 최적화](../optimize-cost-throughput.md)하는 방법을 알아봅니다.
* [예약 용량으로 비용을 최적화](../cosmos-db-reserved-capacity.md)하는 방법을 알아봅니다.
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
