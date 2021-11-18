---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: installer.sh
description: 셸 스크립트를 사용하여 새 SAP 시스템을 배포합니다.
ms.openlocfilehash: ba189394a6bd4ae30c43f4dcbfcb6a1f9c4d0f17
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730396"
---
# <a name="installersh"></a>Installer.sh

## <a name="synopsis"></a>개요
명령을 사용하여 새 SAP 시스템을 배포할 수 `installer.sh` 있습니다. 스크립트를 사용하여 다양한 유형의 배포를 모두 배포할 수 있습니다.

## <a name="syntax"></a>구문

```bash

installer.sh [--parameterfile] <String> [--type] <String> [[--deployer_tfstate_key] <String>]
 [[ --landscape_tfstate_key] <String>] [[--storageaccountname] <String>] [[ --state_subscription ] <String>] [[ --state_subscription ] <String>] [[ --state_subscription ] [ --force ] [ --auto-approve ]<String>]
s>]
```

## <a name="description"></a>Description
`installer.sh`스크립트는 지정된 형식의 새 SAP 시스템을 배포하거나 업데이트합니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

SAP 시스템을 배포하거나 업데이트합니다.

```bash

installer.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>예제 2

SAP 시스템을 배포하거나 업데이트합니다.

```bash

installer.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system \ 
--deployer_tfstate_key MGMT-WEEU-DEP00-INFRASTRUCTURE.terraform.tfstate  \
--landscape_tfstate_key DEV-WEEU-SAP01-INFRASTRUCTURE.terraform.tfstate
```

### <a name="example-3"></a>예제 3

SAP 라이브러리를 배포하거나 업데이트합니다.

```bash
installer.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars --type sap_library
```

## <a name="parameters"></a>매개 변수

### `--parameter_file`
시스템의 매개 변수 파일을 설정합니다. 자세한 내용은 [SAP 시스템 구성을 참조하세요.](../automation-configure-system.md)

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
배포 유형을 설정합니다. 유효한 값은 `sap_deployer` , , 및 `sap_library` `sap_landscape` `sap_system` 입니다.

```yaml
Type: String
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system
Aliases: `-t`

Required: True
```

### <a name="--deployer_tfstate_key"></a>--deployer_tfstate_key
배포자 배포에 대한 상태 파일의 이름을 설정합니다.

```yaml
Type: String
Aliases: `-d`

Required: False
```

### <a name="-landscape_tfstate_key"></a>-landscape_tfstate_key
작업 영역 배포에 대한 상태 파일의 이름을 설정합니다.

```yaml
Type: String
Aliases: `-l`

Required: False
```

### `--state_subscription`
Terraform 스토리지 계정의 구독 ID를 설정합니다.

```yaml
Type: String
Aliases: `-k`

Required: False
```
### `--storageaccountname`
Terraform 상태 파일이 포함된 스토리지 계정의 이름을 설정합니다.

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--keyvault`
배포 자격 증명의 키 자격 증명 모음을 설정합니다.

```yaml
Type: String
Aliases: `-v`

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

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-hana)
