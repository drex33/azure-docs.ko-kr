---
title: Azure의 SAP 모니터링 데이터 참조
description: Azure의 SAP를 모니터링할 때 필요한 중요 참조 자료
author: Ajayan1008
ms.topic: reference
ms.author: v-hborys
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.custom: subject-monitoring
ms.date: 08/27/2021
ms.openlocfilehash: b2e5fe3b8a6ce5afec5489bfa6c95e34641455c6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101817"
---
# <a name="monitor-sap-on-azure-preview-data-reference"></a>Azure의 SAP 모니터링(미리 보기) 데이터 참조

이 문서에서는 SAP 솔루션을 위한 Azure Monitor의 성능 및 가용성을 분석하기 위해 수집된 로그 데이터에 대한 참조를 제공합니다. Azure의 SAP에 대한 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [Azure의 SAP 모니터링(미리 보기)](monitor-sap-on-azure.md)을 참조하세요.

## <a name="metrics"></a>메트릭

SAP 솔루션을 위한 Azure Monitor는 메트릭을 지원하지 않습니다.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 로그 테이블

이 섹션에서는 SAP 솔루션을 위한 Azure Monitor와 관련된 모든 Azure Monitor 로그 Kusto 테이블을 참조하고 Log Analytics 쿼리를 사용할 수 있습니다. SAP 솔루션을 위한 Azure Monitor는 사용자 지정 로그를 사용합니다. 일부 테이블의 스키마는 SAP와 같은 타사 공급자에서 정의합니다. 다음은 자세한 정보 소스에 대한 링크가 있는 SAP 솔루션을 위한 Azure Monitor에 대한 현재 사용자 지정 로그입니다.

### <a name="saphana_hostconfig_cl"></a>SapHana_HostConfig_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_LANDSCAPE_HOST_CONFIGURATION 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/20b1d83875191014b028e076c874e9d7.html)를 참조하세요.

### <a name="saphana_hostinformation_cl"></a>SapHana_HostInformation_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_HOST_INFORMATION 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20b10028751910148c1c9de602d771de.html)를 참조하세요.

### <a name="saphana_systemoverview_cl"></a>SapHana_SystemOverview_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_SYSTEM_OVERVIEW 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20c61f0675191014a1d5f96d9668855b.html)를 참조하세요.

### <a name="saphana_loadhistory_cl"></a>SapHana_LoadHistory_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_LOAD_HISTORY_HOST 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/3fa52abf1d854edbb7342a69364bcb0e.html)를 참조하세요.

### <a name="saphana_disks_cl"></a>SapHana_Disks_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_DISKS 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20aef7a275191014b37acbc35b4f20a4.html)를 참조하세요.

### <a name="saphana_systemavailability_cl"></a>SapHana_SystemAvailability_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_SYSTEM_AVAILABILITY 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.00/en-US/1ef9723a03214bd889c4fb8947765aa4.html)를 참조하세요.

### <a name="saphana_backupcatalog_cl"></a>SapHana_BackupCatalog_CL

자세한 내용은 다음을 참조하세요.
- [M_BACKUP_CATALOG_FILES 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.03/en-US/20a8437d7519101495a3fa7ad9961cf6.html?q=M_BACKUP_CATALOG)
- [M_BACKUP_CATALOG 시스템 뷰](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/1.0.12/en-US/20a8100e75191014870ecf908b5d2abf.html)

### <a name="saphana_systemreplication_cl"></a>SapHana_SystemReplication_CL

