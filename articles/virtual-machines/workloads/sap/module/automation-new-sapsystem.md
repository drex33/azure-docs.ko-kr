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
title: New-SAPSystem
description: 새 SAP 시스템 배포
ms.openlocfilehash: 64854a2ab24a1d39638316168723b27eb6ba2e4b
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757353"
---
# New-SAPSystem

## 개요
명령을 사용 `New-SAPSystem` 하 여 새 SAP 시스템을 배포할 수 있습니다.

## 구문

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPSystem [-Parameterfile] <String> [-Type] <SAP_Types> [[-DeployerStateFileKeyName] <String>]
 [[-LandscapeStateFileKeyName] <String>] [[-StorageAccountName] <String>] [-Force] [-Silent] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

## Description
명령은 `New-SAPSystem` 새 SAP 시스템을 배포 합니다.

## 예

### 예 1

SAP 시스템을 배포 하거나 업데이트 합니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPSystem -Parameterfile .\DEV-WEEU-SAP00-X00.tfvars -Type sap_system
```

### 예제 2

SAP 시스템을 배포 하거나 업데이트 합니다.


```powershell
Import-Module "SAPDeploymentUtilities.psd1"


New-SAPSystem -Parameterfile .\DEV-WEEU-SAP00-X00.tfvars -Type sap_system -DeployerStateFileKeyName MGMT-WEEU-DEP00-INFRASTRUCTURE.terraform.tfstate -LandscapeStateFileKeyName DEV-WEEU-SAP01-INFRASTRUCTURE.terraform.tfstate
```

### 예제 3
```powershell
Import-Module "SAPDeploymentUtilities.psd1"
New-SAPSystem -Parameterfile .\MGMT-WEEU-SAP_LIBRARY.tfvars -Type sap_library
```

## 매개 변수

### `-Parameterfile`
SAP 시스템에 대 한 매개 변수 파일을 설정 합니다.

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

### `-Type`
배포할 SAPsystem의 유형을 설정 합니다. 유효한 값은 `sap_deployer` ,, `sap_library` `sap_landscape` 및 `sap_system` 입니다.

```yaml
Type: SAP_Types
Parameter Sets: (All)
Aliases:
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -DeployerStateFileKeyName
배포자 배포에 대 한 상태 파일의 이름을 설정 합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -LandscapeStateFileKeyName
작업 영역 배포에 대 한 상태 파일의 이름을 설정 합니다.


```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -StorageAccountName
선택적 Terraform 상태 파일의 저장소 계정 이름을 설정 합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -Force
로컬 구성을 정리 합니다.

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
이 cmdlet은,,,,,,,,, `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` 및 `-WarningVariable` 등의 일반 매개 변수를 지원 합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## 참고
v 0.9-초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-automation)
