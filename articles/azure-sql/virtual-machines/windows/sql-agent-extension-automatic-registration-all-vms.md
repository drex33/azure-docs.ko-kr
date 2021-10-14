---
title: SQL IaaS 에이전트 확장을 사용하여 자동 등록
description: Azure Portal을 사용하여 SQL IaaS 에이전트 확장을 통해 모든 이전 및 이후 SQL Server VM을 자동으로 등록하는 자동 등록 기능을 사용하도록 설정하는 방법을 알아봅니다.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 9/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 954164ef583aaebd7829dd67cdccbe2e383db308
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006340"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>SQL IaaS 에이전트 확장을 사용하여 자동 등록
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

경량 모드에서 [SQL IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)을 사용하여 Azure VM(Virtual Machines)에서 현재 및 향후의 모든 SQL Server를 자동으로 등록하려면 Azure Portal에서 자동 등록 기능을 사용하도록 설정합니다. 기본적으로 SQL Server 2016 이상이 설치 된 Azure vm은 [CEIP 서비스](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)에서 검색 될 때 SQL IaaS 에이전트 확장에 자동으로 등록 됩니다. 자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.

이 문서에서는 자동 등록 기능을 사용하도록 설정하는 방법을 설명합니다. 또는 [단일 VM을 등록](sql-agent-extension-manually-register-single-vm.md)하거나 SQL IaaS 에이전트 확장을 사용하여 [VM을 대량으로 등록](sql-agent-extension-manually-register-vms-bulk.md)할 수 있습니다. 

> [!NOTE]
> 2021년 9월부터 전체 모드에서 SQL IaaS 확장에 등록하는 것은 더 이상 SQL Server 서비스를 다시 시작하지 않아도 됩니다. 

## <a name="overview"></a>개요

[SQL IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)에 SQL Server VM을 등록하면 전체 기능의 이점을 활용할 수 있습니다. 

자동 등록을 사용하도록 설정하면 작업이 매일 실행되어 구독의 등록되지 않은 모든 VM에 SQL Server가 설치되어 있는지 여부를 검색합니다. 이 작업은 SQL IaaS 에이전트 확장 이진 파일을 VM에 복사한 다음 SQL Server 레지스트리 하이브를 확인하는 일회성 유틸리티를 실행하여 수행됩니다. SQL Server 하이브가 검색되면 가상 머신이 경량 모드에서 확장을 사용하여 등록됩니다. 레지스트리에 SQL Server 하이브가 없으면 이진 파일이 제거됩니다. 자동 등록은 새로 만든 SQL Server VM을 검색하는 데 최대 4일이 걸릴 수 있습니다.

> [!CAUTION]
> 레지스트리에 SQL Server 하이브가 없는 경우 [리소스 잠금이](/azure/governance/blueprints/concepts/resource-locking#locking-modes-and-states) 설정 되어 있으면 이진 파일을 제거 하는 것이 영향을 받을 수 있습니다. 


구독에 대해 자동 등록을 사용하도록 설정하면 SQL Server가 설치된 모든 현재 및 향후 VM이 **가동 중지 시간 없이 SQL Server 서비스를 다시 시작하지 않고 경량 모드** 로 SQL IaaS 에이전트 확장에 등록됩니다. 전체 기능 집합을 활용하려면 여전히 [전체 관리 모드로 수동 업그레이드](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)해야 합니다. 라이선스 유형은 자동으로 VM 이미지의 라이선스 유형으로 기본 설정됩니다. VM에 종량제 이미지를 사용하는 경우 라이선스 유형은 `PAYG`이고 그렇지 않은 경우 라이선스 유형은 기본적으로 `AHUB`입니다. 

> [!IMPORTANT]
> SQL IaaS Agent 확장은 Azure Virtual Machines 내에서 SQL Server를 사용하는 고객에게 선택적 혜택을 제공하기 위한 목적으로 데이터를 수집합니다. Microsoft는 고객의 사전 동의 없이 라이선스 감사에 이 데이터를 사용하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 제공](/sql/sql-server/sql-server-privacy#non-personal-data)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

확장에 SQL Server VM을 등록하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/) 및 최소한 [기여자 역할](../../../role-based-access-control/built-in-roles.md#all) 권한.
- 공용 또는 Azure Government 클라우드에 배포된 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)가 포함된 Azure 리소스 모델 [Windows Server 2008 R2(이상) 가상 머신](../../../virtual-machines/windows/quick-create-portal.md). Windows Server 2008은 지원되지 않습니다. 


## <a name="enable"></a>사용

Azure Portal에서 SQL Server VM의 자동 등록을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [**SQL 가상 머신**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스 페이지로 이동합니다. 
1. **자동 SQL Server VM 등록** 을 선택하여 **자동 등록** 페이지를 엽니다. 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="자동 등록 페이지를 열려면 자동 SQL Server VM 등록을 선택합니다.":::

1. 드롭다운에서 구독을 선택합니다. 
1. 약관을 읽고 동의하면 **동의합니다** 를 선택합니다. 
1. **등록** 을 선택하여 기능을 사용하고 SQL IaaS 에이전트 확장을 사용하여 현재 및 향후의 모든 SQL Server VM을 자동으로 등록합니다. 그러면 VM에서 SQL Server 서비스가 다시 시작되지 않습니다. 

## <a name="disable"></a>사용 중지

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)을 사용하여 자동 등록 기능을 사용하지 않도록 설정합니다. 자동 등록 기능을 사용하지 않도록 설정하면 구독에 추가된 SQL Server VM을 SQL IaaS 에이전트 확장에 수동으로 등록해야 합니다. 이렇게 하면 이미 등록된 기존 SQL Server VM의 등록이 취소되지 않습니다.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 자동 등록을 해제하려면 다음 명령을 실행합니다. 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 자동 등록을 사용하지 않도록 설정하려면 다음 명령을 실행합니다. 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>여러 구독에 대해 사용

PowerShell을 사용하여 여러 Azure 구독에 대한 자동 등록 기능을 사용하도록 설정할 수 있습니다. 

이렇게 하려면 다음 단계를 따르십시오.

1. [이 스크립트](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/EnableBySubscription.ps1)를 저장합니다.
1. 관리 명령 프롬프트 또는 PowerShell 창을 사용하여 스크립트를 저장한 위치로 이동합니다. 
1. Azure에 연결합니다(`az login`).
1. 구독 Id를 매개 변수로 전달 하 여 스크립트를 실행 합니다. 구독을 지정 하지 않으면 스크립트는 사용자 계정의 모든 구독에 대해 자동 등록을 사용 하도록 설정 합니다.    

   다음 명령은 두 구독에 대해 자동 등록을 사용 하도록 설정 합니다. 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```
   다음 명령을 사용 하 여 모든 구독에 대해 자동 등록을 사용 하도록 설정 합니다. 

   ```console
   .\EnableBySubscription.ps1
   ```

실패한 등록 오류는 `.ps1` 스크립트를 저장하고 실행한 동일한 디렉터리에 있는 `RegistrationErrors.csv`에 저장됩니다. 

## <a name="next-steps"></a>다음 단계

SQL IaaS 에이전트 확장에서 제공하는 전체 기능 집합을 활용하려면 관리 모드를 [전체](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)로 업그레이드합니다. 
