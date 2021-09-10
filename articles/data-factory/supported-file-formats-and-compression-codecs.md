---
title: Azure Data Factory의 복사 작업으로 지원되는 파일 형식
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 항목에서는 Azure Data Factory 및 Azure Synapse Analytics의 복사 작업에서 지원되는 파일 형식 및 압축 코드에 대해 설명합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: jianleishen
ms.openlocfilehash: bd59098a9a03cff5d30a776eb7489df39514bf3b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251797"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory-and-azure-synapse-pipelines"></a>Azure Data Factory 및 Azure Synapse 파이프라인의 복사 작업별로 지원되는 파일 형식 및 압축 코덱
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서는 [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) 및 [SFTP](connector-sftp.md) 커넥터에 적용됩니다.

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

[복사 작업](copy-activity-overview.md)을 사용하여 두 파일 기반 데이터 저장소 간에 있는 그대로 파일을 복사할 수 있습니다. 이 때 데이터는 직렬화 또는 역직렬화 없이 효율적으로 복사됩니다. 

또한 지정된 형식의 파일을 구문 분석하거나 생성할 수도 있습니다. 예를 들어 다음을 수행해야 합니다.

* SQL Server 데이터베이스에서 데이터를 복사하고 Parquet 형식으로 Azure Data Lake Storage Gen2에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트(CSV) 형식의 파일을 복사하여 Avro 형식으로 Azure Blob Storage에 씁니다.
* 온-프레미스 파일 시스템에서 압축된 파일을 복사하여 즉시 압축을 풀고 압축을 푼 파일을 Azure Data Lake Storage Gen2에 씁니다.
* Azure Blob Storage에서 Gzip 압축 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL Database에 씁니다.
* 직렬화/역직렬화 또는 압축/압축 해제가 필요한 작업이 더 많이 있습니다.

## <a name="next-steps"></a>다음 단계

다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
