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
title: Remove-SAPSystem
description: SAP 배포 제거
ms.openlocfilehash: dd0f86e35c0530103b7df882d0bb89008ccf3ae0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730473"
---
# Remove-SAPSystem

## 개요
SAP 배포 제거

## Syntax

매개 변수 파일로 정의된 SAP 배포를 제거합니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-SAPSystem [-Parameterfile] <String> [-Type] <String> [<CommonParameters>]
```

## 설명
매개 변수 파일로 정의된 SAP 배포를 제거합니다.

## 예제

### 예 1

SAP 시스템을 제거합니다.

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

Remove-System -Parameterfile .\PROD-WEEU-SAP00-X00.tfvars -Type sap_system
```

### 예제 2

SAP 라이브러리를 제거합니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Remove-System -Parameterfile .\PROD-WEEU-SAP_LIBRARY.tfvars -Type sap_library
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
SAP 시스템의 유형을 설정합니다.

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
이 cmdlet은 일반적인 매개 `-Debug` 변수인 , `-ErrorAction` , , , , , , , , 및 를 `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` `-WarningVariable` 지원합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## 참고
v0.9 - 초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-hana)
