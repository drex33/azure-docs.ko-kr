---
title: 서버리스 컴퓨팅 계층
description: 이 문서에서는 새 서버리스 컴퓨팅 계층에 대해 설명하고 이를 기존에 Azure SQL Database용으로 프로비저닝된 컴퓨팅 계층과 비교합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: test sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: mathoma, wiassaf
ms.date: 7/29/2021
ms.openlocfilehash: ac1241b28ae85f19aa4bfdbc1a92310b64d88462
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536638"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database 서버리스
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

서버리스는 Azure SQL Database에서 워크로드 수요 및 초당 사용된 컴퓨팅 양에 대한 청구서를 기반으로 컴퓨팅 규모를 자동으로 조정하는 단일 데이터베이스의 컴퓨팅 계층입니다. 또한 서버리스 컴퓨팅 계층은 스토리지 비용만 청구될 때 비활성 기간 동안 데이터베이스를 자동으로 일시 중지하고 활동이 반환되면 데이터베이스를 자동으로 다시 시작합니다.

## <a name="serverless-compute-tier"></a>서버리스 컴퓨팅 계층

Azure SQL Database에서 단일 데이터베이스용 서버리스 컴퓨팅 계층은 컴퓨팅 자동 크기 조정 범위와 자동 일시 중지 지연으로 매개 변수화됩니다. 이러한 매개 변수 구성을 통해 데이터베이스 성능 환경과 컴퓨팅 비용이 구체화됩니다.

