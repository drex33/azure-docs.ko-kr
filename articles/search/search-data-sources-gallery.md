---
title: 데이터 원본 갤러리
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱스로 가져오기 위해 지원되는 모든 데이터 원본이 나열됩니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
layout: LandingPage
ms.date: 06/23/2021
ms.openlocfilehash: e48c464ffa1aa5077f04ea1a7580e0d15e0fd7ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535933"
---
# <a name="data-sources-gallery"></a>데이터 원본 갤러리

Microsoft 또는 파트너의 데이터 커넥터를 찾아서 검색 인덱스로의 데이터 수집을 간소화합니다. 이 문서에는 다음 섹션이 포함되어 있습니다.

+ [Cognitive Search에서 일반적으로 사용 가능한 데이터 원본](#ga)
+ [Cognitive Search의 데이터 원본 미리 보기](#preview)
+ [파워 쿼리 커넥터(미리 보기)](#powerquery)
+ [파트너의 데이터 원본](#partners)

<a name="ga"></a>

## <a name="generally-available-data-sources-by-cognitive-search"></a>Cognitive Search에서 일반적으로 사용 가능한 데이터 원본

인덱서 및 다음 데이터 원본 커넥터를 사용하여 다른 Azure 서비스에서 콘텐츠를 끌어옵니다. 

:::row:::
:::column span="":::

---

### <a name="azure-blob-storage"></a>Azure Blob Storage

제공: [Cognitive Search](search-what-is-azure-search.md)

Blob 메타데이터 및 콘텐츠를 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 데이터 원본 및 인덱서 정의 모두에서 속성을 설정하여 다양한 Blob 콘텐츠 형식에 맞게 최적화합니다. 변경 내용 검색은 자동으로 지원됩니다.

[자세한 정보](search-howto-indexing-azure-blob-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB(SQL API)

제공: [Cognitive Search](search-what-is-azure-search.md)

SQL API를 통해 Cosmos DB에 연결하여 컨테이너에서 항목을 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 데이터베이스의 최신 변경 내용으로 검색 인덱스를 새로 고치도록 변경 내용 추적을 구성합니다.

[자세한 정보](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

제공: [Cognitive Search](search-what-is-azure-search.md)

단일 테이블 또는 보기에서 필드 값을 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 데이터베이스의 최신 변경 내용으로 검색 인덱스를 새로 고치도록 변경 내용 추적을 구성합니다.

[자세한 정보](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

:::image type="icon" source="media/search-data-sources-gallery/azuresqlconnectorlogo_medium.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-table-storage"></a>Azure Table Storage

제공: [Cognitive Search](search-what-is-azure-search.md)

Azure 테이블에서 행을 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 

[자세한 정보](search-howto-indexing-azure-tables.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

제공: [Cognitive Search](search-what-is-azure-search.md)

Azure Data Lake Storage Gen2를 통해 Azure Storage에 연결하여 디렉터리 및 중첩된 하위 디렉터리의 계층 구조에서 콘텐츠를 추출합니다.

[자세한 정보](search-howto-index-azure-data-lake-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="preview"></a>

## <a name="preview-data-sources-by-cognitive-search"></a>Cognitive Search의 데이터 원본 미리 보기

새 데이터 원본은 미리 보기 기능으로 발급됩니다. 시작하려면 [가입](https://aka.ms/azure-cognitive-search/indexer-preview)하세요.

:::row:::
:::column span="":::

---

### <a name="cosmos-db-gremlin-api"></a>Cosmos DB(Gremlin API)

제공: [Cognitive Search](search-what-is-azure-search.md)

Gremlin API를 통해 Cosmos DB에 연결하여 컨테이너에서 항목을 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 데이터베이스의 최신 변경 내용으로 검색 인덱스를 새로 고치도록 변경 내용 추적을 구성합니다.

[자세한 정보](search-howto-index-cosmosdb-gremlin.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="cosmos-db-mongo-api"></a>Cosmos DB(Mongo API)

제공: [Cognitive Search](search-what-is-azure-search.md)

Mongo API를 통해 Cosmos DB에 연결하여 컨테이너에서 항목을 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 데이터베이스의 최신 변경 내용으로 검색 인덱스를 새로 고치도록 변경 내용 추적을 구성합니다.

[자세한 정보](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

제공: [Cognitive Search](search-what-is-azure-search.md)

동일한 테넌트의 계정 및 검색 서비스에 대해 SharePoint Online 사이트에 연결하고 하나 이상의 문서 라이브러리에서 문서를 인덱싱합니다. 텍스트와 정규화된 이미지가 기본적으로 추출됩니다. 선택적으로 더 많은 콘텐츠 변환 및 보강을 위해 기술 세트를 구성하거나 SharePoint의 새 콘텐츠 또는 변경된 콘텐츠로 검색 인덱스를 새로 고치도록 변경 내용 추적을 구성할 수 있습니다.

[자세한 정보](search-howto-index-sharepoint-online.md)

:::image type="icon" source="media/search-data-sources-gallery/sharepoint_online_logo.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-mysql"></a>Azure MySQL

제공: [Cognitive Search](search-what-is-azure-search.md)

MySQL Database on Azure에 연결하여 테이블의 행을 추출하고 JSON 문서로 직렬화하고 검색 인덱스에 검색 문서로 가져옵니다. 후속 실행에서 인덱서는 MySQL 데이터베이스에 대한 모든 변경 사항, 업로드 및 삭제를 수행하고 이러한 변경 사항을 검색 인덱스에 반영합니다.

[자세한 정보](search-howto-index-mysql.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_mysql.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="powerquery"></a>

## <a name="power-query-connectors-preview"></a>파워 쿼리 커넥터(미리 보기)

인덱서와 파워 쿼리 커넥터를 데이터 원본으로 사용하여 다른 클라우드 플랫폼의 데이터에 연결합니다. 시작하려면 [가입](https://aka.ms/azure-cognitive-search/indexer-preview)하세요.

:::row:::
:::column span="":::

---

### <a name="amazon-redshift"></a>Amazon Redshift

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

[Amazon Redshift](https://aws.amazon.com/redshift/)에 연결하고 Cognitive Search에서 인덱싱을 위해 검색 가능한 콘텐츠를 추출합니다.

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

클라우드의 [Elasticsearch](https://www.elastic.co/elasticsearch)에 연결하고 Cognitive Search에서 인덱싱을 위해 검색 가능한 콘텐츠를 추출합니다.

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

클라우드의 [PostgreSQL](https://www.postgresql.org/) 데이터베이스에 연결하고 Cognitive Search에서 인덱싱을 위해 검색 가능한 콘텐츠를 추출합니다.

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce-objects"></a>Salesforce 개체

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

Salesforce 개체에 연결하고 Cognitive Search에서 인덱싱을 위해 검색 가능한 콘텐츠를 추출합니다.

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="salesforce-reports"></a>Salesforce 보고서

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

Salesforce 보고서에 연결하고 Cognitive Search에서 인덱싱을 위해 검색 가능한 콘텐츠를 추출합니다.

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="smartsheet"></a>Smartsheet

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

Smartsheet에 연결하고 Cognitive Search에서 인덱싱을 위해 검색 가능한 콘텐츠를 추출합니다.

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="snowflake"></a>Snowflake

제공: [파워 쿼리](/power-query/power-query-what-is-power-query)

Snowflake 데이터베이스에서 검색 가능한 데이터와 메타데이터를 추출하고 인덱스와 데이터 원본 간의 필드 간 매핑을 기반으로 인덱스를 채웁니다. 

[자세한 정보](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

<a name="partners"></a>

## <a name="data-sources-from-our-partners"></a>파트너의 데이터 원본

데이터 원본 커넥터는 타사 Microsoft 파트너도 제공합니다. 데이터 원본을 사용하기 전에 [사용 약관](search-data-sources-terms-of-use.md)을 참조하고 파트너 라이선스 및 사용 지침을 확인하세요.

:::row:::
:::column span="":::

---

### <a name="aderant"></a>Aderant

제공: [BA Insight](https://www.bainsight.com/)

Aderant 커넥터는 원본 시스템의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/aderant-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

제공: [Accenture](https://www.accenture.com)

Adobe Experience Manager 서버의 콘텐츠를 크롤링하여 연결 제한 및 예상 값 또는 패턴 필터링을 제공할 수 있습니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/AEM+Connector)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

제공: [BA Insight](https://www.bainsight.com/)

Adobe Experience Manager 커넥터를 사용하면 AEM(Adobe Experience Manager) 플랫폼에서 관리되는 콘텐츠를 인덱싱할 수 있고 전체 크롤링과 증분 크롤링을 모두 지원하여 인덱스 새로 고침을 보장합니다.

[자세한 정보](https://www.bainsight.com/connectors/adobe-em-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

제공: [Raytion](https://www.raytion.com/contact)

Adobe AEM(Active Experience Manager)의 콘텐츠를 안정적으로 인덱싱하고 Azure Cognitive Search를 사용하여 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Adobe AEM의 페이지, 첨부 파일 및 기타 생성된 문서 유형을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 또는 기타 디렉터리 서비스를 기반으로 하는 AEM 설치, Adobe AEM의 사용 권한 모델, 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/adobe-experience-manager-aem)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

제공: [BA Insight](https://www.bainsight.com/)

Alfresco 커넥터는 모든 커넥터를 구축하는 데 사용되는 플랫폼인 BAI 커넥터 프레임워크를 기반으로 구축되었으며 엔터프라이즈 시스템에 대한 보안 연결을 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/alfresco-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

제공: [Raytion](https://www.raytion.com/contact)

Alfresco One의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Alfresco One의 파일, 폴더 및 사용자 프로필을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 Alfresco One 설치, Alfresco One의 사용 권한 모델, 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-alfresco-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="amazon-aurora"></a>Amazon Aurora

제공: [BA Insight](https://www.bainsight.com/)

Amazon Aurora 커넥터는 업계 표준 데이터베이스 액세스 방법을 기반으로 하기 때문에 Oracle, MySQL, IBM DB2와 같은 다른 시스템의 데이터베이스를 동일하게 지원합니다.

[자세한 정보](https://www.bainsight.com/connectors/amazon-aurora-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-rds"></a>Amazon RDS

제공: [BA Insight](https://www.bainsight.com/)

Amazon RDS 커넥터는 업계 표준 데이터베이스 액세스 방법을 기반으로 하기 때문에 Oracle, MySQL, IBM DB2와 같은 다른 시스템의 데이터베이스를 동일하게 지원할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/amazon-rds-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-s3"></a>Amazon S3

제공: [BA Insight](https://www.bainsight.com/)

Amazon S3 커넥터는 S3에 저장된 모든 콘텐츠에서 작동합니다. 조직에서 커넥터를 사용하여 S3에 안전하게 연결하고 S3 버킷의 콘텐츠를 인덱싱할 수 있습니다. 강력한 필터링 기능을 통해 S3의 콘텐츠 중 어떤 콘텐츠를 인덱싱해야 하는지 제어할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/amazon-s3-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="aspider"></a>Aspider

제공: [Accenture](https://www.accenture.com)

Aspider 커넥터를 사용하면 HTTP 인증, 증분 크롤링, 연결 제한, 분산 및 HTTPS 크롤링 등을 사용하여 웹 사이트에서 콘텐츠를 크롤링할 수 있습니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Aspider+Web+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="atlassian-confluence-cloud"></a>Atlassian Confluence(Cloud)

제공: [BA Insight](https://www.bainsight.com/)

Confluence(클라우드 버전) 커넥터는 Confluence에 저장된 콘텐츠를 크롤링하고 인덱싱할 수 있는 엔터프라이즈급 인덱싱 커넥터입니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-confluence-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

제공: [BA Insight](https://www.bainsight.com)

BA Insight Azure Active Directory 커넥터를 사용하면 Azure Active Directory 테넌시의 콘텐츠를 다른 리포지토리의 콘텐츠와 함께 단일 통합 검색 인덱스에 노출할 수 있으므로 직원 조회 또는 전문 지식 로케이터와 같은 검색을 구현할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/azure-active-directory-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

제공: [Raytion](https://www.raytion.com/contact)

Microsoft Azure AD(Azure Active Directory)의 콘텐츠를 안정적으로 인덱싱하고 Azure Cognitive Search를 사용하여 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Microsoft Graph API를 통해 Azure AD의 개체를 인덱싱합니다. 이 커넥터를 사용하면 보안 주체를 Cognitive Search에 거의 실시간으로 수집하여 전문가 검색, 장비 검색 및 위치 검색과 같은 사용 사례를 구현하거나 사용자 지정 데이터 원본과 함께 조기 바인딩 보안 트리밍을 제공할 수 있습니다. 이 커넥터는 Microsoft 365에 대한 페더레이션 인증을 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-azure-ad-connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-blobs"></a>Azure Blob

제공: [Accenture](https://www.accenture.com)

Azure Blob 컨테이너의 콘텐츠를 크롤링하는 기능을 제공하여 증분 크롤링, 문서 수준 보안, 폴더 및 하위 폴더에 대한 액세스가 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Blob+Storage+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake"></a>Azure Data Lake

제공: [Accenture](https://www.accenture.com)

Azure Data Lake 커넥터는 증분 크롤링, 개체 ACL 가져오기, OAuth 2 인증 등을 사용하여 루트 또는 지정된 경로에서 Azure Data Lake Store 클라우드의 콘텐츠를 크롤링합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Data+Lake+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-events-hub"></a>Azure Events Hub

제공: [Accenture](https://www.accenture.com)

Azure Events Hub의 콘텐츠를 크롤링하여 모든 유형의 이벤트 또는 특성에 대한 증분 크롤링 및 검색이 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Events+Hub+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 Azure SQL Database 커넥터는 원본 데이터베이스의 보안을 준수하며 전체 크롤링 및 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/azure-sql-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="bentley"></a>Bentley

제공: [BA Insight](https://www.bainsight.com/)

BAI Bentley AssetWise 커넥터를 사용하면 AssetWise의 콘텐츠를 다른 리포지토리의 콘텐츠와 함께 단일 통합 검색 인덱스로 표시할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/bentley-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="box"></a>Box

제공: [Accenture](https://www.accenture.com)

Box 커넥터는 Box 리포지토리의 콘텐츠를 크롤링합니다. 이 커넥터는 RESTful API를 사용하여 지원되는 요소를 검색하고 전체 크롤링 또는 증분 크롤링, 메타데이터 추출, ACL 가져오기 등을 제공합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Box++Connector)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Box

제공: [BA Insight](https://www.bainsight.com/)

Box 커넥터를 사용하면 SharePoint 및 기타 포털에서 Box의 콘텐츠를 표시할 수 있으므로 사용자가 SharePoint 및 Box에서 통합된 검색 결과를 얻을 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/box-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Box

제공: [Raytion](https://www.raytion.com/contact)

Box의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Box의 파일, 폴더, 주석, 사용자, 그룹 및 작업을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Box의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-box-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

제공: [Accenture](https://www.accenture.com)

Confluence 커넥터는 Confluence 콘텐츠 리포지토리의 콘텐츠를 크롤링합니다. 이 커넥터는 REST API를 통해 공간, 페이지, 블로그, 첨부 파일 및 주석을 검색하여 증분 크롤링, ACL 가져오기, HTTP 및 HTTPS 지원 등이 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Atlassian+Confluence+Connector)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

제공: [BA Insight](https://www.bainsight.com/)

Confluence 커넥터는 Confluence에 저장된 콘텐츠를 크롤링하고 인덱싱할 수 있는 엔터프라이즈급 인덱싱 커넥터입니다. 이를 통해 SharePoint 또는 기타 포털은 여러 콘텐츠 원본에서 사용자가 필요한 콘텐츠를 검색할 수 있는 단일 지점으로 작동할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/confluence-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

제공: [Raytion](https://www.raytion.com/contact)

Atlassian Confluence의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 온-프레미스 Confluence 인스턴스의 태그에 대한 페이지, 블로그 게시물, 첨부 파일, 주석, 공백, 프로필 및 허브 사이트를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 Confluence 설치 및 Atlassian Confluence의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-confluence-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence-cloud"></a>Confluence Cloud

제공: [Raytion](https://www.raytion.com/contact)

Atlassian Confluence Cloud의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Confluence Cloud 인스턴스의 태그에 대한 페이지, 블로그 게시물, 첨부 파일, 주석, 공백, 프로필 및 허브 사이트를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Atlassian Confluence Cloud의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-confluence-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="cuadrastar"></a>CuadraSTAR

제공: [BA Insight](https://www.bainsight.com/)

CuadraSTAR 커넥터는 CuadraSTAR의 콘텐츠를 크롤링하고 Azure Cognitive Search를 사용하여 CuadraSTAR 및 70개가 넘는 다른 지원되는 리포지토리 내에서 관련 정보를 찾을 수 있어서 별도의 검색을 수행할 필요가 없는 단일 인덱스를 만듭니다.

[자세한 정보](https://www.bainsight.com/connectors/cuadrastar-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="database"></a>데이터베이스

제공: [Accenture](https://www.accenture.com)

데이터베이스 서버 커넥터는 관계형 데이터베이스 서버의 콘텐츠를 크롤링하고 서버의 모든 데이터베이스를 검색하고 행 및 테이블 정보를 추출합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Database+Server+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="deltek"></a>Deltek

제공: [BA Insight](https://www.bainsight.com/)

Deltek Vision 커넥터는 원본 시스템의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다. Deltek Vision의 콘텐츠를 Azure, SharePoint Online 또는 SharePoint 2016/2013으로 인덱싱하고 BA Insight의 SmartHub를 통해 노출하여 사용자에게 통합 검색 결과를 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/deltek-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

제공: [Accenture](https://www.accenture.com)

Aspire Documentum DQL 커넥터는 Documentum의 콘텐츠를 크롤링하여 사용자 정의 DQL SELECT 문에 기반한 크롤링, 증분 크롤링, ACL 가져오기, 중첩된 권한의 그룹 확장 등이 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Documentum+DQL+Connector)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 Documentum 커넥터는 Documentum 개체의 전체 텍스트와 메타데이터 모두를 Azure Cognitive Search로 안전하게 인덱싱하여 여러 리포지토리의 콘텐츠에서 검색 가능한 단일 결과 집합을 사용할 수 있도록 합니다. 이것은 프로세스 관리를 위해 Azure Cognitive Search를 사용하여 Documentum 레코드를 한 번에 하나씩 노출하는 다른 커넥터와 다릅니다.

[자세한 정보](https://www.bainsight.com/connectors/documentum-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

제공: [Raytion](https://www.raytion.com/contact)

OpenText Documentum의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 리포지토리, 폴더 및 파일을 Documentum의 메타데이터 및 속성과 함께 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 OpenText Documentum의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-documentum-connector)

:::column-end:::
:::column span="":::

---

### <a name="egnyte"></a>egnyte

제공: [BA Insight](https://www.bainsight.com/)

Egnyte 커넥터는 전체 및 증분 크롤링과 인덱스 모두를 매우 높은 처리량으로 지원합니다.

[자세한 정보](https://www.bainsight.com/connectors/egnyte-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

제공: [Accenture](https://www.accenture.com)

Elasticsearch 커넥터는 Elasticsearch 인덱스의 콘텐츠를 크롤링하여 여러 인덱스 크롤링, Slice 지원, 콘텐츠 가져오기를 위한 Get of MGet 메서드 사용, 연결 제한이 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Elasticsearch+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="elite--e3"></a>Elite/E3

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 Elite 커넥터는 변호사가 Azure Cognitive Search를 사용하여 Elite 콘텐츠에 의거한 회사 콘텐츠 및 지식에 액세스할 수 있는 단일 액세스 지점을 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/elite-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="emc-eroom"></a>EMC eRoom

제공: [BA Insight](https://www.bainsight.com/)

eRoom 커넥터는 eRoom 애플리케이션에 대한 보안 연결을 설정하고, 메타데이터 및 첨부 파일을 포함한 콘텐츠를 eRoom 스키마에서 검색 엔진 스키마로 매핑합니다. 그런 다음, 콘텐츠를 추출하고 크롤링이라는 프로세스를 통해 검색 엔진에 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/eroom-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

제공: [BA Insight](https://www.bainsight.com/)

Workplace by Facebook을 사용하는 조직은 BA Insight Workplace by Facebook 커넥터를 통해 이 데이터의 범위를 기존 검색 인덱스로 확장할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-workplace-by-facebook/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

제공: [Raytion](https://www.raytion.com/contact)

Facebook Workplace의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Facebook Workplace의 프로젝트 그룹, 대화 및 공유 문서를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Facebook Workplace의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-facebook-workplace-connector)

:::column-end:::
:::column span="":::
---

### <a name="file-share"></a>File Share

제공: [BA Insight](https://www.bainsight.com/)

File Share 커넥터를 사용하면 File Shares(Windows, SMB/CIFS)의 콘텐츠를 다른 리포지토리의 콘텐츠와 함께 통합된 단일 검색 인덱스에 노출할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/file-share-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="file-system"></a>File System

제공: [Accenture](https://www.accenture.com)

File System 커넥터는 File System 위치의 콘텐츠를 크롤링하여 증분 크롤링, 메타데이터 추출, 경로별 문서 필터링, Windows/Linux/MacOS 파일 시스템 지원이 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/File+System+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::
---

### <a name="file-system"></a>File System

제공: [Raytion](https://www.raytion.com/contact)

로컬로 탑재된 File System의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. File System의 파일과 폴더를 거의 실시간으로 강력하게 인덱싱합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-file-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="firstspirit"></a>FirstSpirit

제공: [Raytion](https://www.raytion.com/contact)

e-Spirit FirstSpirit의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. FirstSpirit의 페이지, 첨부 파일 및 기타 생성된 문서 유형을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 FirstSpirit 설치, e-Spirit FirstSpirit의 기본 제공 사용자, 그룹 및 사용 권한 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-firstspirit-connector)

:::column-end:::
:::column span="":::

---

### <a name="gitlab"></a>GitLab

제공: [Raytion](https://www.raytion.com/contact)

GitLab의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. GitLab의 프로젝트, 파일, 폴더, 커밋 메시지, 문제 및 Wiki 페이지를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 GitLab의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-gitlab-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="google-cloud-sql"></a>Google Cloud SQL

제공: [BA Insight](https://www.bainsight.com/)

Google Cloud SQL 커넥터는 Google Cloud SQL의 콘텐츠를 Azure Cognitive Search 인덱스로 인덱싱하고 BA Insight의 SmartHub를 통해 노출하여 사용자에게 통합 검색 결과를 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/google-cloud-sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

제공: [BA Insight](https://www.bainsight.com/)

BAI Google Drive 커넥터를 사용하면 Google 드라이브의 콘텐츠를 다른 리포지토리의 콘텐츠와 함께 Google Drive 콘텐츠를 참조하는 단일 통합 검색 색인에 노출할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/google-drive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

제공: [Raytion](https://www.raytion.com/contact)

Google Drive의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Google 드라이브의 개인 드라이브 및 팀 드라이브에 있는 파일, 폴더 및 주석을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Google Drive의 기본 제공 권한 모델과 Google Admin Directory에 의한 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-google-drive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="hp-consolidated-archive-eas"></a>HP Consolidated Archive(EAS)

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 HP Consolidated Archive 커넥터는 아카이브에 있는 문서의 전체 텍스트와 메타데이터를 SharePoint Search 및 Azure Search를 포함한 다양한 검색 엔진에 안전하게 인덱싱합니다. 그래서 여러 리포지토리의 콘텐츠에서 검색 가능한 단일 결과 집합을 사용할 수 있습니다. 이를 통해 조직은 Consolidated Archive, SharePoint 및 기타 리포지토리 내에서 액세스할 수 있는 풍부한 정보를 활용할 수 있고 해당 데이터를 사용자가 검색을 통해 즉시 실행할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/hp-consolidated-archive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

제공: [Accenture](https://www.accenture.com)

IBM Connections 커넥터는 증분 크롤링, 메타데이터 추출, ACL 가져오기, regex 패턴으로 문서 필터링 등을 특징으로 하는 IBM Connections 서버의 콘텐츠를 크롤링합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/IBM+Connections+Connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

제공: [BA Insight](https://www.bainsight.com/)

IBM Connections 커넥터는 IBM Connections용으로 개발되었으며 Connections 애플리케이션에 대한 보안 연결을 설정하고 메타데이터 및 첨부 파일을 포함한 콘텐츠를 Connections 스키마에서 검색 엔진 스키마로 매핑합니다. 그런 다음, 콘텐츠를 추출하고 크롤링이라는 프로세스를 통해 검색 엔진에 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/ibm-connections-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

제공: [Raytion](https://www.raytion.com/contact)

IBM Connections의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 온-프레미스 Connections 인스턴스의 공개 파일 및 개인 파일, 블로그, Wiki, 포럼, 커뮤니티, 책갈피, 프로필, 상태 업데이트를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 Connections 설치 및 IBM Connection의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-ibm-connections-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections-cloud"></a>IBM Connections Cloud

제공: [Raytion](https://www.raytion.com/contact)

IBM Connections Cloud의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Connections Cloud의 공개 파일 및 개인 파일, 블로그, Wiki, 포럼, 커뮤니티, 프로필, 상태 업데이트를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 IBM Connections Cloud의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-ibm-connections-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-content-manager"></a>IBM Content Manager

제공: [BA Insight](https://www.bainsight.com/)

IBM Content Manager 커넥터는 원본 애플리케이션의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/ibm-content-manager-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-db2"></a>IBM Db2

제공: [BA Insight](https://www.bainsight.com/)

Db2 커넥터를 사용하면 조직은 DB2 데이터베이스 및 애플리케이션에 저장된 풍부한 데이터를 활용하고 검색을 통해 사용자가 해당 데이터를 즉시 실행할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/ibm-db2-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-filenet-p8"></a>IBM FileNet P8

제공: [BA Insight](https://www.bainsight.com/)

IBM FileNet Content Manager 커넥터를 사용하면 SharePoint 및 기타 포털 사용자가 FileNet 리포지토리에 저장된 콘텐츠를 안전하게 검색할 수 있습니다. 콘텐츠에 대한 액세스 권한이 FileNet에 설정된 보안에 따라 결정되기 때문에 다른 포털을 통해 액세스할 때 콘텐츠가 FileNet 내에서 직접 액세스되는 것처럼 안전합니다.

[자세한 정보](https://www.bainsight.com/connectors/ibm-filenet-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-lotus-notes"></a>IBM Lotus Notes

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 IBM Notes 이메일 커넥터를 사용하면 사용자가 SharePoint 또는 다른 포털 내에서 직접 Lotus Notes 이메일을 검색할 수 있습니다. IBM Notes에 정의된 보안이 검색 환경에 자동으로 반영되기 때문에 사용자는 자신의 사서함, 공용 사서함 및 액세스 권한이 부여된 기타 사서함의 검색 결과를 볼 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-websphere"></a>IBM WebSphere

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 WebSphere 커넥터는 WebSphere 개체의 전체 텍스트와 메타데이터 모두를 Microsoft의 검색 엔진으로 안전하게 인덱싱하여 여러 리포지토리의 콘텐츠에서 검색 가능한 단일 결과 집합을 사용할 수 있도록 합니다. 이를 통해 조직은 Microsoft 플랫폼 내에서 액세스할 수 있는 풍부한 정보를 활용할 수 있고 해당 데이터를 사용자가 검색을 통해 즉시 실행할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/ibm-websphere-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-cloud"></a>iManage Cloud

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 iManage Cloud 커넥터는 Work 작업 영역에 있는 문서의 전체 텍스트와 메타데이터를 모두 검색 엔진에 안전하게 인덱싱합니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-imanage-work-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-work"></a>iManage Work

제공: [BA Insight](https://www.bainsight.com/)

iManage Work 커넥터는 완전한 보안을 제공하고 높은 처리량으로 작동하여 크롤링 시간을 최소화하면서 Work에 대한 낮은 성능 영향을 유지합니다. 읽기 권한만 필요하며 iManage 서버에 클라이언트 소프트웨어를 설치할 필요가 없습니다. 따라서 iManage Work에 저장된 모든 콘텐츠에 동시에 원활하게 액세스할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/imanage-work-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira"></a>Jira

제공: [BA Insight](https://www.bainsight.com/)

Jira 커넥터를 사용하면 사용자가 모든 Jira 개체에 대해 검색을 수행할 수 있으므로 Jira로 직접 이동할 필요가 없습니다.

[자세한 정보](https://www.bainsight.com/connectors/jira-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jira"></a>Jira

제공: [Raytion](https://www.raytion.com/contact)

Atlassian Jira의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 온-프레미스 Jira 인스턴스의 프로젝트, 문제, 첨부 파일, 주석, 작업 로그, 문제 기록, 링크, 프로필을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 Jira 설치 및 Atlassian Jira의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-jira-connector)

:::column-end:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

제공: [BA Insight](https://www.bainsight.com/)

Jira(Cloud 버전) 커넥터는 모든 Jira 개체에 대해 검색을 수행하므로 Jira로 직접 이동할 필요가 없습니다.

[자세한 정보](https://www.bainsight.com/connectors/jira-cloud-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

제공: [Raytion](https://www.raytion.com/contact)

Atlassian Jira Cloud의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Jira Cloud의 프로젝트, 문제, 첨부 파일, 주석, 작업 로그, 문제 기록, 링크, 프로필을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Atlassian Jira Cloud의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-jira-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

제공: [BA Insight](https://www.bainsight.com/)

Jive 커넥터는 Jive용으로 개발되었으며 Jive 애플리케이션에 대한 보안 연결을 설정하고 Jive 스키마의 메타데이터 및 첨부 파일을 포함한 콘텐츠를 검색 엔진 스키마에 매핑합니다. 그런 다음, 콘텐츠를 추출하고 크롤링이라는 프로세스를 통해 검색 엔진에 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/jive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

제공: [Raytion](https://www.raytion.com/contact)

Jive의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 온-프레미스 및 클라우드 호스팅 Jive 인스턴스의 토론, 설문조사, 파일, 블로그, 공간, 그룹, 프로젝트, 작업, 비디오, 메시지, 아이디어, 프로필, 상태 업데이트를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Jive의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원하고 Jive의 네이티브 인증 모델, OAuth 및 기본 인증을 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-jive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="kaltura"></a>Kaltura

제공: [BA Insight](https://www.bainsight.com/)

Kaltura 커넥터를 사용하면 비디오뿐만 아니라 범주, 데이터, 문서 등을 비롯한 다양한 유형의 정보를 인덱싱할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/kaltura-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

제공: [BA Insight](https://www.bainsight.com/) 

LDAP 커넥터를 사용하면 조직에서 모든 LDAP 규격 디렉터리에 연결하고 이 디렉터리의 모든 레코드를 인덱싱할 수 있습니다. 디렉터리의 특정 하위 집합으로 필터링하고 특정 필드만 검색할 수 있기 때문에 디렉터리에 저장된 사용자, 연락처 또는 그룹을 간단하게 검색할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/ldap-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

제공: [Raytion](https://www.raytion.com/contact)

LDAP(Lightweight Directory Access Protocol)와 호환되는 디렉터리 서비스의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Active Directory, Novell E-Directory 및 기타 LDAP 호환 디렉터리 서비스의 LDAP 개체를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 전문가, 장비 및 위치 검색과 같은 사용 사례를 위해 Google Cloud Search에 보안 주체를 수집하거나 사용자 지정 데이터 원본에 대한 보안 조정을 구현하는 데 사용할 수 있습니다. 이 커넥터는 LDAPS(LDAP over SSL)를 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-ldap-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="legalkey"></a>LegalKEY

제공: [BA Insight](https://www.bainsight.com/) 

BA Insight의 OpenText LegalKEY 커넥터는 LegalKEY에 있는 클라이언트 및 문제 레코드의 전체 텍스트와 메타데이터를 모두 Microsoft 검색 엔진에 안전하게 인덱싱하여 여러 리포지토리의 콘텐츠에서 검색 가능한 단일 결과 집합을 사용할 수 있습니다. 이를 통해 조직은 LegalKEY, SharePoint 및 기타 리포지토리 내에서 액세스할 수 있는 풍부한 정보를 활용할 수 있고 해당 데이터를 사용자가 검색을 통해 즉시 실행할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/legalkey-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lexisnexis-interaction"></a>LexisNexis InterAction

제공: [BA Insight](https://www.bainsight.com/)

LexisNexis InterAction 커넥터를 사용하면 조직 전체의 변호사 및 기타 회사 직원이 InterAction에 저장된 중요한 정보를 보다 쉽게 찾을 수 있으며 직접 로그인하여 별도의 검색을 수행할 필요가 없습니다.

[자세한 정보](https://www.bainsight.com/connectors/lexisnexis-interaction-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lotus-notes-databases"></a>Lotus Notes Databases

제공: [BA Insight](https://www.bainsight.com/) 

IBM Notes Databases 커넥터를 통해 사용자는 Azure Cognitive Search를 사용하여 Notes 데이터베이스에 저장된 콘텐츠를 찾을 수 있습니다. IBM Notes 내에 정의된 보안이 검색 환경에 자동으로 반영되므로 사용자는 권한이 부여된 콘텐츠만 볼 수 있습니다. 궁극적으로 사용자는 한 곳에서 필요한 모든 것을 찾을 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/ibm-lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="m-files"></a>M-Files

제공: [BA Insight](https://www.bainsight.com/)

M-Files 커넥터를 사용하면 M-Files 플랫폼에서 관리되는 콘텐츠를 인덱싱할 수 있고 전체 크롤링과 증분 크롤링을 모두 지원하여 인덱스 새로 고침을 보장합니다.

[자세한 정보](https://www.bainsight.com/connectors/m-files-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediaplatform-primetime"></a>MediaPlatform PrimeTime

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 MediaPlatform PrimeTime 인덱싱 커넥터를 사용하면 이 커넥터를 BA Insight의 SmartHub와 결합하여 조직의 엔터프라이즈 검색 플랫폼을 통해 사용자가 콘텐츠에 액세스할 수 있습니다. BA Insight MediaPlatform PrimeTime 커넥터는 MediaPlatform PrimeTime에서 채널 및 비디오에 대한 정보를 검색하고 Azure Cognitive Search를 통해 인덱싱합니다.

[자세한 정보](https://www.bainsight.com/connectors/mediaplatform-primetime-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediawiki"></a>MediaWiki

제공: [Raytion](https://www.raytion.com/contact)

MediaWiki의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. MediaWiki 인스턴스의 페이지, 토론 페이지, 첨부 파일을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 MediaWiki 설치 및 MediaWiki의 기본 제공 사용 권한 모델을 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-mediawiki-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="micro-focus-content-manager-hpe-records-managerhp-trim"></a>Micro Focus Content Manager(HPE Records Manager/HP TRIM)

제공: [BA Insight](https://www.bainsight.com/)

HP TRIM 커넥터는 HP Records Manager용으로 개발되었으며 TRIM 애플리케이션에 대한 보안 연결을 설정하고 TRIM 스키마의 메타데이터 및 첨부 파일을 포함한 콘텐츠를 검색 엔진 스키마에 매핑합니다. 그런 다음, 콘텐츠를 추출하고 크롤링이라는 프로세스를 통해 검색 엔진에 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/hp-trim-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365"></a>Microsoft Dynamics 365

제공: [BA Insight](https://www.bainsight.com/)

Microsoft Dynamics 365 CRM 커넥터는 온-프레미스 CRM 설치와 Dynamics CRM Online을 모두 지원합니다.

[자세한 정보](https://www.bainsight.com/connectors/microsoft-dynamics-crm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365-cloud"></a>Microsoft Dynamics 365(클라우드)

제공: [BA Insight](https://www.bainsight.com/)

Microsoft Dynamics 365(클라우드 버전) CRM 커넥터는 CRM 애플리케이션에 대한 보안 연결을 설정하고 CRM 스키마의 콘텐츠를 검색 엔진 스키마에 매핑합니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-microsoft-dynamics-cloud/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

제공: [BA Insight](https://www.bainsight.com/)

BA Insight Microsoft Exchange Online 커넥터를 사용하면 사용자가 다양한 검색 플랫폼을 통해 Exchange Online에서 콘텐츠를 검색할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/microsoft-exchange-online-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-public-folders"></a>Microsoft Exchange 공용 폴더

제공: [BA Insight](https://www.bainsight.com/)

BAI Microsoft Exchange 공용 폴더 커넥터를 사용하면 사용자가 다양한 검색 플랫폼을 통해 Exchange에서 콘텐츠를 검색할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/microsoft-exchange-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-server"></a>Microsoft Exchange Server

제공: [BA Insight](https://www.bainsight.com/)

BA Insight Microsoft Exchange 커넥터를 사용하면 사용자가 다양한 검색 엔진을 통해 Exchange에서 콘텐츠를 검색할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/microsoft-exchange-server-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

제공: [BA Insight](https://www.bainsight.com/)

데이터베이스 커넥터는 업계 표준 데이터베이스 액세스 방법을 기반으로 하기 때문에 Oracle, MySQL, IBM DB2와 같은 다른 시스템의 데이터베이스를 동일하게 지원할 수 있습니다. 원본 데이터베이스의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-teams"></a>Microsoft Teams

제공: [BA Insight](https://www.bainsight.com/)

BA Insight Microsoft Teams 커넥터는 Microsoft Teams의 콘텐츠를 다른 엔터프라이즈 시스템의 콘텐츠와 함께 인덱싱하여 통합된 결과를 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/microsoft-teams-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-windows-file-server"></a>Microsoft Windows File Server

제공: [Raytion](https://www.raytion.com/contact)

DFS(분산 파일 시스템)를 포함한 Microsoft Windows File Server의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Windows File Server의 파일 및 폴더를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Microsoft Windows File Server의 문서 수준 보안과 최신 버전의 SMB2 및 SMB3 프로토콜을 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-windows-file-server-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="mysql"></a>MySQL

제공: [BA Insight](https://www.bainsight.com/)

MySQL 커넥터는 업계 표준 데이터베이스 액세스 방법을 기반으로 하기 때문에 Oracle, MySQL, IBM DB2와 같은 다른 시스템의 데이터베이스를 동일하게 지원할 수 있습니다. 원본 데이터베이스의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/mysql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="netdocuments"></a>NetDocuments

제공: [BA Insight](https://www.bainsight.com/)

NetDocuments 커넥터는 사용자가 포털 내에서 직접 NetDocuments 콘텐츠를 검색할 수 있도록 NetDocs에 저장된 콘텐츠를 인덱싱합니다. 이 커넥터는 NetDocs의 문서 보안을 Azure Cognitive Search에 자동으로 적용하기 때문에 사용자 정보가 안전하게 유지됩니다. NetDocuments에 저장된 메타데이터를 동등한 용어에 매핑하여 사용자에게 원활한 검색 환경을 제공할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/netdocuments-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="neudesic-the-firm-directory"></a>Neudesic The Firm Directory

제공: [BA Insight](https://www.bainsight.com/)

Firm Directory 커넥터는 원본 시스템의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/the-firm-directory-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="notes"></a>참고

제공: [Raytion](https://www.raytion.com/contact)

IBM Notes(이전 이름: Lotus Note)의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 구성 가능한 Notes 데이터베이스 세트의 레코드를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 IBM Notes의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-notes-connector)

:::column-end:::
:::column span="":::

---

### <a name="nuxeo"></a>Nuxeo

제공: [BA Insight](https://www.bainsight.com/)

Nuxeo 커넥터를 사용하면 보안 정보와 콘텐츠에 설정된 표준 및 사용자 지정 메타데이터를 모두 포함하는 Nuxeo 콘텐츠를 Office 365에 있는 콘텐츠와 함께 Azure Cognitive Search로 인덱싱할 수 있습니다. 궁극적으로 사용자는 한 곳에서 필요한 모든 것을 찾을 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/nuxeo-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="objective"></a>Objective

제공: [BA Insight](https://www.bainsight.com/)

Objective 커넥터는 Objective용으로 개발되었으며, Objective에 대한 보안 연결을 설정하고 Objective 스키마의 메타데이터를 포함하는 콘텐츠를 검색 엔진 스키마로 매핑합니다. 그런 다음, 콘텐츠를 추출하고 크롤링이라는 프로세스를 통해 검색 엔진에 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/objective-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="onedrive"></a>OneDrive

제공: [Accenture](https://www.accenture.com)

OneDrive 커넥터는 Microsoft OneDrive의 콘텐츠를 크롤링하며, regex 패턴에 기반한 항목 포함 및 제외, 메타데이터 추출, 여러 유형의 문서 검색을 포함한 증분 크롤링이 가능합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/OneDrive+Connector)

:::column-end:::
:::column span="":::

---

### <a name="onedrive-for-business"></a>OneDrive for Business

제공: [BA Insight](https://www.bainsight.com/)

BA Insight OneDrive 커넥터를 사용하면 OneDrive의 콘텐츠를 다양한 검색 플랫폼으로 인덱싱하여 사용자에게 여러 원본의 통합 검색 결과를 제공할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/onedrive-business-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

제공: [BA Insight](https://www.bainsight.com/)

이 커넥터는 네이티브 포털 콘텐츠와 거의 동일한 방식으로 Content Server 콘텐츠를 인덱싱하여 전체 크롤링과 증분 크롤링을 모두 지원합니다. Content Server에 정의된 보안이 검색 환경에 자동으로 반영되어 사용자는 권한이 부여된 콘텐츠만 볼 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/livelink-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

제공: [Raytion](https://www.raytion.com/contact)

OpenText Content Server의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Content Server 인스턴스의 파일, 폴더, 가상 폴더, 복합 문서, 뉴스, 이메일, 볼륨, 컬렉션, 분류 및 더 많은 개체를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 OpenText Content Server의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-opentext-content-server-connector)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-cloud"></a>OpenText Documentum(Cloud)

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 OpenText Documentum Cloud 커넥터는 Documentum 개체의 전체 텍스트와 메타데이터 모두를 검색 엔진에 안전하게 인덱싱하여 여러 리포지토리의 콘텐츠에서 검색 가능한 단일 결과 집합을 사용할 수 있도록 합니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-documentum-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-eroom"></a>OpenText Documentum eRoom

제공: [Raytion](https://www.raytion.com/contact)

OpenText Documentum eRoom의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 리포지토리, 폴더 및 파일을 Documentum eRoom의 메타데이터 및 속성과 함께 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 OpenText Documentum eRoom의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-opentext-documentum-eroom-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-edocs-dm"></a>OpenText eDOCS DM

제공: [BA Insight](https://www.bainsight.com/)

OpenText eDOCS DM 커넥터 사용자는 Azure Cognitive Search 내에서 직접 eDOCS 리포지토리에 있는 콘텐츠를 검색할 수 있기 때문에 필요한 콘텐츠를 찾기 위해 여러 번 검색을 수행할 필요가 없습니다. eDOCS 내에서 설정된 보안은 액세스 권한이 부여된 사용자만 콘텐츠를 볼 수 있도록 커넥터에서 유지 관리됩니다.

[자세한 정보](https://www.bainsight.com/connectors/edocs-dm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-database"></a>Oracle 데이터베이스

제공: [BA Insight](https://www.bainsight.com/)

Oracle Database 커넥터는 업계 표준 데이터베이스 액세스 방법을 기반으로 하기 때문에 Microsoft SQL Server, MySQL, IBM DB2와 같은 다른 시스템의 데이터베이스를 동일하게 지원할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/oracle-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter"></a>Oracle WebCenter

제공: [BA Insight](https://www.bainsight.com/)

WebCenter 커넥터는 WebCenter를 Azure Cognitive Search와 통합하기 때문에 조직 전체 사용자가 직접 로그인하여 별도의 검색을 수행하지 않고도 WebCenter에 저장된 중요한 정보를 쉽게 찾을 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/oracle-webcenter-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="oracle-ka-cloud"></a>Oracle KA Cloud

제공: [Raytion](https://www.raytion.com/contact)

Oracle KA(Knowledge Advanced) Cloud의 콘텐츠를 안정적으로 인덱싱하고 Azure Cognitive Search를 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Oracle KA Cloud의 페이지와 첨부 파일을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Oracle KA Cloud의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다. 특히 이 커넥터는 Oracle KA Cloud 페이지 내에서 코드 조각 기반 권한을 처리합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-oracle-ka-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter-content-ucmstellent"></a>Oracle WebCenter Content(UCM/Stellent)

제공: [BA Insight](https://www.bainsight.com/)

WebCenter Content 커넥터는 Azure Cognitive Search에서 사용할 수 있는 모든 콘텐츠의 기본 보안을 완벽하게 지원하고 예약된 크롤링을 통해 이 콘텐츠를 최신 상태로 유지하며 사용자가 검색을 수행할 때 최신 업데이트를 얻을 수 있도록 합니다.

[자세한 정보](https://www.bainsight.com/connectors/oracle-webcenter-content-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="pirobase-cms"></a>pirobase CMS

제공: [Raytion](https://www.raytion.com/contact)

pirobase CMS의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. pirobase CMS의 페이지, 첨부 파일 및 기타 생성된 문서 유형을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 pirobase CMS의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-pirobase-cms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 PostgreSQL 커넥터는 원본 데이터베이스의 보안을 존중하고 전체 크롤링과 증분 크롤링을 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다. PostgreSQL의 콘텐츠를 Azure Cognitive Search로 인덱싱하고 BA Insight의 SmartHub를 통해 노출하여 사용자에게 통합 검색 결과를 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/postgresql-connector-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="practical-law"></a>Practical Law

제공: [BA Insight](https://www.bainsight.com/)

BA Insight Practical Law 커넥터를 사용하면 사용자가 Practical Law 데이터베이스에 대해 검색을 수행할 수 있으므로 Practical Law로 직접 이동할 필요가 없습니다.

[자세한 정보](https://www.bainsight.com/connectors/practical-law-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="prolaw"></a>ProLaw

제공: [BA Insight](https://www.bainsight.com/)

BA Insight Connector for ProLaw는 ProLaw 내의 사용자 권한을 존중하면서 포털을 ProLaw에 연결하여 ProLaw의 정보를 표시할 수 있도록 합니다.

[자세한 정보](https://www.bainsight.com/connectors/prolaw-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="rdb-via-snapshots"></a>RDB via Snapshots

제공: [Accenture](https://www.accenture.com)

RDB via Snapshots 커넥터는 JDBC를 사용하여 액세스할 수 있는 모든 관계형 데이터베이스의 콘텐츠를 크롤링하고 스냅샷 데이터베이스를 사용하여 증분 크롤링을 수행합니다. SQL 문을 기반으로 직접 데이터를 추출합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Snapshots+Connector)

:::column-end:::
:::column span="":::

---

### <a name="rdb-via-tables"></a>RDB via Tables

제공: [Accenture](https://www.accenture.com)

RDB via Tables 커넥터는 JDBC를 사용하여 액세스할 수 있는 모든 관계형 데이터베이스의 콘텐츠를 크롤링하고 업데이트된 콘텐츠의 식별자가 포함된 테이블을 사용하여 업데이트를 가져오는 증분 크롤링을 수행합니다. SQL 문을 기반으로 직접 데이터를 추출합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Table+Connector)

:::column-end:::
:::column span="":::

---

### <a name="s3"></a>S3

제공: [Accenture](https://www.accenture.com)

Amazon S3 커넥터는 Amazon Simple Storage Service의 콘텐츠를 크롤링합니다. 증분 크롤링을 수행하고 S3 문서 수준 보안을 위한 개체 ACL을 가져오고 버킷, 폴더 및 하위 폴더에 저장된 문서를 포함합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Amazon+S3+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

제공: [Accenture](https://www.accenture.com)

Salesforce 커넥터는 Salesforce 리포지토리의 콘텐츠를 크롤링합니다. 이 커넥터는 Salesforce Knowledge Base 및 Chatter, 메타데이터 및 사용자 지정 메타데이터 검색을 지원하고, 전체 또는 증분 크롤링을 수행하고, ACL을 가져오고, 선택 가능한 요소 형식 및 그룹 확장을 수행합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Salesforce+Connector)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

제공: [BA Insight](https://www.bainsight.com/)

Salesforce 커넥터는 Salesforce의 서비스, 영업 및 마케팅 클라우드를 Azure Cognitive Search와 통합하여 이 포털을 통해 모든 직원이 Salesforce 내의 모든 콘텐츠를 사용할 수 있도록 합니다.

[자세한 정보](https://www.bainsight.com/connectors/salesforce-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

제공: [Raytion](https://www.raytion.com/contact)

Salesforce의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Salesforce의 계정, 채팅 메시지, 프로필, 잠재 고객, 사례 및 기타 모든 레코드 개체를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Salesforce의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-salesforce-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-erp"></a>SAP ERP

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SAP ERP 커넥터는 SAP의 항목을 검색 인덱스로 가져오도록 설계되었습니다. 그러면 UI가 밝아지고 SAP, SharePoint 및 기타 시스템의 정보 전반에서 통합된 보기가 가능합니다.

[자세한 정보](https://www.bainsight.com/connectors/sap-erp-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sap-erp-cloud"></a>SAP ERP(Cloud)

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SAP ERP(Cloud 버전) 커넥터는 SAP의 항목을 검색 인덱스로 가져오도록 설계되었습니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-sap-erp-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="sap-hana"></a>SAP HANA

제공: [BA Insight](https://www.bainsight.com/)

SAP HANA 커넥터는 원본 데이터베이스의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다. SAP HANA의 콘텐츠를 Azure Cognitive Search로 인덱싱하고 BA Insight의 SmartHub를 통해 노출하여 사용자에게 통합 검색 결과를 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/sap-hana-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-hana-cloud"></a>SAP HANA(Cloud)

제공: [BA Insight](https://www.bainsight.com/)

SAP HANA(Cloud 버전) 커넥터는 원본 데이터베이스의 보안을 준수하며 전체 크롤링 및 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-sap-hana-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="sap-netweaver-portal"></a>SAP NetWeaver Portal

제공: [Raytion](https://www.raytion.com/contact)

SAP NWP(NetWeaver Portal)의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. SAP NWP, KMC(Knowledge Management and Collaboration), PCD(Portal Content Directory) 영역의 페이지, 첨부 파일 및 기타 문서 유형을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Active Directory 및 기타 디렉터리 서비스를 기반으로 하는 SAP NWP 설치 및 SAP NetWeaver Portal의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-sap-netweaver-portal-connector)

:::column-end:::
:::column span="":::

---

### <a name="sap-plm-dms"></a>SAP PLM DMS

제공: [Raytion](https://www.raytion.com/contact)

SAP PLM DMS의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. SAP PLM DMS의 문서, 첨부 파일 및 기타 레코드를 거의 실시간으로 강력하게 인덱싱합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-sap-plm-dms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="selenium"></a>Selenium

제공: [Accenture](https://www.accenture.com)

Selenium 커넥터는 인터넷 브라우저를 사용하여 웹 사이트의 콘텐츠를 크롤링하여 웹 페이지, 사이트 맵, 바이너리 문서 등과 같은 여러 유형의 문서를 검색합니다. Angular 및 React와 같은 프레임워크와의 호환성 문제를 방지합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Selenium+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

제공: [Accenture](https://www.accenture.com)

ServiceNow 커넥터는 ServiceNow 리포지토리에서 지식 문서, 카탈로그 항목 및 첨부 파일과 같은 여러 유형의 문서를 검색합니다. 또한 보안 ACL을 검색하고 그룹 확장을 수행합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/ServiceNow+Connector)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

제공: [BA Insight](https://www.bainsight.com/)

 ServiceNow 커넥터는 원본 시스템의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/servicenow-connector-sharepoint-azure-elasticsearch)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

제공: [Raytion](https://www.raytion.com/contact)

ServiceNow의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. ServiceNow의 문제, 작업, 첨부 파일, 지식 관리 문서, 페이지 등을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 ServiceNow의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-servicenow-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint"></a>SharePoint

제공: [Raytion](https://www.raytion.com/contact)

Microsoft SharePoint의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. SharePoint 온-프레미스 인스턴스의 사이트, 웹, 최신(SharePoint 2016 이상) 및 클래식 페이지, Wiki 페이지, OneNote 문서, 목록 항목, 작업, 일정 항목, 첨부 파일 및 파일을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Microsoft SharePoint의 기본 제공 사용자 및 그룹 관리는 물론 Active Directory와 SiteMinder 및 Okta와 같은 OAuth 공급자도 완벽하게 지원합니다. 이 커넥터는 기본, NTLM 및 Kerberos 인증을 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-sharepoint-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2010"></a>SharePoint 2010

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SharePoint 2010 커넥터를 사용하면 SharePoint 2010에 연결하고 사이트, 문서 라이브러리 또는 목록에서 데이터를 가져와서 콘텐츠를 안전하게 인덱싱할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sharepoint-2010-connector/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

제공: [Accenture](https://www.accenture.com)

SharePoint 2013 커넥터는 SharePoint 2013 사이트 모음 URL의 콘텐츠를 크롤링합니다. SharePoint의 변경 로그 타임스탬프 또는 스냅샷 데이터베이스를 사용하여 증분 크롤링을 수행하고, ACL을 가져오고, NTLM 및 HTTPS, BCS 외부 목록을 지원하며 SharePoint에 아무 것도 설치하지 않고 실행합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2013+Connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SharePoint 2013 커넥터를 사용하면 SharePoint 2013에 연결하고 사이트, 문서 라이브러리 또는 목록에서 데이터를 가져와서 콘텐츠를 안전하게 인덱싱할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sharepoint-2013-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

제공: [Accenture](https://www.accenture.com)

SharePoint 2016 커넥터는 SharePoint 2016 사이트 모음 URL의 콘텐츠를 크롤링합니다. SharePoint의 변경 로그 타임스탬프 또는 스냅샷 데이터베이스를 사용하여 증분 크롤링을 수행하고, ACL을 가져오고, NTLM 및 HTTPS, BCS 외부 목록을 지원하며 SharePoint에 아무 것도 설치하지 않고 실행합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2016+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SharePoint 커넥터를 사용하면 SharePoint 2016에 연결하고 사이트, 문서 라이브러리 또는 목록에서 데이터를 가져와서 콘텐츠를 안전하게 인덱싱할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sharepoint-2016-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2019"></a>SharePoint 2019

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SharePoint 커넥터를 사용하면 SharePoint 2019에 연결하고 사이트, 문서 라이브러리 또는 목록에서 데이터를 가져와서 콘텐츠를 안전하게 인덱싱할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/connector-for-sharepoint-2019/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

제공: [Accenture](https://www.accenture.com)

SharePoint Online 커넥터는 SharePoint Online 사이트 모음 URL의 콘텐츠를 크롤링합니다. 이 커넥터는 사이트, 목록, 폴더, 목록 항목 및 첨부 파일과 기타 페이지(.aspx 형식)를 검색합니다. Microsoft O365 제품에서 실행되는 SharePoint를 지원합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+Online+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 SharePoint Online 커넥터를 사용하면 SharePoint Online에 연결하고 사이트, 문서 라이브러리 또는 목록에서 데이터를 가져와서 콘텐츠를 안전하게 인덱싱할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sharepoint-online-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

제공: [BA Insight](https://www.bainsight.com/)

Sitecore 커넥터는 원본 시스템의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sitecore-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

제공: [Raytion](https://www.raytion.com/contact)

Sitecore의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. 페이지, 첨부 파일 및 기타 생성된 문서 유형을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Sitecore의 권한 모델 및 연결된 Active Directory의 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-sitecore-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="slack"></a>Slack

제공: [Raytion](https://www.raytion.com/contact)

Slack의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Slack의 모든 공개 채널에서 메시지, 스레드 및 공유 파일을 거의 실시간으로 강력하게 인덱싱합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-slack-connector)

:::column-end:::
:::column span="":::

---

### <a name="smb"></a>SMB

제공: [Accenture](https://www.accenture.com)

SMB 커넥터는 공유 드라이브에서 파일과 디렉터리를 검색합니다. 분산 파일 시스템 지원, 보안 정보 검색 기능이 있으며 마지막으로 액세스한 날짜를 변경하지 않으면서 인덱싱을 위해 문서에 액세스할 수 있습니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/SMB+Connector)

:::column-end:::
:::column span="":::

---

### <a name="smb-file-share"></a>SMB 파일 공유

제공: [Raytion](https://www.raytion.com/contact)

SMB File Share의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. File Share의 파일과 폴더를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 SMB의 문서 수준 보안과 최신 버전의 SMB2 및 SMB3 프로토콜을 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-smb-file-share-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sql-database"></a>SQL Database

제공: [Raytion](https://www.raytion.com/contact)

Microsoft SQL Server 또는 Oracle과 같은 SQL 데이터베이스의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. SQL 데이터베이스의 이진 문서를 포함한 레코드 및 필드를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 사용자 지정 문서 수준 보안 모델의 구현을 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-sql-database-connector)

:::column-end:::
:::column span="":::

---

### <a name="any-sql-based-crm-system"></a>모든 SQL 기반 CRM 시스템

제공: [BA Insight](https://www.bainsight.com/)

SQL Server 커넥터는 업계 표준 데이터베이스 액세스 방법을 기반으로 하기 때문에 Oracle, MySQL, IBM DB2와 같은 다른 시스템의 데이터베이스를 동일하게 지원할 수 있습니다. 원본 데이터베이스의 보안을 존중하고 전체 크롤링과 증분 크롤링을 모두 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="symantec-enterprise-vault"></a>Symantec Enterprise Vault

제공: [Raytion](https://www.raytion.com/contact)

Symantec Enterprise Vault의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Enterprise Vault의 이메일, 첨부 파일, 파일, 일정 항목 및 연락처와 같은 보관된 데이터를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Symantec Enterprise Vault의 인증 모델인 Basic, NTLM 및 Kerberos 인증을 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-enterprise-vault-connector-2)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="twitter"></a>Twitter

제공: [Accenture](https://www.accenture.com)

Twitter 커넥터는 Twitter 계정의 콘텐츠를 크롤링합니다. 전체 크롤링 및 증분 크롤링을 수행하고 Twitter 사용자, 소비자 키 및 소비자 비밀 키를 사용한 인증을 지원합니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Twitter+Connector)

:::column-end:::
:::column span="":::

---

### <a name="veeva-vault"></a>Veeva Vault

제공: [BA Insight](https://www.bainsight.com/)

BA Insight의 Veeva Vault 커넥터는 Veeva Vault 개체의 전체 텍스트와 메타데이터를 Azure Cognitive Search에 안전하게 인덱싱합니다. 이를 통해 사용자는 Veeva Vault 및 Microsoft 365 내 콘텐츠에 대한 단일 결과 집합을 검색할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/veeva-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="veritas-enterprise-vault-symantec-evault"></a>Veritas Enterprise Vault(Symantec eVault)

제공: [BA Insight](https://www.bainsight.com/)

Veritas Enterprise Vault 커넥터는 원본 시스템의 보안을 존중하고 전체 크롤링과 증분 크롤링을 제공하기 때문에 사용자가 항상 최신 정보를 사용할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/enterprise-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="veritas-enterprise-vault"></a>Veritas Enterprise Vault

제공: [Raytion](https://www.raytion.com/contact)

Veritas Enterprise Vault의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Enterprise Vault의 이메일, 첨부 파일, 파일, 일정 항목 및 연락처와 같은 보관된 데이터를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Veritas Enterprise Vault의 인증 모델인 Basic, NTLM 및 Kerberos 인증을 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-enterprise-vault-connector)

:::column-end:::
:::column span="":::
---

### <a name="website-crawler"></a>Website Crawler

제공: [BA Insight](https://www.bainsight.com/)

BA Insight Website Crawler 커넥터를 사용하면 모든 웹사이트의 콘텐츠를 다른 리포지토리의 콘텐츠와 함께 통합된 단일 검색 인덱스에 노출할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/website-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="west-km"></a>West km

제공: [BA Insight](https://www.bainsight.com/)

BA Insight West km 커넥터는 거래 및 소송 문서 검색을 지원하며 사용자 지정 검색 결과 페이지를 생성할 수 있습니다.

[자세한 정보](https://www.bainsight.com/connectors/westkm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="windream-ecm-system"></a>windream ECM-System

제공: [Raytion](https://www.raytion.com/contact)

windream ECM-System의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Windream ECM-System과 관련된 포괄적인 메타데이터 세트를 포함하여 파일과 폴더를 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 windream ECM-System의 권한 모델 및 연결된 Active Directory의 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-windream-ecm-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="xerox-docushare"></a>Xerox DocuShare

제공: [BA Insight](https://www.bainsight.com/)

Azure Cognitive Search 내에서 직접 Docushare 리포지토리에 있는 콘텐츠를 검색할 수 있기 때문에 필요한 콘텐츠를 찾기 위해 여러 번 검색을 수행할 필요가 없습니다.

[자세한 정보](https://www.bainsight.com/connectors/docushare-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::
---

### <a name="xerox-docushare"></a>Xerox DocuShare

제공: [Raytion](https://www.raytion.com/contact)

Xerox DocuShare의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. DocuShare의 데이터 리포지토리, 폴더, 프로필, 그룹 및 파일을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Xerox DocuShare의 기본 제공 사용자 및 그룹 관리를 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-xerox-docushare-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

제공: [Accenture](https://www.accenture.com)

Yammer 커넥터는 Yammer 메시지의 콘텐츠를 크롤링합니다. 그룹, 스레드 또는 토픽별로 메시지를 검색하고 사용자, 그룹 및 스레드 세부 정보를 가져옵니다.

[자세한 정보](https://contentanalytics.digital.accenture.com/display/aspire40/Yammer+Connector)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

제공: [BA Insight](https://www.bainsight.com/)

Yammer 커넥터는 Yammer 애플리케이션에 대한 보안 연결을 설정하고, 메타데이터 및 첨부 파일을 포함한 콘텐츠를 Yammer 스키마에서 검색 엔진 스키마로 매핑합니다. 그런 다음, 콘텐츠를 추출하고 크롤링이라는 프로세스를 통해 검색 엔진에 제공합니다.

[자세한 정보](https://www.bainsight.com/connectors/yammer-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

제공: [Raytion](https://www.raytion.com/contact)

Microsoft Yammer의 콘텐츠를 안정적으로 인덱싱하여 Azure Cognitive Search를 통해 지능적으로 검색하기 위한 보안 엔터프라이즈 검색 커넥터입니다. Yammer의 채널, 게시물, 회신, 첨부 파일, 설문 조사 및 공지 사항을 거의 실시간으로 강력하게 인덱싱합니다. 이 커넥터는 Microsoft Yammer의 기본 제공 사용자 및 그룹 관리, 특히 Microsoft 365에 대한 페더레이션 인증을 완벽하게 지원합니다.

[자세한 정보](https://www.raytion.com/connectors/raytion-yammer-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::
