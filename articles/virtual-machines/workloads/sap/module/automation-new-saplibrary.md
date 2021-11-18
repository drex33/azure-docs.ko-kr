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
title: New-SAPLibrary
description: 제어 평면에서 새 SAP 라이브러리를 부트스트랩 합니다.
ms.openlocfilehash: 0f060d042aeb86c13f4820baad5832d3ea6c1720
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730481"
---
# New-SAPLibrary

## 개요
명령은 `New-SAPLibrary` 새 SAP 라이브러리를 설정 합니다.

## 구문

```powershell
New-SAPLibrary [-Parameterfile] <String> [[-DeployerFolderRelativePath] <String>] [-Silent] [-WhatIf]
 [-Confirm] [<CommonParameters>]
```

## Description
SAP 라이브러리는 Terraform 상태 파일 및 SAP 설치 미디어에 대 한 저장소를 제공 합니다.

## 예제

### 예 1
```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPLibrary -Parameterfile .\MGMT-WEEU-SAP_LIBRARY.tfvars -DeployerFolderRelativePath ..\..\DEPLOYER\MGMT-WEEU-DEP00-INFRASTRUCTURE\
```

## 매개 변수

### `-Parameterfile`
SAP 라이브러리에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [제어 평면 구성](../automation-configure-control-plane.md#sap-library)을 참조 하세요.

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

### -DeployerFolderRelativePath
배포자 VM의 매개 변수 파일이 포함 된 폴더에 대 한 상대 폴더 경로를 설정 `terraform.tfstate ` 합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -자동
자동 배포를 사용 합니다.

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
Cmdlet은,,,,,,,,, `CommonParameters` `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` 및 `-WarningVariable` 등의 일반 매개 변수를 지원 합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.


## 참고
v 0.9-초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)

