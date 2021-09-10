---
title: 읽기 복제본 - Azure Database for MySQL
description: Azure Database for MySQL에서 읽기 복제본에 대해 알아봅니다. 영역 선택, 복제본 만들기, 복제본에 연결, 복제 모니터링 및 복제 중지
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 06/17/2021
ms.custom: references_regions
ms.openlocfilehash: 89cb9122da21887165b2330f75dd316c184de823
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567253"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Azure Database for MySQL의 읽기 복제본

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

읽기 복제본 기능을 사용하면 Azure Database for MySQL 서버에서 읽기 전용 서버로 데이터를 복제할 수 있습니다. 원본 서버에서 최대 5개의 복제본으로 복제할 수 있습니다. 읽복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

복제본은 일반 Azure Database for MySQL 서버와 비슷한 방식으로 관리하는 새로운 서버입니다. 읽기 복제본의 경우, vCore 및 스토리지에 프로비저닝된 컴퓨팅에 대한 비용이 GB/월 단위로 청구됩니다.

MySQL 복제 기능 및 문제에 대한 자세한 내용은 [MySQL 복제 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)를 참조하세요.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 _슬레이브_ 라는 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
>

## <a name="when-to-use-a-read-replica"></a>읽기 복제본을 사용하는 경우

읽기 복제본 기능은 읽기 작업이 많은 워크로드의 성능과 규모를 향상시키는 데 도움이 됩니다. 읽기 워크로드를 복제본과 격리할 수 있고, 쓰기 워크로드를 원본으로 보낼 수 있습니다.

일반적인 시나리오는 BI와 분석 워크로드가 보고를 위한 데이터 원본으로 읽기 복제본을 사용하도록 합니다.

복제본은 읽기 전용이기 때문에 원본에 대한 쓰기 용량 부담을 직접적으로 줄이지는 못합니다. 이 기능은 쓰기 작업이 많은 워크로드를 대상으로 하지 않습니다.

읽기 복제본 기능은 MySQL 동기식 복제를 사용합니다. 이 기능은 동기식 복제 시나리오를 위한 것이 아닙니다. 원본과 복제본 간에는 측정 가능한 지연이 발생합니다. 복제본의 데이터는 결과적으로 원본의 데이터와 일치하게 됩니다. 이러한 지연 시간을 수용할 수 있는 워크로드에 이 기능을 사용합니다.

## <a name="cross-region-replication"></a>지역 간 복제

원본 서버와 다른 지역에 읽기 복제본을 만들 수 있습니다. 지역 간 복제는 재해 복구 계획 또는 사용자에게 더 가까운 데이터 가져오기 등의 시나리오에 유용할 수 있습니다.

[Azure Database for MySQL 지역](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)에 원본 서버를 둘 수 있습니다.  원본 서버는 쌍을 이루는 지역 또는 유니버설 복제본 지역에 복제본이 있을 수 있습니다. 아래 그림은 원본 지역에 따라 사용할 수 있는 복제본 지역을 보여 줍니다.

