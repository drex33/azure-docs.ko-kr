---
title: Azure Data Lake Storage Gen2를 지원하는 Azure 서비스 | Microsoft Docs
description: Azure Data Lake Storage Gen2와 통합하는 Azure 서비스에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 284971b1b9a54d21f29cf3c3c81c194be1f5071c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022496"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2를 지원하는 Azure 서비스

Azure 서비스로 데이터를 수집하고, 분석을 수행하고, 시각적 표현을 만들 수 있습니다. 이 문서는 지원되는 Azure 서비스의 목록을 제공하고, 그 서비스의 지원 수준을 공개하고, Azure Data Lake Storage Gen2에서 이러한 서비스를 사용하는 데 도움이 되는 문서의 링크를 제공합니다.

## <a name="supported-azure-services"></a>지원되는 Azure 서비스

다음 표에는 Azure Data Lake Storage Gen2에서 사용할 수 있는 Azure 서비스가 나열되어 있습니다. 이러한 표에 표시되는 항목은 이후 지원이 계속 확장됨에 따라 변경될 수 있습니다.

> [!NOTE]
> 지원 수준은 Data Lake Storage Gen 2에서 해당 서비스가 지원되는 방법만을 참조합니다.

|Azure 서비스 |지원 수준 |Azure AD |공유 키| 관련된 문서 |
|---------------|-------------------|---|---|---|
|Azure Data Factory|일반 공급|예|예|[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|일반 공급|예|예|[Azure Databricks와 함께 사용](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) <br> [자습서: Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[자습서: Spark를 사용하여 Azure Databricks로 Data Lake Storage Gen2 데이터에 액세스](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|일반 공급|예|예|[Azure Blob Storage 또는 Azure Data Lake Storage에서 Azure Event Hubs를 통해 이벤트 캡처](../../event-hubs/event-hubs-capture-overview.md)|
|Azure Event Grid|일반 공급|예|예|[자습서: 데이터 레이크 캡처 패턴을 구현하여 Databricks Delta 테이블 업데이트](data-lake-storage-events.md)|
|Azure Logic Apps|일반 공급|예|예|[개요 - Azure Logic Apps는 무엇인가요?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|일반 공급|예|예|[Azure Storage 서비스에서 데이터 액세스](../../machine-learning/how-to-access-data.md)|
|Azure Stream Analytics|일반 공급|예|예|[빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Azure Data Lake Gen2로 송신](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|일반 공급|예|예|[Azure Data Box를 사용하여 온-프레미스 HDFS 저장소에서 Azure Storage로 데이터를 마이그레이션](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |일반 공급|예|예|[Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)<br>[Data Lake Storage Gen2에서 HDFS CLI 사용](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[자습서: Azure HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환, 로드](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |일반 공급|예|예|[IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|일반 공급|예|예|[Power BI를 사용하여 Data Lake Storage Gen2의 데이터 분석](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics(이전의 SQL Data Warehouse)|일반 공급|예|예|[스토리지 계정에서 데이터 분석](../../synapse-analytics/get-started-analyze-storage.md)|
|SSIS(SQL Server Integration Services)|일반 공급|예|예|[Azure Storage 연결 관리자](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Azure Data Explorer|일반 공급|예|예|[Azure Data Lake에서 Azure Data Explorer를 사용하여 데이터 쿼리](/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|미리 보기|예|예|[Azure Data Lake Storage Gen2 문서 인덱스 및 검색(미리 보기)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Azure Content Delivery Network|아직 지원되지 않음|해당 없음|해당 없음|[Azure Data Lake Storage Gen2 문서 인덱스 및 검색(미리 보기)](../../cdn/cdn-overview.md)|
|Azure SQL Database|아직 지원되지 않음|해당 없음|해당 없음|[Azure SQL Database란?](../../azure-sql/database/sql-database-paas-overview.md)|

> [!TIP]
> 수집, 다운로드, 처리 및 시각화와 같은 범주로 서비스를 구성 하는 방법을 보려면 [수집, 처리 및 분석](./data-lake-storage-best-practices.md#ingest-process-and-analyze)을 참조 하세요.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
- [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md)
- [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage에 대한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md)
- [Azure Data Lake Storage Gen2 사용에 대한 모범 사례](data-lake-storage-best-practices.md)