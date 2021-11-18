---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: remover.sh
description: 셸 스크립트를 사용 하 여 새 SAP 시스템을 제거 합니다.
ms.openlocfilehash: a6d99d966a6be9ede32d26bef3757038260b0579
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730438"
---
# <a name="removersh"></a>Remover.sh

## <a name="synopsis"></a>개요
명령을 사용 `remover.sh` 하 여 새 SAP 시스템을 제거할 수 있습니다. 스크립트를 사용 하 여 작업 영역 및 SAP 시스템을 제거할 수 있습니다.

## <a name="syntax"></a>구문

```bash

remover.sh [--parameterfile] <String> [--type] <String> [ --help ]<String>]
s>]
```

## <a name="description"></a>Description
`remover.sh`스크립트는 지정 된 형식의 새 배포를 배포 하거나 업데이트 합니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

SAP 시스템 배포를 제거 합니다.

```bash

remover.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>예제 2

작업 배포를 제거 합니다.

```bash
remover.sh --parameterfile DEV-WEEU-SAP00-INFRASTRUCTURE.tfvars --type sap_landscape
```

## <a name="parameters"></a>매개 변수

### `--parameter_file`
시스템에 대 한 매개 변수 파일을 설정 합니다.

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--type`
배포 유형을 설정 합니다. 유효한 값은 `sap_deployer` ,, `sap_library` `sap_landscape` 및 `sap_system` 입니다.

```yaml
Type: String
Accepted values: sap_deployer, sap_landscape, sap_library, sap_system
Aliases: `-t`

Required: True
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

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
