---
title: 단일 데이터베이스 vCore 리소스 제한
description: 이 페이지에서는 Azure SQL Database에서 단일 데이터베이스에 대한 몇 가지 일반적인 vCore 리소스 제한을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: reference
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 06/04/2021
ms.openlocfilehash: 4a400417fef8ac89ccf30799f4969ba8f789e260
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111555311"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>vCore 구매 모델을 사용한 단일 데이터베이스에 대한 리소스 제한
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 vCore 구매 모델을 사용하는 Azure SQL Database의 단일 데이터베이스에 대한 리소스 제한을 자세히 설명합니다.

* 서버에 있는 단일 데이터베이스의 DTU 구매 모델 한도에 관해서는 [서버의 리소스 한도 개요](resource-limits-logical-server.md)를 참조하세요.
* Azure SQL Database의 DTU 구매 모델 리소스 한도에 관해서는 [DTU 리소스 한도 - 단일 데이터베이스](resource-limits-dtu-single-databases.md) 및 [DTU 리소스 한도 - 탄력적 풀](resource-limits-dtu-elastic-pools.md)을 참조하세요.
* vCore 리소스 한도에 관해서는 [vCore 리소스 한도 - Azure SQL Database](resource-limits-vcore-single-databases.md) 및 [vCore 리소스 한도 - 탄력적 풀](resource-limits-vcore-elastic-pools.md)을 참조하세요.
* 다양한 구매 모델에 관한 자세한 내용은 [구매 모델 및 서버 계층](purchasing-models.md)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

각 읽기 전용 복제본에는 vCores, 메모리, 데이터 IOPS, TempDB, 작업자, 세션과 같은 자체 리소스가 있습니다. 각 읽기 전용 복제본에는 이 문서의 뒷부분에서 설명하는 리소스 한도가 적용됩니다.

다음을 사용하여 단일 데이터베이스의 서비스 계층, 컴퓨팅 크기(서비스 목표), 스토리지 용량을 설정할 수 있습니다.

