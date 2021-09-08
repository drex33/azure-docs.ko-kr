---
title: 자동 크기 조정 모드에서 Azure Cosmos 컨테이너 및 데이터베이스를 만듭니다.
description: 자동 크기 조정 모드에서 이점, 사용 사례 및 Azure Cosmos 데이터베이스 및 컨테이너를 프로비저닝하는 방법에 대해 알아봅니다.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/18/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: 628cba9c515b58e23c984c6dcd34fa35ff63c78a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123028620"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>자동 크기 조정 처리량을 사용하여 Azure Cosmos 컨테이너 및 데이터베이스 만들기
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB를 사용하면 데이터베이스 및 컨테이너에서 표준(수동) 또는 프로비전된 처리량 자동 크기 조정을 설정할 수 있습니다. Azure Cosmos DB에서 프로비전된 처리량 지동 크기 조정을 사용하면 **데이터베이스 또는 컨테이너의 처리량(RU/s)을 자동으로 즉시 크기 조정할 수 있습니다**. 처리량은 워크로드의 가용성, 대기 시간, 처리량 또는 성능에 영향을 주지 않고 사용량을 기준으로 크기가 조정됩니다.

자동 크기 조정 프로비저닝된 처리량은 변수 또는 예측할 수 없는 트래픽 패턴이 있는 중요 업무용 워크로드에 적합하며 고성능과 규모에 대한 SLA가 필요합니다. 이 문서에서는 자동 크기 조정 프로비저닝된 처리량의 이점과 사용 사례에 대해 설명합니다.

## <a name="benefits-of-autoscale"></a>자동 크기 조정의 이점

자동 크기 조정 프로비저닝된 처리량으로 구성된 Azure Cosmos 데이터베이스 및 컨테이너의 이점은 다음과 같습니다.

* **Simple:** 자동 크기 조정을 사용하면 사용자 지정 스크립팅 또는 수동 용량 크기 조정으로 RU/s 관리의 복잡성이 제거됩니다. 

* **확장성:** 데이터베이스와 컨테이너는 필요에 따라 프로비저닝된 처리량을 자동으로 크기 조정합니다. 클라이언트 연결, 애플리케이션에 대한 중단 또는 Azure Cosmos DB SLA에 대한 영향은 없습니다.

* **비용 효율성:** 자동 크기 조정은 사용하지 않을 때 축소하여 RU/s 사용량 및 비용 사용량을 최적화하는 데 도움이 됩니다. 워크로드에 필요한 리소스에 대해서만 시간당 기준으로 비용을 지불합니다. 한 달의 모든 시간 중에서 자동 크기 조정 최대 초당 RU/s(Tmax)를 설정하고 시간의 66% 이내에서 전체 크기 Tmax를 사용하는 경우 자동 크기 조정으로 비용을 절감할 것으로 예상됩니다. 자세한 내용은 [프로비전된 처리량 표준(수동) 및 자동 크기 조정 중에서 선택하는 방법](how-to-choose-offer.md) 문서를 참조하세요.

* **고가용성:** 자동 크기 조정을 사용하는 데이터베이스 및 컨테이너는 데이터 내구성 및 고가용성을 보장하기 위해 전 세계적으로 분산된 내결함성이 있으며 가용성이 높은 동일한 Azure Cosmos DB 백 엔드를 사용합니다.

## <a name="use-cases-of-autoscale"></a>자동 크기 조정 사용 사례

자동 크기 조정 사용 사례는 다음과 같습니다.

* **가변적이거나 예측 불가능한 워크로드:** 워크로드의 사용량이 가변적이거나 예측할 수 없는 급증의 경우 자동 크기 조정은 사용량에 따라 자동으로 확장 및 축소하여 지원합니다. 계절성에 따라 서로 다른 트래픽 패턴을 가진 소매점 웹 사이트, 하루 중 다양한 시간에 급증하는 IOT 워크로드, 월 또는 연도와 같이 몇 번 최고 사용량을 보이는 기간 업무 애플리케이션 등을 예로 들 수 있습니다. 자동 크기 조정을 사용하면 더 이상 최대 또는 평균 용량을 수동으로 프로비저닝할 필요가 없습니다. 

