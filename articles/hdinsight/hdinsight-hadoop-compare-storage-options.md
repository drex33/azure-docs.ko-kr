---
title: Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교
description: 스토리지 유형 및 Azure HDInsight에서 작동하는 원리에 대한 개요를 제공합니다.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4d58bfc14e4b61860d239cc3dcfd0ad276aec781
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273517"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교

HDInsight 클러스터를 만들 때 몇 가지 Azure Storage 서비스 중에서 선택할 수 있습니다.

* [Azure Blob Storage에 HDInsight 사용](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2에 HDInsight 사용](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1에 HDInsight 사용](./overview-data-lake-storage-gen1.md)

이 문서에서는 이러한 스토리지 유형 및 각 유형의 고유 기능에 대한 개요를 제공합니다.

## <a name="storage-types-and-features"></a>스토리지 형식 및 기능

다음 표는 다양한 버전의 HDInsight에서 지원되는 Azure Storage 서비스를 간략히 설명합니다.

| 스토리지 서비스 | 계정 유형 | 네임스페이스 형식 | 지원되는 서비스 | 지원되는 성능 계층 | 지원되는 액세스 계층 | HDInsight 버전 | 클러스터 유형 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 범용 V2 | 계층 구조(파일 시스템) | Blob | Standard | 핫, 쿨, 보관 | 3.6 이상 | 모두(Spark 2.1 및 2.2 제외)|
|Azure Storage| 범용 V2 | Object | Blob | Standard | 핫, 쿨, 보관 | 3.6 이상 | 모두 |
|Azure Storage| 범용 V1 | Object | Blob | Standard | 해당 없음 | 모두 | 모두 |
|Azure Storage| Blob Storage** | Object | 블록 Blob | 표준 | 핫, 쿨, 보관 | 모두 | 모두 |
|Azure Data Lake Storage Gen1| 해당 없음 | 계층 구조(파일 시스템) | 해당 없음 | 해당 없음 | 해당 없음 | 3.6만 해당 | 모두(HBase 제외) |
|Azure Storage| 블록 Blob| Object | 블록 Blob | Premium | 해당 없음| 3.6 이상 | 가속 쓰기를 사용하는 HBase만 해당|
|Azure Data Lake Storage Gen2| 블록 Blob| 계층 구조(파일 시스템) | 블록 Blob | Premium | 해당 없음| 3.6 이상 | 가속 쓰기를 사용하는 HBase만 해당|

**HDInsight 클러스터의 경우에는 보조 스토리지 계정만 BlobStorage 형식이 될 수 있으며, 페이지 Blob 저장소 옵션은 지원되지 않습니다.

Azure Storage 계정 형식에 대한 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.

Azure Storage 액세스 계층에 대한 자세한 내용은 [Azure Blob Storage: 프리미엄(미리 보기), 핫, 쿨 및 보관 스토리지 계층](../storage/blobs/access-tiers-overview.md)을 참조하세요.

기본 및 선택적 보조 스토리지에 대한 서비스 조합을 사용하여 클러스터를 만들 수 있습니다. 다음 표는 HDInsight에서 현재 지원되는 클러스터 스토리지 구성을 간략히 설명합니다.

| HDInsight 버전 | 기본 스토리지 | 보조 스토리지 | 지원됨 |
|---|---|---|---|
| 3.6 및 4.0 | 범용 V1 및 범용 V2 | 범용 V1, 범용 V2, BlobStorage(블록 Blob) | 예 |
| 3.6 및 4.0 | 범용 V1 및 범용 V2 | Data Lake Storage Gen2 | 예 |
| 3.6 및 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | 예 |
| 3.6 및 4.0 | Data Lake Storage Gen2* | 범용 V1, 범용 V2, BlobStorage(블록 Blob) | 예 |
| 3.6 및 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 예 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 예 |
| 3.6 | Data Lake Storage Gen1 | 범용 V1, 범용 V2, BlobStorage(블록 Blob) | 예 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 예 |
| 4.0 | Data Lake Storage Gen1 | 모두 | 예 |
| 4.0 | 범용 V1 및 범용 V2 | Data Lake Storage Gen1 | 예 |

*=클러스터 액세스에 동일한 관리 ID를 사용하도록 모두 설정하는 경우 Data Lake Storage Gen2는 한 개일 수도 있고 여러 개일 수도 있습니다.

> [!NOTE]
> Data Lake Storage Gen2 기본 스토리지는 Spark 2.1 또는 2.2 클러스터에 대해 지원되지 않습니다.

## <a name="data-replication"></a>데이터 복제

Azure HDInsight는 고객 데이터를 저장하지 않습니다. 클러스터의 기본 스토리지 수단은 연결된 스토리지 계정입니다. 클러스터를 기존 스토리지 계정에 연결하거나, 클러스터를 만드는 중에 새 스토리지 계정을 만들 수 있습니다. 새 계정을 만드는 경우 LRS(로컬 중복 스토리지) 계정으로 만들어지며 [보안 센터](https://azuredatacentermap.azurewebsites.net)에 명시된 사항을 비롯한 지역 데이터 보존 요구 사항을 충족하게 됩니다.

HDInsight와 연결된 스토리지 계정이 LRS 또는 [보안 센터](https://azuredatacentermap.azurewebsites.net)에 언급된 다른 스토리지 옵션인지 확인하여 HDInsight가 단일 지역에 데이터를 저장하도록 적절히 구성되었는지 확인할 수 있습니다.
 
## <a name="next-steps"></a>다음 단계

* [HDInsight의 Azure Storage 개요](./overview-azure-storage.md)
* [HDInsight의 Azure Data Lake Storage Gen1 개요](./overview-data-lake-storage-gen1.md)
* [HDInsight의 Azure Data Lake Storage Gen2 개요](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Storage 소개](../storage/common/storage-introduction.md)
