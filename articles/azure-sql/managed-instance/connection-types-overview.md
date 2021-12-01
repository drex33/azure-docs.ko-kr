---
title: 연결 유형
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 연결 형식에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: connect
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 12/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a56a0ebc795e66d057f825a762eadbcd840323aa
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133426230"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL Managed Instance 연결 형식
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 클라이언트가 연결 형식에 따라 Azure SQL Managed Instance에 연결하는 방법을 설명합니다. 연결 형식을 변경하는 스크립트 샘플은 기본 연결 설정 변경과 관련된 고려 사항과 함께 아래에 제공됩니다.

## <a name="connection-types"></a>연결 유형

Azure SQL Managed Instance는 다음 두 가지 연결 형식을 지원합니다.

- **리디렉션(권장):** 클라이언트는 데이터베이스를 호스팅하는 노드로 직접 연결을 설정합니다. 리디렉션을 사용하여 연결하려면 포트 1433 및 11000-11999에서 액세스할 수 있도록 방화벽 및 NSG(네트워크 보안 그룹)를 열어야 합니다. 패킷은 데이터베이스로 직접 이동하므로 프록시를 통한 리디렉션을 사용하여 대기 시간 및 처리량 성능이 향상됩니다. 게이트웨이 구성 요소의 계획된 유지 관리 이벤트의 영향도 프록시에 비해 리디렉션 연결 형식으로 최소화됩니다. 연결이 설정되면 게이트웨이에 종속되지 않습니다. 
- **프록시(기본값):** 이 모드에서는 모든 연결이 프록시 게이트웨이 구성 요소를 사용합니다. 연결을 사용하도록 설정하려면 프라이빗 네트워크용 포트 1433과 공용 연결용 포트 3342만 열어야 합니다. 이 모드를 선택하면 워크로드의 특성에 따라 더 높은 대기 시간 및 더 낮은 처리량이 발생할 수 있습니다. 또한 게이트웨이 구성 요소의 계획된 유지 관리 이벤트는 프록시 모드에서 모든 라이브 연결을 중단합니다. 가장 낮은 대기 시간, 가장 높은 처리량 및 계획된 유지 관리의 최소화된 영향을 위해 프록시 연결 정책에 대한 리디렉션 연결 정책을 권장합니다.

## <a name="redirect-connection-type"></a>리디렉션 연결 형식

리디렉션 연결 형식에서는 SQL 엔진에 대한 TCP 세션이 설정된 후 클라이언트 세션이 부하 분산 장치에서 가상 클러스터 노드의 대상 가상 IP를 가져옵니다. 후속 패킷은 게이트웨이를 우회하고 가상 클러스터 노드로 직접 이동됩니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![Azure 가상 네트워크의 게이트웨이에 연결된 redirect-find-db와 가상 네트워크의 데이터베이스 주 노드에 연결된 redirect-query가 있는 온-프레미스 네트워크를 보여 주는 다이어그램](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 리디렉션 연결 형식은 현재 프라이빗 엔드포인트에서만 작동합니다. 연결 형식 설정에 관계없이 공용 엔드포인트를 통해 들어오는 연결은 프록시를 통해 수행됩니다.

## <a name="proxy-connection-type"></a>프록시 연결 형식

프록시 연결 형식에서는 게이트웨이를 사용하여 TCP 세션이 설정되고 모든 후속 패킷이 게이트웨이를 통과합니다. 아래 다이어그램은 이 트래픽 흐름을 보여줍니다.

![Azure 가상 네트워크의 게이트웨이에 연결된 프록시가 다음에는 가상 네트워크의 데이터베이스 주 노드에 연결하는 온-프레미스 네트워크를 보여 주는 다이어그램](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>연결 형식 변경

- **Portal 사용:** Azure Portal을 사용하여 연결 형식을 변경하려면 Virtual Network 페이지를 열고 **연결 형식** 설정을 사용하여 연결 형식을 변경한 다음, 변경 내용을 저장합니다.

- **PowerShell을 사용하여 연결 형식 설정을 변경하는 스크립트:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 PowerShell 스크립트는 관리형 인스턴스의 연결 형식을 `Redirect`로 변경하는 방법을 보여 줍니다.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>다음 단계

- [데이터베이스를 SQL Managed Instance로 복원](restore-sample-database-quickstart.md)
- [SQL Managed Instance에서 퍼블릭 엔드포인트를 구성](public-endpoint-configure.md)하는 방법을 알아봅니다.
- [SQL Managed Instance 연결 아키텍처](connectivity-architecture-overview.md)에 대해 알아봅니다.