![서버리스 청구](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>성능 구성

- **최소 vCore 수** 및 **최대 vCore 수** 는 데이터베이스에 사용할 수 있는 컴퓨팅 용량의 범위를 정의하는 구성 가능한 매개 변수입니다. 메모리 및 IO 제한은 지정된 vCore 범위에 비례합니다.  
- **자동 일시 중지 지연** 은 데이터베이스가 자동으로 일시 중지되기 전에 비활성 상태로 있어야 하는 기간을 정의하는 구성 가능한 매개 변수입니다. 다음 로그인 또는 기타 활동이 발생하면 데이터베이스가 자동으로 다시 시작됩니다.  또는 자동 일시 중지를 사용하지 않을 수 있습니다.

### <a name="cost"></a>비용

- 서버리스 데이터베이스에 대한 비용은 컴퓨팅 비용과 스토리지 비용의 합계입니다.
- 컴퓨팅 사용량이 구성된 최소 및 최대 한도 사이에 있으면 컴퓨팅 비용은 사용된 vCore 및 메모리를 기반으로 합니다.
- 컴퓨팅 사용량이 구성된 최소 한도 미만이면 컴퓨팅 비용은 구성된 최소 vCore 및 최소 메모리를 기반으로 합니다.
- 데이터베이스가 일시 중지되면 컴퓨팅 비용은 0이고 스토리지 비용만 발생합니다.
- 스토리지 비용은 프로비저닝된 컴퓨팅 계층과 동일한 방식으로 결정됩니다.

자세한 비용 정보는 [청구](serverless-tier-overview.md#billing)를 참조하세요.

## <a name="scenarios"></a>시나리오

서버리스는 간헐적이고 예측 불가능한 사용 패턴이 있는 단일 데이터베이스에 최적화된 가격 대비 성능이며, 유휴 사용 기간 후 컴퓨팅 준비가 약간 지연될 수 있습니다. 반면, 프로비저닝된 컴퓨팅 계층은 컴퓨팅 준비를 지연할 수 없는 평균 사용량이 많은 탄력적 풀의 단일 데이터베이스 또는 다중 데이터베이스에 최적화된 가격 대비 성능입니다.

### <a name="scenarios-well-suited-for-serverless-compute"></a>서버리스 컴퓨팅에 적합한 시나리오

- 일정 기간 활동이 없고 평균 컴퓨팅 활용률도 저조하게 나타나는 등, 사용 패턴이 일정하지 않고 예측하기 어려운 단일 데이터베이스
- 자주 재조정되는 프로비저닝된 컴퓨팅 계층의 단일 데이터베이스와 컴퓨팅 재조정을 서비스에 위임하려는 고객
- 사용 기록이 없는 새로운 단일 데이터베이스, SQL Database에 배포하기 전에 컴퓨팅 크기를 추정하기 어렵거나 불가능함

### <a name="scenarios-well-suited-for-provisioned-compute"></a>프로비저닝된 컴퓨팅에 적합한 시나리오

- 보다 규칙적이고, 사용 패턴을 예측할 수 있고, 시간이 지나면서 평균 컴퓨팅 사용률이 높아지는 단일 데이터
- 일시 중지된 상태에서 메모리 조정 또는 다시 시작 지연이 자주 발생하여 성능 절충을 허용할 수 없는 데이터베이스가 있습니다.
- 가격 대비 성능 최적화를 위해 탄력적 풀로 통합될 수 있는, 간헐적이고 예측이 불가능한 사용 패턴의 여러 데이터베이스

## <a name="comparison-with-provisioned-compute-tier"></a>프로비저닝된 컴퓨팅 계층과의 비교

다음 표에서는 서버리스 컴퓨팅 계층과 프로비저닝된 컴퓨팅 계층 간의 차이점을 요약하고 있습니다.

| | **서버리스 컴퓨팅** | **프로비저닝된 컴퓨팅** |
|:---|:---|:---|
|**데이터베이스 사용 패턴**| 시간이 지나면서 평균 컴퓨팅 사용률이 낮아지는 간헐적이고 예측할 수 없는 사용량 | 시간이 지나면서 평균 컴퓨팅 사용률이 높아지는 보다 규칙적인 사용 패턴 또는 탄력적 풀을 사용하는 여러 데이터베이스|
| **성능 관리 작업** |더 낮음|더 높음|
|**컴퓨팅 크기 조정**|자동|수동|
|**컴퓨팅 응답성**|비활성 기간 후 낮음|직접 실행|
|**청구 세분성**|초당|시간당|

## <a name="purchasing-model-and-service-tier"></a>구매 모델 및 서비스 계층

SQL Database 서버리스는 현재 vCore 구매 모델의 5세대 하드웨어에 대한 범용 계층에서만 지원됩니다.

## <a name="autoscaling"></a>자동 확장

### <a name="scaling-responsiveness"></a>크기 조정 응답성

일반적으로 서버리스 데이터베이스는 최대 vCore 값으로 설정된 제한 내에서 요청된 컴퓨팅 양에 대해 중단 없이 리소스 수요를 충족할 수 있는 충분한 용량을 갖춘 머신에서 실행됩니다. 머신에서 리소스 수요를 몇 분 내에 충족할 수 없는 경우 부하 분산이 자동으로 수행되는 경우가 있습니다. 예를 들어, 리소스 수요가 vCore 4개인데 vCore를 2개만 사용할 수 있으면 vCore 4개가 제공되기 전에 부하를 분산하는 데 최대 몇 분 정도 걸릴 수 있습니다. 연결이 끊어지는 경우 작업이 끝날 때의 짧은 기간을 제외하고는 데이터베이스에서 부하 분산 중에 온라인 상태를 유지합니다.

### <a name="memory-management"></a>메모리 관리

서버리스 데이터베이스의 메모리는 프로비저닝된 컴퓨팅 데이터베이스보다 더 자주 회수됩니다. 이 동작은 서버리스에서 비용을 제어하는 데 중요하며 성능에 영향을 줄 수 있습니다.

#### <a name="cache-reclamation"></a>캐시 재사용

프로비저닝된 컴퓨팅 데이터베이스와 달리 CPU 또는 활성 캐시 사용률이 낮으면 서버리스 데이터베이스에서 SQL 캐시의 메모리를 회수합니다.

- 가장 최근에 사용한 캐시 항목의 전체 크기가 일정 기간 동안 임계값 미만으로 떨어지면 활성 캐시 활용도가 낮은 것으로 간주됩니다.
- 캐시 재사용이 트리거되면 대상 캐시 크기가 이전 크기의 일부로 점차 감소하고 재사용은 사용량이 낮은 경우에만 계속됩니다.
- 캐시 재사용이 발생하면 제거할 캐시 항목을 선택하는 정책은 메모리가 부족할 때 프로비저닝된 컴퓨팅 데이터베이스와 동일한 선택 정책입니다.
- 캐시 크기는 구성할 수 있는 최소 vCore에 의해 정의된 최소 메모리 제한 미만으로 절대 줄어들지 않습니다.

서버리스 및 프로비저닝된 컴퓨팅 데이터베이스 모두에서 사용 가능한 메모리가 모두 사용되면 캐시 항목이 제거될 수 있습니다.

CPU 사용률이 낮은 경우 활성 캐시 사용률은 사용 패턴에 따라 높게 유지되므로 메모리 재사용을 방지할 수 있습니다.  또한 이전 사용자 활동에 응답하는 정기적인 백그라운드 프로세스로 인해 사용자 활동이 중지된 후 메모리 재사용이 발생하기 전에 다른 지연이 발생할 수 있습니다.  예를 들어 삭제 작업과 쿼리 저장소 정리 태스크는 삭제하도록 표시되지만 고스트 정리 프로세스가 실행될 때까지 물리적으로 삭제되지 않는 삭제할 레코드를 생성합니다. 고스트 정리에는 추가 데이터 페이지를 캐시로 읽어오는 작업이 포함될 수 있습니다.

#### <a name="cache-hydration"></a>캐시 하이드레이션

SQL 캐시는 프로비저닝된 데이터베이스와 동일한 방식과 동일한 속도로 디스크에서 데이터를 가져올 때 증가합니다. 데이터베이스를 사용 중이면 캐시는 최대 메모리 한도까지 제한 없이 증가할 수 있습니다.

## <a name="auto-pausing-and-auto-resuming"></a>자동 일시 중지 및 자동 다시 시작

### <a name="auto-pausing"></a>자동 일시 중지

자동 일시 중지는 자동 일시 중지 지연 기간 동안 다음 조건이 모두 참이면 트리거됩니다.

- 세션 수 = 0
- CPU = 0(사용자 리소스 풀에서 실행되는 사용자 워크로드의 경우)

원하는 경우 자동 일시 중지를 사용하지 않도록 설정하는 옵션이 제공됩니다.

다음 기능은 자동 일시 중지를 지원하지 않지만 자동 크기 조정은 지원합니다. 다음 기능 중 하나를 사용하는 경우 자동 일시 중지를 사용하지 않도록 설정해야 하며, 데이터베이스 비활성 기간에 관계없이 데이터베이스가 온라인 상태로 유지됩니다.

- 지역 복제([활성 지역 복제](active-geo-replication-overview.md) 및 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md))
- [LTR(장기 백업 보존)](long-term-retention-overview.md)
- [SQL 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)에서 사용되는 동기화 데이터베이스. 동기화 데이터베이스와 달리 허브 및 구성원 데이터베이스는 자동 일시 중지를 지원합니다.
- 서버리스 데이터베이스가 포함된 논리 서버에 대해 생성된 [DNS 별칭](dns-alias-overview.md)
- [탄력적 작업(미리 보기)](elastic-jobs-overview.md)(작업 데이터베이스가 서버리스 데이터베이스인 경우). 탄력적 작업의 대상 데이터베이스는 자동 일시 중지를 지원하며 작업 연결을 통해 다시 시작됩니다.

데이터베이스가 온라인 상태여야 하는 일부 서비스 업데이트를 배포하는 동안에는 자동 일시 중지가 일시적으로 차단됩니다.  이런 경우 서비스 업데이트가 완료되면 자동 일시 중지가 다시 허용됩니다.

#### <a name="auto-pause-troubleshooting"></a>자동 일시 중지 문제 해결

자동 일시 중지가 사용하도록 설정되었지만 지연 기간 후 데이터베이스가 자동으로 일시 중지되지 않고 위에 나열된 기능이 사용되지 않는 경우 애플리케이션 또는 사용자 세션에서 자동 일시 중지를 차단한 것일 수 있습니다. 현재 데이터베이스에 연결된 애플리케이션 또는 사용자 세션이 있는지 확인하려면 클라이언트 도구를 사용하여 데이터베이스에 연결한 후 다음 쿼리를 실행합니다.

```sql
SELECT session_id,
       host_name,
       program_name,
       client_interface_name,
       login_name,
       status,
       login_time,
       last_request_start_time,
       last_request_end_time
FROM sys.dm_exec_sessions AS s
INNER JOIN sys.dm_resource_governor_workload_groups AS wg
ON s.group_id = wg.group_id
WHERE s.session_id <> @@SPID
      AND
      (
      (
      wg.name like 'UserPrimaryGroup.DB%'
      AND
      TRY_CAST(RIGHT(wg.name, LEN(wg.name) - LEN('UserPrimaryGroup.DB') - 2) AS int) = DB_ID()
      )
      OR
      wg.name = 'DACGroup'
      );
```

> [!TIP]
> 쿼리를 실행한 후 데이터베이스 연결을 끊어야 합니다. 그러지 않으면 쿼리에서 사용 중인 열린 세션으로 인해 자동 일시 중지가 차단됩니다.

결과 집합이 비어 있지 않으면 현재 자동 일시 중지를 차단하는 세션이 있는 것입니다. 

결과 집합이 비어 있는 경우에도 이전에 자동 일시 중지 지연 기간 중 짧은 시간 동안 세션이 열렸을 수 있습니다. 지연 기간 동안 해당 활동이 발생했는지 확인하려면 [Azure SQL 감사](auditing-overview.md)를 사용하여 관련 기간의 감사 데이터를 검사할 수 있습니다.

동시 CPU 사용률 여부에 관계없이 사용자 리소스 풀에 열린 세션이 있는 경우가 서버리스 데이터베이스가 예상대로 자동 일시 중지되지 않는 가장 일반적인 이유입니다. 일부 [기능](#auto-pausing)은 자동 일시 중지를 지원하지 않지만 자동 스케일링을 지원합니다.

### <a name="auto-resuming"></a>자동 다시 시작

다음 조건 중 하나라도 참이면 언제든지 자동 다시 시작이 트리거됩니다.

|기능|자동 다시 시작 트리거|
|---|---|
|인증 및 권한 부여|로그인|
|위협 탐지|데이터베이스 또는 서버 수준에서 위협 탐지 설정 사용/사용 안 함.<br>데이터베이스 또는 서버 수준에서 위협 탐지 설정 수정.|
|데이터 검색 및 분류|민감도 레이블 추가, 수정, 삭제 또는 보기|
|감사|감사 레코드 보기,<br>감사 정책 업데이트 또는 보기|
|데이터 마스킹|데이터 마스킹 규칙 추가, 수정, 삭제 또는 보기|
|투명한 데이터 암호화|투명한 데이터 암호화 상태 보기|
|취약점 평가|사용하도록 설정된 경우 임시 검사 및 정기 검사|
|쿼리(성능) 데이터 저장소|쿼리 저장소 설정 수정 또는 보기|
|성능 추천 사항|성능 권장 사항 보기 또는 적용|
|자동 튜닝|자동 인덱싱과 같은 자동 튜닝 권장 사항 적용 및 확인|
|데이터베이스 복사|데이터베이스를 복사본으로 만들기<br>BACPAC 파일로 내보내기|
|SQL 데이터 동기화|구성 가능한 예약에 따라 실행되거나 수동으로 수행되는 허브 및 멤버 데이터베이스 간의 동기화|
|특정 데이터베이스 메타데이터 수정|새 데이터베이스 태그 추가,<br>최대 vCore 수, 최소 vCore 수 또는 자동 일시 중지 지연 변경|
|SSMS(SQL Server Management Studio)|18.1 이전의 SSMS 버전을 사용하고 서버의 데이터베이스에 대한 새 쿼리 창을 열면 동일한 서버에서 자동으로 일시 중지된 데이터베이스가 다시 시작됩니다. SSMS 버전 18.1 이상을 사용하는 경우 이 문제가 발생하지 않습니다.|

위에 나열된 작업을 수행하는 모니터링, 관리 또는 기타 솔루션은 자동 재개를 트리거합니다.

데이터베이스가 온라인 상태여야 하는 일부 서비스 업데이트를 배포하는 동안 자동 다시 시작도 트리거됩니다.

### <a name="connectivity"></a>연결

서버리스 데이터베이스가 일시 중지되면 첫 번째 로그인에서 데이터베이스가 다시 시작되고 40613 오류 코드로 인해 데이터베이스를 사용할 수 없다는 오류가 반환됩니다. 데이터베이스가 다시 시작되면 연결을 설정하기 위해 로그인을 다시 시도해야 합니다. 연결 재시도 논리가 있는 데이터베이스 클라이언트는 수정할 필요가 없습니다.

### <a name="latency"></a>대기 시간

서버리스 데이터베이스가 자동으로 다시 시작되기까지의 대기 시간과 자동으로 일시 중지되기까지의 대기 시간은 일반적으로 각각 1분, 지연 기간 만료 후 1~10분입니다.

### <a name="customer-managed-transparent-data-encryption-byok"></a>고객 관리형 투명 데이터 암호화(BYOK)

[고객 관리형 투명 데이터 암호화](transparent-data-encryption-byok-overview.md)(BYOK)를 사용하고 키 삭제 또는 해지가 발생할 때 서버리스 데이터베이스가 자동 일시 중지된 경우에는 데이터베이스가 자동 일시 중지 상태로 유지됩니다.  이 경우 다음에 데이터베이스를 다시 시작하면 약 10분 내에 데이터베이스에 액세스할 수 없게 됩니다. 데이터베이스에 액세스할 수 없게 되면 복구 프로세스가 프로비저닝된 컴퓨팅 데이터베이스와 동일합니다. 키 삭제 또는 해지가 발생할 때 서버리스 데이터베이스가 온라인이면, 프로비저닝된 컴퓨팅 데이터베이스와 동일한 방식으로 약 10분 내에 데이터베이스에 액세스할 수 없게 됩니다.

## <a name="onboarding-into-serverless-compute-tier"></a>서버리스 컴퓨팅 계층으로 온보딩

새 데이터베이스를 만들거나 기존 데이터베이스를 서버리스 컴퓨팅 계층으로 이동하는 경우 프로비저닝된 컴퓨팅 계층에서 새 데이터베이스를 만드는 것과 동일한 패턴을 따르며 다음 두 단계를 수행해야 합니다.

1. 서비스 목표를 지정합니다. 서비스 목표는 서비스 계층, 하드웨어 세대 및 최대 vCore를 지정합니다. 서비스 목표 옵션은 [서버리스 리소스 제한](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.


2. 필요에 따라 최소 vCore 수와 자동 일시 중지 지연을 지정하여 기본값을 변경합니다. 다음 표에는 이러한 매개 변수에 사용할 수 있는 값이 나와 있습니다.

   |매개 변수|값 선택|기본값|
   |---|---|---|---|
   |최소 vCore|구성된 최대 vCore에 따라 다름 - [리소스 한도](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)를 참조하세요.|0.5개 vCore|
   |자동 일시 중지 지연|최소: 60분(1시간)<br>최대: 10080분(7일)<br>간격: 10분<br>자동 일시 중지 사용 안 함: -1|60분|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>서버리스 컴퓨팅 계층에 새 데이터베이스 만들기

다음 예에서는 서버리스 컴퓨팅 계층에 새 데이터베이스를 만듭니다.

#### <a name="use-azure-portal"></a>Azure Portal 사용

[빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.


#### <a name="use-powershell"></a>PowerShell 사용

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Azure CLI 사용

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>T-SQL(Transact-SQL) 사용

T-SQL을 사용하는 경우 최소 vCore 및 자동 일시 중지 지연에 기본값이 적용됩니다. 나중에 포털에서 또는 다른 관리 API(PowerShell, Azure CLI, REST API)를 통해 변경할 수 있습니다.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

자세한 내용은 [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)를 참조하세요.  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>데이터베이스를 프로비저닝된 컴퓨팅 계층에서 서버리스 컴퓨팅 계층으로 이동

다음 예제에서는 데이터베이스를 프로비저닝된 컴퓨팅 계층에서 서버리스 컴퓨팅 계층으로 이동합니다.

#### <a name="use-powershell"></a>PowerShell 사용

```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Azure CLI 사용

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```

#### <a name="use-transact-sql-t-sql"></a>T-SQL(Transact-SQL) 사용

T-SQL을 사용하는 경우 최소 vCore 및 자동 일시 중지 지연에 기본값이 적용됩니다. 나중에 포털에서 또는 다른 관리 API(PowerShell, Azure CLI, REST API)를 통해 변경할 수 있습니다.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

자세한 내용은 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true)를 참조하세요.

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>데이터베이스를 서버리스 컴퓨팅 계층에서 프로비저닝된 컴퓨팅 계층으로 이동

서버리스 데이터베이스는 프로비저닝된 컴퓨팅 데이터베이스를 서버리스 컴퓨팅 계층으로 이동하는 것과 동일한 방식으로 프로비저닝된 컴퓨팅 계층으로 이동할 수 있습니다.

## <a name="modifying-serverless-configuration"></a>서버리스 구성 수정

### <a name="use-powershell"></a>PowerShell 사용

최대 또는 최소 vCore 및 자동 일시 중지 지연을 수정하는 작업은 PowerShell에서 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 명령을 사용하고 `MaxVcore`, `MinVcore`, `AutoPauseDelayInMinutes` 인수를 사용하여 수행됩니다.

### <a name="use-azure-cli"></a>Azure CLI 사용

최대 또는 최소 vCore 및 자동 일시 중지 지연을 수정하는 작업은 Azure CLI에서 [az sql db update](/cli/azure/sql/db#az_sql_db_update) 명령을 사용하고 `capacity`, `min-capacity`, `auto-pause-delay` 인수를 사용하여 수행됩니다.

## <a name="monitoring"></a>모니터링

### <a name="resources-used-and-billed"></a>리소스 사용 및 청구

서버리스 데이터베이스의 리소스는 앱 패키지, SQL 인스턴스 및 사용자 리소스 풀 엔터티로 캡슐화됩니다.

#### <a name="app-package"></a>앱 패키지

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 앱 패키지는 데이터베이스의 가장 외부에 있는 리소스 관리 경계입니다. 앱 패키지에는 SQL Database의 데이터베이스에서 사용 중인 모든 사용자 및 시스템 리소스를 검색하는 전체 텍스트 검색과 같은 외부 서비스와 SQL 인스턴스가 포함됩니다. SQL 인스턴스는 일반적으로 앱 패키지 전체의 전체 리소스 사용률을 제어합니다.

#### <a name="user-resource-pool"></a>사용자 리소스 풀

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 사용자 리소스 풀은 데이터베이스의 내부 리소스 관리 경계입니다. 사용자 리소스 풀은 DDL 쿼리(예: CREATE, ALTER), DML 쿼리(예: INSERT, UPDATE, DELETE, MERGE), SELECT 쿼리에서 생성된 사용자 워크로드에 대한 CPU와 IO의 범위를 지정합니다. 이러한 쿼리는 일반적으로 앱 패키지 내에서 가장 높은 사용률을 나타냅니다.

### <a name="metrics"></a>메트릭

다음 표에는 서버리스 데이터베이스 앱 패키지 및 사용자 리소스 풀의 리소스 사용량을 모니터링하기 위한 메트릭이 나와 있습니다.

|엔터티|메트릭|설명|단위|
|---|---|---|---|
|앱 패키지|app_cpu_percent|앱에 허용되는 최대 vCore 수에 대한 앱에서 사용한 vCore 수의 백분율입니다.|백분율|
|앱 패키지|app_cpu_billed|보고 기간 동안 앱에 대해 요금이 청구되는 컴퓨팅의 양입니다. 이 기간 동안에 대한 지불 금액은 이 메트릭과 vCore 단가를 곱한 값입니다. <br><br>이 메트릭의 값은 시간이 지남에 따라 사용된 최대 CPU와 사용된 초당 메모리를 집계하여 결정됩니다. 사용된 양이 최소 vCore 수 및 최소 메모리로 설정된 최소 프로비저닝된 양보다 적으면 최소 프로비저닝된 양에 대한 요금이 청구됩니다.청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.|vCore 시간(초)|
|앱 패키지|app_memory_percent|앱에 허용되는 최대 메모리에 대한 앱에서 사용한 메모리의 백분율입니다.|백분율|
|사용자 리소스 풀|cpu_percent|사용자 워크로드에 허용되는 최대 vCore 수에 대한 사용자 워크로드에서 사용한 vCore 수의 백분율입니다.|백분율|
|사용자 리소스 풀|data_IO_percent|사용자 워크로드에 허용되는 최대 데이터 IOPS에 대한 사용자 워크로드에서 사용한 데이터 IOPS의 백분율입니다.|백분율|
|사용자 리소스 풀|log_IO_percent|사용자 워크로드에 허용되는 최대 로그 MB/초에 대한 사용자 워크로드에서 사용한 로그 MB/초의 백분율입니다.|백분율|
|사용자 리소스 풀|workers_percent|사용자 워크로드에 허용되는 최대 작업자 수에 대한 사용자 워크로드에서 사용한 작업자 수의 백분율입니다.|백분율|
|사용자 리소스 풀|sessions_percent|사용자 워크로드에 허용되는 최대 세션 수에 대한 사용자 워크로드에서 사용한 세션 수의 백분율입니다.|백분율|

### <a name="pause-and-resume-status"></a>일시 중지 및 다시 시작 상태

Azure Portal에서 데이터베이스 상태는 해당 상태가 포함된 데이터베이스를 나열하는 서버의 개요 창에 표시됩니다. 또한 데이터베이스 상태는 데이터베이스의 개요 창에도 표시됩니다.

다음 명령을 사용하여 데이터베이스의 일시 중지 및 다시 시작 상태를 쿼리합니다.

#### <a name="use-powershell"></a>PowerShell 사용

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Azure CLI 사용

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```

## <a name="resource-limits"></a>리소스 제한

리소스 제한은 [서버리스 컴퓨팅 계층](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.

## <a name="billing"></a>결제

청구되는 컴퓨팅 양은 초 단위로 사용된 최대 CPU와 메모리입니다. 사용된 CPU와 메모리의 양이 각각에 대해 프로비저닝된 최소 양보다 적으면 프로비저닝된 양에 대해 청구됩니다. 청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.

- **청구되는 리소스**: CPU 및 메모리
- **청구 금액**: vCore 단가 * 최댓값(최소 vCore 수, 사용된 vCore 수, 최소 메모리 GB * 1/3, 사용된 메모리 GB * 1/3) 
- **과금 시간 단위**: 초당

vCore 단가는 초당 vCore당 비용입니다. 지정된 지역의 특정 단가는 [Azure SQL Database 가격 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조하세요.

요금이 청구되는 컴퓨팅 양은 다음 메트릭에서 공개됩니다.

- **메트릭**: app_cpu_billed(vCore 시간(초))
- **정의**: 최댓값(최소 vCore 수, 사용된 vCore 수, 최소 메모리 GB * 1/3, 사용된 메모리 GB * 1/3)
- **보고 빈도**: 분당

이 수량은 초 단위로 계산되어 1분 동안 집계됩니다.

### <a name="minimum-compute-bill"></a>최소 컴퓨팅 청구서

서버리스 데이터베이스가 일시 중지되면 컴퓨팅 청구서는 0입니다.  서버리스 데이터베이스가 일시 중지되지 않은 경우, 최소 컴퓨팅 청구서는 최댓값(최소 vCore 수, 최소 메모리 GB * 1/3)을 기준으로 한 vCore 수에 해당합니다.

예:

- 서버리스 데이터베이스가 일시 중지되었고 최대 vCore 수가 8개이고, 최소 vCore 수가 최소 메모리 3.0GB에 해당하는 1개로 구성된 경우를 가정해 보겠습니다.  그리고 최소 컴퓨팅 청구서는 최댓값(1개 vCore, 3.0GB * 1개 vCore/3GB) = 1개 vCore를 기준으로 합니다.
- 서버리스 데이터베이스가 일시 중지되었고 최대 vCore 수가 4개이고, 최소 vCore 수가 최소 메모리 2.1GB에 해당하는 0.5개로 구성된 경우를 가정해 보겠습니다.  그리고 최소 컴퓨팅 청구서는 최댓값(0.5개 vCore, 2.1GB * 1개 vCore/3GB) = 0.7개 vCore를 기준으로 합니다.

서버리스용 [Azure SQL Database 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 구성된 최대 및 최소 vCore 수를 기준으로 구성할 수 있는 최소 메모리를 결정할 수 있습니다.  대체로 구성된 최소 vCore 수가 0.5개 vCore보다 큰 경우, 최소 컴퓨팅 청구서는 구성된 최소 메모리와 독립적이며, 구성된 최소 vCore 수에 따라서만 달라집니다.

### <a name="example-scenario"></a>예제 시나리오

최소 vCore 1개 최대 vCore 4개로 구성된 서버리스 데이터베이스를 생각해 보면,  약 3GB의 최소 메모리와 12GB의 최대 메모리에 해당합니다.  자동 일시 중지 지연이 6시간으로 설정되고 데이터베이스 워크로드는 24시간 중 처음 2시간 동안 활성 상태이고 그 외에는 비활성 상태라고 가정합니다.    

이 경우 데이터베이스는 처음 8시간 동안 컴퓨팅 및 스토리지 비용이 청구됩니다.  2시간 지난 후부터는 데이터베이스가 비활성 상태이지만, 데이터베이스가 온라인 상태일 때 프로비저닝된 최소 컴퓨팅에 기반하여 나머지 6시간에 대해 컴퓨팅 비용이 청구됩니다.  데이터베이스가 일시 중지되어 있는 동안 24시간 중 나머지 시간은 스토리지 비용만 청구됩니다.

정확하게, 이 예제의 컴퓨팅 청구서는 다음과 같이 계산됩니다.

|시간 간격|초당 사용된 vCore 수|초당 사용된 GB|청구된 컴퓨팅 차원|시간 간격에 따라 청구된 vCore 초|
|---|---|---|---|---|
|0:00-1:00|4|9|사용된 vCore 수|vCore 4개 * 3600초 = 14400 vCore 초|
|1:00-2:00|1|12|사용된 메모리|12GB * 1/3 * 3600초 = 14400 vCore 초|
|2:00-8:00|0|0|프로비저닝된 최소 메모리|3GB * 1/3 * 21600초 = 21600 vCore 초|
|8:00-24:00|0|0|일시 중지되어 있는 동안 청구된 컴퓨팅 없음|0 vCore 초|
|24시간 동안 청구된 총 vCore 초||||50400 vCore 초|

컴퓨팅 단가가 $0.000145/vCore/초라고 가정합니다.  그러면 24시간 기간에 대해 청구되는 컴퓨팅은 컴퓨팅 단가와 청구되는 vCore 초를 곱한 값($0.000145/vCore/second * 50400 vCore 초 ~ $7.31)입니다.

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure 하이브리드 혜택 및 예약된 용량

서버리스 컴퓨팅 계층에는 AHB(Azure 하이브리드 혜택) 및 예약된 용량 할인이 적용되지 않습니다.

## <a name="available-regions"></a>사용 가능한 지역

서버리스 컴퓨팅 계층은 전 세계에서 중국 동부, 중국 북부, 독일 중부, 독일 북동부 및 US Gov 중부(아이오와) 지역을 제외한 모든 지역에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.
