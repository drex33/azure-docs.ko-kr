---
title: SQL Server 가상 머신에 연결(Resource Manager) | Microsoft Docs
description: Azure의 SQL Server 가상 머신에 연결하는 방법을 알아봅니다. 이 항목에서는 클래식 배포 모드를 사용합니다. 시나리오는 네트워킹 구성 및 클라이언트의 위치에 따라 다릅니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 5b970883dbcf8e3c9d19c082622e67a6877fa957
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165950"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Azure의 SQL Server 가상 머신에 연결
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>개요

이 항목에서는 Azure VM(가상 머신)에서 SQL에 연결하는 방법을 설명합니다. [일반적인 연결 시나리오](#connection-scenarios) 일부를 설명하고 [포털에서 연결 설정을 변경하기 위한 단계](#change)를 제공합니다. 포털 외부에서 문제를 해결하거나 연결을 구성해야 하는 경우 이 항목의 끝 부분에 있는 [수동 구성](#manual)을 참조하세요. 

프로비저닝 및 연결의 전체 연습을 확인하려면 [Azure에서 SQL Server 가상 머신 프로비전](create-sql-vm-portal.md)을 참조하세요.

## <a name="connection-scenarios"></a>연결 시나리오

클라이언트가 SQL Server VM에 연결하는 방법은 클라이언트의 위치 및 네트워킹 구성에 따라 달라집니다.

Azure Portal에서 SQL Server VM을 프로비전하는 경우 **SQL 연결** 의 형식을 지정하는 옵션이 있습니다.

![프로비전 중 공용 SQL 연결 옵션](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

연결에 대한 옵션은 다음과 같습니다.

| 옵션 | Description |
|---|---|
| **공용** | 인터넷을 통해 SQL Server에 연결합니다. |
| **프라이빗** | 동일한 가상 네트워크의 SQL Server에 연결합니다. |
| **로컬** | 동일한 가상 머신의 SQL Server에 로컬로 연결합니다. | 

다음 섹션은 **공용** 및 **프라이빗** 옵션을 자세히 설명합니다.

## <a name="connect-to-sql-server-over-the-internet"></a>인터넷을 통해 SQL Server에 연결

인터넷에서 SQL Server 데이터베이스 엔진에 연결하려는 경우 프로비전하는 동안 포털에서 **SQL 연결** 형식에 대해 **공용** 을 선택합니다. 포털에서 다음 단계를 자동으로 수행합니다.

* SQL Server에 대해 TCP/IP 프로토콜을 활성화합니다.
* SQL Server TCP 포트(기본값 1433)를 열도록 방화벽 규칙을 구성합니다.
* 공용 액세스에 필요한 SQL Server 인증을 활성화합니다.
* SQL Server 포트의 모든 TCP 트래픽에 대해 VM에서 네트워크 보안 그룹을 구성합니다.

> [!IMPORTANT]
> SQL Server Developer 및 Express 버전용 가상 머신 이미지는 자동으로 TCP/IP 프로토콜을 사용하지 않습니다. Developer 및 Express 버전의 경우 VM을 만든 후에 SQL Server 구성 관리자를 사용하여 [수동으로 TCP/IP 프로토콜을 사용](#manualtcp)해야 합니다.

인터넷에 연결된 모든 클라이언트는 가상 머신의 공용 IP 주소 또는 IP 주소에 할당된 DNS 이름을 지정하여 SQL Server 인스턴스에 연결할 수 있습니다. SQL Server 포트가 1433인 경우 연결 문자열에 지정할 필요가 없습니다. 다음 연결 문자열은 SQL 인증을 사용하여 `sqlvmlabel.eastus.cloudapp.azure.com`의 DNS 레이블로 SQL VM에 연결합니다(공용 IP 주소를 사용할 수도 있음).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

이 문자열은 인터넷을 통한 클라이언트의 연결이 사용하도록 설정하지만 누구나 SQL Server 인스턴스에 연결할 수 있다는 뜻은 아닙니다. 외부 클라이언트는 정확한 사용자 이름과 암호를 사용해야 합니다. 그러나 추가 보안을 위해 잘 알려진 포트 1433을 피할 수 있습니다. 예를 들어 1500 포트에서 수신하도록 SQL Server를 구성하고 적절한 방화벽 및 네트워크 보안 그룹 규칙을 설정할 경우 서버 이름에 포트 번호를 추가하여 연결할 수 있습니다. 다음 예제에서는 서버 이름에 사용자 지정 포트 번호, **1500** 을 추가하여 이전 항목을 변경합니다.

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> 인터넷을 통해 VM의 SQL Server를 쿼리하는 경우 Azure 데이터 센터에서 보내는 모든 데이터에는 일반적으로 [아웃바운드 데이터 전송 가격](https://azure.microsoft.com/pricing/details/data-transfers/)이 적용됩니다.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>가상 네트워크 내에서 SQL Server에 연결

포털에서 **SQL 연결** 형식에 대해 **프라이빗** 을 선택하는 경우 Azure는 대부분의 설정을 **공용** 과 동일하게 구성합니다. 한 가지 차이점은 SQL Server 포트(기본값 1433)에서 외부 트래픽을 허용하는 네트워크 보안 그룹 규칙이 없다는 점입니다.

> [!IMPORTANT]
> SQL Server Developer 및 Express 버전용 가상 머신 이미지는 자동으로 TCP/IP 프로토콜을 사용하지 않습니다. Developer 및 Express 버전의 경우 VM을 만든 후에 SQL Server 구성 관리자를 사용하여 [수동으로 TCP/IP 프로토콜을 사용](#manualtcp)해야 합니다.

프라이빗 연결은 종종 [가상 네트워크](../../../virtual-network/virtual-networks-overview.md)와 함께 사용되어 여러 가지 시나리오를 구현합니다. VM이 다른 리소스 그룹에 있더라도 동일한 가상 네트워크의 VM에 연결할 수 있습니다. 또한 [사이트 간 VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md)을 통해 온-프레미스 네트워크와 컴퓨터에 VM을 연결하는 하이브리드 아키텍처를 만들 수 있습니다.

가상 네트워크를 사용하면 Azure VM을 도메인에 연결할 수 있습니다. 이것이 SQL Server에 Windows 인증을 사용하는 유일한 방법입니다. 다른 연결 시나리오의 경우 사용자 이름과 암호가 있는 SQL 인증이 필요합니다.

가상 네트워크에 DNS를 구성했다고 가정하면 연결 문자열에 SQL Server VM 컴퓨터 이름을 지정하여 SQL Server에 연결할 수 있습니다. 또한 다음 예에서는 Windows 인증이 구성되었고 사용자에게 SQL Server 인스턴스에 대한 액세스 권한이 있다고 가정합니다.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> SQL 연결 설정 변경

Azure Portal에서 SQL Server 가상 머신에 대한 연결 설정을 변경할 수 있습니다.

1. Azure Portal에서 **SQL 가상 머신** 을 선택합니다.

2. SQL Server VM을 선택합니다.

3. **설정** 에서 **보안** 을 선택합니다.

4. **SQL 연결 수준** 을 필요한 설정으로 변경합니다. 필요에 따라 이 영역을 사용하여 SQL Server 포트 또는 SQL 인증 설정을 변경할 수 있습니다.

   ![SQL 연결 변경](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. 업데이트를 완료할 때까지 몇 분 동안 기다립니다.

   ![SQL VM 업데이트 알림](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Developer 및 Express 버전에 대해 TCP/IP 사용

SQL Server 연결 설정을 변경할 때 Azure는 SQL Server Developer 및 Express 버전에 대해 TCP/IP 프로토콜을 자동으로 설정하지 않습니다. 아래 단계에서는 IP 주소를 통해 원격으로 연결할 수 있도록 TCP/IP를 수동으로 사용하도록 설정하는 방법을 설명합니다.

먼저 원격 데스크톱을 사용하여 SQL Server 가상 머신에 연결합니다.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

다음으로 **SQL Server 구성 관리자** 를 사용하여 TCP/IP 프로토콜을 활성화합니다.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>SSMS를 사용하여 연결

다음 단계에서는 Azure VM에 대한 선택적 DNS 레이블을 만든 다음 SSMS(SQL Server Management Studio)와 연결하는 방법을 보여 줍니다.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> 수동 구성 및 문제 해결

포털에서 자동으로 연결을 구성하는 옵션을 제공하지만 수동으로 연결을 구성하는 방법을 알고 있으면 유용합니다. 요구 사항을 이해하면 문제 해결에 도움이 될 수 있습니다.

다음 표에는 Azure VM의 SQL Server에 연결하기 위한 요구 사항이 나와 있습니다.

| 요구 사항 | Description |
|---|---|
| [SQL Server 인증 모드 사용](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | 가상 네트워크에 Active Directory를 구성하지 않은 경우 원격으로 VM에 연결하는 데 SQL Server 인증이 필요합니다. |
| [SQL 로그인 만들기](/sql/relational-databases/security/authentication-access/create-a-login) | SQL 인증을 사용하는 경우 대상 데이터베이스에 대한 권한이 있는 사용자 이름과 암호를 사용하는 SQL 로그인이 필요합니다. |
| [TCP/IP 프로토콜 사용](#manualtcp) | SQL Server에서 TCP를 통한 연결을 허용해야 합니다. |
| [SQL Server 포트에 방화벽 규칙 사용](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | VM의 방화벽은 SQL Server 포트에서 인바운드 트래픽을 허용해야 합니다(기본값 1433). |
| [TCP 1433에 대한 네트워크 보안 그룹 규칙 만들기](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | 인터넷을 통해 연결하려면 VM에서 SQL Server 포트(기본값 1433)에서 트래픽을 받도록 허용해야 합니다. 로컬 및 가상 네트워크 전용 연결에는 이 기능이 필요하지 않습니다. 이는 Azure Portal에서 요구되는 유일한 단계입니다. |

> [!TIP]
> 위의 표에 나와 있는 단계는 포털에서 연결을 구성할 때 수행됩니다. 이러한 단계는 SQL Server에 대한 구성을 확인하거나 수동으로 연결을 설정할 때만 사용합니다.

## <a name="next-steps"></a>다음 단계

이러한 연결 단계와 함께 프로비저닝 지침을 확인하려면 [Azure에서 SQL Server 가상 머신 프로비전](create-sql-vm-portal.md)을 참조하세요.

Azure VM에서의 SQL Server 실행과 관련된 다른 항목은 [Azure 가상 머신의 SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조하세요.