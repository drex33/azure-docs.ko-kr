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
title: New-SAPWorkloadZone
description: 새 SAP 작업 영역 배포
ms.openlocfilehash: ac984f9ff90ceee4604b50cd2a914e2915d57547
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757334"
---
# New-SAPWorkloadZone

## 개요
명령을 사용 하 여 `New-SAPWorkloadZone` 새 SAP 작업 영역을 배포할 수 있습니다.

## 구문

```powershell
Import-Module "SAPDeploymentUtilities.psd1"


New-SAPWorkloadZone [-Parameterfile] <String> [[-Deployerstatefile] <String>] [[-Deployerenvironment] <String>]
 [[-State_subscription] <String>] [[-Vault] <String>] [[-StorageAccountName] <String>]
 [[-Subscription] <String>] [[-SPN_id] <String>] [[-SPN_password] <String>] [[-Tenant_id] <String>] [-Force]
 [-Silent] [-WhatIf] [-Confirm] [<CommonParameters>]
```

## Description
`New-SAPWorkloadZone`명령은 새 SAP 작업 영역을 배포 합니다. 작업 영역에는 내의 모든 Vm에 대 한 공유 리소스가 포함 됩니다.

## 예

### 예 1

이 예에서는 매개 변수 파일에 정의 된 대로 작업 영역을 배포 합니다. 프로세스에서 SPN 세부 정보를 묻는 메시지를 표시 합니다.

```powershell

Import-Module "SAPDeploymentUtilities.psd1"

New-SAPWorkloadZone -Parameterfile .\PROD-WEEU-SAP00-infrastructure.tfvars
```

### 예제 2

이 예에서는 매개 변수 파일에 정의 된 대로 작업 영역을 배포 합니다. 이 프로세스는 배포 자격 증명을 배포의 주요 자격 증명 모음에 추가 합니다.


```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPWorkloadZone -Parameterfile .\PROD-WEEU-SAP00-infrastructure.tfvars
 -Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
 -SPN_id yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy
 -SPN_password ************************
 -Tenant_id zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz
```

## 매개 변수

### `-Parameterfile`
작업 영역에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [작업 영역 구성](../automation-configure-workload-zone.md)을 참조 하세요.

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

### `-Deployerstatefile`
배포자 VM의 Terraform 상태 파일 이름을 설정 합니다.

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

### `-Deployerenvironment`
배포자 환경 이름

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

### `-State_subscription`
Terraform 저장소 계정에 대 한 구독 ID를 설정 합니다.

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

### `-Vault`
배포 자격 증명의 주요 자격 증명 모음을 설정 합니다.

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

### `-StorageAccountName`
스토리지 계정 이름

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Subscription`
대상 구독을 설정 합니다.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 7
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-SPN_id`
서비스 주체의 앱 ID를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials)를 참조 하세요.
```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 8
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-SPN_password`
서비스 주체의 암호를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials)를 참조 하세요.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 9
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Tenant_id`
테 넌 트 ID를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-workload-zone.md#preparing-the-workload-zone-deployment-credentials)를 참조 하세요.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 10
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### `-Force`
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

### `-Silent`
메시지를 표시 하지 않고 배포 합니다.

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

### `-WhatIf`
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
이 cmdlet은,,,,,,,,, `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` 및 `-WarningVariable` 등의 일반 매개 변수를 지원 합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.


## 참고
v 0.9-초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-automation)
