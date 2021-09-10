---
title: Azure Linux VM에서 스크립트 실행
description: 이 항목에서는 가상 머신 내에서 스크립트를 실행하는 방법 설명
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
ms.openlocfilehash: 1b0f47f11f72a4fa26a15bc96e04052b460d5729
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688208"
---
# <a name="run-scripts-in-your-linux-vm"></a>Linux VM에서 스크립트 실행

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

태스크를 자동화하거나 문제를 해결하려면 VM에서 명령을 실행해야 할 수 있습니다. 다음 문서에서는 VM 내에서 스크립트 및 명령을 실행할 수 있는 기능의 간략한 개요를 제공합니다.

## <a name="custom-script-extension"></a>사용자 지정 스크립트 확장

[사용자 지정 스크립트 확장](../extensions/custom-script-linux.md)은 주로 사후 배포 구성 및 소프트웨어 설치에 사용됩니다.

* Azure 가상 머신에서 스크립트를 다운로드하고 실행합니다.
* Azure Resource Manager 템플릿, Azure CLI, REST API, PowerShell 또는 Azure Portal을 사용하여 실행할 수 있습니다.
* 스크립트 파일은 Azure Storage 또는 GitHub에서 다운로드하거나 Azure Portal에서 실행될 때 사용자 PC에서 제공할 수 있습니다.
* Windows 컴퓨터에서 PowerShell 스크립트 및 Linux 컴퓨터에서 Bash 스크립트를 실행합니다.
* 배포 후 구성, 소프트웨어 설치 및 기타 구성 또는 관리 작업에 유용합니다.

## <a name="run-command"></a>명령 실행

[명령 실행](run-command.md) 기능은 가상 머신 및 애플리케이션 관리 및 스크립트를 사용한 문제 해결을 사용하도록 설정하며, 가상 머신을 연결할 수 없는 경우에도 예를 들어 게스트 방화벽이 RDP 또는 SSH 포트를 개방하지 않는 경우에도 사용할 수 있습니다.

* Azure 가상 머신에서 스크립트를 실행합니다.
* [Azure Portal](run-command.md), [REST API](/rest/api/compute/virtual-machines-run-commands/run-command), [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke) 또는 [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)을 사용하여 실행 가능
* 신속하게 스크립트 및 출력 보기를 실행하고 Azure Portal에서 필요에 따라 반복합니다.
* 스크립트는 직접 입력하거나 기본 제공 스크립트 중 하나를 실행할 수 있습니다.
* Windows 컴퓨터에서 PowerShell 스크립트 및 Linux 컴퓨터에서 Bash 스크립트를 실행합니다.
* 가상 머신 및 애플리케이션 관리 및 연결할 수 없는 가상 머신에서 스크립트 실행에 유용합니다.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

[Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)는 일반 컴퓨터, 애플리케이션 및 환경 관리에 Automation 계정에 저장된 사용자의 사용자 지정 스크립트를 제공합니다.

* Azure 및 비 Azure 컴퓨터에서 스크립트를 실행합니다.
* Azure Portal, Azure CLI, REST API, PowerShell, webhook을 사용하여 실행할 수 있습니다.
* Automation 계정에 저장되고 관리되는 스크립트입니다.
* PowerShell, PowerShell 워크플로, Python 또는 Graphical Runbook 실행
* 스크립트 실행 시간에 시간 제한이 없습니다.
* 여러 스크립트를 동시에 실행할 수 있습니다.
* 전체 스크립트 출력을 반환하고 저장합니다.
* 90일 동안 사용할 수 있는 작업 기록입니다.
* 스크립트는 로컬 시스템으로 또는 사용자 제공 자격 증명으로 실행할 수 있습니다.
* [수동 설치](../../automation/automation-windows-hrw-install.md)가 필요

## <a name="serial-console"></a>직렬 콘솔

[직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-linux)은 키보드를 VM에 연결하는 것과 유사하게 VM에 대 한 직접 액세스를 제공합니다.

* Azure 가상 머신에서 명령을 실행합니다.
* Azure Portal에서 가상 머신에 텍스트 기반 콘솔을 사용하여 실행할 수 있습니다.
* 로컬 사용자 계정으로 가상 머신에 로그인합니다.
* 가상 머신의 네트워크 또는 운영 체제 상태에 관계 없이 가상 머신에 액세스할 때 유용합니다.

## <a name="next-steps"></a>다음 단계

VM 내에서 스크립트 및 명령을 실행할 수 있는 다양한 기능에 대해 자세히 알아봅니다.

* [사용자 지정 스크립트 확장](../extensions/custom-script-linux.md)
* [명령 실행](run-command.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)
* [직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-linux)
