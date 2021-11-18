---
external help file: SAPDeploymentUtilities-help.xml
Module Name: SAPDeploymentUtilities
online version: https://github.com/Azure/sap-automation
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: New-SAPDeployer
description: 컨트롤 플레인에서 새 배포자 부트스트랩
ms.openlocfilehash: b4609bb54c9b767d15666ce44259f32c9c71276f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756137"
---
# New-SAPDeployer

## 개요
명령을 사용하여 `New-SAPDeployer` 제어 평면에 새 배포자 VM을 설정할 수 있습니다.

## 구문

```powershell
New-SAPDeployer [-Parameterfile] <String> [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Description
명령은 `New-SAPDeployer` 컨트롤 플레인에 새 배포기를 설정합니다.

배포자 VM에는 Ansible 및 Terraform이 설치되어 있습니다. 배포자 VM을 사용하여 SAP 아티팩트 배포


## 예

### 예 1

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPDeployer -Parameterfile .\MGMT-WEEU-MGMT00-INFRASTRUCTURE.tfvars
```

## 매개 변수

### `-Parameterfile`
배포자 VM에 대한 매개 변수 파일을 설정합니다. 자세한 내용은 [컨트롤 플레인 구성을 참조하세요.](../automation-configure-control-plane.md#deployer)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Silent
메시지를 표시하지 않고 배포

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -WhatIf
cmdlet이 실행되면 어떻게 되는지 보여 주세요. 그러나 cmdlet은 변경하지 않습니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### CommonParameters
`CommonParameters`cmdlet은 , , 및 일반적인 매개 변수를 `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` `-WarningVariable` 지원합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## 참고
v0.9 - 초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-automation)
