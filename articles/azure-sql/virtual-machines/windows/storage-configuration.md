---
title: SQL Server VM용 스토리지 구성 | Microsoft Docs
description: 이 항목에서는 Azure가 프로비저닝(Azure Resource Manager 배포 모델) 중에 SQL Server VM용 스토리지를 구성하는 방법을 설명합니다. 또한 기존 SQL Server VM에 대한 스토리지를 구성하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 9885950552e7bb62114f8588e95140f16c3f1c04
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071951"
---
# <a name="configure-storage-for-sql-server-vms"></a>SQL Server VM에 대한 스토리지 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VM(가상 머신)에서 SQL Server용 스토리지를 구성하는 방법을 설명합니다.

마켓플레이스 이미지를 통해 배포된 SQL Server VM은 배포 중에 수정할 수 있는 기본 [스토리지 모범 사례](performance-guidelines-best-practices-storage.md)를 자동으로 따릅니다. 이러한 구성 설정 중 일부는 배포 후 변경할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

자동화된 스토리지 구성 설정을 사용하려면 가상 머신에는 다음과 같은 특성이 필요합니다.

* [SQL Server 갤러리 이미지](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)로 프로비저닝되거나 [SQL IaaS 확장]()으로 등록됩니다.
* [Resource Manager 배포 모델](../../../azure-resource-manager/management/deployment-models.md)을 사용합니다.
* [프리미엄 SSD](../../../virtual-machines/disks-types.md)를 사용합니다.

## <a name="new-vms"></a>새 VM

다음 섹션에서는 새 SQL Server 가상 머신에 대한 스토리지를 구성하는 방법을 설명합니다.

### <a name="azure-portal"></a>Azure portal

SQL Server 갤러리 이미지를 사용하여 Azure VM을 프로비전하는 경우 **SQL Server 설정** 탭에서 **구성 변경** 을 선택하여 성능 최적화 스토리지 구성 페이지를 엽니다. 값을 기본값 그대로 두거나 워크로드에 따라 사용자의 요구에 가장 적합하게 디스크 구성 유형을 수정할 수 있습니다.

