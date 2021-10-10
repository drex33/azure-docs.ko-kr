---
title: 포함 파일
description: 포함 파일
services: data-factory
author: jianleishen
ms.service: data-factory
ms.topic: include
ms.date: 09/29/2021
ms.author: jianleishen
ms.custom: include file
ms.openlocfilehash: 4bb3c4e2c51323400ca233193c65ba85b3200316
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725409"
---
| 범주 | 데이터 저장소 | 소스로 지원 | 싱크로 지원 | [Azure IR](../concepts-integration-runtime.md#azure-integration-runtime)에서 지원 | [자체 호스팅 IR](../concepts-integration-runtime.md#self-hosted-integration-runtime)에서 지원 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob Storage](../connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Cognitive Search 인덱스](../connector-azure-search.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB(SQL API)](../connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB의 MongoDB API](../connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Explorer](../connector-azure-data-explorer.md) |✓ |✓ |✓ |✓ |
| &nbsp; |[Azure Data Lake Storage Gen1](../connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Lake Storage Gen2](../connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Database for MariaDB](../connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure Database for MySQL](../connector-azure-database-for-mysql.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Database for PostgreSQL](../connector-azure-database-for-postgresql.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Databricks Delta Lake](../connector-azure-databricks-delta-lake.md) |✓ |✓ |✓ |✓ |
| &nbsp; |[Azure 파일](../connector-azure-file-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Database](../connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Managed Instance](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Synapse Analytics](../connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Table Storage](../connector-azure-table-storage.md) |✓ |✓ |✓ |✓  |
| **Database** |[Amazon RDS for Oracle](../connector-amazon-rds-for-oracle.md) |✓ | |✓ |✓  |
| &nbsp; |[Amazon RDS for SQL Server](../connector-amazon-rds-for-sql-server.md) |✓ | |✓ |✓  |
| &nbsp; |[Amazon Redshift](../connector-amazon-redshift.md) |✓ | |✓ |✓  |
| &nbsp; |[DB2](../connector-db2.md) |✓ | |✓ |✓  |
| &nbsp; |[Drill](../connector-drill.md) |✓ | |✓ |✓  |
| &nbsp; |[Google BigQuery](../connector-google-bigquery.md) |✓ | |✓ |✓  |
| &nbsp; |[Greenplum](../connector-greenplum.md) |✓ | |✓ |✓  |
| &nbsp; |[HBase](../connector-hbase.md) |✓ | |✓ |✓  |
| &nbsp; |[Hive](../connector-hive.md) |✓ | |✓ |✓  |
| &nbsp; |[Apache Impala](../connector-impala.md) |✓ | |✓ |✓  |
| &nbsp; |[Informix](../connector-informix.md) |✓ |✓ | |✓  |
| &nbsp; |[MariaDB](../connector-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft Access](../connector-microsoft-access.md) |✓ |✓ | |✓  |
| &nbsp; |[MySQL](../connector-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Netezza](../connector-netezza.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle](../connector-oracle.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Phoenix](../connector-phoenix.md) |✓ | |✓ |✓  |
| &nbsp; |[PostgreSQL](../connector-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Presto](../connector-presto.md) |✓ | |✓ |✓  |
| &nbsp; |[Open Hub를 통한 SAP Business Warehouse](../connector-sap-business-warehouse-open-hub.md) |✓ | | |✓  |
| &nbsp; |[MDX를 통한 SAP Business Warehouse](../connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../connector-sap-hana.md) |✓ |✓ | |✓  |
| &nbsp; |[SAP 테이블](../connector-sap-table.md) |✓ | | |✓  |
| &nbsp; |[Snowflake](../connector-snowflake.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Spark](../connector-spark.md) |✓ | |✓ |✓  |
| &nbsp; |[SQL Server](../connector-sql-server.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Sybase](../connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[Teradata](../connector-teradata.md) |✓ | |✓ |✓  |
| &nbsp; |[Vertica](../connector-vertica.md) |✓ | |✓ |✓  |
| **NoSQL** |[Cassandra](../connector-cassandra.md) |✓ | |✓ |✓  |
| &nbsp; |[Couchbase(미리 보기)](../connector-couchbase.md) |✓ | |✓ |✓  |
| &nbsp; |[MongoDB](../connector-mongodb.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[MongoDB Atlas](../connector-mongodb-atlas.md) |✓ |✓ |✓ |✓  |
| **최근에 사용한 파일** |[Amazon S3](../connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Amazon S3 호환 스토리지](../connector-amazon-s3-compatible-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[파일 시스템](../connector-file-system.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[FTP](../connector-ftp.md) |✓ | |✓ |✓  |
| &nbsp; |[Google Cloud Storage](../connector-google-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[HDFS](../connector-hdfs.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Cloud Storage](../connector-oracle-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[SFTP](../connector-sftp.md) |✓ |✓ |✓ |✓  |
| **일반 프로토콜** |[일반 HTTP](../connector-http.md) |✓ | |✓ |✓  |
| &nbsp; |[일반 OData](../connector-odata.md) |✓ | |✓ |✓  |
| &nbsp; |[일반 ODBC](../connector-odbc.md) |✓ |✓ | |✓  |
| &nbsp; |[일반 REST](../connector-rest.md) |✓ | ✓ |✓ |✓  |
| **서비스 및 앱** |[Amazon Marketplace 웹 서비스](../connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Concur(미리 보기)](../connector-concur.md) |✓ | |✓ |✓  |
| &nbsp; |[Dataverse](../connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics 365](../connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure IoT Hub](../connector-dynamics-ax.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics CRM](../connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Google AdWords](../connector-google-adwords.md) |✓ | |✓ |✓  |
| &nbsp; |[HubSpot](../connector-hubspot.md) |✓ | |✓ |✓  |
| &nbsp; |[Jira](../connector-jira.md) |✓ | |✓ |✓  |
| &nbsp; |[Magento(미리 보기)](../connector-magento.md) |✓ | |✓ |✓  |
| &nbsp; |[Marketo(미리 보기)](../connector-marketo.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft 365](../connector-office-365.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Eloqua(미리 보기)](../connector-oracle-eloqua.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Responsys(미리 보기)](../connector-oracle-responsys.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Service Cloud(미리 보기)](../connector-oracle-service-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[PayPal(미리 보기)](../connector-paypal.md) |✓ | |✓ |✓  |
| &nbsp; |[QuickBooks(미리 보기)](../connector-quickbooks.md) |✓ | |✓ |✓  |
| &nbsp; |[Salesforce](../connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce 서비스 클라우드](../connector-salesforce-service-cloud.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce 마케팅 클라우드](../connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Cloud for Customer(C4C)](../connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP ECC](../connector-sap-ecc.md) |✓ | |✓ |✓ |
| &nbsp; |[ServiceNow](../connector-servicenow.md) |✓ | |✓ |✓  |
|  |[SharePoint Online 목록](../connector-sharepoint-online-list.md) |✓ | |✓ |✓ |
| &nbsp; |[Shopify(미리 보기)](../connector-shopify.md) |✓ | |✓ |✓  |
| &nbsp; |[Square(미리 보기)](../connector-square.md) |✓ | |✓ |✓  |
| &nbsp; |[웹 테이블(HTML 테이블)](../connector-web-table.md) |✓ | | |✓  |
| &nbsp; |[Xero](../connector-xero.md) |✓ | |✓ |✓  |
| &nbsp; |[Zoho(미리 보기)](../connector-zoho.md) |✓ | |✓ |✓  |

> [!NOTE]
> 커넥터가 *미리 보기* 로 표시되어 있으면 이를 사용해보고 피드백을 제공할 수 있습니다. 솔루션의 미리 보기 커넥터에 종속성을 적용하려면 [Azure 지원](https://azure.microsoft.com/support/)에 문의하세요.
