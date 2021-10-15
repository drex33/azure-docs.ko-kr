---
title: Azure 가상 머신의 SQL Server 설명서 변경 내용
description: Azure Virtual Machines에 있는 다양한 SQL Server 릴리스의 새로운 기능과 개선 사항에 대해 알아봅니다.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.subservice: service-overview
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/01/2021
ms.openlocfilehash: 69d709dc6d58e732f56ef28c9b2e69b2a5ad5594
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130048232"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure 가상 머신의 SQL Server 설명서 변경 내용
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 설치 된 azure VM (가상 머신)을 수동으로 배포 하거나 기본 제공 이미지를 통해 배포 하는 경우 azure 기능을 활용 하 여 환경을 개선할 수 있습니다. 이 문서에서는 [Azure Virtual Machines (vm)에서](https://azure.microsoft.com/services/virtual-machines/sql-server/)최신 버전의 SQL Server의 새로운 기능 및 향상 된 기능에 관련 된 설명서 변경 내용을 요약 합니다. Azure vm에서 SQL Server에 대 한 자세한 내용은 [개요](sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조 하세요. 

## <a name="september-2021"></a>2021년 9월

| 변경 | 세부 정보 |
| --- | --- |
| **SQL IaaS 확장 전체 모드를 더 이상 다시 시작할 필요가 없음** | [전체 모드](sql-agent-extension-manually-register-single-vm.md#full-mode)에서 [SQL IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)을 사용하여 SQL Server VM을 등록할 때 이제 SQL Server 서비스를 다시 시작하지 않아도 됩니다. | 


## <a name="july-2021"></a>2021년 7월

| 변경 | 세부 정보 |
| --- | --- |
| **포털에서 SQL Server IaaS 확장 복구** | 이제 Azure Portal에서 직접 SQL Server IaaS 에이전트 확장의 상태를 확인하고, 필요한 경우 [복구](sql-agent-extension-manually-register-single-vm.md#repair-extension)할 수 있습니다. | 


## <a name="june-2021"></a>2021년 6월

| 변경 | 세부 정보 |
| --- | --- |
| **Azure Portal의 향상된 보안 기능** | [Azure Defender for SQL](../../../security-center/defender-for-sql-usage.md)을 사용하도록 설정하면 [Azure Portal의 SQL 가상 머신 리소스](manage-sql-vm-portal.md#security-center)에서 Security Center 권장 사항을 볼 수 있습니다. | 

## <a name="may-2021"></a>2021년 5월

| 변경 | 세부 정보 |
| --- | --- |
| **HADR 콘텐츠 새로 고침** | HADR(고가용성 및 재해 복구) 콘텐츠를 새로 고치고 개선했습니다. 이제 [Windows Server 장애 조치(failover) 클러스터 개요](hadr-windows-server-failover-cluster-overview.md)와 SQL Server VM에 대해 통합된 [쿼럼 구성 방법](hadr-cluster-quorum-configure-how-to.md)이 있습니다.  또한 클라우드에 도입된 보다 포괄적인 설정 권장 사항으로 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 개선했습니다.| 


## <a name="april-2021"></a>2021년 4월

| 변경 | 세부 정보 |
| --- | --- |
| **VM에 고가용성 마이그레이션** | Azure Migrate는 전체 고가용성 솔루션을 Azure VM의 SQL Server로 리프트 앤 시프트할 수 있도록 지원합니다. 지금 바로 Azure Migrate를 사용하여 [가용성 그룹](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md) 또는 [장애 조치 클러스터 인스턴스](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)를 SQL Server VM으로 가져오세요. | 


## <a name="march-2021"></a>2021년 3월

| 변경 | 세부 정보 |
| --- | --- |
| **성능 모범 사례 새로 고침** | 성능 모범 사례 설명서를 다시 작성, 새로 고침, 업데이트했으며, 한 문서를 [검사 목록](performance-guidelines-best-practices-checklist.md), [VM 크기 지침](performance-guidelines-best-practices-vm-size.md), [스토리지 지침](performance-guidelines-best-practices-storage.md), [기준 지침 수집](performance-guidelines-best-practices-collect-baseline.md)으로 구성된 시리즈로 분할했습니다.   | 



## <a name="2020"></a>2020

| 변경 | 세부 정보 |
| --- | --- |
| **Azure Government 지원** | 이제 [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) 클라우드에서 호스트되는 가상 머신용 SQL IaaS 에이전트 확장에 SQL Server 가상 머신을 등록할 수 있습니다. | 
| **Azure SQL 제품군** | Azure Virtual Machines의 SQL Server는 이제 [Azure SQL 제품군](../../azure-sql-iaas-vs-paas-what-is-overview.md)의 일부입니다. [새로운 모습](../index.yml)을 확인해 보세요. 제품에서 변경된 것은 없지만 설명서에서는 Azure SQL 제품 결정 지원을 목표로 합니다. | 
| **DNN(분산 네트워크 이름)** | Windows Server 2016 이상의 SQL Server 2019는 이제 Azure Load Balancer를 사용하는 대신 [분산 네트워크 이름](./failover-cluster-instance-distributed-network-name-dnn-configure.md)을 사용하여 트래픽을 FCI(장애 조치 클러스터 인스턴스)로 라우팅하는 지원을 미리 보기로 제공하고 있습니다. 이 지원은 Azure의 HA(고가용성) 솔루션에 대한 연결을 간소화합니다. | 
| **Azure 공유 디스크를 사용하는 FCI** | 이제 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md)를 사용하여 [FCI(장애 조치 클러스터 인스턴스)](failover-cluster-instance-overview.md)를 배포할 수 있습니다. |
| **재구성된 FCI 설명서** | [Azure VM의 SQL Server를 사용하는 장애 조치 클러스터 인스턴스](failover-cluster-instance-overview.md)에 대한 설명서가 명확하게 다시 작성되고 재구성되었습니다. [클러스터 구성 모범 사례](hadr-cluster-best-practices.md), [SQL Server FCI용 가상 머신](failover-cluster-instance-prepare-vm.md)을 준비하는 방법, [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md)를 구성하는 방법 등의 일부 구성 콘텐츠를 분리했습니다. | 
| **로그를 울트라 디스크로 마이그레이션** | 고성능과 짧은 대기 시간을 활용하기 위해 [로그 파일을 울트라 디스크로 마이그레이션](storage-migrate-to-ultradisk.md)할 수 있는 방법을 알아봅니다. | 
| **Azure PowerShell을 사용하여 AG 만들기** | 이제 [Azure PowerShell](availability-group-az-commandline-configure.md)과 Azure CLI를 사용하여 가용성 그룹을 만드는 과정을 간소화할 수 있습니다. | 
| **포털에서 AG 구성** | 이제 [Azure Portal을 통해 가용성 그룹을 구성](availability-group-azure-portal-configure.md)할 수 있습니다. 이 기능은 현재 미리 보기로 배포되고 있으므로 원하는 지역을 사용할 수 없는 경우 나중에 다시 확인하세요. | 
| **자동 확장 등록** | 이제 [자동 등록](sql-agent-extension-automatic-registration-all-vms.md) 기능을 사용하도록 설정하여 구독에 이미 배포된 모든 SQL Server VM을 [SQL IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)에 자동으로 등록할 수 있습니다. 이 기능은 기존의 모든 VM에 적용되며, 나중에 추가된 SQL Server VM도 자동으로 모두 등록합니다.   | 
| **AG용 DNN** | 이제 SQL Server 2019 CU8 이상에서 [DNN(분산 네트워크 이름) 수신기](availability-group-distributed-network-name-dnn-listener-configure.md)를 구성하여 기존 [VNN 수신기](availability-group-overview.md#connectivity)를 대체할 수 있으므로 Azure Load Balancer가 필요하지 않습니다.   | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|변경 | 세부 정보 |
 --- | --- |
| **Azure의 무료 DR 복제본** | [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)가 있는 경우 온-프레미스 SQL Server 인스턴스 재해 복구를 위한 [무료 수동 인스턴스](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 Azure에서 호스트할 수 있습니다. | 
| **SQL IaaS 확장 대량 등록** | 이제 [SQL IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)에 SQL Server 가상 머신을 [대량 등록](sql-agent-extension-manually-register-vms-bulk.md)할 수 있습니다. | 
|**성능 최적화 스토리지 구성** | 이제 새 SQL Server VM을 만들 때 [스토리지 구성를 완전히 사용자 지정](storage-configuration.md#new-vms)할 수 있습니다. |
|**FCI용 프리미엄 파일 공유** | 이제 원래 방법인 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 대신 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md)를 사용하여 장애 조치 클러스터 인스턴스를 만들 수 있습니다. 
| **Azure Dedicated Host** | [Azure Dedicated Host](dedicated-host.md)에서 SQL Server VM을 실행할 수 있습니다. | 
| **SQL Server VM을 다른 지역으로 마이그레이션** | Azure Site Recovery를 사용하여 [SQL Server VM을 한 지역에서 다른 지역으로 마이그레이션](move-sql-vm-different-region.md)할 수 있습니다. |
|  **새 SQL IaaS 설치 모드** | 이제 SQL Server 서비스를 다시 시작하지 않도록 SQL Server IaaS 확장을 [경량 모드](sql-server-iaas-agent-extension-automate-management.md)에서 설치할 수 있습니다.  |
| **SQL Server 버전 수정** | 이제 SQL Server VM에 대한 [버전 속성](change-sql-server-edition.md)을 변경할 수 있습니다. |
| **SQL IaaS 에이전트 확장에 대한 변경 내용** | 새 SQL IaaS 모드를 사용하여 [SQL Iaas 에이전트 확장에 SQL Server VM을 등록](sql-agent-extension-manually-register-single-vm.md)할 수 있습니다. 이 기능에는 [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes) 이미지가 포함됩니다.|
| **Azure 하이브리드 혜택을 사용하는 BYOL 이미지** | Azure Marketplace에서 배포된 BYOL 이미지는 이제 [라이선스 유형을 종량제](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)로 전환할 수 있습니다.| 
| **Azure Portal의 새 SQL Server VM 관리** | 이제 Azure Portal에서 SQL Server VM를 관리하는 새로운 방법이 있습니다. 자세한 내용은 [Azure Portal에서 SQL Server VM 관리](manage-sql-vm-portal.md)를 참조하세요.  | 
| **SQL Server 2008 및 2008 R2에 대한 추가 지원** | Azure VM으로 있는 그대로 마이그레이션하여 SQL Server 2008 및 SQL Server 2008 R2에 대한 [지원을 연장](sql-server-2008-extend-end-of-support.md)합니다. | 
| **사용자 지정 이미지 지원 가능성** | 이제 사용자 지정 OS 및 SQL Server 이미지에 [SQL Server IaaS 확장](sql-server-iaas-agent-extension-automate-management.md#installation)을 설치하여 [유연한 라이선스](licensing-model-azure-hybrid-benefit-ahb-change.md)의 제한된 기능을 제공할 수 있습니다. SQL IaaS 에이전트 확장에 사용자 지정 이미지를 등록하는 경우 라이선스 유형을 “AHUB”로 지정합니다. 그렇지 않으면 등록이 실패합니다. | 
| **명명된 인스턴스 지원 가능성** | 이제 기본 인스턴스가 제대로 제거되면 [SQL Server IaaS 확장](sql-server-iaas-agent-extension-automate-management.md#installation)을 명명된 인스턴스와 함께 사용할 수 있습니다. | 
| **포털 기능 향상** | SQL Server VM 배포를 위한 Azure Portal 환경이 사용성을 향상하도록 수정되었습니다. SQL Server VM 배포에 대한 자세한 내용은 간략한 [빠른 시작](sql-vm-create-portal-quickstart.md) 또는 보다 자세한 [방법 가이드](create-sql-vm-portal.md)를 참조하세요.|
| **포털 개선** | 이제 [Azure Portal](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model)을 사용하여 SQL Server VM의 라이선스 모델을 종량제에서 BYOL로 변경할 수 있습니다.|
| **Azure CLI를 통해 SQL Server VM에 대한 가용성 그룹 배포 간소화** | 이제 Azure에서 SQL Server VM에 가용성 그룹을 배포하기가 더 쉬워졌습니다. [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true)를 사용하여 Windows 장애 조치 클러스터, 내부 부하 분산 장치, 가용성 그룹 수신기를 모두 명령줄에서 만들 수 있습니다. 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹 구성](./availability-group-az-commandline-configure.md)을 참조하세요. | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 변경 | 세부 정보 |
| --- | --- |
|  **SQL Server 클러스터에 대한 새 리소스 공급자** | 새 리소스 공급자(Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)는 Windows 장애 조치(failover) 클러스터의 메타데이터를 정의합니다. SQL Server VM을 *SqlVirtualMachineGroups* 에 조인하면 WSFC(Windows Server 장애 조치(failover) 클러스터) 서비스가 부트스트랩되고 VM이 클러스터에 연결됩니다.  |
| **Azure 빠른 시작 템플릿을 사용하여 가용성 그룹 배포 설정 자동화** |이제 Windows 장애 조치 클러스터를 만들고, SQL Server VM을 해당 클러스터에 조인하고, 수신기를 만들고, 두 가지 Azure 빠른 시작 템플릿을 사용하여 내부 부하 분산 장치를 구성할 수 있습니다. 자세한 내용은 [Azure 빠른 시작 템플릿을 사용하여 Azure VM에서 SQL Server에 대한 Always On 가용성 그룹 구성](availability-group-quickstart-template-configure.md)을 참조하세요. | 
| **SQL IaaS 에이전트 확장에 자동 등록** | 이번 달 이후에 배포되는 SQL Server VM은 새 SQL IaaS 에이전트 확장에 자동으로 등록됩니다. 이번 달 이전에 배포된 SQL Server VM은 계속해서 수동으로 등록해야 합니다. 자세한 내용은 [SQL IaaS 에이전트 확장에 Azure의 SQL Server 가상 머신 등록](sql-agent-extension-manually-register-single-vm.md)을 참조하세요.|
|**새 SQL IaaS 에이전트 확장** |  새 리소스 공급자(Microsoft.SqlVirtualMachine)를 통해 SQL Server VM을 보다 효율적으로 관리할 수 있습니다. VM을 등록하는 방법에 대한 자세한 내용은 [SQL IaaS 에이전트 확장에 Azure의 SQL Server 가상 머신 등록](sql-agent-extension-manually-register-single-vm.md)을 참조하세요. |
|**라이선스 모델 전환** | 이제 Azure CLI 또는 PowerShell을 사용하여 SQL Server VM의 라이선스 모델을 종량제와 BYOL 간에 전환할 수 있습니다. 자세한 내용은 [Azure의 SQL Server 가상 머신에 대한 라이선스 모델을 변경하는 방법](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조하세요. | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>추가 리소스

**Windows VM**:

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM에 SQL Server 프로비저닝](create-sql-vm-portal.md)
* [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)
* [Azure 가상 머신의 SQL Server에 대한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 가상 머신의 SQL Server에 대한 성능 모범 사례](./performance-guidelines-best-practices-checklist.md)
* [Azure 가상 머신의 SQL Server에 대한 애플리케이션 패턴 및 개발 전략](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux 가상 머신에서 SQL Server 프로비저닝](../linux/sql-vm-create-portal-quickstart.md)
* [FAQ(Linux)](../linux/frequently-asked-questions-faq.yml)
* [Linux의 SQL Server 설명서](/sql/linux/sql-server-linux-overview)