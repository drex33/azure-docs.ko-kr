---
title: Azure Data Lake Storage Gen2 사용에 대한 모범 사례 | Microsoft Docs
description: Azure Data Lake Storage Gen2 사용과 관련된 데이터 수집, 날짜 보안 및 성능에 대한 모범 사례 알아보기
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: b2d631d165b4a8cd585fbe012d9ab9afaa2e6bf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555435"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 사용에 대한 모범 사례

이 문서에서는 Data Lake Storage Gen2 사용 스토리지 계정의 보안, 복원력 및 디렉터리 구조에 대한 모범 사례 권장 사항을 제공합니다. 성능 최적화에 대한 모범 사례 권장 사항은 [성능을 위해 Azure Data Lake Storage Gen2 최적화를 참조하세요.](data-lake-storage-performance-tuning-guidance.md)

## <a name="security-considerations"></a>보안 고려 사항

### <a name="use-security-groups-instead-of-individual-users"></a>개별 사용자 대신 보안 그룹 사용

ACL(액세스 제어 목록)을 적용하는 경우 항상 Azure AD 보안 그룹을 ACL 항목에서 할당된 보안 주체로 사용합니다. 개별 사용자 또는 서비스 주체를 직접 할당할 수 있는 기회를 거부합니다. 이 구조를 사용하면 ACL을 전체 디렉터리 구조에 다시 적용하지 않고도 사용자 또는 서비스 주체를 추가하고 제거할 수 있습니다. 대신 적절한 Microsoft Azure AD 보안 그룹에서 사용자 및 서비스 주체를 추가하거나 제거할 수 있습니다.

이 모범 사례를 적용하는 방법은 보안 그룹 을 [참조하세요.](data-lake-storage-access-control-model.md#security-groups)

Data Lake Storage Gen2 액세스 제어 모델에 대한 일반적인 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델을 참조하세요.](data-lake-storage-access-control-model.md)

### <a name="configure-the-azure-storage-firewall-with-azure-service-access"></a>Azure 서비스 액세스를 통해 Azure Storage 방화벽 구성

Azure Storage 방화벽을 켜 외부 공격의 벡터를 제한합니다. [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)같은 서비스에서 스토리지 계정에 액세스하려면 해당 서비스의 인스턴스를 가상 네트워크에 배포합니다. 그런 다음 해당 서비스에 대한 스토리지 계정에 대한 액세스 권한을 부여하도록 방화벽을 구성할 수 있습니다.

이 모범 사례를 적용하는 방법에 대한 자세한 내용은 신뢰할 수 있는 [Azure 서비스에 대한 액세스 권한 부여를 참조하세요.](../common/storage-network-security.md)

일반적인 보안 권장 사항은 [Blob Storage에 대한 보안 권장 사항을 참조하세요.](security-recommendations.md)

## <a name="resiliency-considerations"></a>복원력 고려 사항

Data Lake Storage Gen2 또는 클라우드 서비스를 사용하여 시스템을 설계할 때 가용성 요구 사항 및 서비스의 잠재적 중단에 대응하는 방법을 고려합니다. 문제는 특정 인스턴스 또는 지역 전체에 국한될 수 있으므로 둘 모두에 대한 계획이 있어야 합니다. 워크로드에 대한 복구 시간 목표 및 복구 지점 목표 서비스 수준 계약에 따라 고가용성 및 재해 복구를 위해 다소 적극적인 전략을 선택할 수 있습니다.

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

Data Lake Storage Gen2는 이미 지역화된 하드웨어 오류를 보호하기 위해 3배의 복제를 처리합니다. ZRS(영역 중복 스토리지) 및 GZRS(지역 영역 중복 스토리지)와 같은 복제 옵션은 가용성을 향상시킵니다. 이러한 기능을 통해 워크로드는 로컬 또는 새 지역에서 별도로 복제된 인스턴스로 전환하여 서비스 중단에 대응할 수 있습니다.

지역의 치명적인 오류에 대비하려면 GRS(지역 중복 스토리지) 및 RA-GRS(읽기 액세스 지역 중복 스토리지)와 같은 복제 옵션을 사용하여 다른 지역에 데이터를 복제해야 합니다. 또한 백업할 주기적 스냅샷을 만들 수 있는 데이터 손상과 같은 에지 사례에 대한 요구 사항을 고려합니다. 데이터의 중요도와 크기 및 위험 허용 오차에 따라 1시간, 6시간 및 24시간 기간의 델타 스냅샷을 롤링하는 것이 좋습니다.

