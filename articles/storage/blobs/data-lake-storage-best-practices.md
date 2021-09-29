---
title: Azure Data Lake Storage Gen2 사용에 대한 모범 사례 | Microsoft Docs
description: 성능을 최적화하고, 비용을 절감하고, Data Lake Storage Gen2 지원 Azure Storage 계정을 보호하는 방법을 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: e1361fedfb9ee09ebbef416a0ba59eaa58f876aa
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236151"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 사용에 대한 모범 사례

이 문서에서는 성능을 최적화하고, 비용을 절감하고, Data Lake Storage Gen2 지원 Azure Storage 계정을 보호하는 데 도움이 되는 모범 사례 지침을 제공합니다. 

## <a name="find-documentation"></a>설명서 찾기

Azure Data Lake Storage Gen2는 전용 서비스 또는 계정 유형이 아닙니다. 높은 처리량 분석 워크로드를 지원하는 기능 집합입니다. Data Lake Storage Gen2 설명서에서는 이러한 기능을 사용하기 위한 모범 사례 및 지침을 제공합니다. 네트워크 보안 설정, 고가용성 설계 및 재해 복구와 같은 계정 관리의 다른 모든 측면은 [Blob Storage 설명서](storage-blobs-introduction.md) 콘텐츠를 참조하세요. 

#### <a name="evaluate-feature-support-and-known-issues"></a>기능 지원 및 알려진 문제 평가

Blob Storage 기능을 사용하도록 계정을 구성할 때 다음 패턴을 사용합니다.

1. Azure Storage [계정의 Blob Storage 기능 지원을](storage-feature-support-in-storage-accounts.md) 검토하여 계정에서 기능이 완전히 지원되는지 확인합니다. 일부 기능은 아직 지원되지 않거나 Data Lake Storage Gen2 사용 계정에서 부분적으로 지원됩니다. 기능 지원은 항상 확장되므로 업데이트를 위해 이 문서를 정기적으로 검토해야 합니다.

2. Azure [Data Lake Storage Gen2의 알려진 문제를](data-lake-storage-known-issues.md) 검토하여 사용하려는 기능에 대한 제한 사항 또는 특별 지침이 있는지 확인합니다.

3. Data Lake Storage Gen2 사용 계정과 관련된 지침은 기능 문서를 검색합니다. 

#### <a name="understand-the-terms-used-in-documentation"></a>설명서에 사용된 용어 이해

콘텐츠 집합 간에 이동할 때 약간의 용어 차이가 있습니다. 예를 들어 [Blob Storage 설명서](storage-blobs-introduction.md)에 추천되는 콘텐츠는 *파일* 대신 *Blob이라는* 용어를 사용합니다. 기술적으로 스토리지 계정에 저장하는 파일은 계정의 Blob이 됩니다. 따라서 용어가 올바름입니다. 그러나 이 경우 *라는* 용어를 사용하는 경우 혼동이 발생할 수 있습니다. *파일 시스템* 을 참조하는 데 사용되는 *컨테이너라는* 용어도 표시됩니다. 이러한 용어는 동의어로 간주합니다.

## <a name="optimize-for-data-ingest"></a>데이터 수집에 최적화

원본 시스템에서 데이터를 수집할 때 원본 하드웨어, 원본 네트워크 하드웨어 또는 스토리지 계정에 대한 네트워크 연결이 병목 상태가 될 수 있습니다.  

![원본 시스템에서 Data Lake Storage Gen2 데이터를 수집할 때 고려해야 할 요소를 보여주는 다이어그램.](./media/data-lake-storage-best-practices/bottleneck.png)

### <a name="source-hardware"></a>원본 하드웨어

온-프레미스 머신을 사용하든, Azure에서 VM(Virtual Machines)을 사용하든 적절한 하드웨어를 신중하게 선택해야 합니다. 디스크 하드웨어의 경우 SSD(반도체 드라이브)를 사용하고 스핀들이 더 빠른 디스크 하드웨어를 선택하는 것이 좋습니다. 네트워크 하드웨어의 경우 가능한 가장 빠른 NIC(네트워크 인터페이스 컨트롤러)를 사용합니다. Azure에서는 적절한 강력한 디스크 및 네트워킹 하드웨어가 있는 Azure D14 VM을 권장합니다.

### <a name="network-connectivity-to-the-storage-account"></a>스토리지 계정에 대한 네트워크 연결

