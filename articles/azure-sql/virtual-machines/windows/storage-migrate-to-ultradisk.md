---
title: 로그 디스크를 Ultra 디스크로 마이그레이션
description: 고성능 및 짧은 대기 시간의 이점을 누리기 위해 Azure VM(Virtual Machine) 로그 디스크의 SQL Server를 Azure Ultradisk로 마이그레이션하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 54955a953b8386b3dc9b5b4ba814b4826079ef54
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165230"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>로그 디스크를 Ultra 디스크로 마이그레이션
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Ultra Disks는 Azure VM(Virtual Machine)의 SQL Server에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. 

이 문서에서는 울트라 디스크가 제공하는 성능상 혜택을 활용하기 위해 로그 디스크를 울트라 SSD로 마이그레이션하는 방법을 설명합니다. 

## <a name="back-up-database"></a>데이터베이스 백업

데이터베이스의 [전체 백업](backup-restore.md)을 완료합니다. 

## <a name="attach-disk"></a>디스크 연결

VM에서 울트라 디스크 호환성을 사용하도록 설정한 후 가상 머신에 Ultra SSD를 연결합니다. 

울트라 디스크는 VM 크기 및 지역의 하위 집합에서 지원됩니다. 진도를 나가기 전에 VM이 울트라 디스크를 지원하는 지역, 영역 및 크기인지 확인하세요. Azure CLI 또는 PowerShell을 사용하여 [VM 크기 및 지역을 결정하고 유효성을 검사](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability)할 수 있습니다. 

### <a name="enable-compatibility"></a>호환성 사용

호환성을 사용하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 가상 머신으로 이동합니다. 
1. 가상 머신을 중지/할당 취소합니다. 
1. **설정** 아래에서 **디스크** 를 선택하고 **추가 설정** 을 선택합니다. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Azure Portal의 설정에서 디스크에 대한 추가 설정 선택":::

1. **예** 를 선택하여 **Ultra 디스크 호환성을 사용하도록 설정** 합니다. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="예 옵션을 보여 주는 스크린샷.":::

1. **저장** 을 선택합니다. 



### <a name="attach-disk"></a>디스크 연결

Azure Portal를 사용하여 가상 머신에 울트라 디스크를 연결합니다. 자세한 내용은 [울트라 디스크 연결](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk)을 참조하세요.

디스크가 연결되면 Azure Portal을 사용하여 VM을 다시 한번 시작합니다. 



## <a name="format-disk"></a>디스크 형식 지정

가상 머신에 연결하고 울트라 디스크의 형식을 지정합니다.  

울트라 디스크의 형식을 지정하려면 다음 단계를 수행합니다.

1. RDP(원격 데스크톱 프로토콜)를 사용하여 VM에 연결.
1. [디스크 관리](/windows-server/storage/disk-management/overview-of-disk-management)를 사용하여 새로 연결된 울트라 디스크를 형식 지정하고 분할합니다. 


## <a name="use-disk-for-log"></a>로그에 디스크 사용

새 로그 드라이브를 사용하도록 SQL Server를 구성합니다. T-SQL(Transact-SQL) 또는 SSMS(SQL Server Management Studio)를 사용하여 해당 작업을 수행할 수 있습니다. SQL Server 서비스 계정에 사용되는 계정은 새 로그 파일 위치를 완전히 컨트롤해야 합니다. 

### <a name="configure-permissions"></a>권한 구성

1. SQL Server에서 사용하는 서비스 계정을 확인합니다. SQL Server 구성 관리자 또는 Services.msc를 사용하여 해당 작업을 수행할 수 있습니다.
1. 새 디스크로 이동합니다. 
1. 로그 파일에 사용할 폴더 한 개(또는 여러 개)를 만듭니다. 
1. 해당 폴더를 마우스 오른쪽 단추로 클릭한 다음 **속성** 을 선택합니다.
1. **보안** 탭에서 SQL Server 서비스 계정에 대해 완전한 컨트롤 액세스 권한을 부여합니다. 
1. **확인** 을 선택하여 설정을 저장합니다. 
1. SQL 데이터를 사용하려는 모든 루트 수준 폴더에 대해 이 작업을 반복합니다. 

### <a name="use-new-log-drive"></a>새 로그 드라이브 사용 

사용 권한이 부여된 후에는 T-SQL(Transact-SQL) 또는 SSMS(SQL Server Management Studio)를 사용하여 데이터베이스를 분리하고 기존 로그 파일을 새 위치로 이동합니다.

   > [!CAUTION]
   > 데이터베이스를 분리하면 오프라인으로 전환되어 연결이 끊기고 진행 중인 트랜잭션이 롤백됩니다. 가동 정지된 유지 관리 기간에 주의하여 진행하세요. 



# <a name="transact-sql-t-sql"></a>[T-SQL(Transact-SQL)](#tab/tsql)

T-SQL을 사용하여 기존 파일을 새 위치로 이동합니다.

1. SQL Server Management Studio의 데이터베이스에 연결하고 **새 쿼리** 창을 엽니다. 
1. 기존 파일 및 위치를 가져옵니다.

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. 데이터베이스를 분리합니다. 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. 파일 탐색기를 사용하여 로그 파일을 울트라 디스크의 새 위치로 이동합니다. 

1. 새 파일 위치를 지정하여 데이터베이스를 연결합니다. 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

이제 데이터베이스는 새 위치에 자리한 로그와 함께 온라인 상태가 됩니다. 



# <a name="sql-server-management-studio-ssms"></a>[SSMS(SQL Server Management Studio)](#tab/ssms)

SSMS를 사용하여 기존 파일을 새 위치로 이동합니다.

1. SSMS(SQL Server Management Studio)에서 데이터베이스에 연결합니다. 
1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택한 다음, **파일** 을 선택합니다. 
1. 기존 파일의 경로를 적어 둡니다. 
1. **확인** 을 선택하여 대화 상자를 닫습니다. 
1. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **태스크** > **분리** 를 선택합니다. 
1. 마법사에 따라 데이터베이스를 분리합니다. 
1. 파일 탐색기를 사용하여 로그 파일을 수동으로 새 위치로 이동합니다.
1. SQL Server Management Studio에서 데이터베이스 연결
   1. **개체 탐색기** 에서 **데이터베이스** 를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 연결** 을 선택합니다. 
   1. 대화 상자를 사용하여 새로운 위치에 있는 로그 파일을 포함한 각 파일을 추가합니다. 
   1. **확인** 을 선택하여 데이터베이스를 연결합니다. 

이제 데이터베이스는 새 위치에 자리한 로그와 함께 온라인 상태가 됩니다.

---


## <a name="next-steps"></a>다음 단계

성능 향상을 위한 추가 설정을 보려면 [성능 모범 사례](./performance-guidelines-best-practices-checklist.md)를 검토합니다. 

Azure Virtual Machines의 SQL Server에 대한 개요는 다음 문서를 참조하세요.

- [Windows VM의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux VM의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)