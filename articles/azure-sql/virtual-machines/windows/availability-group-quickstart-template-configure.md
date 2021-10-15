---
title: 가용성 그룹 구성(Azure 빠른 시작 템플릿)
description: Azure 빠른 시작 템플릿을 사용하여 Azure에서 Windows 장애 조치(failover) 클러스터를 만들고, SQL Server VM을 해당 클러스터에 연결하고, 수신기를 만들고, 내부 Load Balancer를 구성할 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 84b0198f96c7a04a6e30e45820df48038489c9b5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037177"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Azure 빠른 시작 템플릿을 사용하여 Azure VM에서 SQL Server에 대한 가용성 그룹 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure 빠른 시작 템플릿을 사용하여 Azure에서 SQL Server VM(가상 머신)에 대한 Always On 가용성 그룹 구성의 배포를 부분적으로 자동화하는 방법을 설명합니다. 이 프로세스에는 두 가지 Azure 빠른 시작 템플릿이 사용됩니다. 

   | 템플릿 | 설명 |
   | --- | --- |
   | [sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-ag-setup) | Windows 장애 조치(failover) 클러스터를 만들고 SQL Server VM을 연결합니다. |
   | [sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-aglistener-setup) | 가용성 그룹 수신기를 만들고 내부 Load Balancer를 구성합니다. 이 템플릿은 Windows 장애 조치(failover) 클러스터가 **101-sql-vm-ag-setup** 템플릿으로 생성된 경우에만 사용할 수 있습니다. |
   | &nbsp; | &nbsp; |

가용성 그룹 만들기 및 내부 Load Balancer 만들기와 같은 가용성 그룹 구성의 기타 부분은 수동으로 수행해야 합니다. 이 문서에서는 자동 및 수동 단계의 시퀀스를 제공합니다.

이 문서에서는 Azure 빠른 시작 템플릿을 사용하여 가용성 그룹 환경을 구성하지만, [Azure Portal](availability-group-azure-portal-configure.md), [PowerShell 또는 Azure CLI](availability-group-az-commandline-configure.md)를 사용하거나 [수동으로](availability-group-manually-configure-tutorial.md)도 구성할 수 있습니다. 

> [!NOTE]
> 이제 Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 가용성 그룹 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [가용성 그룹 마이그레이션](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md)을 참조하세요. 
 

## <a name="prerequisites"></a>필수 구성 요소 
빠른 시작 템플릿을 사용하여 Always On 가용성 그룹의 설정을 자동화하려면 다음과 같은 필수 조건을 충족해야 합니다. 
- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- [SQL IaaS 에이전트 확장에 등록](sql-agent-extension-manually-register-single-vm.md)되었으며, 동일한 가용성 집합 또는 가용성 영역에서 [SQL Server 2016 이상의 Enterprise Edition을 실행하는 Azure의 도메인 가입 VM](./create-sql-vm-portal.md)이 하나 이상  
- SQL Server VM과 동일한 서브넷 내 가용성 그룹 수신기에 대 한 내부 Azure Load Balancer 및 사용 가능한 (엔터티에서 사용 하지 않음) IP 주소입니다. 


## <a name="permissions"></a>사용 권한
Azure 빠른 시작 템플릿을 사용하여 Always On 가용성 그룹을 구성하려면 다음 권한이 필요합니다. 

