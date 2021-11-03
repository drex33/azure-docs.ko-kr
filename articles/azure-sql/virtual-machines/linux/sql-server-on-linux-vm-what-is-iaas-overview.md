---
title: Linux용 Azure Virtual Machines의 SQL Server 개요 | Microsoft Docs
description: Linux용 Azure Virtual Machines에서 전체 SQL Server 버전을 실행하는 방법을 알아봅니다. 모든 Linux SQL Server VM 이미지 및 관련된 내용에 대한 직접 링크를 가져옵니다.
services: virtual-machines-sql
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: service-overview
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7bcf2e64643d27822f2f58e56b054e25d3adaca5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080957"
---
# <a name="overview-of-sql-server-on-linux-azure-virtual-machines"></a>SQL Server on Linux Azure Virtual Machines 개요
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Azure Virtual Machines의 SQL Server를 사용하면 온-프레미스 하드웨어를 관리할 필요 없이 클라우드에서 SQL Server의 전체 버전을 사용할 수 있습니다. 또한 SQL Server VM을 사용하면 종량제로 지불하는 경우 라이선스 비용이 간소화됩니다.

Azure 가상 머신은 전 세계 여러 [지리적 지역](https://azure.microsoft.com/regions/)에서 실행됩니다. 또한 다양한 [컴퓨터 크기](../../../virtual-machines/sizes.md)가 제공됩니다. 가상 머신 이미지 갤러리를 통해 적합한 버전 운영 체제로 SQL Server VM을 만들 수 있습니다. 따라서 가상 머신은 다양한 SQL Server 워크로드에 적합한 옵션입니다. 

Azure SQL을 처음 사용하는 경우에는 심층 [Azure SQL 비디오 시리즈](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)에서 *Azure VM의 SQL Server 개요* 비디오를 참조하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> SQL Server VM 시작

시작하려면 필요한 버전 및 운영 체제로 SQL Server 가상 머신 이미지를 선택합니다. 다음 섹션에서는 SQL Server 가상 머신 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다.

> [!TIP]
> SQL Server 이미지의 가격 책정을 이해하는 방법에 대한 자세한 내용은 [SQL Server를 실행하는 Linux VM에 대한 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조하세요.

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | RHEL(Red Hat Enterprise Linux) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SLES(SUSE Linux Enterprise Server) v12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | RHEL(Red Hat Enterprise Linux) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SLES(SUSE Linux Enterprise Server) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Windows에서 사용 가능한 SQL Server 가상 머신 이미지를 보려면 [Azure Virtual Machines의 SQL Server 개요(Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조하세요.

## <a name="installed-packages"></a><a id="packages"></a>설치된 패키지

Linux에서 SQL Server를 구성하는 경우 데이터베이스 엔진 패키지를 설치한 다음, 요구 사항에 따라 몇 가지 선택적 패키지를 설치합니다. SQL Server의 Linux 가상 머신 이미지는 대부분의 패키지를 자동으로 설치합니다. 다음 표에서는 각 배포에 설치된 패키지를 보여줍니다.

| 배포 | [데이터베이스 엔진](/sql/linux/sql-server-linux-setup) | [Tools](/sql/linux/sql-server-linux-setup-tools) | [SQL Server 에이전트](/sql/linux/sql-server-linux-setup-sql-agent) | [전체 텍스트 검색](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [HA 추가 기능](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL 및 데이터베이스 엔진](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL 및 도구](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL 및 SQL Server 에이전트](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL 및 전체 텍스트 검색](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL 및 SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL 및 HA 추가 기능](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES 및 데이터베이스 엔진](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES 및 도구](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES 및 SQL Server 에이전트](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES 및 전체 텍스트 검색](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES 및 SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES 및 HA 추가 기능](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu 및 데이터베이스 엔진](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu 및 도구](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu 및 SQL Server 에이전트](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu 및 전체 텍스트 검색](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu 및 SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu 및 HA 추가 기능](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |


> [!NOTE]
> Azure Linux Virtual Machines의 SQL Server용 SQL IaaS 에이전트 확장은 Ubuntu Linux 배포에만 사용할 수 있습니다.

## <a name="related-products-and-services"></a>관련 제품 및 서비스

### <a name="linux-virtual-machines"></a>Linux 가상 머신

* [Azure Virtual Machines 개요](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>스토리지

* [Microsoft Azure Storage 소개](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>네트워킹

* [Virtual Network 개요](../../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../../virtual-network/ip-services/public-ip-addresses.md)
* [Azure 포털에서 정규화된 도메인 이름 만들기](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [Linux의 SQL Server 설명서](/sql/linux)
* [Azure SQL Database 비교](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>다음 단계

Linux 가상 머신에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](sql-vm-create-portal-quickstart.md)

Linux의 SQL Server VM에 대한 일반적인 질문에 대한 답변 구하기:

* [Azure Virtual Machines의 SQL Server FAQ](frequently-asked-questions-faq.yml)