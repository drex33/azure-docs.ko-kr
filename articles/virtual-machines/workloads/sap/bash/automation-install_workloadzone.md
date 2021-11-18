---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_workloadzone sh
description: 셸 스크립트를 사용 하 여 새 SAP 작업 영역을 배포 합니다.
ms.openlocfilehash: cb24454eba2a785c2b33244a2474535747102c8f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132750608"
---
# <a name="install_workloadzonesh"></a>install_workloadzone sh

## <a name="synopsis"></a>개요
스크립트를 사용 `install_workloadzone.sh` 하 여 새 SAP 작업 영역을 배포할 수 있습니다.

## <a name="syntax"></a>구문

```bash

install_workloadzone.sh [ -p or --parameterfile ] <String> 
 [[ --deployer_tfstate_key ] <String>] [[ --deployer_environment] <String>] [[ --state_subscription] <String>] [[ --storageaccountname ]
 [[ --subscription] <String>] [[ --spn_id  ] <String>] [[ --spn_secret ] <String>] [[ --tenant_id ] <String>]
 [[ --storageaccountname] <String>] [ force] [-i | --auto-approve]
```

## <a name="description"></a>Description
`install_workloadzone.sh`명령은 새 SAP 작업 영역을 배포 합니다. 작업 영역에는 모든 Vm에 대 한 공유 리소스가 포함 됩니다.

## <a name="examples"></a>예

### <a name="example-1"></a>예 1

이 예에서는 매개 변수 파일에 정의 된 대로 작업 영역을 배포 합니다. 프로세스에서 SPN 세부 정보를 묻는 메시지를 표시 합니다.

```bash

install_workloadzone.sh -parameterfile PROD-WEEU-SAP00-infrastructure.tfvars
```

### <a name="example-2"></a>예제 2

이 예에서는 매개 변수 파일에 정의 된 대로 작업 영역을 배포 합니다. 이 프로세스는 배포 자격 증명을 배포의 주요 자격 증명 모음에 추가 합니다.

```bash
cd ~/Azure_SAP_Automated_Deployment/WORKSPACES/LANDSCAPE/DEV-WEEU-SAP01-INFRASTRUCTURE

export subscriptionID=<subscriptionID>
export appId=<appID>
export spn_secret="<password>"
export tenant_id=<tenant>
export keyvault=<keyvaultName>
export storageaccount=<storageaccountName>
export statefile_subscription=<statefile_subscription>

${DEPLOYMENT_REPO_PATH}/deploy/scripts/install_workloadzone.sh \
        --parameter_file DEV-WEEU-SAP01-INFRASTRUCTURE.tfvars  \
        --keyvault $keyvault                                   \
        --state_subscription $statefile_subscription           \
        --subscription $subscriptionID                         \
        --spn_id $appID                                        \
        --spn_secret $spn_secret                               \ 
        --tenant_id $tenant
```
## <a name="parameters"></a>매개 변수

### `--parameter_file`
작업 영역에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [작업 영역 구성](../automation-configure-workload-zone.md)을 참조 하세요.

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_tfstate_key`
배포자 VM의 Terraform 상태 파일 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-d`

Required: False
```

### `deployer_environment`
배포자 환경 이름

```yaml
Type: String
Aliases: `-e`

Required: False
```

### `--state_subscription`
Terraform 저장소 계정에 대 한 구독 ID를 설정 합니다.

```yaml
Type: String
Aliases: `-k`

Required: False
```
### `--storageaccountname`
Terraform 상태 파일이 포함 된 저장소 계정의 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--keyvault`
배포 자격 증명의 주요 자격 증명 모음을 설정 합니다.

```yaml
Type: String
Aliases: `-v`

Required: False
```

### `--subscription`
대상 Azure 구독을 설정 합니다.

```yaml
Type: String
Aliases: `-s`

Required: False
```

### `-spn_id`
서비스 주체의 앱 ID를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)를 참조 하세요.

```yaml
Type: String
Aliases: `-c`

Required: False
```

### `--spn_secret`
서비스 사용자 암호를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)를 참조 하세요. 

```yaml
Type: String
Aliases: `-p`

Required: False
```

### `--tenant_id`
서비스 사용자에 대 한 테 넌 트 ID를 설정 합니다. 자세한 내용은 [배포 자격 증명 준비](../automation-deploy-control-plane.md#prepare-the-deployment-credentials)를 참조 하세요. 

```yaml
Type: String
Aliases: `-t`

Required: False
```

### `--force`
로컬 구성을 정리 합니다.

```yaml
Type: SwitchParameter
Aliases: `-f`

Required: False
```

### `--auto-approve`
자동 배포를 사용 합니다.

```yaml
Type: SwitchParameter
Aliases: `-i`

Required: False
```

### `--help`
스크립트에 대 한 도움말을 표시 합니다.

```yaml
Type: SwitchParameter
Aliases: `-h`

Required: False
```


## <a name="notes"></a>참고
v 0.9-초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## <a name="related-links"></a>관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-automation)