- 도메인에서 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정입니다.  예를 들어 도메인 관리자 계정에는 일반적으로 충분한 권한이 있습니다(예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server를 제어하는 도메인 사용자 계정. 


## <a name="create-cluster"></a>클러스터 만들기
SQL Server VM이 SQL IaaS 에이전트 확장에 등록된 후 SQL Server VM을 *SqlVirtualMachineGroups* 에 연결할 수 있습니다. 이 리소스는 Windows 장애 조치(failover) 클러스터의 메타데이터를 정의합니다. 메타데이터에는 버전, 에디션, 정규화된 도메인 이름, 클러스터와 SQL Server를 관리하는 Active Directory 계정 및 스토리지 계정(클라우드 감시)이 포함됩니다. 

*SqlVirtualMachineGroups* 리소스 그룹에 SQL Server VM을 추가하면 Windows 장애 조치(failover) 클러스터 서비스가 클러스터를 만들도록 부트스트랩된 다음, 해당 SQL Server VM이 클러스터에 연결됩니다. 이 단계는 **101-sql-vm-ag-setup** 빠른 시작 템플릿을 사용하여 자동화됩니다. 다음 단계를 사용하여 구현할 수 있습니다.

1. [**sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-ag-setup) 빠른 시작 템플릿으로 이동합니다. 그런 다음 **Azure에 배포** 를 선택하여 Azure Portal에서 빠른 시작 템플릿을 엽니다.
1. 필수 필드에 정보를 입력하여 Windows 장애 조치(failover) 클러스터 메타데이터를 구성합니다. 선택적 필드는 비워 둘 수 있습니다.

   다음 표에는 템플릿에 필요한 값이 나와 있습니다. 

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  SQL Server VM이 있는 구독입니다. |
   |**리소스 그룹** | SQL Server VM이 있는 리소스 그룹입니다. | 
   |**장애 조치(failover) 클러스터 이름** | 새 Windows 장애 조치(failover) 클러스터에 사용할 이름입니다. |
   | **기존 VM 목록** | 가용성 그룹에 참여하고 새 클러스터의 일부로 포함하려는 SQL Server VM입니다. 이러한 값을 쉼표와 공백으로 구분합니다(예: *SQLVM1, SQLVM2*). |
   | **SQL Server 버전**: | SQL Server VM의 SQL Server 버전입니다. 드롭다운 목록에서 이를 선택합니다. 현재 SQL Server 2016 및 SQL Server 2017 이미지만 지원됩니다. |
   | **기존 정규화된 도메인 이름** | SQL Server VM이 있는 도메인의 기존 FQDN입니다. |
   | **기존 도메인 계정** | 템플릿 배포 중에 [CNO](/windows-server/failover-clustering/prestage-cluster-adds)가 생성될 때 도메인에 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정입니다. 예를 들어 도메인 관리자 계정에는 일반적으로 충분한 권한이 있습니다(예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.| 
   | **도메인 계정 암호** | 이전에 언급한 도메인 사용자 계정의 암호입니다. | 
   | **기존 SQL 서비스 계정** | 가용성 그룹 배포 중에 [SQL Server 서비스](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)를 제어하는 도메인 사용자 계정입니다(예: account@domain.com). |
   | **SQL 서비스 암호** | SQL Server를 제어하는 도메인 사용자 계정에서 사용하는 암호입니다. |
   | **클라우드 감시 이름** | 클라우드 감시를 위해 만들고 사용할 새 Azure Storage 계정입니다. 이 이름은 수정할 수 있습니다. |
   | **\_artifacts 위치** | 이 필드는 기본적으로 설정되어 있으며 수정하면 안 됩니다. |
   | **\_artifacts 위치 SAS 토큰** | 이 필드는 의도적으로 비어 있습니다. |
   | &nbsp; | &nbsp; |

1. 사용 약관에 동의하는 경우 **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다. 그런 다음 **구매** 를 선택하여 빠른 시작 템플릿 배포를 완료합니다. 
1. 배포를 모니터링하려면 위쪽 탐색 배너의 **알림** 종 모양 아이콘에서 배포를 선택하거나 Azure Portal의 **리소스 그룹** 으로 이동합니다. **설정** 에서 **배포** 를 선택하고 **Microsoft.Template** 배포를 선택합니다. 

>[!NOTE]
> 템플릿 배포 중에 제공한 자격 증명은 배포 기간에만 저장됩니다. 배포가 완료되면 해당 암호가 제거됩니다. 클러스터에 SQL Server VM을 더 추가하는 경우 다시 입력하라는 메시지가 표시됩니다. 

## <a name="configure-quorum"></a>쿼럼 구성

디스크 감시는 가장 탄력적으로 수행되는 쿼럼 옵션이지만 가용성 그룹에 몇 가지 제한을 적용하는 Azure 공유 디스크가 필요합니다. 따라서 클라우드 감시는 Azure VM의 SQL Server에 대한 가용성 그룹을 호스트하는 클러스터에 권장되는 쿼럼 솔루션입니다. 

클러스터에 짝수 투표가 있는 경우 비즈니스 요구에 가장 적합한 [쿼럼 솔루션](hadr-cluster-quorum-configure-how-to.md)을 구성합니다. 자세한 내용은 [SQL Server VM에 대한 쿼럼](hadr-windows-server-failover-cluster-overview.md#quorum)을 참조하세요. 

## <a name="validate-cluster"></a>클러스터 유효성 검사 

Microsoft에서 지원하는 장애 조치(failover) 클러스터를 사용하려면 클러스터 유효성 검사를 통과해야 합니다. RDP(원격 데스크톱 프로토콜)와 같은 선호하는 방법을 사용하여 VM에 연결하고 클러스터가 유효성 검사를 통과했는지 확인한 다음 계속 진행합니다. 이렇게 하지 않으면 클러스터가 지원되지 않는 상태가 됩니다. 

FCM(장애 조치(Failover) 클러스터 관리자)를 사용하거나 다음 PowerShell 명령을 사용하여 클러스터 유효성을 검사할 수 있습니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>가용성 그룹 만들기 
[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) 또는 [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)을 사용하여 일반적인 방법으로 가용성 그룹을 직접 만듭니다. 

>[!IMPORTANT]
> 지금 수신기를 만들지 *마세요*. 이 작업은 4단계의 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿을 통해 자동으로 수행됩니다. 

## <a name="create-load-balancer"></a>부하 분산 장치 만들기

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Always On 가용성 그룹 수신기를 사용하려면 Azure Load Balancer의 내부 인스턴스가 필요합니다. 내부 Load Balancer는 더 빠른 장애 조치(failover) 및 다시 연결을 허용하는 가용성 그룹 수신기에 대한 "부동" IP 주소를 제공합니다. 가용성 그룹의 SQL Server VM이 동일한 가용성 집합의 일부인 경우 기본 부하 분산 장치를 사용할 수 있습니다. 그렇지 않으면 표준 부하 분산 장치를 사용해야 합니다. 

> [!IMPORTANT]
> 내부 부하 분산 장치는 SQL Server VM 인스턴스와 동일한 가상 네트워크에 있어야 합니다. 

내부 부하 분산 장치를 만들기만 하면 됩니다. 4단계에서 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿은 나머지 구성(예: 백 엔드 풀, 상태 프로브 및 부하 분산 규칙)을 처리합니다. 

1. Azure 포털에서 SQL Server 가상 머신을 포함하는 리소스 그룹을 엽니다. 
2. 리소스 그룹에서 **추가** 를 선택합니다.
3. **부하 분산 장치** 를 검색합니다. 검색 결과에서 **Microsoft** 에서 게시하는 **Load Balancer** 를 선택합니다.
4. **부하 분산 장치** 블레이드에서 **만들기** 를 선택합니다.
5. **부하 분산 장치 만들기** 대화 상자에서 다음과 같이 부하 분산 장치를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |부하 분산 장치를 나타내는 텍스트 이름을 입력합니다. 예를 들어 **sqlLB** 를 입력합니다. |
   | **형식** |**내부**: 대부분의 구현에서는 동일한 가상 네트워크 내에 있는 애플리케이션이 가용성 그룹에 연결할 수 있도록 하는 내부 부하 분산 장치를 사용합니다.  </br> **외부**: 애플리케이션이 공용 인터넷 연결을 통해 가용성 그룹에 연결할 수 있도록 합니다. |
   | **가상 네트워크** | SQL Server 인스턴스가 있는 가상 네트워크를 선택합니다. |
   | **서브넷** | SQL Server 인스턴스가 있는 서브넷을 선택합니다. |
   | **IP 주소 할당** |**정적** |
   | **개인 IP 주소** | 서브넷에서 사용 가능한 IP 주소를 지정합니다. |
   | **구독** |구독이 여러 개인 경우 이 필드가 나타날 수 있습니다. 이 리소스와 연결할 구독을 선택합니다. 일반적으로 가용성 그룹에 대한 모든 리소스와 동일한 구독입니다. |
   | **리소스 그룹** |SQL Server 인스턴스가 있는 리소스 그룹을 선택합니다. |
   | **위치** |SQL Server 인스턴스가 있는 Azure 위치를 선택합니다. |
   | &nbsp; | &nbsp; |

6. **만들기** 를 선택합니다. 


>[!IMPORTANT]
> 각 SQL Server VM에 대한 공용 IP 리소스에 표준 Load Balancer와 호환되는 표준 SKU가 있어야 합니다. VM 공용 IP 리소스의 SKU를 확인하려면 **리소스 그룹** 으로 이동하여 SQL Server VM에 대한 **공용 IP 주소** 리소스를 선택하고 **개요** 창의 **SKU** 아래에서 값을 찾습니다. 

## <a name="create-listener"></a>수신기 만들기 

가용성 그룹 수신기를 만들고 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿을 사용하여 내부 부하 분산 장치를 자동으로 구성합니다. 템플릿은 Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener 리소스를 프로비저닝합니다. SQL IaaS 에이전트 확장을 통해 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿은 다음 작업을 수행합니다.

- 배포 중에 제공한 IP 주소 값을 기준으로, 수신기에 대한 새 프런트 엔드 IP 리소스를 만듭니다. 
- 클러스터 및 내부 부하 분산 장치에 대한 네트워크 설정을 구성합니다. 
- 내부 부하 분산 장치, 상태 프로브 및 부하 분산 규칙에 대한 백 엔드 풀을 구성합니다.
- 지정된 IP 주소와 이름을 사용하여 가용성 그룹 수신기를 만듭니다.
 
>[!NOTE]
> **101-sql-vm-aglistener-setup** 은 Windows 장애 조치(failover) 클러스터가 **101-sql-vm-ag-setup** 템플릿으로 생성된 경우에만 사용할 수 있습니다.
   
   
내부 부하 분산 장치를 구성하고 가용성 그룹 수신기를 만들려면 다음을 수행합니다.
1. [sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-aglistener-setup) 빠른 시작 템플릿으로 이동한 다음, **Azure에 배포** 를 선택하여 Azure Portal 내에서 빠른 시작 템플릿을 시작합니다.
1. 필수 필드에 정보를 입력하여 내부 Load Balancer를 구성하고 가용성 그룹 수신기를 만듭니다. 선택적 필드는 비워 둘 수 있습니다. 

   다음 표에는 템플릿에 필요한 값이 나와 있습니다. 

   | **필드** | 값 |
   | --- | --- |
   |**리소스 그룹** | SQL Server VM 및 가용성 그룹이 있는 리소스 그룹입니다. | 
   |**기존 장애 조치(failover) 클러스터 이름** | SQL Server VM이 연결된 클러스터의 이름입니다. |
   | **기존 SQL 가용성 그룹**| SQL Server VM이 속한 가용성 그룹의 이름입니다. |
   | **기존 VM 목록** | 이전에 언급한 가용성 그룹에 속한 SQL Server VM의 이름입니다. 이름을 쉼표와 공백으로 구분합니다(예: *SQLVM1, SQLVM2*). |
   | **수신기** | 수신기에 할당하려는 DNS 이름입니다. 기본적으로 이 템플릿은 "aglistener"라는 이름을 지정하지만 변경할 수 있습니다. 이름은 15자를 초과할 수 없습니다. |
   | **수신기 포트** | 수신기에서 사용할 포트입니다. 일반적으로 이 포트의 기본값은 1433입니다. 템플릿에서 지정하는 포트 번호입니다. 그러나 기본 포트가 변경된 경우에는 수신기 포트에서 해당 값을 대신 사용해야 합니다. | 
   | **수신기 IP** | 수신기에서 사용할 IP 주소입니다. 이 주소는 템플릿 배포 중에 생성되므로 아직 사용되지 않은 주소를 제공합니다.  |
   | **기존 서브넷** | SQL Server VM의 내부 서브넷 이름(예: *default*)입니다. **리소스 그룹** 으로 이동하고, 가상 네트워크를 선택하고, **설정** 창에서 **서브넷** 을 선택하고, **이름** 아래의 값을 복사하여 이 값을 확인할 수 있습니다. |
   | **기존 내부 Load Balancer** | 3단계에서 만든 내부 부하 분산 장치의 이름입니다. |
   | **프로브 포트** | 내부 부하 분산 장치에서 사용할 프로브 포트입니다. 템플릿은 기본적으로 59999를 사용하지만 이 값을 변경할 수 있습니다. |
   | &nbsp; | &nbsp; |

1. 사용 약관에 동의하는 경우 **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다. **구매** 를 선택하여 빠른 시작 템플릿 배포를 완료합니다. 
1. 배포를 모니터링하려면 위쪽 탐색 배너의 **알림** 종 모양 아이콘에서 배포를 선택하거나 Azure Portal의 **리소스 그룹** 으로 이동합니다. **설정** 에서 **배포** 를 선택하고 **Microsoft.Template** 배포를 선택합니다. 

>[!NOTE]
>배포에 실패하는 경우 **101-sql-vm-aglistener-setup** 빠른 시작 템플릿을 다시 배포하기 전에 PowerShell을 사용하여 [새로 만든 수신기를 수동으로 제거](#remove-listener)해야 합니다. 

## <a name="remove-listener"></a>수신기 제거
템플릿을 통해 구성된 가용성 그룹 수신기를 나중에 제거해야 하는 경우 SQL IaaS 에이전트 확장을 이용해야 합니다. 수신기가 SQL IaaS 에이전트 확장을 통해 등록되었으므로 SQL Server Management Studio를 통해 수신기를 삭제하는 것만으로는 충분하지 않습니다. 

가장 좋은 방법은 Azure PowerShell에서 다음 코드 조각을 사용하여 SQL IaaS 에이전트 확장을 통해 삭제하는 것입니다. 이렇게 하면 SQL IaaS 에이전트 확장에서 가용성 그룹 수신기 메타데이터가 제거됩니다. 또한 가용성 그룹에서 수신기가 물리적으로 삭제됩니다. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>일반 오류
이 섹션에서는 몇 가지 알려진 문제와 가능한 해결 방법을 설명합니다. 

**가용성 그룹 '\<AG-Name>'에 대한 가용성 그룹 수신기가 이미 있음** 가용성 그룹 수신기의 Azure 빠른 시작 템플릿에서 사용된 선택한 가용성 그룹에 이미 수신기가 포함되어 있습니다. 실제로 가용성 그룹 내에 있거나 해당 메타데이터가 SQL IaaS 에이전트 확장 내에 유지됩니다. **101-sql-vm-aglistener-setup** 빠른 시작 템플릿을 다시 배포하기 전에 [PowerShell](#remove-listener)을 사용하여 수신기를 제거합니다. 

**연결은 기본 복제본에서만 작동함** 이 동작은 내부 부하 분산 장치의 구성이 일관되지 않은 상태로 유지된 실패한 **101-sql-vm-aglistener-setup** 템플릿 배포로 인한 것일 수 있습니다. 백 엔드 풀에 가용성 집합이 나열되는지, 상태 프로브 및 부하 분산 규칙에 대한 규칙이 있는지 확인합니다. 누락된 항목이 있으면 내부 부하 분산 장치의 구성이 일관되지 않은 상태입니다. 

이 동작을 해결하려면 [PowerShell](#remove-listener)을 사용하여 수신기를 제거하고 Azure Portal을 통해 내부 Load Balancer를 삭제한 다음, 3단계에서 다시 시작합니다. 

**BadRequest - SQL 가상 머신 목록만 업데이트 가능** 이 오류는 수신기가 SSMS(SQL Server Management Studio)를 통해 삭제되었지만 SQL IaaS 에이전트 확장에서 삭제되지 않은 경우 **101-sql-vm-aglistener-setup** 템플릿을 배포할 때 발생할 수 있습니다. SSMS를 통해 수신기를 삭제해도 SQL IaaS 에이전트 확장에서 수신기의 메타데이터는 제거되지 않습니다. 수신기는 [PowerShell](#remove-listener)을 통해 리소스 공급자에서 삭제해야 합니다. 

**도메인 계정이 없음** 이 오류는 두 가지 원인이 있을 수 있습니다. 지정된 도메인 계정이 없거나 데이터 [UPN(사용자 계정 이름)](/windows/desktop/ad/naming-properties#userprincipalname)이 없기 때문입니다. **101-sql-vm-ag-setup** 템플릿에는 UPN 형태(즉, user@domain.com)의 도메인 계정이 필요한데, 일부 도메인 계정에서 이 데이터가 누락되었을 수 있습니다. 일반적으로 서버가 도메인 컨트롤러로 승격될 때 로컬 사용자가 첫 번째 도메인 관리자 계정으로 마이그레이션되었거나 사용자가 PowerShell을 통해 생성된 경우에 이러한 상황이 발생합니다. 

계정이 있는지 확인합니다. 계정이 있는 경우 두 번째 상황일 수 있습니다. 이 문제를 해결하려면 다음을 수행합니다.

1. 도메인 컨트롤러에서 **서버 관리자** 의 **도구** 옵션을 통해 **Active Directory 사용자 및 컴퓨터** 창을 엽니다. 
2. 왼쪽 창에서 **사용자** 를 선택하여 계정으로 이동합니다.
3. 계정을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
4. **계정** 탭을 선택합니다. **사용자 로그온 이름** 상자가 비어 있는 경우 오류의 원인이 됩니다. 

    ![비어 있는 사용자 계정은 UPN 누락을 나타냅니다.](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. 사용자 이름과 일치하도록 **사용자 로그온 이름** 상자에 입력하고 드롭다운 목록에서 적절한 도메인을 선택합니다. 
6. **적용** 을 선택하여 변경 내용을 저장한 다음, **확인** 을 선택하여 대화 상자를 닫습니다. 

이러한 변경을 수행한 후에는 Azure 빠른 시작 템플릿을 한 번 더 배포해 봅니다. 


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [SQL Server VM 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM에 대한 FAQ](frequently-asked-questions-faq.yml)
* [SQL Server VM에 대한 가격 책정 지침](pricing-guidance.md)
* [Azure VM에서 SQL Server 새로운 내용](doc-changes-updates-release-notes-whats-new.md)
* [SQL Server VM에 대한 라이선스 모델 전환](licensing-model-azure-hybrid-benefit-ahb-change.md)