자세한 내용은 SAP HANA SQL 및 시스템 뷰 참조에서 [M_SERVICE_REPLICATION 시스템 뷰](https://help.sap.com/viewer/c1d3f60099654ecfb3fe36ac93c121bb/2021_2_QRC/en-US/20c43fc975191014b0ece11b47a86c10.html)를 참조하세요.
 
### <a name="prometheus_osexporter_cl"></a>Prometheus_OSExporter_CL

자세한 내용은 [GitHub의 prometheus/node_exporter](https://github.com/prometheus/node_exporter)를 참조하세요.

### <a name="prometheus_haclusterexporter_cl"></a>Prometheus_HaClusterExporter_CL

자세한 내용은 [ClusterLabs/ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)를 참조하세요.

### <a name="mssql_dbconnections_cl"></a>MSSQL_DBConnections_CL

자세한 내용은 다음을 참조하세요.
- [sys.dm_exec_sessions(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 
- [sys.databases(Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)

### <a name="mssql_systemprops_cl"></a>MSSQL_SystemProps_CL

자세한 내용은 다음을 참조하세요. 
- [sys.dm_os_windows_info(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-windows-info-transact-sql) 
- [sys.database_files(Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)
- [sys.dm_exec_sql_text(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)
- [sys.dm_io_virtual_file_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)
- [sys.dm_db_partition_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)
- [sys.dm_os_performance_counters(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)
- [sys.dm_os_wait_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)
- [sys.fn_xe_file_target_read_file(Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [SQL Server 운영 체제 관련 동적 관리 뷰(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)
- [sys.availability_groups(Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)
- [sys.dm_exec_requests(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)
- [sys.dm_xe_session_targets(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file(Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)
- [backupset(Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)
- [sys.sysprocesses(Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)

### <a name="mssql_fileoverview_cl"></a>MSSQL_FileOverview_CL

자세한 내용은 [sys.database_files(Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 참조하세요.

### <a name="mssql_memoryoverview_cl"></a>MSSQL_MemoryOverview_CL

자세한 내용은 [sys.dm_os_memory_clerks(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-memory-clerks-transact-sql)를 참조하세요.

### <a name="mssql_top10statements_cl"></a>MSSQL_Top10Statements_CL

자세한 내용은 다음을 참조하세요.
- [sys.dm_exec_sql_text(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)
- [sys.dm_exec_query_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)

### <a name="mssql_ioperformance_cl"></a>MSSQL_IOPerformance_CL

자세한 내용은 [sys.dm_io_virtual_file_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)를 참조하세요.

### <a name="mssql_tablesizes_cl"></a>MSSQL_TableSizes_CL

자세한 내용은 [sys.dm_db_partition_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)를 참조하세요.

### <a name="mssql_batchrequests_cl"></a>MSSQL_BatchRequests_CL

자세한 내용은 [sys.dm_os_performance_counters(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)를 참조하세요.

### <a name="mssql_waitpercs_cl"></a>MSSQL_WaitPercs_CL

자세한 내용은 [sys.dm_os_wait_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)를 참조하세요.

### <a name="mssql_pagelifeexpectancy2_cl"></a>MSSQL_PageLifeExpectancy2_CL

자세한 내용은 [sys.dm_os_performance_counters(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)를 참조하세요.

### <a name="mssql_error_cl"></a>MSSQL_Error_CL

자세한 내용은 [sys.fn_xe_file_target_read_file(Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)을 참조하세요.

### <a name="mssql_cpuusage_cl"></a>MSSQL_CPUUsage_CL

자세한 내용은 [SQL Server 운영 체제 관련 동적 관리 뷰(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sql-server-operating-system-related-dynamic-management-views-transact-sql)를 참조하세요.

### <a name="mssql_aooverview_cl"></a>MSSQL_AOOverview_CL

자세한 내용은 [sys.availability_groups(Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-availability-groups-transact-sql)를 참조하세요.

### <a name="mssql_aowaiter_cl"></a>MSSQL_AOWaiter_CL

자세한 내용은 [dm_exec_requests(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)를 참조하세요.

### <a name="mssql_aowaitstats_cl"></a>MSSQL_AOWaitstats_CL

자세한 내용은 [sys.dm_os_wait_stats(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)를 참조하세요.

### <a name="mssql_aofailovers_cl"></a>MSSQL_AOFailovers_CL

자세한 내용은 다음을 참조하세요.
- [sys.dm_xe_session_targets(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-xe-session-targets-transact-sql)
- [sys.fn_xe_file_target_read_file(Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

### <a name="mssql_bckbackups2_cl"></a>MSSQL_BckBackups2_CL

자세한 내용은 [backupset(Transact-SQL)](/sql/relational-databases/system-tables/backupset-transact-sql)를 참조하세요.

### <a name="mssql_blockingprocesses_cl"></a>MSSQL_BlockingProcesses_CL

자세한 내용은 [sys.sysprocesses(Transact-SQL)](/sql/relational-databases/system-compatibility-views/sys-sysprocesses-transact-sql)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- SAP 솔루션을 위한 Azure Monitor 사용에 대한 자세한 내용은 [Azure의 SAP 모니터링](monitor-sap-on-azure.md)을 참조하세요.
- Azure Monitor에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
