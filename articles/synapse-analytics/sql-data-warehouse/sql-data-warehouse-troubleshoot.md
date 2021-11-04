---
title: 전용 SQL 풀(이전의 SQL DW) 문제 해결
description: Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW) 문제 해결
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5305da8dbdbf08cc7fa3cb0aecbdc0faa08a9a49
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428124"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW) 문제 해결

이 문서에서는 Azure Synapse Analytics 전용 SQL 풀(이전의 SQL DW)의 일반적인 문제 해결 사안을 나열합니다.

## <a name="connecting"></a>Connecting

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 'NT AUTHORITY\ANONYMOUS LOGON' 사용자에 대해 로그인 실패 (Microsoft SQL Server, 오류: 18456) | 이 오류는 Azure AD 사용자가 마스터 데이터베이스에 연결하려고 하는데 마스터에 사용자가 없는 경우에 발생합니다.  이 문제를 해결하려면 연결 시에 연결하려는 전용 SQL 풀(이전의 SQL DW)을 지정하거나 마스터 데이터베이스에 사용자를 추가합니다.  자세한 내용은 [보안 개요](sql-data-warehouse-overview-manage-security.md) 를 참조하세요. |
| 현재 보안 컨텍스트로는 서버 보안 주체 "MyUserName" 이(가) 데이터베이스 "master" 에 액세스할 수 없습니다. 사용자 기본 데이터베이스를 열 수 없습니다. 로그인이 실패했습니다. 사용자 'MyUserName'에 대한 로그인이 실패했습니다. (Microsoft SQL Server, 오류: 916) | 이 오류는 Azure AD 사용자가 마스터 데이터베이스에 연결하려고 하는데 마스터에 사용자가 없는 경우에 발생합니다.  이 문제를 해결하려면 연결 시에 연결하려는 전용 SQL 풀(이전의 SQL DW)을 지정하거나 마스터 데이터베이스에 사용자를 추가합니다.  자세한 내용은 [보안 개요](sql-data-warehouse-overview-manage-security.md) 를 참조하세요. |
| CTAIP 오류                                                  | 이 오류는 로그인이 특정 SQL 데이터베이스가 아닌 SQL Database 마스터 데이터베이스에서 만들어진 경우에 발생할 수 있습니다.  이 오류가 발생하는 경우 [보안 개요](sql-data-warehouse-overview-manage-security.md) 문서를 살펴보세요.  이 문서에서는 마스터 데이터베이스에서 로그인과 사용자를 만드는 방법과 SQL 데이터베이스에서 사용자를 만드는 방법을 차례로 설명합니다. |
| 방화벽에 의해 차단                                          | 전용 SQL 풀(이전의 SQL DW)은 알려진 IP 주소만 데이터베이스에 액세스할 수 있도록 방화벽으로 보호됩니다. 방화벽은 기본적으로 안전하며 이는 연결하기 전에 IP 주소 또는 주소 범위를 명시적으로 설정해야 한다는 의미입니다.  액세스를 위해 방화벽을 구성하려면 [프로비전 지침](create-data-warehouse-portal.md)의 [클라이언트 IP에 대한 서버 방화벽 액세스 구성](create-data-warehouse-portal.md) 단계를 따르세요. |
| 도구 또는 드라이버에 연결할 수 없음                           | 전용 SQL 풀(이전의 SQL DW)을 사용할 때는 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [SSDT for Visual Studio](sql-data-warehouse-install-visual-studio.md) 또는 [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)를 사용하여 데이터를 쿼리하는 것이 좋습니다. 드라이버와 Azure Synapse에 연결하는 방법에 대한 자세한 내용은 [Azure Synapse용 드라이버](sql-data-warehouse-connection-strings.md) 및 [Azure Synapse에 연결](sql-data-warehouse-connect-overview.md) 문서를 참조하세요. |