적절한 복제 모델을 선택하는 것 외에도 모니터링 트리거, 실패한 시도 길이를 통해 클라이언트를 보조 지역으로 자동으로 장애 조치(fail over)하거나 수동 개입을 위해 관리자에게 알림을 보내는 방법을 고려합니다. 서비스가 온라인 상태로 돌아올 때까지 기다리는 것과 비교하여 장애 조치에 대한 절충점이 있음을 명심하세요.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>두 위치 간 데이터 이동에 Distcp 사용

DistCp(Distributed Copy의 약자)는 Hadoop과 함께 제공되고 두 위치 간에 분산된 데이터 이동을 제공하는 Linux 명령줄 도구입니다. 두 위치는 Data Lake Storage Gen2, HDFS(Hadoop 분산 파일 시스템) 또는 S3일 수 있습니다. 이 도구는 Hadoop 클러스터(예: HDInsight)에서 MapReduce 작업을 사용하여 모든 노드의 크기를 확장합니다. Distcp는 특수 네트워크 압축 어플라이언스 없이 빅 데이터를 이동하는 가장 빠른 방법 중 하나로 간주됩니다. 또한 Distcp는 두 위치 간의 델타만 업데이트하고 자동 재시도 및 컴퓨팅의 동적 크기 조정을 처리하는 옵션을 제공합니다. 이 방법은 단일 디렉터리에 많은 대용량 파일을 가질 수 있는 Hive/Spark 테이블과 같은 항목을 복제하고 수정된 데이터만 복사하려는 경우 매우 효율적입니다. 이러한 이유로 Distcp는 빅 데이터 저장소 간에 데이터를 복사하는 데 가장 많이 권장되는 도구입니다.

복사 작업은 Linux cron 작업뿐만 아니라 빈도 또는 데이터 트리거를 사용하는 Apache Oozie 워크플로를 통해서도 트리거될 수 있습니다. 집약적인 복제 작업의 경우 복사 작업에 맞게 특별히 조정 및 확장할 수 있는 별도의 HDInsight Hadoop 클러스터를 스핀업하는 것이 좋습니다. 이렇게 하면 복사 작업이 중요한 작업을 방해하지 않습니다. 매우 충분한 빈도로 복제를 실행하는 경우 각 작업 간에 클러스터가 중단될 수도 있습니다. 보조 지역으로 장애 조치하는 경우 백업이 시작되면 보조 지역에서 또 다른 클러스터가 작동되어 새 데이터를 기본 Data Lake Storage Gen2 계정으로 다시 복제해야 합니다. DistCp 사용에 대한 예제는 [DistCp를 사용하여 Azure Storage Blob과 Data Lake Storage Gen2 간에 데이터 복사](../blobs/data-lake-storage-use-distcp.md)를 참조하세요.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory를 사용하여 복사 작업 예약

[Azure Data Factory](../../data-factory/introduction.md) 복사 작업을 사용하여 복사 작업을 예약하는 데 사용할 수도 있으며 복사 마법사를 통해 빈도로 설정할 수도 있습니다. Azure Data Factory에는 클라우드 DMU(데이터 이동 단위) 제한이 있으며, 결국에는 대규모 데이터 작업에 대한 처리량/계산을 제한한다는 것을 명심하세요. 또한 Azure Data Factory는 현재 Data Lake Storage Gen2 계정 간의 델타 업데이트를 제공하지 않으므로 Hive 테이블과 같은 디렉터리에는 복제하는 데 전체 복사본이 필요합니다. Data Factory를 사용하여 복사하는 방법에 대한 자세한 내용은 [Data Factory 문서](../../data-factory/load-azure-data-lake-storage-gen2.md)를 참조하세요.

## <a name="monitoring-considerations"></a>모니터링 고려 사항

Data Lake Storage Gen2는 Azure Portal의 Data Lake Storage Gen2 계정 및 Azure Monitor에서 메트릭을 제공합니다. Data Lake Storage Gen2의 가용성은 Azure Portal에 표시됩니다. Data Lake Storage Gen2 계정의 최신 가용성을 가져오려면 사용자 고유의 가상 테스트를 실행하여 가용성의 유효성을 검사해야 합니다. 총 스토리지 사용률, 읽기/쓰기 요청 수 및 수신/송신과 같은 다른 메트릭은 애플리케이션을 모니터링하여 활용할 수 있으며, 임계값(예: 평균 지연 시간 또는 분당 오류 수)이 초과되면 경고를 트리거할 수도 있습니다.

## <a name="directory-layout-considerations"></a>디렉터리 레이아웃 고려 사항

