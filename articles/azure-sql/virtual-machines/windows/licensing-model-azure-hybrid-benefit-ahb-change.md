---
title: Azure에서 SQL VM에 대한 라이선스 모델 변경
description: Azure 하이브리드 혜택을 사용하여 Azure의 SQL Server VM에 대한 라이선스를 종량제에서 BYOL(사용자 라이선스 필요)로 전환하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cd9975e1554c6c2f56561ce5e3fba5086c342c21
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987414"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Azure에서 SQL 가상 머신의 라이선스 모델 변경
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


이 문서에서는 [SQL IaaS 에이전트 확장](./sql-server-iaas-agent-extension-automate-management.md)을 사용하여 Azure에서 SQL Server VM(가상 머신)의 라이선스 모델을 변경하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

SQL Server를 호스트하는 Azure VM에 대한 라이선스 모델에는 종량제, AHB(Azure 하이브리드 혜택), HA/DR(고가용성/재해복구)의 세 가지가 있습니다. Azure Portal, Azure CLI 또는 PowerShell을 사용하여 SQL Server VM의 라이선스 모델을 수정할 수 있습니다. 

- **종량제** 모델은 Azure VM의 초당 비용에 SQL Server 라이선스 비용이 포함됨을 의미합니다.
- [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 SQL Server를 실행하는 VM에서 자신의 SQL Server 라이선스를 사용할 수 있습니다. 
- **HA/DR** 라이선스 유형은 Azure에서 [HA/DR 복제본 평가판](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)에 사용됩니다. 

Azure 하이브리드 혜택을 통해 Azure 가상 머신에서 Software Assurance가 있는 SQL Server 라이선스("적격 라이선스")를 사용할 수 있습니다. Azure 하이브리드 혜택을 사용하면 고객에게 VM에서 SQL Server 라이선스 사용에 대한 요금이 부과되지 않습니다. 하지만 기본 클라우드 컴퓨팅(즉, 기본 요금), 스토리지 및 백업에 대한 비용은 지불해야 합니다. 서비스의 사용과 관련된 I/O에 대해서도 요금을 지불해야 합니다(해당하는 경우).

Microsoft [제품 사용 약관](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)에 따라 “고객은 Azure에서 워크로드를 구성할 때 SQL Server에 대한 Azure 하이브리드 혜택으로 Azure SQL Database(관리형 인스턴스, 탄력적 풀, 단일 데이터베이스), Azure Data Factory, SQL Server Integration Services 또는 SQL Server 가상 머신을 사용하고 있음을 나타내야 합니다.”

Azure VM에서 SQL Server에 대한 Azure 하이브리드 혜택 사용을 나타내고 규정을 준수하는 데 다음 세 가지 옵션을 사용할 수 있습니다.

- Azure Marketplace에서 BYOL(사용자 라이선스 필요) SQL Server 이미지를 사용하여 가상 머신을 프로비저닝합니다. 이 옵션은 기업계약이 있는 고객만 사용할 수 있습니다.
- Azure Marketplace의 종량제 SQL Server 이미지를 사용하여 가상 머신을 프로비저닝하고 Azure 하이브리드 혜택을 활성화합니다.
- Azure VM에서 SQL Server를 자체 설치하고, 수동으로 [SQL IaaS 에이전트 확장에 등록](sql-agent-extension-manually-register-single-vm.md)하고, Azure 하이브리드 혜택을 활성화합니다.

VM을 프로비저닝할 때 또는 이후에 언제든지 SQL Server 라이선스 유형을 구성할 수 있습니다. 라이선스 모델 간을 전환할 때 가동 중지 시간이 발생하지 않으며, VM 또는 SQL Server 서비스를 다시 시작하지 않고 추가 비용 없이 즉시 적용됩니다. 실제로 Azure 하이브리드 혜택을 활성화하면 비용이 *줄어듭니다*.

## <a name="prerequisites"></a>필수 구성 요소

SQL Server VM 라이선스 모델을 변경하려면 다음 요구 사항이 충족되어야 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL IaaS 에이전트 확장](./sql-server-iaas-agent-extension-automate-management.md)에 등록된 [SQL Server VM](./create-sql-vm-portal.md)입니다.
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 활용하기 위한 요구 사항입니다. 


## <a name="change-license-model"></a>라이선스 모델 변경

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

