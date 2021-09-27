---
title: Azure Arc 지원 서버를 Azure로 마이그레이션
description: 온-프레미스 또는 기타 클라우드 환경에서 실행되는 Azure Arc 지원 서버를 Azure로 마이그레이션하는 방법에 대해 알아봅니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 0ed30fac28c3491a9772466aa193f721f4fa440f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815998"
---
# <a name="migrate-your-on-premises-or-other-cloud-azure-arc-enabled-server-to-azure"></a>온-프레미스 또는 기타 클라우드 Azure Arc 사용 서버를 Azure로 마이그레이션

이 문서는 Azure Arc 지원 서버를 통해 관리되는 온-프레미스 서버 또는 가상 머신을 Azure로 마이그레이션하도록 계획하고 성공적으로 수행하는 데 도움을 주기 위해 작성되었습니다. 이러한 단계를 수행 하면 설치 된 지원 되는 VM 확장을 기반으로 하는 Azure Arc 사용 서버와 Arc 서버 리소스 id를 기반으로 하는 Azure 서비스에서 관리를 전환할 수 있습니다.

이 단계를 수행하기 전에 Azure Migrate [Azure로 마이그레이션할 온-프레미스 컴퓨터 준비](../../migrate/prepare-for-migration.md) 문서를 검토하여 Azure Migrate 사용을 준비하는 방법에 대한 요구 사항을 이해하세요.

이 문서에서는 다음 작업을 수행합니다.

* 설치된 Azure Arc 지원 서버 지원 VM 확장의 인벤토리를 작성합니다.
* Azure Arc 사용 서버에서 모든 VM 확장을 제거 합니다.
* Azure Arc 사용 서버 관리 id를 사용 하 여 인증 하도록 구성 된 Azure 서비스를 확인 하 고 마이그레이션 후 Azure VM id를 사용 하도록 해당 서비스를 업데이트 하도록 준비 합니다.
* Azure Arc 사용 서버 리소스에 부여 된 azure RBAC (역할 기반 액세스 제어) 액세스 권한을 검토 하 여 Azure VM으로 마이그레이션한 후 리소스에 대 한 액세스 권한이 있는 사용자를 유지 합니다. 
* Azure에서 Azure Arc 사용 서버 리소스 id를 삭제 하 고 Azure Arc 사용 서버 에이전트를 제거 합니다.
* Azure 게스트 에이전트를 설치합니다.
* 서버 또는 VM을 Azure로 마이그레이션합니다.

## <a name="step-1-inventory-and-remove-vm-extensions"></a>1 단계: VM 확장의 인벤토리 작성 및 제거

Azure Arc 사용 서버에 설치 된 VM 확장을 인벤토리에 포함 하려면 Azure CLI 또는 Azure PowerShell를 사용 하 여 해당 확장을 나열할 수 있습니다.