## <a name="tools"></a>도구

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio 개체 탐색기에 Azure AD 사용자가 없음           | 이것은 알려진 문제이며  해결 방법으로 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)에서 사용자를 봅니다.  전용 SQL 풀(이전의 SQL DW)에서 Azure Active Directory를 사용하는 방법에 대한 자세한 내용은 [Azure Synapse에 대한 인증](sql-data-warehouse-authentication.md)을 참조하세요. |
| 수동 스크립팅, 스크립팅 마법사 사용 또는 SSMS를 통한 연결이 느려지거나 응답하지 않거나 오류를 생성함 | 사용자가 master 데이터베이스에서 만들어졌는지 확인합니다. 스크립팅 옵션에서 엔진 버전이 "Microsoft Azure Synapse Analytics Edition"으로 설정되고 엔진 유형이 "Microsoft Azure SQL Database"로 설정되어 있는지도 확인합니다. |
| SSMS에서 스크립트 생성 실패                               | "종속 개체에 대한 스크립트 생성" 옵션이 "True"로 설정된 경우 전용 SQL 풀(이전의 SQL DW)용 스크립트 생성이 실패합니다. 이 문제를 해결하려면 사용자가 수동으로 **도구 -> 옵션 -> SQL Server 개체 탐색기 -> 종속 개체에 대한 스크립트 생성 옵션으로 이동하여 false로 설정** 해야 합니다 |

## <a name="data-ingestion-and-preparation"></a>데이터 수집 및 준비

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| CETAS를 사용하여 빈 문자열을 내보내면 Parquet 및 ORC 파일에서 NULL 값이 생성됩니다. 참고 NOT NULL 제약 조건이 있는 열에서 빈 문자열을 내보내는 경우 CETAS는 거부된 레코드를 생성하며 내보내기에 실패할 수 있습니다. | CETAS의 SELECT 문에서 빈 문자열이나 문제가 있는 열을 제거합니다. |
| Parquet 및 ORC 파일 형식의 경우 0~127 범위 밖의 값을 tinyint 열에 로드하는 것은 지원되지 않습니다. | 대상 열에 더 큰 데이터 형식을 지정합니다.           |

