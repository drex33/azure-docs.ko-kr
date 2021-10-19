---
title: SQL IaaS 에이전트 확장을 통해 Azure에 여러 SQL VM 등록
description: SQL IaaS 에이전트 확장을 통해 SQL Server VM을 대량 등록하여 관리 효율성을 개선합니다.
services: virtual-machines-windows
documentationcenter: na
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bc4bfc48c9b8f6891c45730215f2c49e84467a42
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161893"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>SQL IaaS 에이전트 확장을 통해 Azure에 여러 SQL VM 등록
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 `Register-SqlVMs` Azure PowerShell cmdlet을 사용하여 Azure에서 [SQL IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)에 SQL Server VM(가상 머신)을 대량 등록하는 방법을 설명합니다. 


이 문서에서는 SQL Server VM을 대량으로 수동 등록하는 방법을 설명합니다. 또는 [모든 SQL Server VM을 자동으로](sql-agent-extension-automatic-registration-all-vms.md) 등록하거나 [개별 SQL Server VM을 수동으로](sql-agent-extension-manually-register-single-vm.md) 등록할 수 있습니다. 

> [!NOTE]
> 2021년 9월부터 전체 모드에서 SQL IaaS 확장에 등록하는 것은 더 이상 SQL Server 서비스를 다시 시작하지 않아도 됩니다. 

## <a name="overview"></a>개요

