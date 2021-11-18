---
external help file: SAPDeploymentUtilities-help.xml
Module Name: SAPDeploymentUtilities
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: New-SAPDeployer
description: 컨트롤 평면에서 새 배포자를 부트스트랩 합니다.
ms.openlocfilehash: 26c2510726f8d24337d92546c06ec33accfb056e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730484"
---
# New-SAPDeployer

## 개요
명령을 사용 하 여 `New-SAPDeployer` 제어 평면에 새 배포자 VM을 설정할 수 있습니다.

## 구문

```powershell
New-SAPDeployer [-Parameterfile] <String> [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Description
이 명령은 `New-SAPDeployer` 컨트롤 평면에 새 배포자을 설정 합니다.

배포자 VM에는 Ansible 및 Terraform이 설치 되어 있습니다. 배포자 VM을 사용 하 여 SAP 아티팩트를 배포 합니다.


## 예제

### 예 1

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPDeployer -Parameterfile .\MGMT-WEEU-MGMT00-INFRASTRUCTURE.tfvars
```

## 매개 변수

### `-Parameterfile`
배포자 VM에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [제어 평면 구성](../automation-configure-control-plane.md#deployer)을 참조 하세요.

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

### -자동
메시지를 표시 하지 않고 배포

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
Cmdlet이 실행 될 경우 발생 하는 상황을 보여 줍니다. 그러나이 cmdlet은 아무 변경도 수행 하지 않습니다.

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
Cmdlet은,,,,,,,,, `CommonParameters`  `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` 및와 `-WarningVariable` 같은 일반 매개 변수를 지원 합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## 참고
v 0.9-초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
