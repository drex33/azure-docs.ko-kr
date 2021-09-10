---
title: Azure Arc 지원 서버를 Azure로 마이그레이션
description: 온-프레미스 또는 기타 클라우드 환경에서 실행되는 Azure Arc 지원 서버를 Azure로 마이그레이션하는 방법에 대해 알아봅니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 9dd7baa2466f4acd3e4106c3cec5a0d7e7afe05c
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390239"
---
# <a name="migrate-your-on-premises-or-other-cloud-arc-enabled-server-to-azure"></a>온-프레미스 또는 기타 클라우드 Arc 지원 서버를 Azure로 마이그레이션

이 문서는 Azure Arc 지원 서버를 통해 관리되는 온-프레미스 서버 또는 가상 머신을 Azure로 마이그레이션하도록 계획하고 성공적으로 수행하는 데 도움을 주기 위해 작성되었습니다. 이러한 단계를 수행하면 설치된 지원되는 VM 확장에 기반한 Arc 지원 서버 및 Arc 서버 리소스 ID에 기반한 Azure 서비스에서 관리를 전환할 수 있습니다.

이 단계를 수행하기 전에 Azure Migrate [Azure로 마이그레이션할 온-프레미스 컴퓨터 준비](../../migrate/prepare-for-migration.md) 문서를 검토하여 Azure Migrate 사용을 준비하는 방법에 대한 요구 사항을 이해하세요.

이 문서에서는 다음 작업을 수행합니다.

* 설치된 Azure Arc 지원 서버 지원 VM 확장의 인벤토리를 작성합니다.
* Arc 지원 서버에서 모든 VM 확장을 제거합니다.
* Arc 지원 서버 관리 ID로 인증하도록 구성된 Azure 서비스를 식별하고 마이그레이션 후 Azure VM ID를 사용하도록 해당 서비스를 업데이트할 준비를 합니다.
* Azure VM으로 마이그레이션된 후 리소스에 액세스할 수 있는 사람을 유지 관리하기 위해 Arc 지원 서버 리소스에 부여된 Azure RBAC(역할 기반 액세스 제어) 액세스 권한을 검토합니다. 
* Azure에서 Arc 지원 서버 리소스 ID를 삭제하고 Arc 지원 서버 에이전트를 제거합니다.
* Azure 게스트 에이전트를 설치합니다.
* 서버 또는 VM을 Azure로 마이그레이션합니다.

## <a name="step-1-inventory-and-remove-vm-extensions"></a>1 단계: VM 확장의 인벤토리 작성 및 제거

Arc 지원 서버에 설치된 VM 확장의 인벤토리를 작성하려면 Azure CLI 또는 Azure PowerShell을 사용하여 나열할 수 있습니다.

