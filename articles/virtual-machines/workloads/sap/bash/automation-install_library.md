---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_library sh
description: 셸 스크립트를 사용 하 여 제어 평면에서 새 SAP 라이브러리를 부트스트랩 합니다.
ms.openlocfilehash: 0fadaace80e1b349484c29bd115c4f79e3295530
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730399"
---
# <a name="install_librarysh"></a>Install_library sh

## <a name="synopsis"></a>개요
`install_library.sh`스크립트는 새 SAP 라이브러리를 설정 합니다.

## <a name="syntax"></a>구문

```bash
install_library.sh [ --parameterfile ] <String> [ --deployer_statefile_foldername ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>Description
이 `install_library.sh` 명령은 제어 평면에 새 SAP 라이브러리를 설정 합니다.
SAP 라이브러리는 Terraform 상태 파일 및 SAP 설치 미디어에 대 한 저장소를 제공 합니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1
```bash

install_library.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars -deployer_statefile_foldername ../../DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE
```

## <a name="parameters"></a>매개 변수

### `--parameterfile`
배포자 VM에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [제어 평면 구성](../automation-configure-control-plane.md#deployer)을 참조 하세요.

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_statefile_foldername`
배포자 VM의 매개 변수 파일이 포함 된 폴더에 대 한 상대 폴더 경로를 설정 `terraform.tfstate` 합니다.

```yaml
Type: String
Aliases: `-d`

Required: True
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

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