데이터를 수집할 때 보안, 분할 및 처리를 효과적으로 활용할 수 있도록 데이터 구조를 미리 계획하는 것이 중요합니다. 다음 추천 사항 중 대다수는 모든 빅 데이터 워크로드에 적용할 수 있습니다. 모든 워크로드에는 데이터 사용 방법에 대한 요구 사항이 다르지만, 다음은 사물 인터넷(IoT) 및 일괄 처리 시나리오로 작업할 때 고려해야 할 몇 가지 일반적인 레이아웃입니다.

### <a name="iot-structure"></a>IoT 구조

IoT 워크로드에는 다양한 제품, 디바이스, 조직 및 고객에 걸쳐 수집되는 많은 데이터가 있을 수 있습니다. 다운스트림 소비자를 위해 조직의 디렉터리 레이아웃, 보안 및 효율적인 데이터 처리를 미리 계획하는 것이 중요합니다. 고려해야 할 일반적인 템플릿은 다음과 같은 레이아웃일 수 있습니다.

*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*

예를 들어 비행기 엔진에 대한 영국 내 착륙 원격 분석은 다음 구조와 같이 표시될 수 있습니다.

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

이 예제에서는 디렉터리 구조의 끝에 날짜를 배치하여 ACL을 사용하여 지역을 보다 쉽게 보호하고 특정 사용자 및 그룹에 문제를 적용할 수 있습니다. 데이터 구조를 시작 부분에 배치하면 이러한 영역을 보호하는 것이 훨씬 더 어렵고 주체가 중요합니다. 예를 들어 영국 데이터 또는 특정 평면에 대한 액세스 권한만 제공하려는 경우 매시간 디렉터리에 있는 여러 디렉터리에 대해 별도의 권한을 적용해야 합니다. 또한 이 구조는 시간이 지남에 따라 디렉터리 수를 기하급수적으로 증가합니다.

### <a name="batch-jobs-structure"></a>일괄 처리 작업 구조

일괄 처리에서 일반적으로 사용되는 방법은 데이터를 "in" 디렉터리에 배치하는 것입니다. 그런 다음, 데이터가 처리되면 다운스트림 프로세스에서 사용할 수 있도록 새 데이터를 "out" 디렉터리에 배치합니다. 이 디렉터리 구조는 개별 파일에 대한 처리가 필요하고 큰 데이터 세트에 대해 대규모 병렬 처리가 필요하지 않을 수 있는 작업에 사용되는 경우가 있습니다. 위에서 권장하는 IoT 구조와 마찬가지로 좋은 디렉터리 구조에는 지역 및 주제 문제(예: 조직, 제품 또는 생산자)와 같은 항목에 대한 부모 수준 디렉터리도 있습니다. 더 나은 조직, 필터링된 검색, 보안 및 자동화를 처리할 수 있도록 구조의 날짜와 시간을 고려합니다. 날짜 구조의 세분성 수준은 데이터가 업로드되거나 처리되는 간격(예: 시간별, 일별 또는 월별)에 따라 결정됩니다.

데이터 손상 또는 예기치 않은 형식으로 인해 파일 처리가 실패하는 경우가 있습니다. 이러한 경우 디렉터리 구조는 추가 검사를 위해 파일을 로 이동하는 **/bad** 폴더의 이점을 누릴 수 있습니다. 또한 일괄 처리 작업은 이러한 *불량* 파일에 대한 보고 또는 알림을 처리하여 수동으로 개입할 수 있습니다. 다음과 같은 템플릿 구조를 고려합니다.

*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*

예를 들어 북아메리카 지역의 고객으로부터 고객 업데이트의 일일 데이터 추출 결과를 받는 마케팅 회사는 처리 전과 후에 다음 코드 조각처럼 표시될 수 있습니다.

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

Hive 또는 기존 SQL 데이터베이스와 같은 데이터베이스로 직접 처리되는 일괄 처리 데이터의 일반적인 경우 출력이 이미 Hive 테이블 또는 외부 데이터베이스에 대한 별도의 폴더로 들어가므로 **/in** 또는 **/out** 디렉터리에 대한 필요가 없습니다. 예를 들어 고객의 일별 추출은 해당 디렉터리에 접어들 수 있습니다. 그런 [다음, Azure Data Factory,](../../data-factory/introduction.md) [Apache Oozie](https://oozie.apache.org/)또는 [Apache Airflow와](https://airflow.apache.org/) 같은 서비스는 데이터를 처리하고 Hive 테이블에 쓰기 위해 매일 Hive 또는 Spark 작업을 트리거합니다.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md)
- [성능을 위한 Azure Data Lake Storage Gen2 최적화](data-lake-storage-performance-tuning-guidance.md)
- [Data Lake에 대한 hitchhiker 가이드](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Azure Data Lake Storage Gen2 개요](data-lake-storage-introduction.md)
