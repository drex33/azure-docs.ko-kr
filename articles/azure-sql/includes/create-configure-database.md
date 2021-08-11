---
ms.service: sql-database
ms.subservice: deployment-configuration
ms.topic: include
ms.date: 12/17/2020
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: e57370bdceed2b22120985cc68da12152ed7032c
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255881"
---
  다음 빠른 시작 중 하나를 사용하여 데이터베이스를 만들고 구성할 수 있습니다.

  | 작업 | SQL Database | SQL Managed Instance | Azure VM의 SQL Server | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | 생성| [포털](../database/single-database-create-quickstart.md) | [포털](../managed-instance/instance-create-quickstart.md) | [포털](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [포털](../../synapse-analytics/quickstart-create-workspace.md) |
  || [CLI](../database/scripts/create-and-configure-database-cli.md) | | | [CLI](../../synapse-analytics/quickstart-create-workspace-cli.md) |
  || [PowerShell](../database/scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [PowerShell](../../synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [배포 템플릿](../virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [배포 템플릿](../../synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | 구성 | [서버 수준 IP 방화벽 규칙](../database/firewall-create-server-level-portal-quickstart.md)| [VM에서 연결](../managed-instance/connect-vm-instance-configure.md)| |
  |||[온-프레미스에서 연결](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  | 연결 정보 가져오기 | [Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)|[Azure SQL](../database/connect-query-content-reference-guide.md#get-server-connection-information)| [SQL VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md?#connect-to-sql-server)| [Synapse SQL](../../synapse-analytics/sql/connect-overview.md#find-your-server-name)|
  |||||
