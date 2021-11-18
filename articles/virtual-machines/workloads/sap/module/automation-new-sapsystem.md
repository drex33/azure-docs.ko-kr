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
title: New-SAPSystem
description: 새 SAP 시스템 배포
ms.openlocfilehash: 888fb0f1ee736dcb495117da8c931c76cf7d75a6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730476"
---
# New-SAPSystem

## 개요
명령을 사용하여 새 SAP 시스템을 배포할 수 `New-SAPSystem` 있습니다.

## 구문

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPSystem [-Parameterfile] <String> [-Type] <SAP_Types> [[-DeployerStateFileKeyName] <String>]
 [[-LandscapeStateFileKeyName] <String>] [[-StorageAccountName] <String>] [-Force] [-Silent] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

## Description
명령은 `New-SAPSystem` 새 SAP 시스템을 배포합니다.

## 예제

### 예 1

SAP 시스템을 배포하거나 업데이트합니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPSystem -Parameterfile .\DEV-WEEU-SAP00-X00.tfvars -Type sap_system
```

### 예제 2

SAP 시스템을 배포하거나 업데이트합니다.


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
SAP 시스템의 매개 변수 파일을 설정합니다.

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
배포할 SAPsystem의 유형을 설정합니다. 유효한 값은 `sap_deployer` , , 및 `sap_library` `sap_landscape` `sap_system` 입니다.

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
배포자 배포에 대한 상태 파일의 이름을 설정합니다.

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
작업 영역 배포에 대한 상태 파일의 이름을 설정합니다.


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
선택적 Terraform 상태 파일의 스토리지 계정 이름을 설정합니다.

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
로컬 구성을 정리합니다.

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
이 cmdlet은 일반적인 매개 `-Debug` 변수인 , `-ErrorAction` , , , , , , , , 및 를 `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` `-WarningVariable` 지원합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## 참고
v0.9 - 초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-hana)
