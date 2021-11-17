---
title: 비규격 Azure Automation 상태 구성 서버 재구성
description: 이 문서에서는 구성 상태가 드리프트된 서버에 요청 시 구성을 다시 적용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: cf4286634e72d687fe78c53a8f20abe5d5b2b6eb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132489873"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>비규격 Azure Automation 상태 구성 서버 재구성

서버가 Azure Automation 상태 구성에 등록되면 구성 모드는 `ApplyOnly`, `ApplyAndMonitor` 또는 `ApplyAndAutoCorrect`로 설정됩니다. 모드가 `ApplyAndAutoCorrect`로 설정되어 있지 않은 경우 어떤 이유로든 규격 상태에서 드리프트한 서버는 수동으로 수정될 때까지 비규격 상태로 유지됩니다.

Azure 컴퓨팅은 고객이 가상 머신 내에서 스크립트를 실행할 수 있도록 하는 Run Command라는 기능을 제공합니다.
이 문서에서는 구성 드리프트를 수동으로 수정할 때 이 기능에 대한 예제 스크립트를 제공합니다.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell을 사용하여 Windows 가상 머신의 드리프트 수정

`Run` 명령 기능을 사용하여 Windows 가상 머신의 드리프트를 수정할 수 있습니다. [실행 명령을 사용하여 Windows VM에서 PowerShell 스크립트 실행](../virtual-machines/windows/run-command.md)을 참조하세요.

Azure Automation 상태 구성 노드에서 최신 구성을 다운로드하여 적용하려면 [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) cmdlet을 사용합니다.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 가상 머신의 올바른 드리프트

Linux 가상 머신의 경우 `Run` 명령을 사용할 수 있는 옵션이 없습니다. 등록 프로세스를 반복하여 이러한 머신의 드리프트만 수정할 수 있습니다. 

Azure 노드의 경우 Azure Portal에서 또는 Az 모듈 cmdlet을 사용하여 드리프트를 수정할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Azure Portal을 통해 VM 사용](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal)에 설명되어 있습니다.

하이브리드 노드의 경우 Python 스크립트를 사용하여 드리프트를 수정할 수 있습니다. [Linux 컴퓨터에서 DSC 작업 수행](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation/#automation)을 참조하세요.
- 연속 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Chocolatey를 사용한 연속 배포 설정](automation-dsc-cd-chocolatey.md)을 참조하세요.
