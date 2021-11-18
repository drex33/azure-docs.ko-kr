---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: advanced_state_management
description: 셸 스크립트를 사용 하 여 Terraform 상태 파일을 업데이트 합니다.
ms.openlocfilehash: 40a7810227802415fa69578936f00d7715d40191
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730397"
---
# <a name="advanced_state_managementsh"></a>advanced_state_management sh

## <a name="synopsis"></a>개요
Terraform 상태 파일을 업데이트 합니다.

## <a name="syntax"></a>구문

```bash

advanced_state_management.sh [--parameterfile] <String> 
[--type] <String> 
[--terraform_keyfile] <String>
[--subscription] <String> 
[--storage_account_name] <String> 
[--tf_resource_name] <String>
[--azure_resource_id] <String> 
[--help]
```

## <a name="description"></a>Description
이 cmdlet을 사용 하 여 누락 또는 수정 된 리소스를 Terraform 상태 파일에 추가할 수 있습니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

```bash

advanced_state_management.sh --parameterfile DEV-WEEU-SAP01-X00.tfvars --type sap_system
 --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
 --storage_account_name devweeutfstateABC 
 --terraform_keyfile DEV-WEEU-SAP01-X00.terraform.tfstate 
 --tf_resource_name module.sap_system.azurerm_resource_group.deployer[0] 
 --azure_resource_id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/DEV-WEEU-SAP01-X00
```

## <a name="parameters"></a>매개 변수

### `--parameterfile`
시스템에 대 한 매개 변수 파일을 설정 합니다.

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
시스템의 유형을 설정 합니다. 유효한 값은 `sap_deployer` ,, `sap_library` `sap_landscape` 및 `sap_system` 입니다.

```yaml
Type: String
Aliases: `-t`
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system

Required: True
```

### `--terraform_keyfile`
Terraform 상태 파일의 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-k`

Required: True
```

### `--subscription`
대상 Azure 구독을 설정 합니다.

```yaml
Type: String
Aliases: `-s`

Required: False
```

### `--storageaccountname`
Terraform 상태 파일이 포함 된 저장소 계정의 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-a`

Required: False
```

### `--tf_resource_name`
Terraform 상태 파일의 리소스 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-n`

Required: False
```

### `--azure_resource_id`
가져올 Azure 리소스의 리소스 ID를 설정 합니다.

```yaml
Type: String
Aliases: `-i`

Required: False
```

## <a name="notes"></a>참고
v 0.9-초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.

## <a name="related-links"></a>관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
