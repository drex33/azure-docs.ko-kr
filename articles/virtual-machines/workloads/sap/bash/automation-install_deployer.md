---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_deployer sh
description: 셸 스크립트를 사용 하 여 제어 평면에서 새 배포자를 부트스트랩 합니다.
ms.openlocfilehash: 5b9bea75379c1dd99917424eff23b976291c073b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730405"
---
# <a name="install_deployersh"></a>install_deployer sh

## <a name="synopsis"></a>개요
스크립트를 사용 하 여 `install_deployer.sh` 제어 평면에 새 배포자 VM을 설정할 수 있습니다.

## <a name="syntax"></a>구문

```bash
install_deployer.sh [ --parameterfile ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>Description
이 스크립트는 `install_deployer.sh` 제어 평면에 새 배포자을 설정 합니다.

배포자 VM에는 Ansible 및 Terraform이 설치 되어 있습니다. 배포자 VM을 사용 하 여 SAP 아티팩트를 배포 합니다.


## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

```bash
install_deployer.sh --parameterfile MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars
```

## <a name="parameters"></a>매개 변수

### `--parameterfile`
배포자 VM에 대 한 매개 변수 파일을 설정 합니다. 자세한 내용은 [제어 평면 구성](../automation-configure-control-plane.md#deployer)을 참조 하세요.

```yaml
Type: String
Aliases: `-p`

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
