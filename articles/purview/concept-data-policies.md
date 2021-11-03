---
title: Azure 부서의 범위 액세스 정책 개념
description: Azure 부서의 범위 액세스 정책 이해
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4d3d635b3f38f344ca0ae6cf89e16b0e6714073
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053063"
---
# <a name="concepts-for-azure-purview-data-access-policies"></a>Azure 부서의 범위 데이터 액세스 정책에 대 한 개념

이 문서는 azure 부서의 범위 내에서 데이터 공간에 대 한 액세스를 관리할 수 있는 Azure 부서의 범위 데이터 액세스 정책을 이해 하는 데 도움이 됩니다.

> [!Note]
> 이 기능은 azure [부서의 범위의 액세스 제어](catalog-permissions.md)에서 설명 하는 azure 부서의 범위 자체에 대 한 액세스 제어와 다릅니다.

> [!IMPORTANT]
> Azure 부서의 범위 액세스 정책은 현재 미리 보기 상태입니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

## <a name="overview"></a>개요

Azure 부서의 범위의 액세스 정책을 사용 하 여 전체 데이터 공간에서 다른 데이터 시스템에 대 한 액세스를 관리할 수 있습니다. 예:

사용자는 Azure 부서의 범위에 등록 된 Azure Storage 계정에 대 한 읽기 권한이 필요 합니다. 부서의 범위 Studio에서 **정책 관리** 앱을 통해 데이터 액세스 정책을 만들어 Azure 부서의 범위에서 직접이 액세스 권한을 부여할 수 있습니다.

데이터 액세스 정책은 정책에 등록 된 데이터 시스템의 부서의 범위를 통해 적용할 수 있습니다.

## <a name="azure-purview-policy-concepts"></a>Azure 부서의 범위 정책 개념

### <a name="azure-purview-policy"></a>Azure 부서의 범위 정책

**정책은** 정책 문의 명명 된 모음입니다. 부서의 범위의 거 버 넌 스에서 하나 이상의 데이터 시스템에 정책을 게시 하면 그에 따라 적용 됩니다. 정책 정의에는 정책 이름, 설명 및 하나 이상의 정책 문 목록이 포함 됩니다.

### <a name="policy-statement"></a>정책 문

**정책 문은** 데이터 원본에서 특정 데이터 작업을 처리 하는 방법을 결정 하는 사람이 읽을 수 있는 명령입니다. 정책 문은 **효과**, **작업, 데이터 리소스** 및 **제목** 으로 구성 됩니다.

#### <a name="action"></a>작업

**작업** 은이 정책의 일부로 허용 되거나 거부 되는 작업입니다. 예: 읽기 또는 쓰기 이러한 상위 수준의 논리적 작업은 데이터가 적용 되는 데이터 시스템의 하나 이상의 데이터 작업에 매핑됩니다.

#### <a name="effect"></a>영향

**효과** 는이 정책의 결과 영향을 나타내는 결과입니다. 현재 유일 하 게 지원 되는 값은 **허용** 입니다.

#### <a name="data-resource"></a>데이터 리소스

**데이터 리소스** 는 정책 설명이 적용 되는 정규화 된 데이터 자산 경로입니다. 다음 형식을 따릅니다.

*/subscription/ \<subscription-id> /wsourceg/ \<resource-group-name> /providers/\<provider-name>/\<data-asset-path>*

Azure Storage 데이터-자산-경로 형식:

*Microsoft. Storage/storageAccounts/ \<account-name> /blobservice/default/containers/\<container-name>*

Azure SQL DB 데이터-자산-경로 형식:

*Microsoft .Sql/servers/\<server-name>*

#### <a name="subject"></a>제목

이 정책 설명이 적용 되는 Azure Active Directory (AAD)의 최종 사용자 id입니다. 이 id는 서비스 사용자, 개별 사용자, 그룹 또는 MSI (관리 서비스 id) 일 수 있습니다.

### <a name="example"></a>예제

데이터 자산에 대 한 읽기 거부: */subscription/finance/resourcegroups/prod/providers/Microsoft. Storage/storageAccounts/finDataLake/blobservice/default/containers/FinData to group 재무-분석가*

위의 정책 문에서는 결과가 *거부* 이 고, 작업을 *읽고*, 데이터 리소스가 Azure Storage 컨테이너 *findata* 이며, 제목은 AAD 그룹 *재무 분석가* 입니다. 이 그룹에 속한 사용자가 저장소 컨테이너 *Findata* 에서 데이터를 읽으려고 하면 요청이 거부 됩니다.

### <a name="hierarchical-enforcement-of-policies"></a>정책의 계층적 적용