![SQL Server 설정 탭과 변경 구성 옵션을 강조 표시하는 스크린샷](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

**스토리지 최적화** 에서 SQL Server를 배포하는 워크로드의 유형을 선택합니다. **일반** 최적화 옵션을 사용하는 경우 기본적으로 최대 5,000 IOPS를 포함하는 하나의 데이터 디스크가 있으며 데이터, 트랜잭션 로그 및 TempDB 스토리지에 대해 동일한 드라이브를 사용합니다.

**트랜잭션 처리**(OLTP) 또는 **데이터 웨어하우징** 을 선택하면 데이터용 및 트랜잭션 로그용으로 별도의 디스크가 생성되고 TempDB에 로컬 SSD가 사용됩니다. **트랜잭션 처리** 와 **데이터 웨어하우징** 간에는 스토리지 차이점이 없지만 사용자의 [스트라이프 구성 및 추적 플래그](#workload-optimization-settings)를 변경합니다. Premium Storage를 선택하면 데이터 드라이브에 *ReadOnly* 캐싱이 설정되고 [SQL Server VM 성능 모범 사례](./performance-guidelines-best-practices-checklist.md)에 따라 로그 드라이브에 없음이 설정됩니다.

![프로비전하는 동안 SQL Server VM 스토리지 구성](./media/storage-configuration/sql-vm-storage-configuration.png)

디스크 구성은 완전히 사용자 지정할 수 있으므로 SQL Server VM에 필요한 스토리지 토폴로지, 디스크 유형 및 IOP를 구성할 수 있습니다. 또한 SQL Server VM이 지원되는 지역(미국 동부 2, 동남아시아 및 북유럽) 중 하나에 있고 [구독에 Ultra Disk를 사용](../../../virtual-machines/disks-enable-ultra-ssd.md)하도록 설정한 경우 **디스크 유형** 옵션으로 UltraSSD(미리 보기)를 사용할 수 있습니다.

또한 디스크에 대한 캐싱을 설정할 수 있습니다. Azure VM은 [프리미엄 디스크](../../../virtual-machines/disks-types.md#premium-ssds)를 사용할 경우 [Blob 캐시](../../../virtual-machines/premium-storage-performance.md#disk-caching)라는 다중 계층 캐싱 기술이 적용됩니다. Blob 캐시는 캐싱을 위해 가상 머신 RAM과 로컬 SSD의 조합을 사용합니다.

프리미엄 SSD에 대한 디스크 캐싱은 *ReadOnly*, *ReadWrite* 또는 없음일 수 있습니다.

- *ReadOnly* 캐싱은 Premium Storage에 저장된 SQL Server 데이터 파일에 매우 유용합니다. *ReadOnly* 캐싱은 낮은 읽기 대기 시간과 높은 읽기 IOPS 및 처리량을 제공합니다. 읽기가 VM 메모리 및 로컬 SSD 내에 있는 캐시에서 수행되기 때문입니다. 이러한 읽기는 Azure Blob Storage에서 데이터 디스크를 읽는 것보다 훨씬 빠릅니다. Premium Storage는 캐시에서 처리된 읽기를 디스크 IOPS 및 처리량으로 계산하지 않습니다. 따라서 애플리케이션은 더 높은 총 IOPS 및 처리량을 달성할 수 있습니다.
- SQL Server 로그 파일을 호스트하는 디스크에는 없음 캐시 구성을 사용해야 합니다. 로그 파일은 순차적으로 기록되고 *ReadOnly* 캐싱이 도움이 되지 않기 때문입니다.
- *ReadWrite* 캐싱은 SQL Server 파일을 호스트하는 데 사용할 수 없습니다. SQL Server는 *ReadWrite* 캐시와의 데이터 일관성을 지원하지 않기 때문입니다. 쓰기가 *ReadOnly* Blob 캐시 계층을 통과할 경우 읽기가 *ReadOnly* Blob 캐시 용량을 불필요하게 소비하고 대기 시간을 약간 증가시킵니다.


   > [!TIP]
   > 스토리지 구성이 선택한 VM 크기에 적용되는 제한과 일치해야 합니다. VM 크기의 성능 용량을 초과하는 스토리지 매개 변수를 선택하면 다음 경고가 발생합니다. `The desired performance might not be reached due to the maximum virtual machine disk performance cap`. 디스크 유형을 변경하여 IOP를 줄이거나 VM 크기를 늘려 성능 용량 제한을 높입니다. 이 경우 프로비저닝이 중지되지 않습니다.


선택을 기반으로 Azure는 VM을 만든 후에 다음과 같은 스토리지 구성 작업을 수행합니다.

* 프리미엄 SSD를 만들고 가상 머신에 연결합니다.
* SQL Server에 액세스할 수 있도록 데이터 디스크를 구성합니다.
* 지정된 크기와 성능(IOPS 및 처리량) 요구 사항에 따라 스토리지 풀에 데이터 디스크를 구성합니다.
* 가상 컴퓨터에 새 드라이브와 스토리지 풀을 연결합니다.
* 지정한 워크로드 유형(데이터 웨어하우징, 트랜잭션 처리 또는 일반)에 따라 새 드라이브를 최적화합니다.

Azure Portal에서 SQL Server VM을 만드는 방법의 전체 연습은 [프로비전 자습서](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)를 참조하세요.

### <a name="resource-manager-templates"></a>리소스 관리자 템플릿

다음 Resource Manager 템플릿을 사용하는 경우 두 개의 프리미엄 데이터 디스크는 스토리지 풀 구성 없이 기본적으로 연결됩니다. 그러나 이러한 템플릿을 사용자 지정하여 가상 머신에 연결된 프리미엄 데이터 디스크의 수를 변경할 수 있습니다.

* [자동화된 Backup을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autobackup)
* [자동화된 패치를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autopatching)
* [AKV 통합을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-keyvault)

### <a name="quickstart-template"></a>빠른 시작 템플릿

다음 빠른 시작 템플릿을 사용하여 스토리지 최적화를 기반으로 SQL Server VM을 배포할 수 있습니다.

* [스토리지 최적화를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage/)
* [UltraSSD를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>기존 VM

기존 SQL Server VM의 경우 Azure 포털에서 일부 스토리지 설정을 수정할 수 있습니다. [SQL 가상 머신 리소스](manage-sql-vm-portal.md#access-the-resource)를 열고 **개요** 를 선택합니다. SQL Server 개요 페이지에 VM의 현재 스토리지 사용량이 표시됩니다. VM에 있는 모든 드라이브는 이 차트에 표시됩니다. 각 드라이브의 경우 스토리지 공간은 네 개의 섹션에 표시됩니다.

* SQL data
* SQL 로그
* 기타(비 SQL 스토리지)
* 사용 가능

스토리지 설정을 수정하려면 **설정** 에서 **구성** 을 선택합니다.

![구성 옵션 및 스토리지 사용량 섹션을 강조 표시하는 스크린샷](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

SQL Server VM 만들기 프로세스 중에 구성된 드라이브의 디스크 설정을 수정할 수 있습니다. **드라이브 확장** 을 선택하면 드라이브 수정 페이지가 열리며 디스크 유형을 변경하고 디스크를 더 추가할 수 있습니다.

![기존 SQL Server VM에 대한 스토리지 구성](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>자동화된 변경 내용

이 섹션에서는 Azure가 SQL Server VM을 프로비저닝하거나 Azure Portal에서 구성하는 동안 자동으로 수행하는 스토리지 구성 변경에 대한 참조를 제공합니다.

* Azure는 VM에서 선택된 스토리지로부터 스토리지 풀을 구성합니다. 이 항목의 다음 섹션에서는 스토리지 풀 구성에 대해 자세히 설명합니다.
* 자동 스토리지 구성은 항상 [프리미엄 SSD](../../../virtual-machines/disks-types.md) P30 데이터 디스크를 사용합니다. 따라서 선택한 테라바이트 수와 VM에 연결된 데이터 디스크의 수 간에 1:1 매핑이 됩니다.

가격 책정 정보는 [디스크 스토리지](https://azure.microsoft.com/pricing/details/storage) 탭의 **스토리지 가격 책정** 페이지를 참조하세요.

### <a name="creation-of-the-storage-pool"></a>스토리지 풀 만들기

Azure는 다음 설정을 사용하여 SQL Server VM에 스토리지 풀을 만듭니다.

| 설정 | 값 |
| --- | --- |
| 스트라이프 크기 |256KB(데이터 웨어하우징); 64KB(트랜잭션) |
| 디스크 크기 |각각 1TB |
| 캐시 |읽기 |
| 할당 크기 |64KB NTFS 할당 단위 크기 |
| 복구 | 단순 복구(복원력 없음) |
| 열 수 |데이터 디스크 수 최대 8<sup>1</sup> |


<sup>1</sup> 스토리지 풀을 만든 후에 스토리지 풀에서 열 수를 변경할 수 없습니다.


### <a name="workload-optimization-settings"></a>워크로드 최적화 설정

다음 테이블에서는 사용할 수 있는 세 가지 워크로드 유형 옵션 및 이에 해당하는 최적화를 설명합니다.

| 워크로드 유형 | Description | 최적화 |
| --- | --- | --- |
| **일반** |대부분의 워크로드를 지원하는 기본 설정 |None |
| **트랜잭션 처리** |기존의 데이터베이스 OLTP 워크로드용으로 스토리지를 최적화합니다. |추적 플래그 1117<br/>추적 플래그 1118 |
| **데이터 웨어하우징** |분석 및 보고 워크로드용으로 스토리지를 최적화합니다. |추적 플래그 610<br/>추적 플래그 1117 |

> [!NOTE]
> 스토리지 구성 단계에서 선택하여 SQL Server 가상 머신을 프로비저닝할 때만 워크로드 유형을 지정할 수 있습니다.

## <a name="enable-caching"></a>캐싱 설정

디스크 수준에서 캐싱 정책을 변경합니다. Azure Portal, [PowerShell](/powershell/module/az.compute/set-azvmdatadisk) 또는 [Azure CLI](/cli/azure/vm/disk)를 사용하여 수행할 수 있습니다.

Azure Portal에서 캐싱 정책을 변경하려면 다음 단계를 따르세요.

1. SQL Server 서비스를 중지합니다.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 가상 머신으로 이동하여 **설정** 에서 **디스크** 를 선택합니다.

   ![Azure Portal의 VM 디스크 구성 블레이드를 보여 주는 스크린샷](./media/storage-configuration/disk-in-portal.png)

1. 드롭다운에서 디스크에 대한 적절한 캐싱 정책을 선택합니다.

   ![Azure Portal의 디스크 캐싱 정책 구성을 보여 주는 스크린샷](./media/storage-configuration/azure-disk-config.png)

1. 변경 내용이 적용되면 SQL Server VM을 다시 부팅하고 SQL Server 서비스를 시작합니다.


## <a name="enable-write-accelerator"></a>Write Accelerator 사용

쓰기 가속화는 M-시리즈 VM(가상 머신)에서만 사용할 수 있는 디스크 기능입니다. 쓰기 가속화의 목적은 대용량 중요 업무용 OLTP 워크로드 또는 데이터 웨어하우스 환경으로 인해 한 자릿수 I/O 대기 시간이 필요할 때 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 개선하는 것입니다.

쓰기 가속화 정책을 변경하기 전에 모든 SQL Server 활동을 중지하고 SQL Server 서비스를 종료합니다.

디스크가 스트라이프된 경우 각 디스크에 대해 개별적으로 쓰기 가속을 사용하도록 설정하고 변경하기 전에 Azure VM을 종료해야 합니다.

Azure Portal을 사용하여 쓰기 가속을 사용하도록 설정하려면 다음 단계를 따르세요.

1. SQL Server 서비스를 중지합니다. 디스크가 스트라이프된 경우 가상 머신을 종료합니다.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 가상 머신으로 이동하여 **설정** 에서 **디스크** 를 선택합니다.

   ![Azure Portal의 VM 디스크 구성 블레이드를 보여 주는 스크린샷](./media/storage-configuration/disk-in-portal.png)

1. 드롭다운에서 디스크에 대한 **쓰기 가속기** 로 캐시 옵션을 선택합니다.

   ![쓰기 가속기 캐시 정책을 보여 주는 스크린샷](./media/storage-configuration/write-accelerator.png)

1. 변경 사항이 적용되면 가상 머신과 SQL Server 서비스를 시작합니다.

## <a name="disk-striping"></a>디스크 스트라이프

더 많은 처리량이 필요한 경우 추가 데이터 디스크를 추가하고 디스크 스트라이프를 사용할 수 있습니다. 데이터 디스크 수를 확인하려면 로그 및 tempdb를 포함하여 SQL Server 데이터 파일에 필요한 처리량 및 대역폭을 분석합니다. 처리량 및 대역폭 제한은 VM 크기에 따라 달라집니다. 자세한 내용은 [VM 크기](../../../virtual-machines/sizes.md)를 참조하세요.


* Windows 8/Windows Server 2012 이상인 경우 다음 지침을 통해 [스토리지 공간](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))을 사용합니다.

  1. 인터리브(스트라이프 크기)를 64KB(65,536 바이트)로 설정하여 파티션 잘못 맞춤으로 인한 성능 영향을 방지합니다. 이는 PowerShell로 설정되어야 합니다.

  2. 열 수를 실제 디스크 수로 설정합니다. 8개 이상의 디스크(서버 관리자 UI 아님)를 구성하는 경우 PowerShell을 사용합니다.

예를 들어 다음 PowerShell은 인터리브 크기가 64KB이고 열 수가 스토리지 풀의 물리적 디스크 양과 동일한 새 스토리지 풀을 만듭니다.

# <a name="windows-server-2016-"></a>[Windows Server 2016 이상](#tab/windows2016)

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Windows Storage on <VM Name>" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false
  ```

Windows Server 2016 이상에서 `-StorageSubsystemFriendlyName`의 기본값은 `Windows Storage on <VM Name>`입니다.



# <a name="windows-server-2008---2012-r2"></a>[Windows Server 2008 - 2012 R2](#tab/windows2012)



  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces on <VMName>" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

Windows Server 2008 ~ 2012 R2에서 `-StorageSubsystemFriendlyName`의 기본값은 `Storage Spaces on <VMName>`입니다. 

---


  * Windows 2008 R2 또는 이전 버전에서는 동적 디스크(OS 스트라이프 볼륨)를 사용할 수 있으며 스트라이프 크기는 항상 64KB입니다. 이 옵션은 Windows 8/Windows Server 2012부터 사용되지 않습니다. 자세한 내용은 [가상 디스크 서비스가 Windows 스토리지 관리 API로 전환](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)에 있는 지원 설명을 참조하세요.

  * [SQL Server 장애 조치(failover) 클러스터 인스턴스](./failover-cluster-instance-storage-spaces-direct-manually-configure.md)와 함께 [S2D(스토리지 공간 다이렉트)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)를 사용하는 경우 단일 풀을 구성해야 합니다. 해당 단일 풀에서 여러 볼륨을 만들 수 있지만 모두 동일한 캐싱 정책 등의 동일한 특성을 공유합니다.

  * 예상되는 부하에 따라 스토리지 풀에 연결되는 디스크 수를 결정합니다. VM 크기가 다르면 연결된 데이터 디스크 수도 다를 수 있다는 점에 유의하세요. 자세한 내용은 [가상 머신의 크기](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조하세요.