Azure PowerShell에서 [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 명령을 `-MachineName` 및 `-ResourceGroupName` 매개 변수와 함께 사용합니다.

Azure CLI에서 [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) 명령을 `--machine-name` 및 `--resource-group` 매개 변수와 함께 사용합니다. 기본적으로 Azure CLI 명령의 출력은 JSON(JavaScript Object Notation)으로 제공됩니다. 기본 출력을 목록이나 테이블로 변경하려면 [az configure --output](/cli/azure/reference-index)을 사용합니다. 출력 형식을 1번 변경하기 위해 명령에 `--output`을 추가할 수도 있습니다.

어떤 VM 확장이 배포되었는지 식별한 후 [Azure Portal](manage-vm-extensions-portal.md), [Azure PowerShell](manage-vm-extensions-powershell.md) 또는 [Azure CLI](manage-vm-extensions-cli.md)를 사용하여 제거할 수 있습니다. Log Analytics VM 확장 또는 종속성 에이전트 VM 확장이 Azure Policy 및 [vm insights 이니셔티브](../../azure-monitor/vm/vminsights-enable-policy.md)를 사용 하 여 배포 된 경우 마이그레이션이 완료 되기 전에 Azure Arc 사용 서버에 대 한 확장의 재평가 및 배포를 방지 하기 위해 [제외를 만들어야](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion) 합니다.

## <a name="step-2-review-access-rights"></a>2단계: 액세스 권한 검토 

Azure Arc 사용 서버 리소스에 대 한 역할 할당 나열 [Azure PowerShell](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-resource) 및 다른 PowerShell 코드를 사용 하 여 결과를 CSV 또는 다른 형식으로 내보낼 수 있습니다. 

Azure Arc 사용 서버에서 실행 중인 응용 프로그램 또는 프로세스에 관리 되는 id를 사용 하는 경우 Azure VM에 관리 id가 할당 되어 있는지 확인 해야 합니다. 관리 ID에 대한 역할 할당을 보려면 Azure PowerShell `Get-AzADServicePrincipal` cmdlet을 사용하면 됩니다. 자세한 내용은 [관리 ID에 대한 역할 할당 나열](../../role-based-access-control/role-assignments-list-powershell.md#list-role-assignments-for-a-managed-identity)을 참조하세요. 

시스템 관리 ID는 Azure Policy를 사용하여 머신 또는 서버 내 설정을 감사하거나 구성하는 경우에도 사용됩니다. Azure Arc 사용 서버를 사용 하 여 게스트 구성 에이전트 서비스가 포함 되 고 감사 설정의 유효성 검사를 수행 합니다. 마이그레이션 후 Azure VM을 수동으로 구성하는 방법이나 게스트 구성 확장을 사용하여 정책을 통해 구성하는 방법에 대한 자세한 내용은 [Azure 가상 머신의 배포 요구 사항](../../governance/policy/concepts/guest-configuration.md#deploy-requirements-for-azure-virtual-machines)을 참조하세요.

새 Azure VM ID가 해당 서비스에 인증할 수 있도록 관리 ID에서 액세스하는 리소스로 역할 할당을 업데이트합니다. [Azure 리소스에 대한 관리 ID가 Azure VM(Virtual Machine)에서 작동하는 방식](../../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md)을 알아보려면 다음을 참조하세요.

## <a name="step-3-disconnect-from-azure-arc-and-uninstall-agent"></a>3단계: Azure Arc에서 연결 끊기 및 에이전트 제거

다음 방법 중 하나를 사용 하 여 Azure에서 Azure Arc 사용 서버에 대 한 리소스 ID를 삭제 합니다.

   * 머신 또는 서버에서 `azcmagent disconnect` 명령을 실행합니다.

   * Azure Portal에서 선택한 등록 된 Azure Arc 사용 서버에서 위쪽 표시줄에서 **삭제** 를 선택 하 여 선택 합니다.

   * [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) 또는 [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)을 사용합니다. `ResourceType` 매개 변수에 대해 `Microsoft.HybridCompute/machines`를 사용합니다.

그런 다음, [에이전트 제거](manage-agent.md#remove-the-agent) 단계에 따라 Azure Arc 지원 서버 Windows 또는 Linux 에이전트를 제거합니다.

## <a name="step-4-install-the-azure-guest-agent"></a>4단계: Azure 게스트 에이전트 설치

온-프레미스에서 Azure로 마이그레이션된 VM에는 Linux 또는 Windows Azure 게스트 에이전트가 설치되어 있지 않습니다. 이러한 시나리오에서는 VM 에이전트를 수동으로 설치해야 합니다. VM 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure Virtual Machine Windows 에이전트 개요](../../virtual-machines/extensions/agent-windows.md) 또는 [Azure Virtual Machine Linux 에이전트 개요](../../virtual-machines/extensions/agent-linux.md)를 참조하세요.

## <a name="step-5-migrate-server-or-machine-to-azure"></a>5 단계: 서버 또는 컴퓨터를 Azure로 마이그레이션

Azure 마이그레이션을 사용하여 마이그레이션을 진행하기 전에 [Azure로 마이그레이션할 온-프레미스 컴퓨터 준비](../../migrate/prepare-for-migration.md) 문서를 검토하여 Azure Migrate를 사용하는 데 필요한 요구 사항에 대해 알아보세요. Azure로의 마이그레이션을 완료하려면 환경에 따라 Azure Migrate [마이그레이션 옵션](../../migrate/prepare-for-migration.md#next-steps)을 검토하세요.

## <a name="step-6-deploy-azure-vm-extensions"></a>6 단계: Azure VM 확장 배포

모든 마이그레이션 후 구성 단계를 마이그레이션 및 완료 한 후에는 이제 Azure Arc 사용 서버에 원래 설치 된 VM 확장을 기반으로 Azure VM 확장을 배포할 수 있습니다. [Azure 가상 머신 확장 및 기능](../../virtual-machines/extensions/overview.md)을 검토하면 확장 배포를 계획하는 데 도움이 됩니다. 

게스트 구성 정책 정의를 통해 머신 내에서 감사 설정 사용을 재개하려면 [게스트 구성 사용](../../governance/policy/concepts/guest-configuration.md#enable-guest-configuration)을 참조하세요.

Log Analytics VM 확장 또는 종속성 에이전트 VM 확장이 Azure Policy 및 [VM 인사이트 이니셔티브](../../azure-monitor/vm/vminsights-enable-policy.md)를 사용하여 배포된 경우에는 이전에 만든 [제외](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)를 제거합니다. Azure Policy를 사용하여 Azure 가상 머신을 사용하도록 설정하려면 [Azure Policy를 사용하여 대규모로 Azure Monitor 배포](../../azure-monitor/deploy-scale.md#vm-insights)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

문제 해결 정보는 [연결된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 확인할 수 있습니다.