`Register-SqlVMs` cmdlet을 사용하여 지정된 구독, 리소스 그룹 목록 또는 특정 가상 머신 목록에 있는 모든 가상 머신을 등록할 수 있습니다. 이 cmdlet은 [경량 관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)에서 가상 머신을 등록한 다음, [보고서 및 로그 파일](#output-description)을 둘 다 생성합니다. 

등록 프로세스는 위험 없이 진행되고, 가동 중지 시간이 없으며, SQL Server 서비스 또는 가상 머신을 다시 시작하지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소

확장에 SQL Server VM을 등록하려면 다음이 필요합니다. 

- [**Microsoft.SqlVirtualMachine** 리소스 공급자에 등록되었으며](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp), 등록되지 않은 SQL Server 가상 머신을 포함하는 [Azure 구독](https://azure.microsoft.com/free/)입니다. 
- 가상 머신을 등록하는 데 사용되는 클라이언트 자격 증명은 **Virtual Machine 기여자**, **기여자** 또는 **소유자** 와 같은 Azure 역할 중 하나에 존재합니다. 
- 최신 버전의 [Az PowerShell](/powershell/azure/new-azureps-module-az)(최소 5.0). 


## <a name="get-started"></a>시작하기

계속하기 전에 먼저 스크립트의 로컬 복사본을 만들고, PowerShell 모듈로 가져온 다음, Azure에 연결해야 합니다. 

### <a name="create-the-script"></a>스크립트를 작성합니다.

스크립트를 만들려면 이 문서의 끝부분부터 [전체 스크립트](#full-script)를 복사하고 `RegisterSqlVMs.psm1`로 로컬로 저장합니다. 

### <a name="import-the-script"></a>스크립트 가져오기

스크립트를 만든 후에는 PowerShell 터미널에서 모듈로 가져올 수 있습니다. 

관리 PowerShell 터미널을 열고 `RegisterSqlVMs.psm1` 파일을 저장한 위치로 이동합니다. 그런 다음, 아래 PowerShell cmdlet을 실행하여 스크립트를 모듈로 가져옵니다. 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Azure에 연결

다음 PowerShell cmdlet을 사용하여 Azure에 연결합니다.

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>구독 목록의 모든 VM 

다음 cmdlet을 사용하여 구독 목록의 모든 SQL Server 가상 머신을 등록합니다.

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

예제 출력: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>단일 구독의 모든 VM

다음 cmdlet을 사용하여 단일 구독의 모든 SQL Server 가상 머신을 등록합니다. 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

예제 출력:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>여러 리소스 그룹의 모든 VM

다음 cmdlet을 사용하여 단일 구독 내 여러 리소스 그룹의 모든 SQL Server 가상 머신을 등록합니다.

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

예제 출력:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>리소스 그룹의 모든 VM

다음 cmdlet을 사용하여 단일 리소스 그룹의 모든 SQL Server 가상 머신을 등록합니다. 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

예제 출력:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>단일 리소스 그룹의 특정 VM

다음 cmdlet을 사용하여 단일 리소스 그룹 내에 있는 특정 SQL Server 가상 머신을 등록합니다.

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

예제 출력:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>특정 VM

다음 cmdlet을 사용하여 특정 SQL Server 가상 머신을 등록합니다. 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

예제 출력:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>출력 설명

`Register-SqlVMs` cmdlet을 사용할 때마다 보고서와 로그 파일이 둘 다 생성됩니다. 

### <a name="report"></a>보고서

보고서는 `RegisterSqlVMScriptReport<Timestamp>.txt`라는 `.txt` 파일로 생성됩니다. 여기서 timestamp는 cmdlet이 시작된 시간입니다. 보고서에는 다음 세부 정보가 나열됩니다.

| **출력 값** | **설명** |
| :--------------  | :-------------- | 
| 액세스 권한이 없거나 자격 증명이 잘못되었기 때문에 많은 구독을 등록하지 못함 | 여기에는 제공된 인증에 문제가 발생한 구독의 수와 목록이 제공됩니다. 자세한 오류는 로그에서 구독 ID를 검색하여 찾을 수 있습니다. | 
| 리소스 공급자에 등록되지 않았기 때문에 시도할 수 없는 구독 수 | 이 섹션에는 SQL IaaS 에이전트 확장에 등록되지 않은 구독 수와 목록이 포함됩니다. |
| 찾은 총 VM 수 | cmdlet에 전달된 매개 변수 범위에서 발견된 가상 머신 개수입니다. | 
| VM이 이미 등록됨 | 확장에 이미 등록되었기 때문에 건너뛴 가상 머신의 수입니다. |
| 성공적으로 등록된 VM 수 | cmdlet을 실행한 후 성공적으로 등록된 가상 머신 개수입니다. `SubscriptionID, Resource Group, Virtual Machine` 형식으로 등록된 가상 머신을 나열합니다. | 
| 오류로 인해 등록하지 못한 VM 수 | 일부 오류로 인해 등록하지 못한 가상 머신 개수입니다. 오류 세부 정보는 로그 파일에서 찾을 수 있습니다. | 
| VM 또는 VM의 게스트 에이전트가 실행되고 있지 않아 건너뛴 VM 수 | 가상 머신 또는 가상 머신의 게스트 에이전트가 실행되고 있지 않아 등록할 수 없는 가상 머신의 개수 및 목록입니다. 가상 머신 또는 게스트 에이전트가 시작된 후 이 작업을 다시 시도할 수 있습니다. 세부 정보는 로그 파일에서 찾을 수 있습니다. |
| Windows에서 SQL Server를 실행하고 있지 않아 건너뛴 VM 수 | SQL Server 또는 Windows 가상 머신을 실행하고 있지 않아 건너뛴 가상 머신 개수입니다. 가상 머신은 `SubscriptionID, Resource Group, Virtual Machine` 형식으로 나열됩니다. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>로그 

오류는 `VMsNotRegisteredDueToError<Timestamp>.log`라는 로그 파일에 기록됩니다. 여기서 timestamp는 스크립트가 시작된 시간입니다. 오류가 구독 수준에 있으면 로그에는 쉼표로 구분된 구독 ID 및 오류 메시지가 포함됩니다. 가상 머신 등록에 오류가 있는 경우 로그에는 쉼표로 구분된 구독 ID, 리소스 그룹 이름, 가상 머신 이름, 오류 코드 및 메시지가 포함됩니다. 

## <a name="remarks"></a>설명

제공된 스크립트를 사용하여 확장에 SQL Server VM을 등록하는 경우 다음을 고려하세요.

- 확장을 등록하려면 SQL Server VM에서 실행되는 게스트 에이전트가 필요합니다. Windows Server 2008 이미지에는 게스트 에이전트가 없으므로 이 가상 머신은 실패하며 [NoAgent 관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)를 사용하여 수동으로 등록해야 합니다.
- 투명 오류를 해결하기 위한 재시도 논리가 기본 제공됩니다. 가상 머신이 성공적으로 등록된 경우 이는 신속한 작업입니다. 그러나 등록에 실패하면 각 가상 머신이 다시 시도됩니다.  따라서 실제 시간 요구 사항은 오류 유형 및 수에 따라 다르지만 등록 프로세스를 완료하는 데 상당한 시간을 허용해야 합니다. 

## <a name="full-script"></a>전체 스크립트

GitHub에 있는 전체 스크립트는 [Az PowerShell을 사용하여 SQL Server VM 대량 등록](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)을 참조하세요. 

전체 스크립트를 복사하고 `RegisterSqLVMs.psm1`로 저장합니다.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Azure vm에서 SQL Server의 새로운 기능](doc-changes-updates-release-notes-whats-new.md)
