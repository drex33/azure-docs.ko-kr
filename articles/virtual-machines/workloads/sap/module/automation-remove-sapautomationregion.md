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
title: Remove-SAPAutomationRegion
description: SAP 제어 평면 (배포자, 라이브러리)을 제거 합니다.
ms.openlocfilehash: 3de052a6ef1ce5e3bacf82cfa7439af49547735a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730474"
---
# Remove-SAPAutomationRegion

## 개요

배포자 VM 및 SAP 라이브러리를 포함 하 여 제어 평면을 제거 합니다.
## 구문

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPAutomationRegion [-DeployerParameterfile] <String> [-LibraryParameterfile] <String>
 [<CommonParameters>]
```

## Description
배포자 VM 및 SAP 라이브러리를 포함 하 여 SAP 제어 평면을 제거 합니다.

## 예제

### 예 1
```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-SAP01-INFRASTRUCTURE\MGMT-WEEU-SAP01-INFRASTRUCTURE.tfvars 
 -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars

```

## 매개 변수

### `-DeployerParameterfile`
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

### `-LibraryParameterfile`
SAP 라이브러리에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [제어 평면 구성](../automation-configure-control-plane.md#sap-library)을 참조 하세요.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
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

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
