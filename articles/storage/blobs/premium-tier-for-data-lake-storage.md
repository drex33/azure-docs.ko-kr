---
title: Azure Data Lake Storage에 대한 프리미엄 계층
description: Azure Data Lake Storage Gen2에서 프리미엄 성능 계층 사용
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.openlocfilehash: 5e16a5c6f158b9223c3982b00daba258025f4d03
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596986"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 프리미엄 계층

Azure Data Lake Storage Gen2는 이제 [프리미엄 성능 계층](storage-blob-performance-tiers.md#premium-performance)을 지원합니다. 프리미엄 성능 계층은 빅 데이터 분석 애플리케이션 및 일관성 있는 대기 시간이 낮고 트랜잭션 수가 많은 워크로드에 적합합니다.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>프리미엄 성능 계층의 이점을 누릴 수 있는 워크로드

워크로드 예에는 대화형 워크로드, IoT, 스트리밍 분석, 인공 지능 및 기계 학습이 포함됩니다.

**대화형 워크로드**

이러한 워크로드에는 전자상거래 및 매핑 애플리케이션, 대화형 비디오 애플리케이션 등의 사용자 의견 및 즉각적인 업데이트가 필요합니다. 예를 들어 전자상거래 애플리케이션에서 자주 표시되지 않는 항목은 캐시되지 않을 가능성이 높습니다. 그러나 고객이 요청 시 즉시 표시되어야 합니다. 또 다른 예로, 데이터 과학자 분석가 및 개발자는 프리미엄 성능 계층을 사용하는 계정에 저장된 데이터에 대해 쿼리를 실행하여 시간이 중요한 인사이트를 훨씬 더 빠르게 파생할 수 있습니다.

**IoT/스트리밍 분석**

IoT 시나리오에서는 매초 소량의 다양한 쓰기 작업을 클라우드에 푸시할 수 있습니다. 대량 데이터를 수집하고 분석 목적으로 집계한 다음 거의 즉각적으로 삭제할 수 있습니다. 프리미엄 성능 계층의 높은 수집 기능은 이러한 유형의 워크로드에 효율적입니다.

**AI/ML(인공 지능/기계 학습)**

AI/ML은 시각적 개체, 음성 및 텍스트와 같은 다양한 데이터 형식의 소비와 처리를 다룹니다. 워크로드의 고성능 컴퓨팅 형식은 데이터 분석에 대한 신속한 응답과 효율적인 수집 시간을 필요로 하는 대량의 데이터를 처리합니다.

## <a name="cost-effectiveness"></a>비용 효과

프리미엄 성능 계층은 표준 성능 계층에 비해 스토리지 비용은 더 높지만 트랜잭션 비용은 더 낮습니다. 애플리케이션 및 워크로드에서 많은 수의 트랜잭션을 실행하는 경우 프리미엄 성능 계층은 비용 효율적일 수 있습니다.

다음 표에서는 Azure Data Lake Storage에 대한 프리미엄 계층의 비용 효율성을 보여 줍니다. 각 열은 한 달의 트랜잭션 수를 나타냅니다. 각 행은 읽은 트랜잭션의 백분율을 나타냅니다. 테이블의 각 셀은 읽기 트랜잭션 백분율 및 실행된 트랜잭션 수와 관련된 비용 감소 백분율을 보여 줍니다.

예를 들어 사용자의 계정이 미국 동부 2 지역에 있고, 계정의 트랜잭션 수가 9000만을 초과하고, 해당 트랜잭션 중 70%가 읽기 트랜잭션이면 프리미엄 성능 계층은 더 비용 효율적입니다.

> [!div class="mx-imgBorder"]
> ![이미지가 여기에 표시됩니다.](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> 각 TB급 데이터의 초당 트랜잭션 수를 기반으로 비용 효율성을 평가하려는 경우 테이블의 아래쪽에 표시되는 열 제목을 사용할 수 있습니다.

가격 책정에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/) 페이지를 참조하세요.

## <a name="feature-availability"></a>기능 가용성

일부 Blob storage 기능을 사용하지 못할 수도 있고 프리미엄 성능 계층을 부분적으로만 지원할 수도 있습니다. 전체 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능](./storage-feature-support-in-storage-accounts.md)을 참조하세요. 그런 다음 [알려진 문제](data-lake-storage-known-issues.md) 목록을 검토하여 기능의 차이를 평가합니다.

## <a name="enabling-the-premium-performance-tier"></a>프리미엄 성능 계층 사용

**계층적 네임스페이스** 를 **사용** 으로 설정한 BlockBlobStorage 계정을 만들어 Azure Data Lake Storage에 대한 프리미엄 계층을 사용할 수 있습니다. 전체 지침은 [BlobStorage 계정 만들기](../common/storage-account-create.md) 계정을 참조하세요.

계정을 만들 때 **프리미엄** 성능 옵션과 **BlockBlobStorage** 계정 종류를 선택해야 합니다.

> [!div class="mx-imgBorder"]
> ![blockblobstorageacount 만들기](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

**스토리지 계정 만들기** 페이지의 **고급** 탭에서 **계층적 네임스페이스** 설정을 사용합니다. 계정을 만들 때 이 설정을 사용하도록 설정해야 합니다. 나중에 사용하도록 설정할 수 없습니다.

다음 이미지는 **스토리지 계정 만들기** 페이지의 이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>다음 단계

Azure Databricks, Azure HDInsight 및 Azure Synapse Analytics와 같이 선호하는 분석 서비스와 Azure Data Lake Storage에 대한 프리미엄 계층을 사용합니다.

- [자습서: Azure Data Lake Storage Gen2, Azure Databricks 및 Spark](data-lake-storage-use-databricks-spark.md)
- [Azure HDInsight 클러스터에서 Azure Data Lake Storage Gen2를 사용합니다.](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight는 현재 가속화된 쓰기를 사용하도록 설정된 HBase 클러스터와 함께 프리미엄 성능 계층을 사용하는 계정을 지원합니다.
- [빠른 시작: Synapse 작업 영역 만들기](../../synapse-analytics/quickstart-create-workspace.md)