* [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)를 통해 [Transact-SQL](single-database-manage.md#transact-sql-t-sql)
* [Azure Portal](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure CLI](single-database-manage.md#the-azure-cli)
* [REST API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> 크기 조정 지침 및 고려 사항은 [단일 데이터베이스 크기 조정](single-database-scale.md)을 참조하세요.

## <a name="general-purpose---serverless-compute---gen5"></a>범용 - 서버리스 컴퓨팅 - Gen5

[서버리스 컴퓨팅 계층](serverless-tier-overview.md)은 현재 Gen5 하드웨어에서만 사용할 수 있습니다.

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|
|최소-최대 vCore|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|최소-최대 메모리(GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|최소-최대 자동 일시 중지 지연(분)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Columnstore 지원 여부|예 <sup>1</sup>|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|512|1024|1024|1024|1536|
|최대 로그 크기(GB) <sup>2</sup>|154|307|307|307|461|
|TempDB 최대 데이터 크기(GB)|32|64|128|192|256|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>3</sup>|320|640|1280|1920|2560|
|최대 로그 전송률(MBps)|4.5.|9|18|27|36|
|최대 동시 작업자(요청)|75|150|300|450|600|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 최대 vCore 구성이 더 작은 서비스 목표는 열 저장소 인덱스를 만들고 사용하기 위한 메모리가 부족할 수 있습니다.  열 저장소에 성능 문제가 발생하는 경우 최대 vCore 구성을 높여 사용 가능한 최대 메모리를 늘리세요.  

<sup>2</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>3</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|
|최소-최대 vCore|1.25-10|1.50-12|1.75-14|2.00-16|
|최소-최대 메모리(GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|최소-최대 자동 일시 중지 지연(분)|60-10080|60-10080|60-10080|60-10080|
|Columnstore 지원 여부|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1536|3072|3072|3072|
|최대 로그 크기(GB) <sup>1</sup>|461|461|461|922|
|TempDB 최대 데이터 크기(GB)|320|384|448|512|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|3200|3840|4480|5120|
|최대 로그 전송률(MBps)|45|50|50|50|
|최대 동시 작업자(요청)|750|900|1050|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="gen5-compute-generation-part-3"></a>Gen5 컴퓨팅 세대(3부)

|컴퓨팅 크기(서비스 목표)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|
|최소-최대 vCore|2.25-18|2.5-20|3-24|4-32|5-40|
|최소-최대 메모리(GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|최소-최대 자동 일시 중지 지연(분)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Columnstore 지원 여부|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|3072|3072|4096|4096|4096|
|최대 로그 크기(GB) <sup>1</sup>|922|922|1024|1024|1024|
|TempDB 최대 데이터 크기(GB)|576|640|768|1024|1280|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|5760|6400|7680|10240|12800|
|최대 로그 전송률(MBps)|50|50|50|50|50|
|최대 동시 작업자(요청)|1350|1500|1800|2400|3000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.


## <a name="hyperscale---provisioned-compute---gen4"></a>하이퍼스케일 - 프로비저닝된 컴퓨팅 - Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|1|2|3|4|5|6|
|메모리(GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) 크기|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100|
|최대 로그 크기(TB)|제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |
|TempDB 최대 데이터 크기(GB)|32|64|96|128|160|192|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 로컬 SSD IOPS <sup>1</sup>|4000 |8000 |12000 |16000 |20000 |24000 |
|최대 로그 전송률(MBps)|100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|예|예|예|예|예|예|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|
|||

<sup>1</sup> 로컬 SSD IO 외에도 워크로드는 원격 [페이지 서버](service-tier-hyperscale.md#page-server) IO를 사용합니다. 유효 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance) 및 [리소스 사용률 통계의 데이터 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)를 참조하세요.

### <a name="gen4-compute-generation-part-2"></a>Gen4 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|7|8|9|10|16|24|
|메모리(GB)|49|56|63|70|112|159.5|
|[RBPEX](service-tier-hyperscale.md#compute) 크기|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100 |
|최대 로그 크기(TB)|제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |
|TempDB 최대 데이터 크기(GB)|224|256|288|320|512|768|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 로컬 SSD IOPS <sup>1</sup>|28000 |32000 |36000 |40,000 |64000 |76800 |
|최대 로그 전송률(MBps)|100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 동시 작업자(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|예|예|예|예|예|예|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|
|||

<sup>1</sup> 로컬 SSD IO 외에도 워크로드는 원격 [페이지 서버](service-tier-hyperscale.md#page-server) IO를 사용합니다. 유효 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance) 및 [리소스 사용률 통계의 데이터 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)를 참조하세요.

## <a name="hyperscale---provisioned-compute---gen5"></a>하이퍼스케일 - 프로비저닝된 컴퓨팅 - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|2|4|6|8|10|12|14|
|메모리(GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](service-tier-hyperscale.md#compute) 크기|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|
|Columnstore 지원 여부|예|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100 |100|
|최대 로그 크기(TB)|제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |
|TempDB 최대 데이터 크기(GB)|64|128|192|256|320|384|448|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 로컬 SSD IOPS <sup>1</sup>|8000 |16000 |24000 |32000 |40,000 |48000 |56000 |
|최대 로그 전송률(MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|1400|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|예|예|예|예|예|예|예|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|7 일|
|||

<sup>1</sup> 로컬 SSD IO 외에도 워크로드는 원격 [페이지 서버](service-tier-hyperscale.md#page-server) IO를 사용합니다. 유효 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance) 및 [리소스 사용률 통계의 데이터 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)를 참조하세요.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|16|18|20|24|32|40|80|
|메모리(GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](service-tier-hyperscale.md#compute) 크기|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|3X 메모리|
|Columnstore 지원 여부|예|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100 |100 |
|최대 로그 크기(TB)|제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |제한 없음 |
|TempDB 최대 데이터 크기(GB)|512|576|640|768|1024|1280|2560|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 로컬 SSD IOPS <sup>1</sup>|64000 |72000 |80,000 |96000 |128000 |160000 |204800 |
|최대 로그 전송률(MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 동시 작업자(요청)|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|예|예|예|예|예|예|예|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|7 일|
|||

<sup>1</sup> 로컬 SSD IO 외에도 워크로드는 원격 [페이지 서버](service-tier-hyperscale.md#page-server) IO를 사용합니다. 유효 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance) 및 [리소스 사용률 통계의 데이터 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)를 참조하세요.

#### <a name="notes"></a>참고

**참고 1**: 하이퍼스케일은 별도의 컴퓨팅 및 스토리지 구성 요소를 사용하는 다중 계층 아키텍처입니다. [하이퍼스케일 서비스 계층 아키텍처](service-tier-hyperscale.md#distributed-functions-architecture)

**참고 2**: 대기 시간은 가장 많이 사용되는 데이터 페이지를 캐시하는 로컬 컴퓨팅 복제본 SSD의 데이터에 대해 1~2밀리초입니다. 페이지 서버에서 검색되는 데이터는 대기 시간이 더 높습니다.

## <a name="hyperscale---provisioned-compute---dc-series"></a>하이퍼스케일 - 프로비저닝된 컴퓨팅 - DC 시리즈

|컴퓨팅 크기(서비스 목표)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|컴퓨팅 세대|DC 시리즈|DC 시리즈|DC 시리즈|DC 시리즈|
|vCore 수|2|4|6|8|
|메모리(GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) 크기|3X 메모리|3X 메모리|3X 메모리|3X 메모리|
|Columnstore 지원 여부|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |
|최대 로그 크기(TB)|제한 없음 |제한 없음 |제한 없음 |제한 없음 |
|TempDB 최대 데이터 크기(GB)|64|128|192|256|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |
|최대 로컬 SSD IOPS <sup>1</sup>|14000|28000|42000|44800|
|최대 로그 전송률(MBps)|100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 동시 작업자(요청)|160|320|480|640|
|최대 동시 세션|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|예|예|예|예|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|
|||

<sup>1</sup> 로컬 SSD IO 외에도 워크로드는 원격 [페이지 서버](service-tier-hyperscale.md#page-server) IO를 사용합니다. 유효 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance) 및 [리소스 사용률 통계의 데이터 IO](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics)를 참조하세요.

### <a name="notes"></a>참고

**참고 1**: 하이퍼스케일은 별도의 컴퓨팅 및 스토리지 구성 요소를 사용하는 다중 계층 아키텍처입니다. [하이퍼스케일 서비스 계층 아키텍처](service-tier-hyperscale.md#distributed-functions-architecture)

**참고 2**: 대기 시간은 가장 많이 사용되는 데이터 페이지를 캐시하는 로컬 컴퓨팅 복제본 SSD의 데이터에 대해 1~2밀리초입니다. 페이지 서버에서 검색되는 데이터는 대기 시간이 더 높습니다.

## <a name="general-purpose---provisioned-compute---gen4"></a>범용 - 프로비저닝된 컴퓨팅 - Gen4

> [!IMPORTANT]
> 새 Gen4 데이터베이스는 오스트레일리아 동부 또는 브라질 남부 지역에서 더 이상 지원되지 않습니다.

### <a name="gen4-compute-generation-part-1"></a>Gen4 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|1|2|3|4|5|6|
|메모리(GB)|7|14|21|28|35|42|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1024|1024|1536|1536|1536|3072|
|최대 로그 크기(GB) <sup>1</sup>|307|307|461|461|461|922|
|TempDB 최대 데이터 크기(GB)|32|64|96|128|160|192|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|320|640|960|1280|1600|1920|
|최대 로그 전송률(MBps)|4.5.|9|13.5|18|22.5|27|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="gen4-compute-generation-part-2"></a>Gen4 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|7|8|9|10|16|24|
|메모리(GB)|49|56|63|70|112|159.5|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|3072|3072|3072|3072|4096|4096|
|최대 로그 크기(GB) <sup>1</sup>|922|922|922|922|1,229|1,229|
|TempDB 최대 데이터 크기(GB)|224|256|288|320|512|768|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)
|최대 데이터 IOPS <sup>2</sup>|2240|2560|2880|3200|5120|7680|
|최대 로그 전송률(MBps)|31.5|36|40.5|45|50|50|
|최대 동시 작업자(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="general-purpose---provisioned-compute---gen5"></a>범용 - 프로비저닝된 컴퓨팅 - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|2|4|6|8|10|12|14|
|메모리(GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Columnstore 지원 여부|예|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1024|1024|1536|1536|1536|3072|3072|
|최대 로그 크기(GB) <sup>1</sup>|307|307|461|461|461|922|922|
|TempDB 최대 데이터 크기(GB)|64|128|192|256|320|384|384|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|640|1280|1920|2560|3200|3840|4480|
|최대 로그 전송률(MBps)|9|18|27|36|45|50|50|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|1400|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|1|
|다중 AZ|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|16|18|20|24|32|40|80|
|메모리(GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Columnstore 지원 여부|예|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|3072|3072|3072|4096|4096|4096|4096|
|최대 로그 크기(GB) <sup>1</sup>|922|922|922|1024|1024|1024|1024|
|TempDB 최대 데이터 크기(GB)|512|576|640|768|1024|1280|2560|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|5120|5760|6400|7680|10240|12800|12800|
|최대 로그 전송률(MBps)|50|50|50|50|50|50|50|
|최대 동시 작업자(요청)|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|1|
|다중 AZ|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[미리 보기로 제공](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>범용 - 프로비저닝된 컴퓨팅 - Fsv2 시리즈

### <a name="fsv2-series-compute-generation-part-1"></a>Fsv2 시리즈 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|컴퓨팅 세대|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|
|vCore 수|8|10|12|14|16|
|메모리(GB)|15.1|18.9|22.7|26.5|30.2|
|Columnstore 지원 여부|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1536|
|최대 로그 크기(GB) <sup>1</sup>|336|336|336|336|512|
|TempDB 최대 데이터 크기(GB)|37|46|56|65|74|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|2560|3200|3840|4480|5120|
|최대 로그 전송률(MBps)|36|45|50|50|50|
|최대 동시 작업자(요청)|400|500|600|700|800|
|최대 동시 로그인|800|1000|1200|1400|1600|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="fsv2-series-compute-generation-part-2"></a>Fsv2 시리즈 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|컴퓨팅 세대|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|Fsv2 시리즈|
|vCore 수|18|20|24|32|36|72|
|메모리(GB)|34.0|37.8|45.4|60.5|68.0|136.0|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1536|1536|1536|3072|3072|4096|
|최대 로그 크기(GB) <sup>1</sup>|512|512|512|1024|1024|1024|
|TempDB 최대 데이터 크기(GB)|83|93|111|148|167|333|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|5760|6400|7680|10240|11520|12800|
|최대 로그 전송률(MBps)|50|50|50|50|50|50|
|최대 동시 작업자(요청)|900|1000|1200|1600|1800|3600|
|최대 동시 로그인|1800|2000|2400|3200|3600|7200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="general-purpose---provisioned-compute---dc-series"></a>범용 - 프로비저닝된 컴퓨팅 - DC 시리즈

|컴퓨팅 크기(서비스 목표)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|컴퓨팅 세대|DC 시리즈|DC 시리즈|DC 시리즈|DC 시리즈|
|vCore 수|2|4|6|8|
|메모리(GB)|9|18|27|36|
|Columnstore 지원 여부|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1024|1536|3072|3072|
|최대 로그 크기(GB) <sup>1</sup>|307|461|922|922|
|TempDB 최대 데이터 크기(GB)|64|128|192|256|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|640|1280|1920|2560|
|최대 로그 전송률(MBps)|9|18|27|36|
|최대 동시 작업자(요청)|160|320|480|640|
|최대 동시 세션|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="business-critical---provisioned-compute---gen4"></a>중요 비즈니스용 - 프로비저닝된 컴퓨팅 - Gen4

> [!IMPORTANT]
> 새 Gen4 데이터베이스는 오스트레일리아 동부 또는 브라질 남부 지역에서 더 이상 지원되지 않습니다.

### <a name="gen4-compute-generation-part-1"></a>Gen4 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|1|2|3|4|5|6|
|메모리(GB)|7|14|21|28|35|42|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|1|2|3|4|5|6|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기(GB) <sup>1</sup>|307|307|307|307|307|307|
|TempDB 최대 데이터 크기(GB)|32|64|96|128|160|192|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|1356|1356|1356|1356|1356|1356|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|4,000|8,000|12,000|16,000|20,000|24,000|
|최대 로그 전송률(MBps)|8|16|24|32|40|48|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|
|최대 동시 로그인|200|400|600|800|1000|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|
|다중 AZ|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="gen4-compute-generation-part-2"></a>Gen4 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|7|8|9|10|16|24|
|메모리(GB)|49|56|63|70|112|159.5|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|7|8|9.5|11|20|36|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기(GB) <sup>1</sup>|307|307|307|307|307|307|
|TempDB 최대 데이터 크기(GB)|224|256|288|320|512|768|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|1356|1356|1356|1356|1356|1356|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|28,000|32,000|36,000|40,000|64,000|76,800|
|최대 로그 전송률(MBps)|56|64|64|64|64|64|
|최대 동시 작업자(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 로그인(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|
|다중 AZ|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="business-critical---provisioned-compute---gen5"></a>중요 비즈니스용 - 프로비저닝된 컴퓨팅 - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|2|4|6|8|10|12|14|
|메모리(GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Columnstore 지원 여부|예|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|최대 데이터 크기(GB)|1024|1024|1536|1536|1536|3072|3072|
|최대 로그 크기(GB) <sup>1</sup>|307|307|461|461|461|922|922|
|TempDB 최대 데이터 크기(GB)|64|128|192|256|320|384|448|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|4829|4829|4829|4829|4829|4829|4829|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|8000|16,000|24,000|32,000|40,000|48,000|56,000|
|최대 로그 전송률(MBps)|24|48|72|96|96|96|96|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|1400|
|최대 동시 로그인|200|400|600|800|1000|1200|1400|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|4|
|다중 AZ|예|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|16|18|20|24|32|40|80|
|메모리(GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Columnstore 지원 여부|예|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|최대 데이터 크기(GB)|3072|3072|3072|4096|4096|4096|4096|
|최대 로그 크기(GB) <sup>1</sup>|922|922|922|1024|1024|1024|1024|
|TempDB 최대 데이터 크기(GB)|512|576|640|768|1024|1280|2560|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|4829|4829|4829|4829|4829|4829|4829|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|64,000|72,000|80,000|96,000|128,000|160,000|204,800|
|최대 로그 전송률(MBps)|96|96|96|96|96|96|96|
|최대 동시 작업자(요청)|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 로그인|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|4|
|다중 AZ|예|예|예|예|예|예|예|
|읽기 확장|예|예|예|예|예|예|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="business-critical---provisioned-compute---m-series"></a>중요 비즈니스용 - 프로비저닝된 컴퓨팅 - M 시리즈

### <a name="m-series-compute-generation-part-1"></a>M 시리즈 컴퓨팅 세대(1부)

|컴퓨팅 크기(서비스 목표)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|컴퓨팅 세대|M 시리즈|M 시리즈|M 시리즈|M 시리즈|M 시리즈|M 시리즈|
|vCore 수|8|10|12|14|16|18|
|메모리(GB)|235.4|294.3|353.2|412.0|470.9|529.7|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|64|80|96|112|128|150|
|최대 데이터 크기(GB)|512|640|768|896|1024|1,152|
|최대 로그 크기(GB) <sup>1</sup>|171|213|256|299|341|384|
|TempDB 최대 데이터 크기(GB)|256|320|384|448|512|576|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|13836|13836|13836|13836|13836|13836|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|12,499|15,624|18,748|21,873|24,998|28,123|
|최대 로그 전송률(MBps)|48|60|72|84|96|108|
|최대 동시 작업자(요청)|800|1,000|1,200|1,400|1,600|1,800|
|최대 동시 로그인|800|1,000|1,200|1,400|1,600|1,800|
|최대 동시 세션|30000|30000|30000|30000|30000|30000|
|복제본 수|4|4|4|4|4|4|
|다중 AZ|예|아니요|아니요|아니요|아니요|예|
|읽기 확장|예|예|예|예|예|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

### <a name="m-series-compute-generation-part-2"></a>M 시리즈 컴퓨팅 세대(2부)

|컴퓨팅 크기(서비스 목표)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|컴퓨팅 세대|M 시리즈|M 시리즈|M 시리즈|M 시리즈|M 시리즈|
|vCore 수|20|24|32|64|128|
|메모리(GB)|588.6|706.3|941.8|1883.5|3767.0|
|Columnstore 지원 여부|예|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|172|216|304|704|1768|
|최대 데이터 크기(GB)|1280|1536|2048|4096|4096|
|최대 로그 크기(GB) <sup>1</sup>|427|512|683|1024|1024|
|TempDB 최대 데이터 크기(GB)|4096|2048|1024|768|640|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|13836|13836|13836|13836|13836|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|31,248|37,497|49,996|99,993|160,000|
|최대 로그 전송률(MBps)|120|144|192|264|264|
|최대 동시 작업자(요청)|2,000|2,400|3,200|6,400|12,800|
|최대 동시 로그인|2,000|2,400|3,200|6,400|12,800|
|최대 동시 세션|30000|30000|30000|30000|30000|
|복제본 수|4|4|4|4|4|
|다중 AZ|예|아니요|아니요|아니요|예|
|읽기 확장|예|예|예|예|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="business-critical---provisioned-compute---dc-series"></a>중요 비즈니스용 - 프로비저닝된 컴퓨팅 - DC 시리즈

|컴퓨팅 크기(서비스 목표)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|컴퓨팅 세대|DC 시리즈|DC 시리즈|DC 시리즈|DC 시리즈|
|vCore 수|2|4|6|8|
|메모리(GB)|9|18|27|36|
|Columnstore 지원 여부|예|예|예|예|
|메모리 내 OLTP 스토리지(GB)|1.7|3.7|5.9|8.2|
|최대 데이터 크기(GB)|768|768|768|768|
|최대 로그 크기(GB) <sup>1</sup>|230|230|230|230|
|TempDB 최대 데이터 크기(GB)|64|128|192|256|
|[최대 로컬 스토리지 크기](resource-limits-logical-server.md#storage-space-governance)(GB)|1406|1406|1406|1406|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS <sup>2</sup>|14000|28000|42000|44800|
|최대 로그 전송률(MBps)|24|48|72|96|
|최대 동시 작업자(요청)|200|400|600|800|
|최대 동시 로그인|200|400|600|800|
|최대 동시 세션|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|
|다중 AZ|예|아니요|아니요|예|
|읽기 확장|예|아니요|아니요|예|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

<sup>1</sup> 문서화된 최대 데이터 크기 값의 경우. 최대 데이터 크기를 줄이면 최대 로그 크기가 비례적으로 줄어듭니다.

<sup>2</sup> 최대 IO 크기 값은 8KB~64KB입니다. 실제 IOPS는 워크로드에 따라 달라집니다. 자세한 내용은 [데이터 IO 거버넌스](resource-limits-logical-server.md#resource-governance)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 대한 DTU 리소스 제한의 경우 [DTU 구매 모델을 사용하는 단일 데이터베이스에 대한 리소스 제한](resource-limits-dtu-single-databases.md)을 참조하세요.
- 탄력적 풀에 대한 vCore 리소스 제한의 경우 [vCore 구매 모델을 사용하는 탄력적 풀에 대한 리소스 제한](resource-limits-vcore-elastic-pools.md)을 참조하세요.
- 탄력적 풀에 대한 DTU 리소스 제한의 경우 [DTU 구매 모델을 사용하는 탄력적 풀에 대한 리소스 제한](resource-limits-dtu-elastic-pools.md)을 참조하세요.
- SQL Managed Instance에 대한 리소스 제한의 경우 [SQL Managed Instance 리소스 제한](../managed-instance/resource-limits.md)을 참조하세요.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- 서버의 리소스 제한에 대한 자세한 내용은 [서버의 리소스 제한 개요](resource-limits-logical-server.md)에서 서버 및 구독 수준 한도에 관한 정보를 참조하세요.
