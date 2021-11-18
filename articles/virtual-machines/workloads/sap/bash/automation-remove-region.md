---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: Remove_region.sh
description: 셸 스크립트를 사용하여 SAP 컨트롤 플레인(배포자, 라이브러리)을 제거합니다.
ms.openlocfilehash: 29f7461208120ee7d88c44943b7c393a0ca47ebf
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730640"
---
# <a name="remove_regionsh"></a>Remove_region.sh

## <a name="synopsis"></a>개요

배포자 VM 및 SAP 라이브러리를 포함하여 컨트롤 플레인을 제거합니다.
## <a name="syntax"></a>구문

```bash

Remove_region.sh  [-d or --deployer_parameter_file ] <String> [-l or --library_parameter_file ] <String>
```

## <a name="description"></a>Description
배포자 VM 및 SAP 라이브러리를 포함하여 SAP 컨트롤 플레인을 제거합니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1
```bash
${DEPLOYMENT_REPO_PATH}/deploy/scripts/prepare_region.sh                                                         \
        --deployer_parameter_file DEPLOYER/MGMT-WEEU-DEP00-INFRASTRUCTURE/MGMT-WEEU-DEP00-INFRASTRUCTURE.tfvars  \
        --library_parameter_file LIBRARY/MGMT-WEEU-SAP_LIBRARY/MGMT-WEEU-SAP_LIBRARY.tfvars                      
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
