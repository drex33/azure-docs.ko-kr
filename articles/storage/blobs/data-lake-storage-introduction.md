---
title: Azure Data Lake Storage Gen2 소개
description: Azure Data Lake Storage Gen2 소개를 읽어보세요. 주요 기능에 대해 알아봅니다. 지원되는 Blob 스토리지 기능, Azure 서비스 통합 및 플랫폼을 검토합니다.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 65d8ad19426aca491260035364c4e99bfc1b5179
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469647"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 소개

‎Azure Data Lake Storage Gen2는 [Azure Blob Storage](storage-blobs-introduction.md)를 기준으로 하는 빅 데이터 분석 전용의 기능 세트입니다. 

Data Lake Storage Gen2는 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml)의 기능을 Azure Blob Storage와 통합합니다. 예를 들어 Data Lake Storage Gen2는 파일 시스템 의미 체계, 파일 수준 보안 및 확장을 제공합니다. 이러한 기능은 Blob 스토리지를 기반으로 하기 때문에 고가용성/재해 복구 기능을 갖춘 저비용의 계층화된 스토리지를 가져올 수도 있습니다.

## <a name="designed-for-enterprise-big-data-analytics"></a>엔터프라이즈 빅 데이터 분석에 맞게 설계됨

Data Lake Storage Gen2는 Azure에서 Azure Storage를 엔터프라이즈 데이터 레이크를 구축하기 위한 기반으로 만듭니다. 처음부터 수백 기가비트의 처리량을 유지하면서 수 페타바이트의 정보에 대한 서비스를 제공하도록 설계된 Data Lake Storage Gen2는 방대한 양의 데이터를 쉽게 관리할 수 있습니다.

Data Lake Storage Gen2의 기본적인 부분은 [계층 구조 네임스페이스](data-lake-storage-namespace.md)를 Blob 스토리지에 추가하는 것입니다. 계층 구조 네임스페이스는 효율적인 데이터 액세스를 위해 개체/파일을 디렉터리 계층 구조로 구성합니다. 일반적인 개체 저장소 명명 규칙은 이름에 슬래시를 사용하여 계층적 디렉터리 구조를 모방합니다. 이 구조는 Data Lake Storage Gen2에서 구현됩니다. 디렉터리 이름 변경 또는 삭제와 같은 작업은 디렉터리에 대한 단일 원자성 메타데이터 작업이 됩니다. 디렉터리의 이름 접두사를 공유하는 모든 개체를 열거하고 처리할 필요가 없습니다.

Data Lake Storage Gen2는 Blob 스토리지를 기반으로 하며 다음과 같은 방법으로 성능, 관리 및 보안을 향상시킵니다.

-   분석의 필수 구성 요소로 데이터를 복사하거나 변환할 필요가 없으므로 **성능** 이 최적화됩니다. Blob 스토리지의 단일 구조 네임스페이스와 비교할 때 계층 구조 네임스페이스는 디렉터리 관리 작업의 성능을 크게 향상시켜 전반적인 작업 성능을 향상시킵니다.

-   디렉터리 및 하위 디렉터리를 통해 파일을 구성하고 조작할 수 있으므로 **관리** 가 더 쉽습니다.

-   **보안** 은 디렉터리 또는 개별 파일에 대한 POSIX 권한을 정의할 수 있으므로 적용할 수 있습니다.

Data Lake Storage Gen2는 저비용 [Azure Blob Storage](storage-blobs-introduction.md) 위에 빌드되므로 매우 비용 효과적입니다. 추가 기능을 통해 Azure에서 빅 데이터 분석을 실행하기 위한 총 소유 비용이 더 낮아집니다.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2의 주요 기능

-   **Hadoop 호환 액세스**: Data Lake Storage Gen2를 사용하면 [HDFS(Hadoop 분산 파일 시스템)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)에서와 마찬가지로 데이터를 관리하고 액세스할 수 있습니다. 데이터에 액세스하는 데 사용되는 새로운 [ABFS 드라이버](data-lake-storage-abfs-driver.md)는 모든 Apache Hadoop 환경 내에서 사용할 수 있습니다. 이러한 환경에는 [Azure HDInsight](../../hdinsight/index.yml) *,* [Azure Databricks](/azure/databricks/) 및 [Azure Synapse Analytics](../../synapse-analytics/index.yml)가 포함됩니다.

