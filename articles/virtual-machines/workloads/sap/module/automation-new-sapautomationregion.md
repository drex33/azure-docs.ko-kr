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
title: New-SAPAutomationRegion
description: 제어 평면 (배포자, SAP 라이브러리)을 배포 합니다.
ms.openlocfilehash: e24353fb30a2895e459e1c8191eb72fbcddb1cd8
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759519"
---
# New-SAPAutomationRegion

## 개요
PowerShell 명령은 `New-SAPAutomationRegion` 배포자 VM, Azure Key Vault 및 SAP 라이브러리를 포함 하 여 제어 평면을 배포 합니다.

배포자 VM에는 Ansible 및 Terraform이 설치 되어 있습니다. 이 VM은 SAP 아티팩트를 배포 합니다.

## 구문

```
New-SAPAutomationRegion [-DeployerParameterfile] <String> [-LibraryParameterfile] <String>
 [[-Subscription] <String>] [[-SPN_id] <String>] [[-SPN_password] <String>] [[-Tenant_id] <String>]
 [[-Vault] <String>] [[-StorageAccountName] <String>] [-Force] [-Silent] [<CommonParameters>]
```

## Description
배포자 VM 및 SAP 라이브러리를 포함 하는 제어 평면을 배포 합니다. 자세한 내용은 [제어 평면 구성](../automation-configure-control-plane.md) 및 [제어 평면 배포](../automation-deploy-control-plane.md) 를 참조 하세요.

## 예

### 예 1

이 예제에서는 매개 변수 파일에 정의 된 대로 제어 평면을 배포 합니다. 프로세스에서 SPN 세부 정보를 묻는 메시지를 표시 합니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
 -LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
```

### 예제 2

이 예제에서는 매개 변수 파일에 정의 된 대로 제어 평면을 배포 합니다. 이 프로세스는 배포 자격 증명을 배포의 주요 자격 증명 모음에 추가 합니다.

```powershell
Import-Module "SAPDeploymentUtilities.psd1"

$Subscription=<subscriptionID>
$SPN_id=<appID>
$SPN_password=<password>
$Tenant_id=<tenant>

New-SAPAutomationRegion -DeployerParameterfile .\DEPLOYER\MGMT-WEEU-DEP01-INFRASTRUCTURE\MGMT-WEEU-DEP01-INFRASTRUCTURE.tfvars 
-LibraryParameterfile .\LIBRARY\MGMT-WEEU-SAP_LIBRARY\MGMT-WEEU-SAP_LIBRARY.tfvars
-Subscription $Subscription
-SPN_id $SPN_id
-SPN_password $SPN_password
-Tenant_id $Tenant_id
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

### `-Subscription`
대상 Azure 구독을 설정 합니다.

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

### `-SPN_id`
서비스 주체의 앱 ID를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)를 참조 하세요.

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

### `-SPN_password`
서비스 사용자 암호를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)를 참조 하세요. 

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

### `-Tenant_id`
서비스 사용자에 대 한 테 넌 트 ID를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)를 참조 하세요. 

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

### `-Vault`
배포의 주요 자격 증명 모음 이름을 설정 합니다.

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

### `-StorageAccountName`
Terraform 상태 파일이 포함 된 저장소 계정의 이름을 설정 합니다.

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

### CommonParameters
이 cmdlet은,,,,,,,,, `-Debug` `-ErrorAction` `-ErrorVariable` `-InformationAction` `-InformationVariable` `-OutVariable` `-OutBuffer` `-PipelineVariable` `-Verbose` `-WarningAction` 및 `-WarningVariable` 등의 일반 매개 변수를 지원 합니다. 자세한 내용은 [about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)를 참조하세요.
## 참고
v 0.9-초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## 관련 링크

+[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-automation)
