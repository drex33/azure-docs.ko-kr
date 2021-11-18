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
title: Set-SAPSecrets
description: Azure Key vault에서 SPN 암호를 설정 합니다.
ms.openlocfilehash: 5bf2d85e57827227a3ab81a0658a64eee668a097
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730472"
---
# Set-SAPSecrets

## 개요
Azure Key Vault에서 서비스 주체 암호를 설정 합니다.

## 구문

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Set-SAPSecrets [-Region] <String> [-Environment] <String> [-VaultName] <String> [-SPN_id] <String>
 [-SPN_password] <String> [-Tenant_id] <String> [-Workload] [<CommonParameters>]
```

## Description
배포 자동화에 필요한 Key Vault 암호를 설정 합니다.

## EXAMPLES

### 예제 1

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

Set-SAPSecrets -Environment PROD -VaultName <vaultname> -SPN_id <appId> -SPN_password <clientsecret> -Tenant_id <Tenant_idID>
```

## 매개 변수

### `-Region`
배포를 위한 Azure 지역의 이름을 설정 합니다.

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

### `-Environment`
배포 환경의 이름을 설정 합니다.

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

### `-VaultName`
키 자격 증명 모음의 이름을 설정 합니다.

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

### `-SPN_id`
서비스 사용자 응용 프로그램 ID를 설정 합니다.

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

### `-SPN_password`
서비스 주체의 암호를 설정 합니다.

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

### `-Tenant_id`
테 넌 트 ID를 설정 합니다.

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

### `-Workload`
작업을 설정 합니다.

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

### CommonParameters
이 cmdlet은,,,,,,,,, `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` 및 `-WarningVariable` 등의 일반 매개 변수를 지원 합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.

## 참고
v 0.9-초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.
## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