-   **POSIX 권한 상위 세트**: Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 지원합니다. 설정은 Storage Explorer 또는 프레임워크(예: Hive 및 Spark)를 통해 구성할 수 있습니다.

-   **비용 효율성**: Data Lake Storage Gen2는 낮은 비용의 스토리지 용량 및 트랜잭션을 제공합니다. [Azure Blob Storage 수명 주기](storage-lifecycle-management-concepts.md)와 같은 기능은 수명 주기를 통해 데이터가 전환될 때 비용을 최적화합니다.

-   **최적화된 드라이버**: ABFS 드라이버는 빅 데이터 분석을 위해 [특별히 최적화](data-lake-storage-abfs-driver.md)되었습니다. 해당 REST API는 `dfs.core.windows.net` 엔드포인트를 통해 표시됩니다.

### <a name="scalability"></a>확장성

Azure Storage는 Data Lake Storage Gen2 또는 Blob Storage 인터페이스를 통해 액세스하는지 여부에 관계없이 계획적으로 확장 가능합니다. ‘많은 엑사바이트의 데이터’를 저장하고 제공할 수 있습니다. 이 스토리지 양은 높은 IOPS(초당 입출력 작업 수) 수준에서 Gbps(초당 기가비트 수)로 측정되는 처리량에 사용할 수 있습니다. 서비스, 계정 및 파일 수준에서 측정되는 거의 지속적인 요청별 대기 시간으로 처리가 실행됩니다.

### <a name="cost-effectiveness"></a>비용 효과

Data Lake Storage Gen2는 Azure Blob Storage를 기준으로 빌드되므로 스토리지 용량 및 트랜잭션 비용이 낮아집니다. 다른 클라우드 스토리지 서비스와 달리 분석하기 전에 데이터를 이동하거나 변환할 필요가 없습니다. 가격 책정에 대한 자세한 내용은 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage)을 참조하세요.

또한 [계층 구조 네임스페이스](data-lake-storage-namespace.md)와 같은 기능은 많은 분석 작업의 전반적인 성능을 크게 개선합니다. 이 성능 개선은 동일한 양의 데이터를 처리하는 데 필요한 컴퓨팅 능력이 감소하므로 엔드투엔드 분석 작업에 대한 TCO(총 소유 비용)가 낮아짐을 의미합니다.

### <a name="one-service-multiple-concepts"></a>하나의 서비스, 여러 개념

Data Lake Storage Gen2는 Azure Blob Storage를 기준으로 빌드되므로 여러 개념이 동일한 공유 항목을 설명할 수 있습니다.

여러 개념으로 설명되는 동등한 실체는 다음과 같습니다. 달리 지정하지 않는 한 이러한 실체는 직접적인 동의어입니다.

| 개념                                | 최상위 수준 조직 | 낮은 수준의 조직                                            | 데이터 컨테이너 |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob – 범용 개체 스토리지 | 컨테이너              | 가상 디렉터리(SDK만 해당 - 원자성 조작을 제공하지 않음) | Blob           |
| Azure Data Lake Storage Gen2 - Analytics Storage          | 컨테이너            | 디렉터리                                                           | 파일           |

## <a name="supported-blob-storage-features"></a>지원되는 Blob Storage 기능

[진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md), [Blob Storage 수명 주기 관리 정책](storage-lifecycle-management-concepts.md)과 같은 Blob Storage 기능을 계정에서 사용할 수 있습니다. 대부분의 Blob Storage 기능은 완전히 지원되지만 일부 기능은 미리 보기 수준에서만 지원되거나 아직 지원되지 않습니다.  

Data Lake Storage Gen2에서 각 Blob Storage 기능이 어떻게 지원되는지 확인하려면 [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md)을 참조하세요.

## <a name="supported-azure-service-integrations"></a>지원되는 Azure 서비스 통합

Azure Data Lake Storage Gen2는 여러 Azure 서비스를 지원합니다. 데이터를 수집하고, 분석을 수행하고, 시각적 표현을 만드는 데 사용할 수 있습니다. 지원되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)를 참조하세요.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)