[ :::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="읽기 복제본 지역":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>유니버설 복제본 지역

원본 서버가 있는 위치에 관계 없이 다음 지역에서 읽기 복제본을 만들 수 있습니다. 지원되는 유니버설 복제본 지역에는 다음이 포함됩니다.

| 지역 | 복제본 가용성 | 
| --- | --- | 
| 오스트레일리아 동부 | :heavy_check_mark: | 
| 오스트레일리아 동남부 | :heavy_check_mark: | 
| 브라질 남부 | :heavy_check_mark: | 
| 캐나다 중부 | :heavy_check_mark: |
| 캐나다 동부 | :heavy_check_mark: |
| 미국 중부 | :heavy_check_mark: | 
| 미국 동부 | :heavy_check_mark: | 
| 미국 동부 2 | :heavy_check_mark: |
| 동아시아 | :heavy_check_mark: | 
| 일본 동부 | :heavy_check_mark: | 
| 일본 서부 | :heavy_check_mark: | 
| 한국 중부 | :heavy_check_mark: |
| 한국 남부 | :heavy_check_mark: |
| 북유럽 | :heavy_check_mark: | 
| 미국 중북부 | :heavy_check_mark: | 
| 미국 중남부 | :heavy_check_mark: | 
| 동남 아시아 | :heavy_check_mark: | 
| 영국 남부 | :heavy_check_mark: | 
| 영국 서부 | :heavy_check_mark: | 
| 미국 중서부 | :heavy_check_mark: | 
| 미국 서부 | :heavy_check_mark: | 
| 미국 서부 2 | :heavy_check_mark: | 
| 서유럽 | :heavy_check_mark: | 
| 인도 중부* | :heavy_check_mark: | 
| 프랑스 중부* | :heavy_check_mark: | 
| 아랍에미리트 북부* | :heavy_check_mark: | 
| 남아프리카 북부* | :heavy_check_mark: |

> [!Note] 
> \* Azure Database for MySQL에 퍼블릭 미리 보기로 제공되는 범용 스토리지 v2가 있는 지역입니다.  <br /> \* 이러한 Azure 지역의 경우 범용 스토리지 v1 및 v2 모두에 서버를 만들 수 있습니다. 퍼블릭 미리 보기에서 범용 스토리지 v2를 사용하여 만든 서버의 경우 범용 스토리지 v2를 지원하는 Azure 지역에만 복제본 서버를 만들 수 있습니다.

### <a name="paired-regions"></a>쌍을 이루는 지역

유니버설 복제본 지역 외에도 원본 서버의 Azure 쌍을 이루는 지역에서 읽기 복제본을 만들 수 있습니다. 해당 지역의 쌍을 모르는 경우 [Azure 쌍을 이루는 지역 문서](../best-practices-availability-paired-regions.md)에서 자세히 알아볼 수 있습니다.

재해 복구 계획에 지역 간 복제본을 사용하는 경우 다른 지역 중 하나가 아닌 쌍을 이루는 지역에 복제본을 만드는 것이 좋습니다. 쌍을 이루는 지역은 동시 업데이트를 방지하고 물리적 격리 및 데이터 보존의 우선 순위를 지정합니다.  

그러나 고려해야 할 제한 사항이 있습니다. 

* 지역별 가용성: Azure Database for MySQL은 프랑스 중부, 아랍에미리트 북부 및 독일 중부에서 사용할 수 있습니다. 그러나 쌍을 이루는 지역에는 사용할 수 없습니다.

* 단방향 쌍: 일부 Azure 지역은 한 방향으로만 쌍으로 연결됩니다. 이러한 지역에는 인도 서부, 브라질 남부 및 US Gov 버지니아가 포함됩니다.
   즉, 인도 서부의 원본 서버는 인도 남부에서 복제본을 만들 수 있습니다. 그러나 인도 남부의 원본 서버는 인도 서부에서 복제본을 만들 수 없습니다. 이는 인도 서부의 보조 지역은 인도 남부이지만 인도 남부의 보조 지역은 인도 서부가 아니기 때문입니다.

## <a name="create-a-replica"></a>복제본 만들기

> [!IMPORTANT]
> 읽기 복제본 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MySQL 서버에서만 사용 가능합니다. 원본 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인합니다.

원본 서버에 기존 복제본 서버가 없는 경우 원본은 먼저 자체적으로 복제를 위한 준비를 위해 다시 시작됩니다.

복제본 만들기 워크플로를 시작하면 빈 Azure Database for MySQL 서버가 만들어집니다. 새 서버는 원본 서버에 있던 데이터로 채워집니다. 생성 시간은 원본의 데이터 양과 마지막 매주 전체 백업 이후의 시간에 따라 달라집니다. 시간은 몇 분에서 몇 시간까지 걸릴 수 있습니다. 복제본 서버는 항상 원본 서버와 동일한 리소스 그룹 및 동일한 구독에 생성됩니다. 다른 리소스 그룹 또는 다른 구독에 대한 복제본 서버를 만들려는 경우 복제본 서버를 만든 후 [이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)할 수 있습니다.

모든 복제본은 스토리지 [자동 증가](concepts-pricing-tiers.md#storage-auto-grow)에 대해 사용하도록 설정됩니다. 자동 증가 기능을 사용하면 복제본에 복제된 데이터를 유지하고 스토리지 부족 오류로 인한 복제 중단을 방지할 수 있습니다.

[Azure Portal에서 읽기 복제본을 만드는 방법](howto-read-replicas-portal.md)을 알아봅니다.

## <a name="connect-to-a-replica"></a>복제본에 연결

복제본은 생성될 때 원본 서버의 방화벽 규칙을 상속합니다. 나중에 이러한 규칙은 원본 서버와는 독립적이 됩니다.

복제본은 원본 서버에서 해당 관리자 계정을 상속합니다. 원본 서버의 모든 사용자 계정은 읽기 복제본으로 복제됩니다. 원본 서버에서 사용 가능한 사용자 계정을 사용해야만 읽기 복제본에 연결할 수 있습니다.

일반 Azure Database for MySQL 서버에서처럼 같이 해당 호스트 이름 및 유효한 사용자 계정을 사용하여 복제본에 연결할 수 있습니다. 관리 사용자 이름이 **myadmin** 인 **myreplica** 라는 서버의 경우 mysql CLI를 사용하여 복제본에 연결할 수 있습니다.

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

프롬프트가 표시되면 사용자 계정의 암호를 입력합니다.

## <a name="monitor-replication"></a>복제 모니터링

Azure Database for MySQL은 Azure Monitor에 **복제 지연 시간(초)** 메트릭을 제공합니다. 이 메트릭은 복제본에만 사용할 수 있습니다. 이 메트릭은 `seconds_behind_master`MySQL에서 사용할 수 있는 메트릭`SHOW SLAVE STATUS` 명령을 사용하여 계산됩니다. 복제 지연 시간이 워크로드에 적합하지 않은 값에 도달하면 알리도록 경고를 설정합니다.

복제 지연 시간이 늘어나면 [복제 대기 시간 문제 해결](howto-troubleshoot-replication-latency.md)을 참조하여 문제를 해결하고 가능한 원인을 파악하세요.

## <a name="stop-replication"></a>복제 중지

원본 및 복제본 간의 복제를 중지할 수 있습니다. 원본 서버와 읽기 복제본 간의 복제가 중지되면 복제본이 독립 실행형 서버가 됩니다. 독립 실행형 서버의 데이터는 복제 중지 명령이 시작될 때 복제본에서 사용할 수 있던 데이터입니다. 독립 실행형 서버는 원본 서버를 따라잡지 않습니다.

복제본에 대한 복제를 중지한다면 이전 원본 및 기타 복제본에 대한 모든 연결이 손실됩니다. 원본과 해당 복제본 사이에는 자동화된 장애 조치(failover)가 없습니다.

> [!IMPORTANT]
> 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.
> 읽기 복제본에서 복제를 중지하기 전에 복제본에 필요한 모든 데이터가 있는지 확인하십시오.

[복제본에 대한 복제를 중지](howto-read-replicas-portal.md)하는 방법을 알아봅니다.

## <a name="failover"></a>장애 조치 

원본과 해당 복제본 서버 사이에는 자동화된 장애 조치(failover)가 없습니다.

복제는 비동기식이므로 원본과 복제본 사이에 지연이 발생합니다. 지연 시간은 원본 서버에서 실행되는 워크로드의 크기와 데이터 센터 간에 발생하는 대기 시간 등 여러 가지 요인에 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 시간 범위는 몇 초에서 몇 분 사이입니다. 각 복제본에 사용 가능한 *복제본 지연 시간* 메트릭을 사용하여 실제 복제 지연 시간을 추적할 수 있습니다. 이 메트릭은 마지막으로 재생된 트랜잭션 이후의 시간을 보여 줍니다. 일정 기간 동안 복제본 지연 시간을 관찰하여 평균 지연 시간을 확인하는 것이 좋습니다. 예상 범위를 벗어나면 조치를 취할 수 있도록 복제본 지연 시간에 대한 경고를 설정할 수 있습니다.

> [!Tip]
> 복제본으로 장애 조치(failover)하는 경우 복제본이 원본에서 분리될 때 발생하는 지연 시간이 손실된 데이터의 양을 나타냅니다.

복제본으로 장애 조치(failover)하기로 결정했으면 다음을 수행합니다.

1. 복제본에 대한 복제를 중지합니다.<br/>
   이 단계는 복제본 서버에서 쓰기를 허용하도록 하는 데 필요합니다. 이 프로세스의 일부로 복제본 서버가 원본에서 분리됩니다. 복제 중지를 시작한 후 백 엔드 프로세스를 완료하는 데 일반적으로 약 2분이 소요됩니다. 이 작업의 의미를 이해하려면 이 문서의 [복제 중지](#stop-replication) 섹션을 참조하세요.

2. 애플리케이션이 (이전) 복제본을 가리키도록 합니다.<br/>
   각 서버에는 고유한 연결 문자열이 있습니다. 원본 대신 (이전) 복제본을 가리키도록 애플리케이션을 업데이트합니다.

애플리케이션이 읽기 및 쓰기를 성공적으로 처리하면 장애 조치(failover)를 완료한 것입니다. 애플리케이션에 발생하는 가동 중지 시간은 문제를 감지하고 위의 1단계와 2단계를 완료하는 시기에 따라 달라집니다.

## <a name="global-transaction-identifier-gtid"></a>GTID(전역 트랜잭션 식별자)

GTID(전역 트랜잭션 식별자)는 원본 서버에서 커밋된 각 트랜잭션을 사용하여 생성되는 고유 식별자이며 Azure Database for MySQL에서 기본적으로 해제되어 있습니다. GTID는 버전 5.7 및 8.0과 최대 16TB의 스토리지를 지원하는 서버에서만 지원됩니다. GTID 및 GTID가 복제에 사용되는 방법에 대한 자세한 내용은 MySQL의 [GTID를 사용하는 복제](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) 설명서를 참조하세요.

MySQL은 GTID 트랜잭션(GTID로 식별)과 익명 트랜잭션(GTID가 할당되지 않음) 등 두 가지 형식의 트랜잭션을 지원합니다.

GTID를 구성하는 데 사용할 수 있는 서버 매개 변수는 다음과 같습니다. 

|**서버 매개 변수**|**설명**|**기본값**|**값**|
|--|--|--|--|
|`gtid_mode`|GTID가 트랜잭션을 식별하는 데 사용되는지 여부를 나타냅니다. 모드 간 변경 작업은 오름차순으로 한 번에 한 단계씩만 진행될 수 있습니다. (예: `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: 새 트랜잭션과 복제 트랜잭션은 모두 익명이어야 합니다. <br> `OFF_PERMISSIVE`: 새 트랜잭션은 익명입니다. 복제된 트랜잭션은 익명 또는 GTID 트랜잭션이 될 수 있습니다. <br> `ON_PERMISSIVE`: 새 트랜잭션은 GTID 트랜잭션입니다. 복제된 트랜잭션은 익명 또는 GTID 트랜잭션이 될 수 있습니다. <br> `ON`: 신규 및 복제된 트랜잭션은 모두 GTID 트랜잭션이어야 합니다.|
|`enforce_gtid_consistency`|트랜잭션 측면에서 안전하게 기록될 수 있는 문만 실행하도록 허용하여 GTID 일관성을 적용합니다. GTID 복제를 사용하도록 설정하려면 먼저 이 값을 `ON`으로 설정해야 합니다. |`OFF`|`OFF`: 모든 트랜잭션은 GTID 일관성을 위반할 수 있습니다.  <br> `ON`: 어떠한 트랜잭션도 GTID 일관성을 위반해서는 안 됩니다. <br> `WARN`: 모든 트랜잭션은 GTID 일관성을 위반할 수 있지만 경고가 생성됩니다. | 

> [!NOTE]
> * GTID를 사용하도록 설정한 후에는 다시 해제할 수 없습니다. GTID를 해제해야 하는 경우 고객 지원팀에 문의하세요. 
>
> * GTID의 값을 변경하려면 모드의 오름차순으로 한 번에 한 단계씩만 수행할 수 있습니다. 예를 들어 gtid_mode가 현재 OFF_PERMISSIVE로 설정되어 있는 경우 ON_PERMISSIVE로 변경할 수 있지만 ON으로는 변경할 수 없습니다.
>
> * 복제를 일관되게 유지하기 위해 마스터/복제본 서버에 대해 업데이트할 수 없습니다.
>
> * gtid_mode=ON을 설정하기 전에 enforce_gtid_consistency를 ON으로 설정하는 것이 좋습니다.


GTID를 사용하도록 설정하고 일관성 동작을 구성하려면 [Azure Portal](howto-server-parameters.md), [Azure CLI](howto-configure-server-parameters-using-cli.md) 또는 [PowerShell](howto-configure-server-parameters-using-powershell.md)을 사용하여 `gtid_mode` 및 `enforce_gtid_consistency` 서버 매개 변수를 업데이트합니다.

원본 서버에서 GTID를 사용하는 경우(`gtid_mode` = ON) 새로 만든 복제본도 GTID를 사용하도록 설정하고 GTID 복제를 사용합니다. GTID를 사용하도록 설정하고 마스터 또는 복제본 서버를 만든 다음에는 복제를 일관되게 유지하기 위해 `gtid_mode`를 변경할 수 없습니다. 

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

### <a name="pricing-tiers"></a>가격 책정 계층

읽기 복제본은 범용 및 메모리 최적화 가격 책정 계층에서만 사용할 수 있습니다.

> [!NOTE]
> 복제 서버의 실행 비용은 복제본 서버가 실행되는 지역을 기반으로 책정됩니다.

### <a name="source-server-restart"></a>원본 서버 다시 시작

기존 복제본이 없는 원본에 대한 복제본을 만드는 경우 원본이 먼저 다시 시작하여 자체적으로 복제할 준비를 합니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.

### <a name="new-replicas"></a>새 복제본

읽기 복제본은 새로운 Azure Database for MySQL 서버로 생성됩니다. 기존 서버를 복제본으로 만들 수 없습니다. 다른 읽기 복제본의 복제본은 만들 수 없습니다.

### <a name="replica-configuration"></a>복제본 구성

복제본은 원본과 같은 서버 구성을 사용하여 생성됩니다. 복제본을 만든 후에는 원본 서버와는 별도로 컴퓨팅 세대, vCore, 스토리지, 백업 보존 기간 등 일부 설정을 변경할 수 있습니다. 가격 책정도 기본 계층에서 다른 계층으로 또는 다른 계층에서 기본 계층으로 변경하는 경우를 제외하고 독립적으로 변경할 수 있습니다.

> [!IMPORTANT]
> 원본 서버 구성을 새 값으로 업데이트하기 전에 복제본의 구성을 같거나 더 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 원본에 대한 변경 내용을 유지할 수 있습니다.

복제본을 만들 때 방화벽 규칙 및 매개 변수 설정은 원본 서버에서 복제본으로 상속됩니다. 나중에 복제본의 규칙은 독립적입니다.

### <a name="stopped-replicas"></a>중지된 복제본

원본 서버와 읽기 복제본 간의 복제를 중지하면 중지된 복제본은 읽기와 쓰기를 모두 허용하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

### <a name="deleted-source-and-standalone-servers"></a>삭제된 원본 및 독립 실행형 서버

원본 서버가 삭제되면 모든 읽기 복제본에 대한 복제가 중지됩니다. 이러한 복제본은 자동으로 독립 실행형 서버가 되며 읽기와 쓰기를 모두 허용할 수 있습니다. 원본 서버 자체는 삭제됩니다.

### <a name="user-accounts"></a>사용자 계정

원본 서버의 사용자는 읽기 복제본으로 복제됩니다. 원본 서버에서 사용 가능한 사용자 계정을 사용하여 읽기 복제본에만 연결할 수 있습니다.

### <a name="server-parameters"></a>서버 매개 변수

데이터가 동기화되지 않고 데이터가 손실 또는 손상될 가능성으로부터 데이터를 보호하기 위해 읽기 복제본을 사용하는 경우 일부 서버 매개 변수는 업데이트할 수 없도록 잠깁니다.

다음 서버 매개 변수는 원본 서버와 복제 서버에서 모두 잠깁니다.

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

[`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) 매개 변수가 복제본 서버에서 잠겨 있습니다.

원본 서버에서 위의 매개 변수 중 하나를 업데이트하려면 복제본 서버를 삭제하고, 원본의 매개 변수 값을 업데이트하고, 복제본을 다시 만듭니다.

### <a name="gtid"></a>GTID

GTID가 지원되는 대상은 다음과 같습니다.

* MySQL 버전 5.7 및 8.0
* 최대 16TB의 스토리지를 지원하는 서버 16TB 스토리지를 지원하는 지역의 전체 목록은 [가격 책정 계층](concepts-pricing-tiers.md#storage) 문서를 참조하세요.

GTID가 기본적으로 해제되어 있습니다. GTID를 사용하도록 설정한 후에는 다시 해제할 수 없습니다. GTID를 해제해야 하는 경우 고객 지원팀에 문의하세요.

원본 서버에서 GTID를 사용하는 경우 새로 만든 복제본도 GTID를 사용하도록 설정하고 GTID 복제를 사용합니다. 복제 일관성을 유지하려면 원본 또는 복제 서버에서 `gtid_mode`를 업데이트할 수 없습니다.

### <a name="other"></a>기타

* 복제본의 복제본 만들기는 지원되지 않습니다.
* 메모리 내 테이블이 있으면 복제본이 동기화되지 않을 수 있기 때문입니다. 이것은 MySQL 복제 기술의 제한입니다. 자세한 내용은 [MySQL 참조 문서](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html)를 참조하세요.
* 원본 서버 테이블에 기본 키가 있는지 확인합니다. 기본 키가 없는 경우 원본 및 복제본 간의 복제 대기 시간이 발생할 수 있습니다.
* [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)에서 MySQL 복제 제한 사항의 전체 목록을 검토하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 읽기 복제본 생성 및 관리](howto-read-replicas-portal.md) 방법 알아보기
* [Azure CLI 및 REST API를 사용하여 읽기 복제본 생성 및 관리](howto-read-replicas-cli.md) 방법 알아보기