* **새 애플리케이션:** 새 애플리케이션을 개발하고 필요한 처리량(RU/s)에 대해 잘 모르는 경우 자동 크기 조정을 사용하면 쉽게 시작할 수 있습니다. 400~4000RU/s의 자동 크기 조정 진입점으로 시작하고, 사용량을 모니터링하고, 시간에 따라 올바른 RU/s를 결정할 수 있습니다.

* **자주 사용하지 않는 애플리케이션:** 일, 주 또는 월(예: 볼륨이 낮은 애플리케이션/웹/블로그 사이트)에 몇 시간 동안만 사용되는 애플리케이션이 있는 경우 자동 크기 조정은 최대 사용량을 처리하고 초과될 때 규모를 축소하기 위해 용량을 조정합니다. 

* **개발 및 테스트 워크로드:** 사용자 또는 팀에서 근무 시간 동안 Azure Cosmos 데이터베이스 및 컨테이너를 사용하지만 야간 또는 주말에는 필요하지 않은 경우 자동 크기 조정을 통해 사용하지 않을 때 최소로 축소하여 비용을 절감할 수 있습니다. 

* **예약된 프로덕션 워크로드/쿼리:** 유휴 기간 동안 실행하려는 일련의 예약된 요청, 작업 또는 쿼리가 있는 경우 자동 크기 조정을 사용하여 쉽게 수행할 수 있습니다. 워크로드를 실행해야 하는 경우 처리량은 필요한 만큼 자동으로 크기가 조정되고 나중에 축소됩니다. 

이러한 문제에 대한 사용자 지정 솔루션을 구축하려면 상당한 시간이 필요할 뿐만 아니라 애플리케이션의 구성 또는 코드에 복잡성을 야기할 수 있습니다. 자동 크기 조정은 위의 시나리오를 즉시 사용하도록 설정하고 용량에 대한 사용자 지정 또는 수동 크기 조정의 필요성을 제거합니다. 

## <a name="how-autoscale-provisioned-throughput-works"></a>자동 크기 조정 프로비저닝된 처리량의 작동 방식

자동 크기 조정을 사용하여 컨테이너 및 데이터베이스를 구성할 때 필요한 최대 처리량 `Tmax`를 지정합니다. Azure Cosmos DB는 `0.1*Tmax <= T <= Tmax`와 같은 처리량 `T`를 크기 조정합니다. 예를 들어 최대 처리량을 20,000RU/s로 설정하면 처리량은 2000에서 20,000RU/s 사이로 크기 조정됩니다. 크기 조정은 언제든지 자동으로 또한 즉각적으로 진행되므로 지연 없이 프로비저닝된 `Tmax`까지 사용할 수 있습니다. 

매 시간마다 시스템이 해당 시간 내에 크기 조정된 최고 처리량 `T`에 대해 요금이 청구됩니다.

자동 크기 조정 최대 처리량 `Tmax`의 진입점은 400~4000RU/s 범위에서 크기 조정되는 4000RU/s에 시작합니다. `Tmax`를 1000RU/s 단위로 설정하고 언제든지 값을 변경할 수 있습니다.  

## <a name="enable-autoscale-on-existing-resources"></a>기존 리소스에서 자동 크기 조정 사용

[Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), [CLI](how-to-provision-autoscale-throughput.md#azure-cli), [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell)을 통해 기존 데이터베이스 또는 컨테이너에서 자동 크기 조정을 사용하도록 설정할 수 있습니다. 언제든지 자동 크기 조정과 표준(수동) 프로비저닝된 처리량 사이를 전환할 수 있습니다. 자세한 내용은 이 [설명서](autoscale-faq.yml#how-does-the-migration-between-autoscale-and-standard--manual--provisioned-throughput-work-)를 참조하세요.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> 자동 크기 조정에 대한 처리량 및 스토리지 제한

`Tmax`의 값의 경우 데이터베이스나 컨테이너는 총 `0.01 * Tmax GB`를 저장할 수 있습니다. 이 스토리지 용량에 도달하면 애플리케이션에 영향을 주지 않고 새 스토리지 값에 따라 최대 RU/s가 자동으로 증가합니다. 

예를 들어 최대 RU/s를 50,000RU/s(5,000~50,000RU/s 범위에서 크기 조정)에서 시작하는 경우 최대 500GB까지 데이터를 저장할 수 있습니다. 500GB를 초과하는 경우(예: 이제 스토리지가 600GB) 새 최대 RU/s는 60,000RU/s(6,000~60,000RU/s 범위에서 크기 조정)가 됩니다.

자동 크기 조정으로 데이터베이스 수준 처리량을 사용하는 경우 40GB의 스토리지를 초과하지 않는 한 처음 25개 컨테이너가 4000의 자동 크기 조정 최대 RU/s(400~4000RU/s 범위에서 크기 조정)를 공유할 수 있습니다. 자세한 내용은 이 [설명서](autoscale-faq.yml#can-i-change-the-max-ru-s-on-the-database-or-container--)를 참조하세요.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>비교 - 수동 vs 자동 크기 조정 처리량으로 구성된 컨테이너
자세한 내용은 표준(수동) 및 자동 크기 조정 처리량 중에서 선택하는 방법에 대한 이 [설명서](how-to-choose-offer.md)를 참조하세요.  

|| 표준(수동) 처리량을 사용하는 컨테이너  | 자동 크기 조정 처리량을 사용하는 컨테이너 |
|---------|---------|---------|
| **프로비저닝된 처리량(RU/s)** | 수동으로 프로비저닝됨 | 워크로드 사용량 패턴을 기반으로 자동으로 즉시 크기 조정됩니다. |
| **요청/작업의 속도 제한(429)**  | 소비가 프로비저닝된 용량을 초과하는 경우 발생할 수 있습니다. | 설정한 자동 크기 조정 처리량 범위 내에서 RU/s를 사용하는 경우에는 발생하지 않습니다.    |
| **용량 계획** |  용량 계획을 수행하고 필요한 정확한 처리량을 프로비저닝해야 합니다. |    시스템은 용량 계획 및 용량 관리를 자동으로 처리합니다. |
| **가격** | [시간당 표준(수동) RU/s 비율](https://azure.microsoft.com/pricing/details/cosmos-db/)을 사용하여 시간당 수동 프로비저닝된 RU/s에 대해 요금을 지불합니다. | 시스템이 해당 시간 내로 확장된 최대 RU/s에 대해 시간당 요금이 청구됩니다. <br/><br/> 단일 쓰기 지역 계정의 경우 [시간당 자동 크기 조정 RU/s 비율](https://azure.microsoft.com/pricing/details/cosmos-db/)을 사용하여 시간 단위로 사용되는 RU/s에 대해 요금을 지불합니다. <br/><br/>여러 쓰기 지역이 있는 계정의 경우에는 자동 크기 조정에 대한 추가 요금이 부과되지 않습니다. 동일한 [시간당 다중 영역 쓰기 RU/s 비율](https://azure.microsoft.com/pricing/details/cosmos-db/)을 사용하여 시간 단위로 사용되는 처리량에 대해 요금이 청구됩니다. |
| **워크로드 유형에 가장 적합함** |  예측 가능하고 안정적인 워크로드|   예측 불가능하고 가변적인 워크로드  |

## <a name="next-steps"></a>다음 단계

* [자동 크기 조정 FAQ](autoscale-faq.yml)를 검토합니다.
* [수동 및 자동 크기 조정 처리량 중 선택하는 방법](how-to-choose-offer.md)을 알아봅니다.
* [Azure Cosmos 데이터베이스 또는 컨테이너에서 자동 크기 조정 처리량을 프로비저닝](how-to-provision-autoscale-throughput.md)하는 방법을 알아봅니다.
* Azure Cosmos DB의 [분할](partitioning-overview.md)에 대해 자세히 알아봅니다.
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.


