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
title: Update-TFState
description: Terraform 상태 파일을 업데이트합니다.
ms.openlocfilehash: f20c04a79364230f8918a08b97e58e4a881b2a0f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730471"
---
# Update-TFState

## 개요
Terraform 상태 파일을 업데이트합니다.

## 구문

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Update-TFState [-Parameterfile] <String> [-Type] <SAP_Types> [-TerraformStateFileName] <String>
 [-Subscription] <String> [-StorageAccountName] <String> [-TerraformResourceName] <String>
 [-AzureResourceID] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Description
이 cmdlet을 사용하여 누락되거나 수정된 리소스를 Terraform 상태 파일에 추가할 수 있습니다.

## 예제

### 예 1

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Update-TFState -Parameterfile .\DEV-WEEU-SAP01-X00.tfvars -Type sap_system
 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
 -StorageAccountName devweeutfstate### 
 -TerraformStateFileName DEV-WEEU-SAP01-X00.terraform.tfstate 
 -TerraformResourceName module.sap_system.azurerm_resource_group.deployer[0] 
 -AzureResourceID /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/DEV-WEEU-SAP01-X00
```

## 매개 변수

### `-Parameterfile`
시스템의 매개 변수 파일을 설정합니다.

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
시스템 유형을 설정합니다. 유효한 값은 `sap_deployer` , , 및 `sap_library` `sap_landscape` `sap_system` 입니다.

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

### `-TerraformStateFileName`
Terraform 상태 파일의 이름을 설정합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Subscription`
Terraform 상태를 포함하는 Azure 구독을 설정합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-StorageAccountName`
Terraform 상태 파일의 스토리지 계정 이름을 설정합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-TerraformResourceName`
Terraform 상태 파일에서 리소스 이름을 설정합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-AzureResourceID`
가져올 Azure 리소스의 리소스 ID를 설정합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 7
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-WhatIf`
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

### `-Confirm`
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
