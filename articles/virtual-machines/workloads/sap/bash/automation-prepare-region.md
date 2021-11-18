---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: 지역 준비
description: 셸 스크립트를 사용하여 컨트롤 플레인(배포자, SAP 라이브러리)을 배포합니다.
ms.openlocfilehash: 2510e8a289047e21364e16c147290fa0ef9d4475
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730642"
---
# <a name="prepare_regionsh"></a>prepare_region.sh

## <a name="synopsis"></a>개요
`prepare_region.sh`스크립트는 배포자 VM, Azure Key Vault 및 SAP 라이브러리를 포함하여 제어 평면을 배포합니다.

배포자 VM에는 Ansible 및 Terraform이 설치되어 있습니다. 이 VM은 SAP 아티팩트 배포를 합니다.

## <a name="syntax"></a>구문

```bash
prepare_region.sh [ --deployer_parameter_file ] <String> [ --library_parameter_file ] <String>
 [[ --subscription] <String>] [[ --spn_id  ] <String>] [[ --spn_secret ] <String>] [[ --tenant_id ] <String>]
 [[ --storageaccountname] <String>] [ --force ] [ --auto-approve ]
```

## <a name="description"></a>Description
배포자 VM 및 SAP 라이브러리를 포함하는 컨트롤 플레인을 배포합니다. 자세한 내용은 [컨트롤 플레인 구성](../automation-configure-control-plane.md) 및 컨트롤 [플레인 배포를 참조하세요.](../automation-deploy-control-plane.md)

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

이 예제에서는 매개 변수 파일에 정의된 대로 컨트롤 플레인을 배포합니다. 프로세스에서 SPN 세부 정보를 묻는 메시지를 표시합니다.

```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      
```

### <a name="example-2"></a>예제 2

이 예제에서는 매개 변수 파일에 정의된 대로 컨트롤 플레인을 배포합니다. 이 프로세스는 배포의 키 자격 증명 모음에 배포 자격 증명을 추가합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES

az logout
az login

subscriptionID=<subscriptionID>
appId=<appID>
spn_secret=<password>
tenant_id=<tenant>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      \
        --subscription $subscriptionID                                                                           \
        --spn_id $appID                                                                                          \
        --spn_secret "$spn_secret"                                                                               \ 
        --tenant_id $tenant
```

## <a name="parameters"></a>매개 변수

### `--deployer_parameter_file`
배포자 VM에 대한 매개 변수 파일을 설정합니다. 자세한 내용은 [컨트롤 플레인 구성을 참조하세요.](../automation-configure-control-plane.md#deployer)

```yaml
Type: String
Aliases: `-d`

Required: True
```

### `--library_parameter_file`
SAP 라이브러리에 대한 매개 변수 파일을 설정합니다. 자세한 내용은 [컨트롤 플레인 구성을 참조하세요.](../automation-configure-control-plane.md#sap-library)

```yaml
Type: String
Aliases: `-l`

Required: True
```

### `--subscription`
대상 Azure 구독을 설정합니다.

```yaml
Type: String
Aliases: `-s`

Required: False
```

### `-spn_id`
서비스 주체의 앱 ID를 설정합니다. 자세한 내용은 [배포 자격 증명 준비를 참조하세요.](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)

```yaml
Type: String
Aliases: `-c`

Required: False
```

### `--spn_secret`
서비스 주체 암호를 설정합니다. 자세한 내용은 [배포 자격 증명 준비를 참조하세요.](../automation-deploy-control-plane.md#prepare-the-deployment-credentials) 

```yaml
Type: String
Aliases: `-p`

Required: False
```

### `--tenant_id`
서비스 주체의 테넌트 ID를 설정합니다. 자세한 내용은 [배포 자격 증명 준비를 참조하세요.](../automation-deploy-control-plane.md#prepare-the-deployment-credentials) 

```yaml
Type: String
Aliases: `-t`

Required: False
```


### `--storageaccountname`
Terraform 상태 파일이 포함된 스토리지 계정의 이름을 설정합니다.

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--force`
로컬 구성을 정리합니다.

```yaml
Type: SwitchParameter
Aliases: `-f`

Required: False
```

### `--auto-approve`
자동 배포를 사용하도록 설정합니다.

```yaml
Type: SwitchParameter
Aliases: `-i`

Required: False
```

### `--help`
스크립트에 대한 도움말을 표시합니다.

```yaml
Type: SwitchParameter
Aliases: `-h`

Required: False
```

## <a name="notes"></a>참고
v0.9 - 초기 버전

Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## <a name="related-links"></a>관련 링크

+[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-hana)
