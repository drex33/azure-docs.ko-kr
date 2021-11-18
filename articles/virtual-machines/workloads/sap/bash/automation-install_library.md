---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_library.sh
description: 셸 스크립트를 사용하여 컨트롤 플레인에서 새 SAP 라이브러리를 부트스트랩합니다.
ms.openlocfilehash: 223589c6e2481df99f2dc8e8f6e4c7e1650680fa
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132750513"
---
# <a name="install_librarysh"></a>Install_library.sh

## <a name="synopsis"></a>개요
`install_library.sh`스크립트는 새 SAP 라이브러리를 설정합니다.

## <a name="syntax"></a>구문

```bash
install_library.sh [ --parameterfile ] <String> [ --deployer_statefile_foldername ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>Description
명령은 `install_library.sh` 컨트롤 플레인에 새 SAP 라이브러리를 설정합니다.
SAP 라이브러리는 Terraform 상태 파일 및 SAP 설치 미디어에 대한 스토리지를 제공합니다.

## <a name="examples"></a>예

### <a name="example-1"></a>예 1
```bash

install_library.sh -Parameterfile MGMT-WEEU-SAP_LIBRARY.tfvars -deployer_statefile_foldername ../../DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE
```

## <a name="parameters"></a>매개 변수

### `--parameterfile`
배포자 VM에 대한 매개 변수 파일을 설정합니다. 자세한 내용은 [컨트롤 플레인 구성을 참조하세요.](../automation-configure-control-plane.md#deployer)

```yaml
Type: String
Aliases: `-p`

Required: True
```

### `--deployer_statefile_foldername`
배포자 VM의 매개 변수 파일()이 포함된 폴더에 대한 상대 폴더 경로를 `terraform.tfstate` 설정합니다.

```yaml
Type: String
Aliases: `-d`

Required: True
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

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-automation)
