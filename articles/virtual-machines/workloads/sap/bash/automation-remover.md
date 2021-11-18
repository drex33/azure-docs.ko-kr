---
online version: https://github.com/Azure/sap-automation
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: remover.sh
description: 셸 스크립트를 사용하여 새 SAP 시스템을 제거합니다.
ms.openlocfilehash: 53245ebf535e555eb804e88180e11f711d8c334c
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758816"
---
# <a name="removersh"></a>Remover.sh

## <a name="synopsis"></a>개요
명령을 사용하여 새 SAP 시스템을 제거할 수 `remover.sh` 있습니다. 스크립트를 사용하여 워크로드 영역 및 SAP 시스템을 제거할 수 있습니다.

## <a name="syntax"></a>구문

```bash

remover.sh [--parameterfile] <String> [--type] <String> [ --help ]<String>]
s>]
```

## <a name="description"></a>Description
`remover.sh`스크립트는 지정된 형식의 새 배포를 배포하거나 업데이트합니다.

## <a name="examples"></a>예

### <a name="example-1"></a>예 1

SAP 시스템 배포를 제거합니다.

```bash

remover.sh --parameterfile DEV-WEEU-SAP00-X00.tfvars --type sap_system
```

### <a name="example-2"></a>예제 2

워크로드 배포를 제거합니다.

```bash
remover.sh --parameterfile DEV-WEEU-SAP00-INFRASTRUCTURE.tfvars --type sap_landscape
```

## <a name="parameters"></a>매개 변수

### `--parameter_file`
시스템의 매개 변수 파일을 설정합니다.

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

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-automation)