정책 문에 지정 된 데이터 리소스는 기본적으로 계층적입니다. 이는 policy 문이 데이터 개체 자체 및 데이터 개체에 포함 된 **모든** 자식 개체에 적용 됨을 의미 합니다. 예를 들어 Azure storage 컨테이너의 정책 문은 그 안에 포함 된 모든 blob에 적용 됩니다.

### <a name="policy-combining-algorithm"></a>정책 결합 알고리즘 

Azure 부서의 범위에는 동일한 데이터 자산을 참조 하는 다른 정책 문이 있을 수 있습니다. 데이터 액세스에 대 한 결정을 평가할 때 Azure 부서의 범위는 적용 가능한 모든 정책을 결합 하 고 통합 된 결정을 제공 합니다. 결합 전략은 가장 제한적인 정책을 선택 하는 것입니다.
예를 들어 다음과 같이 Azure Storage 컨테이너 *findata* 에 대 한 두 개의 다른 정책을 가정해 보겠습니다.

정책 1- *데이터 자산에 대 한 읽기/Subscription/..../containers/FinData 재무 그룹에 대 한 읽기 허용-분석가*

정책 2- *데이터 자산에 대 한 읽기 거부/Subscription/..../containers/FinData 금융-계약자*

그런 다음, 두 그룹의 일부인 ' s u s ' 사용자를 가정 합니다. 즉, *재무 분석가* 및 *재무 계약자* 는 blob 읽기 API에 대 한 호출을 실행 합니다. 두 정책이 모두 적용 될 수 있으므로 Azure 부서의 범위는 *읽기* *거부* 인 가장 제한적인 하나를 선택 합니다. 따라서 액세스 요청이 거부 됩니다.

## <a name="policy-publishing"></a>정책 게시

새로 만든 정책이 초안 모드 상태에 있으며 Azure 부서의 범위에만 표시 됩니다. 게시 작업은 지정 된 데이터 시스템에서 정책의 적용을 시작 합니다. 기본 데이터 원본에 적용 되는 데 최대 2 분이 소요 될 수 있는 비동기 작업입니다.

데이터 원본에 게시 된 정책에는 다른 데이터 원본에 속하는 자산에 대 한 참조가 포함 될 수 있습니다. 이러한 참조는 정책이 적용 되는 데이터 원본에 해당 자산이 없기 때문에 무시 됩니다.

## <a name="azure-purview-to-data-source-action-mapping"></a>Azure 부서의 범위에서 데이터 원본 작업 매핑

다음 표에서는 Azure 부서의 범위 데이터 정책의 작업을 데이터 시스템의 특정 작업에 매핑하는 방법을 보여 줍니다.

| **부서의 범위 정책 작업** | **데이터 원본 관련 작업**                                                                |
|---------------------------|-------------------------------------------------------------------------------------------------|
|||
| *읽기*                      |<sub>Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/항목/읽기                        |
|                           |<sub>Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/executeQuery                      |
|                           |<sub>Microsoft DocumentDB/databaseAccounts/sqlDatabases/컨테이너/readChangeFeed                    |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/read                            |
|||
| *수정*                    |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/read                            |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/write                           |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/add/action                      |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/move/action                     |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/delete                          |
|||
| *SQL 성능 모니터*   |<sub>Microsoft .Sql/sqlservers/SystemViewsAndFunctions/ServerMetadata/rows/select                     |
|                           |<sub>Microsoft .Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseMetadata/rows/select         |
|                           |<sub>Microsoft .Sql/sqlservers/커넥트                                                                |
|                           |<sub>Microsoft .Sql/sqlservers/SystemViewsAndFunctions/ServerState/rows/select                        |
|                           |<sub>Microsoft .Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseState/rows/select            |
|                           |<sub>Microsoft .Sql/sqlservers/databases/커넥트                                                      |
|||
| *SQL 감사자*               |<sub>Microsoft .Sql/sqlservers/databases/커넥트                                                      |
|                           |<sub>Microsoft .Sql/sqlservers/커넥트                                                                |
|                           |<sub>Microsoft .Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityState/rows/select                |
|                           |<sub>Microsoft .Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityState/rows/select    |
|                           |<sub>Microsoft .Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityMetadata/rows/select             |
|                           |<sub>Microsoft .Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityMetadata/rows/select |

## <a name="next-steps"></a>다음 단계
Azure 부서의 범위에서 정책을 만드는 방법에 대 한 자습서를 확인 하 고 Azure Storage와 같은 특정 데이터 시스템에서 작동 합니다.

* [데이터 소유자가 Azure Storage에 대 한 데이터 집합 프로 비전](how-to-access-policies-storage.md)

<!--
[Dataset provisioning by data owner for Azure SQL DB](how-to-access-policies-sql.md)
-->
