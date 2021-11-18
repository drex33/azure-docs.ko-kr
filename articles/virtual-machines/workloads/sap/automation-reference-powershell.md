---
title: SAP deployment automation framework PowerShell 참조 | Microsoft Docs
description: Azure PowerShell 참조에서 SAP 배포 자동화 프레임 워크
services: virtual-machines-windows
author: kimforss
manager: kimforss
keywords: Azure, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.workload: infrastructure
ms.date: 11/17/2021
ms.author: kimforss
ms.openlocfilehash: baff0193394e64a4699e519565117e1dd3996afa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730393"
---
# <a name="using-powershell-in-sap-deployment-automation-framework"></a>SAP 배포 자동화 프레임 워크에서 PowerShell 사용

Microsoft PowerShell을 사용 하 여 Azure 구성 요소 [에 모든 SAP deployment automation 프레임 워크](automation-deployment-framework.md) 를 배포할 수 있습니다.

## <a name="control-plane-operations"></a>제어 평면 작업

[SAPAutomationRegion](module/automation-new-sapautomationregion.md) PowerShell 명령을 사용 하 여 컨트롤 평면을 배포 하거나 업데이트할 수 있습니다.

PowerShell 명령을 사용 하 여 제어 평면을 제거 합니다 [`Remove-SAPAutomationRegion`](module/automation-remove-sapautomationregion.md) .

[SAPDeployer](module/automation-new-sapdeployer.md) PowerShell 명령을 사용 하 여 컨트롤 평면에서 배포자을 부트스트랩 할 수 있습니다.

[새-SAPLibrary](module/automation-new-saplibrary.md) PowerShell 명령을 사용 하 여 제어 평면에서 SAP 라이브러리를 부트스트랩 할 수 있습니다.

## <a name="workload-zone-operations"></a>작업 영역 작업

PowerShell 명령을 사용 하 여 작업 영역을 배포 하거나 업데이트 합니다 [`New-SAPWorkloadZone`](module/automation-new-sapworkloadzone.md) .

PowerShell 명령을 사용 하 여 작업 영역을 제거 합니다 [`Remove-SAPSystem`](module/automation-remove-sapsystem.md)  .


## <a name="sap-system-operations"></a>SAP 시스템 작업

PowerShell 명령을 사용 하 여 SAP 시스템을 배포 하거나 업데이트 [`New-SAPSystem`](module/automation-new-sapsystem.md) 합니다.

PowerShell 명령을 사용 하 여 SAP 시스템을 제거 [`Remove-SAPSystem`](module/automation-remove-sapsystem.md)  합니다.


## <a name="other-operations"></a>기타 작업

PowerShell 명령을 사용 하 여 배포 자격 증명을 설정 합니다 [`Set-SAPSecrets`](module/automation-set-sapsecrets.md) .

PowerShell 명령을 사용 하 여 Terraform 상태 파일을 업데이트 합니다 [`Update-TFState`](module/automation-update-tfstate.md) .

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 컨트롤 평면 배포](module/automation-new-sapautomationregion.md)




