---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: install_deployer.sh
description: 셸 스크립트를 사용하여 컨트롤 플레인에서 새 배포자를 부트스트랩합니다.
ms.openlocfilehash: 05d1feee8669d90732db981209d294fbb73b84e8
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759672"
---
# <a name="install_deployersh"></a>install_deployer.sh

## <a name="synopsis"></a>개요
스크립트를 사용하여 `install_deployer.sh` 제어 평면에 새 배포자 VM을 설정할 수 있습니다.

## <a name="syntax"></a>구문

```bash
install_deployer.sh [ --parameterfile ] <String> 
[-i | --auto-approve]
```

## <a name="description"></a>Description
스크립트는 `install_deployer.sh` 컨트롤 플레인에 새 배포기를 설정합니다.

배포자 VM에는 Ansible 및 Terraform이 설치되어 있습니다. 배포자 VM을 사용하여 SAP 아티팩트 배포


## <a name="examples"></a>예

### <a name="example-1"></a>예 1

```bash
install_deployer.sh --parameterfile MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars
```

## <a name="parameters"></a>매개 변수

### `--parameterfile`
배포자 VM에 대한 매개 변수 파일을 설정합니다. 자세한 내용은 [컨트롤 플레인 구성을 참조하세요.](../automation-configure-control-plane.md#deployer)

```yaml
Type: String
Aliases: `-p`

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
