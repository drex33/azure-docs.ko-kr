---
title: 구성 옵션 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 노드 컴퓨팅, 스토리지 및 지역을 포함한 하이퍼스케일(Citus) 서버 그룹에 대한 옵션입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/03/2021
ms.openlocfilehash: e2dfb0eda9d4e10c97591a6cc534adaa188d87e1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602017"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus) 구성 옵션

## <a name="compute-and-storage"></a>컴퓨팅 및 스토리지
 
하이퍼스케일(Citus) 서버 그룹의 작업자 노드 및 코디네이터 노드에 대해 개별적으로 컴퓨팅 및 스토리지 설정을 선택할 수 있습니다.  컴퓨팅 리소스는 기본 하드웨어의 논리적 CPU를 나타내는 vCore 수로 제공됩니다. 프로비저닝을 위한 스토리지 크기는 하이퍼스케일(Citus) 서버 그룹의 코디네이터 및 작업자 노드에 사용 가능한 용량을 나타냅니다. 스토리지에는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 Postgres 서버 로그가 포함됩니다.

### <a name="standard-tier"></a>표준 계층
 
| 리소스              | 작업자 노드           | 코디네이터 노드      |
|-----------------------|-----------------------|-----------------------|
| 컴퓨팅, vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| vCore당 메모리, GiB | 8                     | 4                     |
| 스토리지 크기, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 스토리지 유형          | 범용(SSD) | 범용(SSD) |
| IOPS                  | 최대 3 IOPS/GiB      | 최대 3 IOPS/GiB      |

단일 하이퍼스케일(Citus) 노드의 총 RAM 용량은 선택된 vCore 수를 기반으로 합니다.

| vCore 수 | 단일 작업자 노드, GiB RAM | 코디네이터 노드, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

프로비저닝하는 총 스토리지 크기는 각 작업자 및 코디네이터 노드에 사용할 수 있는 I/O 용량을 정의합니다.

| 스토리지 크기, TiB | 최대 IOPS |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

전체 하이퍼스케일(Citus) 클러스터의 경우 집계된 IOPS는 다음 값으로 계산됩니다.

| 작업자 노드 | 0.5 TiB, 총 IOPS | 1 TiB, 총 IOPS | 2 TiB, 총 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier"></a>기본 계층

하이퍼스케일(Citus) [기본 계층](concepts-hyperscale-tiers.md)은 하나의 노드만 있는 서버 그룹입니다.  코디네이터 노드와 작업자 노드는 구별되지 않으므로 컴퓨팅 및 스토리지 리소스를 선택하는 것은 그다지 복잡하지 않습니다.

| 리소스              | 사용 가능한 옵션     |
|-----------------------|-----------------------|
| 컴퓨팅, vCore       | 2, 4, 8               |
| vCore당 메모리, GiB | 4                     |
| 스토리지 크기, GiB     | 128, 256, 512         |
| 스토리지 유형          | 범용(SSD) |
| IOPS                  | 최대 3 IOPS/GiB      |

단일 하이퍼스케일(Citus) 노드의 총 RAM 용량은 선택된 vCore 수를 기반으로 합니다.

| vCore 수 | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

프로비저닝하는 총 스토리지 크기도 기본 계층 노드에 사용할 수 있는 I/O 용량을 정의합니다.

| 스토리지 크기, GiB | 최대 IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>영역
하이퍼스케일(Citus) 서버 그룹은 다음 Azure 지역에서 사용할 수 있습니다.

* 아메리카:
    * 브라질 남부
    * 캐나다 중부
    * 미국 중부
    * 미국 동부
    * 미국 동부 2
    * 미국 중북부
    * 미국 서부 2
* 아시아 태평양:
    * 오스트레일리아 동부
    * 일본 동부
    * 일본 서부
    * 한국 중부
    * 동남 아시아
* 유럽:
    * 프랑스 중부
    * 독일 중서부
    * 북유럽
    * 스위스 북부
    * 영국 남부
    * 서유럽

해당 지역 중 일부는 처음에는 일부 Azure 구독에서 활성화되지 않을 수 있습니다. 위 목록의 지역을 사용하려 하는데 해당 지역이 구독에 표시되지 않는 경우 또는 이 목록에 없는 지역을 사용하려는 경우 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 엽니다.

## <a name="pricing"></a>가격 책정
최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
원하는 구성의 비용을 확인할 수 있도록 [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)에서는 선택하는 옵션에 따라 **구성** 탭에 월별 비용을 표시합니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서 **항목 추가** 를 선택하고, **데이터베이스** 범주를 확장하고, **Azure Database for PostgreSQL - 하이퍼스케일(Citus)** 을 선택하여 옵션을 사용자 지정합니다.
 
## <a name="next-steps"></a>다음 단계
[포털에서 하이퍼스케일(Citus) 서버 그룹 만드는](quickstart-create-hyperscale-portal.md) 방법을 알아봅니다.