## <a name="performance"></a>성능

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 쿼리 성능 문제 해결                            | 특정 쿼리 문제를 해결하려는 경우 [쿼리 모니터링 방법 알아보기](sql-data-warehouse-manage-monitor.md#monitor-query-execution)문서부터 참조합니다. |
| TempDB 공간 문제 | [TempDB 공간 사용량을 모니터링](sql-data-warehouse-manage-monitor.md#monitor-tempdb)합니다.  TempDB 공간이 부족하게 되는 일반적인 원인은 다음과 같습니다.<br>- 쿼리에 할당된 리소스가 부족하여 데이터가 TempDB로 분산되지 않습니다.  [워크로드 관리](resource-classes-for-workload-management.md)를 참조하세요. <br>- 통계가 누락되었거나 오래되어 데이터 이동이 과도합니다.  통계를 만드는 방법에 대한 자세한 내용은 [테이블 통계 유지 관리](sql-data-warehouse-tables-statistics.md)를 참조하세요.<br>- TempDB 공간이 서비스 수준에 따라 할당됩니다.  더 높은 DWU 설정으로 [전용 SQL 풀(이전의 SQL DW)을 확장](sql-data-warehouse-manage-compute-overview.md#scaling-compute)하면 TempDB 공간이 더 많이 할당됩니다.|
| 쿼리 성능 및 계획이 부적합하여 종종 통계가 누락됨 | 성능 저하의 가장 일반적인 원인은 테이블에 대한 통계 부족입니다.  통계를 만드는 방법 및 통계가 성능에 중요한 이유에 대한 자세한 내용은 [테이블 통계 유지 관리](sql-data-warehouse-tables-statistics.md)를 참조하세요. |
| 낮은 동시성/큐에 쿼리 대기                             | 동시성과 함께 메모리 할당을 적절히 적용하려면 [워크로드 관리](resource-classes-for-workload-management.md) 를 이해하는 것이 중요합니다. |
| 모범 사례 구현 방법                              | 쿼리 성능을 향상시키는 방법은 [전용 SQL 풀(이전의 SQL DW) 모범 사례](../sql/best-practices-dedicated-sql-pool.md) 문서에서 알아볼 수 있습니다. |
| 크기 조정을 통해 성능을 향상시키는 방법                      | 때때로 성능을 향상시키기 위한 솔루션은 [전용 SQL 풀(이전의 SQL DW) 크기를 조정](sql-data-warehouse-manage-compute-overview.md)하여 단순히 더 많은 컴퓨팅 능력을 쿼리에 추가하는 것입니다. |
| 인덱스 품질 저하로 인한 쿼리 성능 저하     | 경우에 따라 [columnstore 인덱스 품질 저하](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)로 인해 쿼리가 느려질 수 있습니다.  자세한 내용 및 [세그먼트 품질을 개선하기 위해 인덱스 다시 작성](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)방법에 대해서는 이 문서를 참조하세요. |

## <a name="system-management"></a>시스템 관리

| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: 서버가 허용되는 데이터베이스 트랜잭션 단위 할당량인 45000을 초과하므로 작업을 수행할 수 없습니다. | 만들려는 데이터베이스의 [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 줄이거나 [할당량 증가를 요청](sql-data-warehouse-get-started-create-support-ticket.md)합니다. |
| 공간 사용률 조사                              | 시스템의 공간 사용률을 이해하려면 [테이블 크기](sql-data-warehouse-tables-overview.md#table-size-queries) 를 참조하세요. |
| 테이블 관리 도움말                                    | 테이블 관리에 대한 도움이 필요한 경우 [테이블 개요](sql-data-warehouse-tables-overview.md) 문서를 참조하세요.  이 문서에는 [테이블 데이터 유형](sql-data-warehouse-tables-data-types.md), [테이블 배포](sql-data-warehouse-tables-distribute.md), [테이블 인덱싱](sql-data-warehouse-tables-index.md), [테이블 분할](sql-data-warehouse-tables-partition.md), [테이블 통계 유지 관리](sql-data-warehouse-tables-statistics.md) 및 [임시 테이블](sql-data-warehouse-tables-temporary.md)과 같이 좀 더 자세한 항목으로 연결되는 링크가 포함되어 있습니다. |
| TDE(투명한 데이터 암호화) 진행률 표시줄이 Azure Portal에서 업데이트되지 않음 | [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 통해 TDE의 상태를 볼 수 있습니다. |

## <a name="differences-from-sql-database"></a>SQL Database와의 차이점

| 문제                                 | 해결 방법                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| 지원되지 않는 SQL Database 기능     | [지원되지 않는 테이블 기능](sql-data-warehouse-tables-overview.md#unsupported-table-features)을 참조하세요. |
| 지원되지 않는 SQL Database 데이터 형식   | [지원되지 않는 데이터 형식](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)을 참조하세요.        |
| 저장 프로시저 제한 사항          | 저장 프로시저의 몇 가지 제한을 이해하려면 [저장 프로시저 제한 사항](sql-data-warehouse-develop-stored-procedures.md#limitations) 을 참조하세요. |
| UDF가 SELECT 문을 지원하지 않음 | 이 문제가 UDF의 현재 제한 사항입니다.  지원되는 구문에 대해서는 [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 을 참조하세요. |
| 열에 대한 sp_rename(미리 보기)이 *dbo* 외부의 스키마에서 작동하지 않음 | 이는 현재 Synapse [열에 대한 sp_rename(미리 보기)](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)의 제한 사항입니다.  *dbo* 스키마의 일부가 아닌 개체의 열은 CTAS를 통해 새 테이블로 이름을 바꿀 수 있습니다. |

## <a name="next-steps"></a>다음 단계

문제 해결 방법을 찾는 데 도움이 필요한 경우 다음과 같은 리소스를 사용해 보세요.

* [블로그](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [기능 요청](https://feedback.azure.com/d365community/forum/9b9ba8e4-0825-ec11-b6e6-000d3a4f07b8)
* [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [지원 티켓 만들기](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&A 질문 페이지](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