포털에서 직접 라이선스 모델을 수정할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)을 열고 SQL Server VM에 대한 [SQL 가상 머신 리소스](manage-sql-vm-portal.md#access-the-resource)를 엽니다. 
1. **설정** 에서 **구성** 을 선택합니다. 
1. **Azure 하이브리드 혜택** 옵션을 선택하고 확인란을 선택하여 Software Assurance가 포함된 SQL Server 라이선스가 있음을 확인합니다. 
1. **구성** 페이지의 맨 아래에서 **적용** 을 선택합니다. 

![포털의 Azure 하이브리드 혜택](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 라이선스 모델을 변경할 수 있습니다.  

**라이선스 형식** 에 대해 다음 값을 지정합니다.
- `AHUB` Azure 하이브리드 혜택
- `PAYG` 종량제
- `DR` HA/DR 복제본 평가판 활성화하기


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 라이선스 모델을 변경할 수 있습니다.

**라이선스 형식** 에 대해 다음 값을 지정합니다.
- `AHUB` Azure 하이브리드 혜택
- `PAYG` 종량제
- `DR` HA/DR 복제본 평가판 활성화하기


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>설명

- Azure CSP(클라우드 솔루션 공급자) 고객은 활성 Software Assurance가 있는 경우 먼저 종량제 VM을 배포한 다음 BYOL(사용자 라이선스 필요)로 변환하여 Azure 하이브리드 혜택을 사용할 수 있습니다.
- SQL 가상 머신 리소스를 끊으면 이미지의 하드 코드된 라이선스 설정으로 돌아갑니다. 
- 라이선스 모델을 변경하는 기능은 SQL IaaS 에이전트 확장의 기능입니다. Azure Portal을 통해 Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 확장에 등록됩니다. 그러나 SQL Server를 자체 설치하는 고객은 수동으로 [SQL Server VM을 등록](sql-agent-extension-manually-register-single-vm.md)해야 합니다. 
- 가용성 집합에 SQL Server VM을 추가하려면 VM을 다시 만들어야 합니다. 따라서 가용성 집합에 추가되는 모든 VM은 기본 종량제 라이선스 유형으로 돌아갑니다. Azure 하이브리드 혜택을 다시 사용하도록 설정해야 합니다. 


## <a name="limitations"></a>제한 사항

라이선스 모델을 변경하는 경우:
   - [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)가 있는 고객만 사용할 수 있습니다.
   - SQL Server의 Standard 및 Enterprise 버전에만 지원됩니다. Express, Web 및 Developer에 대한 라이선스 변경은 지원되지 않습니다. 
   - Azure Resource Manager 모델을 통해 배포된 가상 머신에만 지원됩니다. 클래식 모델을 통해 배포된 가상 머신은 지원되지 않습니다. 
   - 공용 클라우드 또는 Azure Government 클라우드에 대해서만 사용할 수 있습니다. Azure 중국 지역에서 현재 사용할 수 없습니다. 

> [!Note]
> Software Assurance 또는 구독 라이선스를 사용하는 SQL Server 코어 기반 라이선스만 Azure 하이브리드 혜택에 적합합니다. SQL Server에 대해 서버 + CAL 라이선스를 사용하고 있고 Software Assurance가 있는 경우, Azure SQL Server 가상 머신 이미지에 대한 BYOL(사용자 라이선스 필요)을 사용하여 해당 서버에 대한 라이선스 이동을 활용할 수 있지만 Azure 하이브리드 혜택의 다른 기능을 활용할 수는 없습니다. 

## <a name="known-errors"></a>알려진 오류

일반적으로 알려진 오류와 해결 방법을 검토합니다. 

리소스 그룹 ‘\<resource-group>’ 아래의 **리소스 ‘Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>’를 찾을 수 없습니다.**

다음 오류는 SQL Server IaaS 에이전트 확장에 등록되지 않은 SQL Server VM에서 라이선스 모델을 변경하려고 할 때 발생합니다.

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

리소스 공급자에 구독을 등록한 다음 [SQL Server VM을 SQL IaaS 에이전트 확장에 등록](sql-agent-extension-manually-register-single-vm.md)해야 합니다. 



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](../../database/doc-changes-updates-release-notes.md)
* [SQL IaaS 에이전트 확장 개요](./sql-server-iaas-agent-extension-automate-management.md)