원본 데이터와 스토리지 계정 간의 네트워크 연결이 병목 상태가 될 수 있습니다. 원본 데이터가 온-프레미스에 있는 경우 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)전용 링크를 사용하는 것이 좋습니다. 원본 데이터가 Azure에 있는 경우 데이터가 Data Lake Storage Gen2 사용 계정과 동일한 Azure 지역에 있는 경우 성능이 가장 좋습니다.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>최대 병렬 처리를 위한 데이터 수집 도구 구성

최상의 성능을 얻으려면 가능한 한 많은 읽기 및 쓰기를 병렬로 수행하여 사용 가능한 모든 처리량을 사용합니다.

![Data Lake Storage Gen2 성능](./media/data-lake-storage-best-practices/throughput.png)

다음 표에서는 몇 가지 인기 있는 검색 도구에 대한 주요 설정을 요약하여 보여줍니다.  

| 도구               | 설정 | 
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m(mapper) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    | 
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m(mapper)   |   

> [!NOTE]
> 수집 작업의 전반적인 성능은 데이터를 수집하기 위해 사용하는 도구와 관련된 다른 요인에 따라 달라집니다. 최신 지침은 사용하려는 각 도구에 대한 설명서를 참조하세요.

계정을 확장하여 모든 분석 시나리오에 필요한 처리량을 제공할 수 있습니다. 기본적으로 Data Lake Storage Gen2 사용 계정은 광범위한 사용 사례 범주의 요구 사항을 충족하기에 충분한 처리량을 기본 구성에 제공합니다. 기본 제한에 초과하는 경우 Azure 지원 에 [문의하여](https://azure.microsoft.com/support/faq/)더 많은 처리량을 제공하도록 계정을 구성할 수 있습니다.

## <a name="structure-data-sets"></a>구조 데이터 집합

데이터의 구조를 미리 계획하는 것이 좋습니다. 파일 형식, 파일 크기 및 디렉터리 구조는 모두 성능 및 비용에 영향을 줄 수 있습니다. 

### <a name="file-formats"></a>파일 형식

다양한 형식으로 데이터를 수집할 수 있습니다. 데이터는 사람이 읽을 수 있는 형식(예: JSON, CSV 또는 XML) 또는 압축된 이진 형식(예: )으로 표시될 수 `.tar.gz` 있습니다. 데이터는 다양한 크기로도 제공됩니다. 데이터는 온-프레미스 시스템에서 SQL 테이블 내보내기의 데이터와 같은 대용량 파일(몇 테라바이트)로 구성될 수 있습니다. 또한 데이터는 IoT(사물 인터넷) 솔루션의 실시간 이벤트의 데이터와 같이 많은 수의 작은 파일(몇 킬로바이트)의 형태로 제공됩니다. 적절한 파일 형식 및 파일 크기를 선택하여 효율성과 비용을 최적화할 수 있습니다. 

Hadoop은 구조적 데이터를 저장하고 처리하는 데 최적화된 파일 형식 집합을 지원합니다. 몇 가지 일반적인 형식은 Avro, Parquet 및 ORC(Optimized Row Columnar) 형식입니다. 이러한 형식은 모두 컴퓨터에서 읽을 수 있는 이진 파일 형식입니다. 파일 크기를 관리하는 데 도움이 되며 압축됩니다. 각 파일에 스키마가 포함되어 있어 자체 설명이 됩니다. 이러한 형식 간의 차이점은 데이터가 저장되는 방식입니다. Avro는 데이터를 행 기반 형식으로 저장하고 Parquet 및 ORC 형식은 데이터를 열 형식으로 저장합니다.

I/O 패턴이 쓰기 집약적이거나 쿼리 패턴이 여러 레코드 행 전체를 검색하는 것을 선호하는 경우 Avro 파일 형식을 사용하는 것이 좋습니다. 예를 들어 Avro 형식은 여러 이벤트/메시지를 연속해서 쓰는 이벤트 허브 또는 Kafka와 같은 메시지 버스에서 잘 작동합니다.

I/O 패턴을 더 많이 읽거나 쿼리 패턴이 레코드의 열 하위 집합에 초점을 맞춘 경우 Parquet 및 ORC 파일 형식을 고려합니다. 읽기 트랜잭션은 전체 레코드를 읽는 대신 특정 열을 검색하도록 최적화할 수 있습니다.

Apache Parquet은 읽기가 많은 분석 파이프라인에 최적화된 오픈 소스 파일 형식입니다. Parquet의 열형 스토리지 구조를 사용하면 관련 없는 데이터를 건너뛸 수 있습니다. 쿼리는 스토리지에서 분석 엔진으로 보낼 데이터의 범위를 좁힐 수 있으므로 훨씬 더 효율적입니다. 또한 유사한 데이터 형식(열의 경우)이 함께 저장되기 때문에 Parquet는 데이터 스토리지 비용을 낮출 수 있는 효율적인 데이터 압축 및 인코딩 체계를 지원합니다. [Azure Synapse Analytics,](../../synapse-analytics/overview-what-is.md) [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 및 [Azure Data Factory](../../data-factory/introduction.md) 같은 서비스에는 Parquet 파일 형식을 활용하는 네이티브 기능이 있습니다.

### <a name="file-size"></a>파일 크기

파일이 클수록 성능이 향상되고 비용이 절감됩니다. 

일반적으로 HDInsight와 같은 분석 엔진에는 나열, 액세스 확인 및 다양한 메타데이터 작업 수행과 같은 작업이 포함된 파일당 오버헤드가 있습니다. 데이터를 많은 작은 파일로 저장하는 경우 성능이 저하될 수 있습니다. 일반적으로 성능 향상을 위해 데이터를 더 큰 크기의 파일로 구성합니다(256MB~100GB 크기). 일부 엔진 및 애플리케이션은 크기가 100GB보다 큰 파일을 효율적으로 처리하는 데 문제가 있을 수 있습니다. 

파일 크기를 늘리면 트랜잭션 비용도 줄일 수 있습니다. 읽기 및 쓰기 작업은 4MB 증분으로 청구되므로 파일에 4MB 또는 몇 킬로바이트만 포함되는지 여부에 관계없이 작업에 대한 요금이 청구됩니다. 가격 책정 정보는 [Azure Data Lake Storage 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

때때로 작은 파일들이 많은 원시 데이터에 대해 데이터 파이프라인의 제어가 제한될 수 있습니다. 일반적으로 시스템에는 다운스트림 응용 프로그램에서 사용하기 위해 작은 파일을 더 큰 파일로 집계하는 일종의 프로세스를 갖추는 것이 좋습니다. 실시간으로 데이터를 처리하는 경우 실시간 스트리밍 엔진(예: [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) 또는 Spark 스트리밍)을 [](https://databricks.com/glossary/what-is-spark-streaming)메시지 브로커(예: [Event Hub](../../event-hubs/event-hubs-about.md) 또는 [Apache Kafka)와](https://kafka.apache.org/)함께 사용하여 데이터를 더 큰 파일로 저장할 수 있습니다. 작은 파일을 더 큰 파일로 집계할 때 다운스트림 처리를 위해 [Apache Parquet과](https://parquet.apache.org/) 같은 읽기 최적화 형식으로 저장하는 것이 좋습니다. 

### <a name="directory-structure"></a>디렉터리 구조

모든 워크로드에는 데이터 사용 방법에 대한 요구 사항이 다르지만, IoT(사물 인터넷), 일괄 처리 시나리오를 사용하거나, 시리즈 데이터를 최적화할 때 고려해야 할 몇 가지 일반적인 레이아웃입니다.

#### <a name="iot-structure"></a>IoT 구조

IoT 워크로드에는 다양한 제품, 디바이스, 조직 및 고객에 걸쳐 수집되는 많은 데이터가 있을 수 있습니다. 다운스트림 소비자를 위해 조직의 디렉터리 레이아웃, 보안 및 효율적인 데이터 처리를 미리 계획하는 것이 중요합니다. 고려해야 할 일반적인 템플릿은 다음과 같은 레이아웃일 수 있습니다.

`*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*`

예를 들어 비행기 엔진에 대한 영국 내 착륙 원격 분석은 다음 구조와 같이 표시될 수 있습니다.

`*UK/Planes/BA1293/Engine1/2017/08/11/12/*`

이 예제에서는 디렉터리 구조의 끝에 날짜를 배치하여 ACL을 사용하여 지역을 보다 쉽게 보호하고 특정 사용자 및 그룹에 문제를 적용할 수 있습니다. 데이터 구조를 처음에 배치 하는 경우 이러한 지역 및 주제를 보호 하는 것이 훨씬 더 어려워집니다. 예를 들어 영국 데이터 또는 특정 평면에만 액세스를 제공 하려는 경우 모든 시간 디렉터리에서 여러 디렉터리에 대해 별도의 권한을 적용 해야 합니다. 또한이 구조는 시간이 소요 되 면 디렉터리 수를 급격 하 게 늘립니다.

#### <a name="batch-jobs-structure"></a>일괄 처리 작업 구조

일괄 처리에서 일반적으로 사용 되는 방법은 데이터를 "in" 디렉터리에 배치 하는 것입니다. 그런 다음 데이터가 처리 되 면 다운스트림 프로세스에서 사용할 새 데이터를 "out" 디렉터리에 배치 합니다. 이 디렉터리 구조는 개별 파일에 대 한 처리를 필요로 하는 작업에 사용 되 고, 대량 데이터 집합에 대 한 대규모 병렬 처리가 필요 하지 않을 수 있습니다. 위에서 권장 하는 IoT 구조와 마찬가지로 좋은 디렉터리 구조에는 지역 및 주제 (예: 조직, 제품 또는 생산자)와 같은 사물에 대 한 부모 수준 디렉터리가 있습니다. 구조에서 날짜 및 시간을 고려 하 여 처리 중에 더 나은 구성, 필터링 된 검색, 보안 및 자동화를 허용 합니다. 날짜 구조의 세분성 수준은 데이터가 업로드되거나 처리되는 간격(예: 시간별, 일별 또는 월별)에 따라 결정됩니다.

데이터 손상 또는 예기치 않은 형식으로 인해 파일 처리가 실패하는 경우가 있습니다. 이러한 경우, 디렉터리 구조는 추가 검사를 위해 파일을로 이동 하는 **/잘못** 된 폴더의 이점을 누릴 수 있습니다. 또한 일괄 처리 작업은 이러한 *불량* 파일에 대한 보고 또는 알림을 처리하여 수동으로 개입할 수 있습니다. 다음과 같은 템플릿 구조를 고려합니다.

`*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/*\`
`*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/*\`
`*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*`

예를 들어 북아메리카 지역의 고객으로부터 고객 업데이트의 일일 데이터 추출 결과를 받는 마케팅 회사는 처리 전과 후에 다음 코드 조각처럼 표시될 수 있습니다.

`*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\`
`*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*`

일괄 처리 데이터를 hive 또는 기존의 SQL 데이터베이스와 같은 데이터베이스에 직접 처리 하는 일반적인 경우에는 출력이 이미 hive 테이블이 나 외부 데이터베이스에 대 한 별도의 폴더로 이동 하기 때문에 **/in** 또는 **/out** 디렉터리가 필요 하지 않습니다. 예를 들어 고객의 매일 추출은 해당 하는 디렉터리로 이동 합니다. 그런 다음 [Azure Data Factory](../../data-factory/introduction.md), [Apache Oozie](https://oozie.apache.org/)또는 [apache 공기 흐름과](https://airflow.apache.org/) 같은 서비스는 매일 hive 또는 Spark 작업을 트리거하여 데이터를 처리 하 고 Hive 테이블에 기록 합니다.

#### <a name="time-series-data-structure"></a>시계열 데이터 구조

Hive 작업의 경우 시계열 데이터의 파티션 정리를 통해 일부 쿼리가 데이터의 하위 집합만 읽을 수 있으므로 성능이 향상 됩니다.    

시계열 데이터를 수집하는 이러한 파이프라인은 파일 및 폴더에 대해 구조적 명명을 사용하여 파일을 배치하는 경우가 많습니다. 다음은 날짜별로 구조화 된 데이터에 대 한 일반적인 예입니다.

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

datetime 정보가 폴더 및 파일 이름 둘 다에 나타납니다.

날짜 및 시간의 일반적인 패턴은 다음과 같습니다.

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

앞에서 설명했듯이, 더 큰 파일 크기와 각 폴더에 포함된 적절한 파일 수의 요건에 맞는 폴더 및 파일 구성을 선택해야 합니다.

## <a name="set-up-security"></a>보안 설정

먼저 [Blob 저장소에 대 한 보안 권장 사항](security-recommendations.md) 문서의 권장 사항을 검토 합니다. 실수로 또는 악의적인 삭제 로부터 데이터를 보호 하 고, 방화벽 뒤의 데이터를 보호 하 고, id 관리의 기준으로 Azure Active Directory (Azure AD)를 사용 하는 방법에 대 한 모범 사례 지침을 찾을 수 있습니다. 

그런 다음 [Azure Data Lake Storage Gen2의 액세스 제어 모델에서](data-lake-storage-access-control-model.md) Data Lake Storage Gen2 사용 가능한 계정과 관련 된 지침을 검토 합니다. 이 문서는 Azure RBAC (역할 기반 액세스 제어) 역할을 Acl (액세스 제어 목록)과 함께 사용 하 여 계층적 파일 시스템의 디렉터리 및 파일에 대 한 보안 권한을 적용 하는 방법을 이해 하는 데 도움이 됩니다. 

## <a name="ingest-data"></a>데이터 수집

이 섹션에서는 다양한 데이터 원본과 해당 데이터를 Data Lake Storage Gen2 계정에 수집할 수 있는 여러 가지 방법을 중점적으로 설명합니다.

#### <a name="ad-hoc-data"></a>임시 데이터

빅 데이터 응용 프로그램을 프로토타입 하는 데 사용 되는 더 작은 데이터 집합. 이러한 도구 중 하나를 사용 하 여 데이터를 수집 하는 것이 좋습니다. 

- Azure portal
- [Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [Azure CLI](data-lake-storage-directory-file-acl-cli.md)
- [REST (영문)](/rest/api/storageservices/data-lake-storage-gen2)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [AZCopy](../common/storage-use-azcopy-v10.md)

#### <a name="streamed-data"></a>스트리밍된 데이터

응용 프로그램, 장치 및 센서와 같은 다양 한 소스에서 생성 됩니다. 이러한 유형의 데이터를 수집 하는 데 사용 되는 도구는 일반적으로 이벤트에 따라 실시간으로 데이터를 캡처하고 처리 한 다음 사용자 계정에 일괄 처리로 이벤트를 씁니다. 이러한 도구 중 하나를 사용 하 여 데이터를 수집 하는 것이 좋습니다.

- [HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md)
- [Azure Stream Analytics](../../stream-analytics/stream-analytics-quick-create-portal.md)

#### <a name="relational-data"></a>관계형 데이터

관계형 데이터베이스는 빅 데이터 파이프라인을 통해 처리 되는 경우 주요 통찰력을 제공할 수 있는 많은 수의 레코드를 수집 합니다. [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) 를 사용 하 여 관계형 데이터를 수집 하는 것이 좋습니다.

#### <a name="web-server-log-data"></a>웹 서버 로그 데이터

페이지 요청 기록과 같은 정보가 포함 된 로그 파일입니다. 사용자 지정 스크립트나 응용 프로그램을 작성 하 여이 데이터를 업로드 하는 것이 좋습니다. 이렇게 하면 데이터 업로드 구성 요소를 큰 빅 데이터 응용 프로그램의 일부로 포함할 수 있습니다. 이러한 도구와 Sdk를 사용 하는 것이 좋습니다.

- [Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [Azure CLI](data-lake-storage-directory-file-acl-cli.md)
- [REST (영문)](/rest/api/storageservices/data-lake-storage-gen2)
- Azure Sdk ([.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)및 [Node.js](data-lake-storage-directory-file-acl-javascript.md))
- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

#### <a name="hdinsight-clusters"></a>HDInsight 클러스터

대부분의 HDInsight 클러스터 유형(Hadoop, HBase, Storm)은 Data Lake Storage Gen2를 데이터 스토리지 리포지토리로 지원합니다. 이러한 도구 중 하나를 사용 하 여 데이터를 수집 하는 것이 좋습니다.

- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [AZCopy](../common/storage-use-azcopy-v10.md)

#### <a name="hadoop-clusters"></a>Hadoop 클러스터

이러한 클러스터는 온-프레미스 또는 클라우드에서 실행 될 수 있습니다. 이러한 도구 중 하나를 사용 하 여 데이터를 수집 하는 것이 좋습니다.

- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [Azure 용 WANdisco 사용 중인 데이터 Migrator](migrate-gen2-wandisco-live-data-platform.md)
- [Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)

#### <a name="large-data-sets"></a>대량 데이터 집합

용량이 수 테라바이트에 달하는 데이터 세트를 업로드하는 경우 위에서 설명한 방법을 사용하면 속도가 느리고 비용이 많이 들 수 있습니다. 이러한 경우 Azure ExpressRoute를 사용할 수 있습니다.  

Azure ExpressRoute를 사용하면 온-프레미스의 인프라와 Azure 데이터 센터 사이에 프라이빗 연결을 만들 수 있습니다. 이렇게 하면 대용량 데이터를 안전하게 전송할 수 있습니다. 자세한 내용은 [Azure ExpressRoute 설명서](../../expressroute/expressroute-introduction.md)를 참조하세요.

## <a name="process-and-analyze-data"></a>데이터 처리 및 분석

Data Lake Storage Gen2에서 데이터를 사용할 수 있게 되 면 해당 데이터에 대 한 분석을 실행 하 고, 시각화를 만들며, 로컬 컴퓨터 또는 Azure SQL 데이터베이스 또는 SQL Server 인스턴스와 같은 다른 리포지토리로 데이터를 다운로드할 수 있습니다. 다음 섹션에서는 데이터를 분석, 시각화 및 다운로드 하는 데 사용할 수 있는 도구를 권장 합니다.

#### <a name="tools-for-analyzing-data"></a>데이터 분석 도구

- [Azure Synapse Analytics](../../synapse-analytics/get-started-analyze-storage.md)
- [Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse)

#### <a name="tools-for-visualizing-data"></a>데이터 시각화 도구

- [Power BI](/power-query/connectors/datalakestorage)
- [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md)

#### <a name="tools-for-downloading-data"></a>데이터를 다운로드 하기 위한 도구

- Azure portal
- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [Azure CLI](data-lake-storage-directory-file-acl-cli.md)
- [REST (영문)](/rest/api/storageservices/data-lake-storage-gen2)
- Azure Sdk ([.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)및 [Node.js](data-lake-storage-directory-file-acl-javascript.md))
- [Azure Storage Explorer](data-lake-storage-explorer.md)
- [AZCopy](../common/storage-use-azcopy-v10.md#transfer-data)
- [Azure Data Factory](../../data-factory/copy-activity-overview.md)
- [Apache DistCp](./data-lake-storage-use-distcp.md)

## <a name="monitor-telemetry"></a>원격 분석 모니터링

사용 및 성능 모니터링은 서비스 운영화의 중요한 부분입니다. 예를 들어 자주 발생하는 작업, 대기 시간이 높은 작업 또는 서비스 쪽 제한을 유발하는 작업이 있습니다. 

스토리지 계정에 대한 모든 원격 분석은 [Azure Monitor 의 Azure Storage 로그를](monitor-blob-storage.md)통해 사용할 수 있습니다. 이 기능은 Log Analytics 및 Event Hubs 스토리지 계정을 통합하는 동시에 다른 스토리지 계정에 로그를 보관할 수 있도록 합니다. 메트릭 및 리소스 로그 및 관련 스키마의 전체 목록을 보려면 [모니터링 데이터 참조 Azure Storage](monitor-blob-storage-reference.md)참조하세요.

로그를 저장하도록 선택하는 위치는 로그에 액세스하려는 방법에 따라 달라집니다. 예를 들어 로그에 거의 실시간으로 액세스하고 로그의 이벤트를 Azure Monitor 다른 메트릭과 상호 연결하려는 경우 Log Analytics 작업 영역에 로그를 저장할 수 있습니다. 이렇게 하면 KQL을 사용하여 로그를 쿼리하고 작업 영역의 테이블을 열거하는 쿼리를 `StorageBlobLogs` 작성할 수 있습니다.

근 실시간 쿼리 및 장기 보존을 위해 로그를 저장하려는 경우 Log Analytics 작업 영역과 스토리지 계정 모두에 로그를 보내도록 진단 설정을 구성할 수 있습니다.

Splunk와 같은 다른 쿼리 엔진을 통해 로그에 액세스하려는 경우 이벤트 허브로 로그를 보내고 Event Hub에서 선택한 대상으로 로그를 검색하도록 진단 설정을 구성할 수 있습니다.

Azure Monitor Azure Storage 로그는 Azure Portal, PowerShell, Azure CLI 및 Azure Resource Manager 템플릿을 통해 사용하도록 설정할 수 있습니다. 대규모 배포의 경우 Azure Policy 수정 작업을 완전히 지원하는 데 사용할 수 있습니다. 자세한 내용은 [Azure/Community-Policy](https://github.com/Azure/Community-Policy/tree/master/Policies/Storage/deploy-storage-monitoring-log-analytics) 및 [ciphertxt/AzureStoragePolicy 를 참조하세요.](https://github.com/ciphertxt/AzureStoragePolicy)


## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md)
- [Data Lake에 대한 hitchhiker 가이드](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Azure Data Lake Storage Gen2 개요](data-lake-storage-introduction.md)