Azure PowerShell에서 [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 명령을 `-MachineName` 및 `-ResourceGroupName` 매개 변수와 함께 사용합니다.

Azure CLI에서 [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) 명령을 `--machine-name` 및 `--resource-group` 매개 변수와 함께 사용합니다. 기본적으로 Azure CLI 명령의 출력은 JSON(JavaScript Object Notation)으로 제공됩니다. 기본 출력을 목록이나 테이블로 변경하려면 [az configure --output](/cli/azure/reference-index)을 사용합니다. 출력 형식을 1번 변경하기 위해 명령에 `--output`을 추가할 수도 있습니다.

어떤 VM 확장이 배포되었는지 식별한 후 [Azure Portal](manage-vm-extensions-portal.md), [Azure PowerShell](manage-vm-extensions-powershell.md) 또는 [Azure CLI](manage-vm-extensions-cli.md)를 사용하여 제거할 수 있습니다. Log Analytics VM 확장 또는 종속성 에이전트 VM 확장이 Azure Policy 및 [VM 인사이트 이니셔티브](../../azure-monitor/vm/vminsights-enable-policy.md)를 사용하여 배포된 경우에는 마이그레이션이 완료되기 전에 Arc 지원 서버에서 확장이 재평가 및 배포되는 것을 방지하기 위해 [제외를 만들](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)어야 합니다.

## <a name="step-2-review-access-rights"></a>2단계: 액세스 권한 검토 

[Azure PowerShell](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-resource) 및 다른 PowerShell 코드를 사용하여 Arc 지원 서버 리소스에 대한 역할 할당을 나열하고 결과를 CSV 또는 다른 형식으로 내보낼 수 있습니다. 

Arc 지원 서버에서 실행되는 애플리케이션 또는 프로세스에 관리 ID를 사용하는 경우 Azure VM에 관리 ID가 할당되어 있는지 확인해야 합니다. 관리 ID에 대한 역할 할당을 보려면 Azure PowerShell `Get-AzADServicePrincipal` cmdlet을 사용하면 됩니다. 자세한 내용은 [관리 ID에 대한 역할 할당 나열](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-managed-identity)을 참조하세요. 

시스템 관리 ID는 Azure Policy를 사용하여 컴퓨터 또는 서버 내의 설정을 감사하는 경우에도 사용됩니다. Arc 지원 서버에는 게스트 구성 에이전트가 포함되며 감사 설정의 유효성 검사를 수행합니다. 마이그레이션 후에는 [Azure 가상 머신의 요구 사항 배포](../../governance/policy/concepts/guest-configuration.md#deploy-requirements-for-azure-virtual-machines)에서 Azure VM을 수동으로 구성하는 방법 또는 게스트 구성 확장을 사용하여 정책을 통해 구성하는 방법에 대한 자세한 내용을 참조하세요.

새 Azure VM ID가 해당 서비스에 인증할 수 있도록 관리 ID에서 액세스하는 리소스로 역할 할당을 업데이트합니다. [Azure 리소스에 대한 관리 ID가 Azure VM(Virtual Machine)에서 작동하는 방식](../../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)을 알아보려면 다음을 참조하세요.

## <a name="step-3-disconnect-from-azure-arc-and-uninstall-agent"></a>3단계: Azure Arc에서 연결 끊기 및 에이전트 제거

다음 방법 중 하나를 사용하여 Azure에서 Arc 지원 서버의 리소스 ID를 삭제합니다.

   * 머신 또는 서버에서 `azcmagent disconnect` 명령을 실행합니다.

   * Azure Portal에 등록된 Arc 지원 서버를 선택하고 상단 막대에서 **삭제** 를 선택합니다.

   * [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) 또는 [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)을 사용합니다. `ResourceType` 매개 변수에 대해 `Microsoft.HybridCompute/machines`를 사용합니다.

그런 다음, [에이전트 제거](manage-agent.md#remove-the-agent) 단계에 따라 Azure Arc 지원 서버 Windows 또는 Linux 에이전트를 제거합니다.

## <a name="step-4-install-the-azure-guest-agent"></a>4단계: Azure 게스트 에이전트 설치

온-프레미스에서 Azure로 마이그레이션된 VM에는 Linux 또는 Windows Azure 게스트 에이전트가 설치되어 있지 않습니다. 이러한 시나리오에서는 VM 에이전트를 수동으로 설치해야 합니다. VM 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure Virtual Machine Windows 에이전트 개요](../../virtual-machines/extensions/agent-windows.md) 또는 [Azure Virtual Machine Linux 에이전트 개요](../../virtual-machines/extensions/agent-linux.md)를 참조하세요.

## <a name="step-5-migrate-server-or-machine-to-azure"></a>5 단계: 서버 또는 컴퓨터를 Azure로 마이그레이션

Azure 마이그레이션을 사용하여 마이그레이션을 진행하기 전에 [Azure로 마이그레이션할 온-프레미스 컴퓨터 준비](../../migrate/prepare-for-migration.md) 문서를 검토하여 Azure Migrate를 사용하는 데 필요한 요구 사항에 대해 알아보세요. Azure로의 마이그레이션을 완료하려면 환경에 따라 Azure Migrate [마이그레이션 옵션](../../migrate/prepare-for-migration.md#next-steps)을 검토하세요.

## <a name="step-6-deploy-azure-vm-extensions"></a>6 단계: Azure VM 확장 배포

마이그레이션 및 마이그레이션 후 구성 단계를 모두 완료한 후에는 원래 Arc 지원 서버에 설치된 VM 확장을 기반으로 Azure VM 확장을 배포할 수 있습니다. [Azure 가상 머신 확장 및 기능](../../virtual-machines/extensions/overview.md)을 검토하면 확장 배포를 계획하는 데 도움이 됩니다. 

Azure Policy 게스트 구성 정책 정의가 있는 컴퓨터 내에서 감사 설정 사용을 재개하려면 [게스트 구성 사용](../../governance/policy/concepts/guest-configuration.md#enable-guest-configuration)을 참조하세요.

Log Analytics VM 확장 또는 종속성 에이전트 VM 확장이 Azure Policy 및 [VM 인사이트 이니셔티브](../../azure-monitor/vm/vminsights-enable-policy.md)를 사용하여 배포된 경우에는 이전에 만든 [제외](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)를 제거합니다. Azure Policy를 사용하여 Azure 가상 머신을 사용하도록 설정하려면 [Azure Policy를 사용하여 대규모로 Azure Monitor 배포](../../azure-monitor/deploy-scale.md#vm-insights)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

문제 해결 정보는 [연결된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 확인할 수 있습니다.