---
title: Azure Resource Manager 템플릿 - Azure SQL Database 및 SQL Managed Instance
description: Azure Resource Manager 템플릿을 사용하여 Azure SQL Database 및 Azure SQL Managed Instance를 만들고 구성합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma
ms.date: 06/30/2021
ms.openlocfilehash: d2f7111238c99a1f55ffe1f7657db188cc38a91c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528852"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance용 Azure Resource Manager 템플릿
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager 템플릿을 사용하여 인프라를 코드로 정의하고 Azure SQL Database 및 SQL Managed Instance용 Azure 클라우드에 솔루션을 배포할 수 있습니다.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

다음 표에는 Azure SQL Database용 Azure Resource Manager 템플릿 링크가 포함되어 있습니다.

|링크 |Description|
|---|---|
| [SQL 데이터베이스](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-database-transparent-encryption-create) | 이 Azure Resource Manager 템플릿은 Azure SQL Database에 단일 데이터베이스를 만들고 서버 수준 IP 방화벽 규칙을 구성합니다. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server) | 이 Azure Resource Manager 템플릿은 Azure SQL Database에 대한 서버를 만듭니다. |
| [탄력적 풀](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-elastic-pool-create) | 이 템플릿을 사용하여 탄력적 풀을 배포하고 데이터베이스를 할당할 수 있습니다. |
| [장애 조치(failover) 그룹](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-with-failover-group) | 이 템플릿은 Azure SQL Database에 두 서버, 단일 데이터베이스 및 장애 조치(failover) 그룹을 만듭니다.|
| [위협 탐지](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-threat-detection-db-policy-multiple-databases) | 이 템플릿에서는 각 데이터베이스에 대한 경고용 이메일 주소를 통해 위협 탐지를 사용하도록 설정된 데이터베이스 세트와 서버를 배포할 수 있습니다. 위협 탐지는 SQL ATP(Advanced Threat Protection) 제품의 일부이며 서버 및 데이터베이스에 대한 잠재적 위협에 대응하는 보안 계층을 제공합니다.|
| [Azure Blob 스토리지 감사](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-auditing-server-policy-to-blob-storage) | 이 템플릿에서는 Blob 스토리지에 감사 로그를 쓰도록 설정된 감사를 통해 서버를 배포할 수 있습니다. Azure SQL Database 감사는 데이터베이스 이벤트를 추적하여 Azure 스토리지 계정, OMS 작업 영역 또는 Event Hubs에 배치할 수 있는 감사 로그에 기록합니다.|
| [Azure Event Hub 감사](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-auditing-server-policy-to-eventhub) | 이 템플릿에서는 기존 이벤트 허브에 감사 로그를 쓰도록 설정된 감사를 통해 서버를 배포할 수 있습니다. 감사 이벤트를 Event Hubs로 보내려면 감사 설정을 `Enabled` `State`로 설정하고 `IsAzureMonitorTargetEnabled`를 `true`로 설정합니다. 또한 `master` 데이터베이스에서 `SQLSecurityAuditEvents` 로그 범주를 사용하여 진단 설정을 구성합니다(서버 수준 감사인 경우). 감사는 데이터베이스 이벤트를 추적하여 Azure 스토리지 계정, OMS 작업 영역 또는 Event Hubs에 배치할 수 있는 감사 로그에 기록합니다.|
| [SQL Database를 사용하는 Azure 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.web/web-app-sql-database) | 이 샘플은 "기본" 서비스 수준에서 체험판 Azure 웹앱과 Azure SQL Database를 만듭니다.|
| [SQL Database를 사용하는 Azure 웹앱 및 Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.web/web-app-redis-cache-sql-database) | 이 템플릿은 동일한 리소스 그룹에 웹앱, Redis Cache 및 데이터베이스를 만들고, 웹앱에 데이터베이스 및 Redis Cache에 대한 두 개의 연결 문자열을 만듭니다.|
| [ADF V2를 사용하여 Blob 스토리지에서 데이터 가져오기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.datafactory/data-factory-v2-blob-to-sql-copy) | 이 Azure Resource Manager 템플릿은 Azure Blob 스토리지에서 SQL Database로 데이터를 복사하는 Azure Data Factory V2 인스턴스를 만듭니다.|
| [데이터베이스를 사용하는 HDInsight 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-linux-with-sql-database) | 이 템플릿을 사용하면 HDInsight 클러스터, 논리적 SQL 서버, 데이터베이스 및 두 개의 테이블을 만들 수 있습니다. 이 템플릿은 [HDInsight에서 Hadoop과 Sqoop 사용 문서](../../hdinsight/hadoop/hdinsight-use-sqoop.md)에서 사용됩니다. |
| [일정에 따라 SQL 저장 프로시저를 실행하는 Azure 논리 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-sql-proc) | 이 템플릿을 사용하면 일정에 따라 SQL 저장 프로시저를 실행하는 논리 앱을 만들 수 있습니다. 프로시저에 대한 인수를 템플릿의 본문 섹션에 삽입할 수 있습니다.|
| [Azure AD 전용 인증을 사용하도록 설정된 서버 프로비전](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth) | 이 템플릿은 서버에 대한 Azure AD 관리자 집합 및 Azure AD 전용 인증을 사용하도록 설정된 SQL 논리 서버를 만듭니다. |

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

다음 표에는 Azure SQL Managed Instance에 대한 Azure Resource Manager 템플릿 링크가 포함되어 있습니다.

|링크|Description|
|---|---|
| [새 VNet의 SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sqlmi-new-vnet) | 이 Azure Resource Manager 템플릿은 가상 네트워크에 새로 구성된 Azure 가상 네트워크 및 관리형 인스턴스를 만듭니다. |
| [SQL Managed Instance에 대한 네트워크 환경](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-managed-instance-azure-environment) | 이 배포는 두 개의 서브넷이 있는 구성된 Azure 가상 네트워크를 만듭니다. 두 서브넷 중 하나는 관리형 인스턴스 전용이고, 다른 하나는 기타 리소스(예: VM, App Service 환경 등)를 배치할 수 있습니다. 이 템플릿은 관리형 인스턴스를 배포할 수 있도록 올바르게 구성된 네트워킹 환경을 만듭니다. |
| [P2S 연결을 사용하는 SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sqlmi-new-vnet-w-point-to-site-vpn) | 이 배포는 `ManagedInstance` 및 `GatewaySubnet` 서브넷이 있는 Azure 가상 네트워크를 만듭니다. SQL Managed Instance는 ManagedInstance 서브넷에 배포됩니다. 가상 네트워크 게이트웨이는 `GatewaySubnet` 서브넷에 생성되고 지점 및 사이트 간 VPN 연결에 대해 구성됩니다. |
| [가상 머신을 사용하는 SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sqlmi-new-vnet-w-jumpbox) | 이 배포는 `ManagedInstance` 및 `Management` 서브넷이 있는 Azure 가상 네트워크를 만듭니다. SQL Managed Instance는 `ManagedInstance` 서브넷에 배포됩니다. 최신 버전의 SSMS(SQL Server Management Studio)가 설치된 가상 머신이 `Management` 서브넷에 배포됩니다. |
| [진단 로그를 사용하도록 설정된 SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sqlmi-new-vnet-w-diagnostic-settings) | 이 배포는 `ManagedInstance` 서브넷이 있는 Azure Virtual Network를 만들고 진단 로그가 활성화된 내부에 Managed Instance를 배포합니다. 또한 인스턴스 진단 로그를 보내고 저장하기 위해 이벤트 허브, 진단 작업 영역 및 스토리지 계정을 배포합니다. |

---
