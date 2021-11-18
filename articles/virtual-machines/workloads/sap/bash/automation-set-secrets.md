---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: set_secrets sh
description: 셸 스크립트를 사용 하 여 Azure Key vault에서 SPN 암호를 설정 합니다.
ms.openlocfilehash: 7f1c6102648aef85dbcb69c5adbdb4c2eeddb490
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730635"
---
# <a name="set_secretssh"></a>set_secrets sh

## <a name="synopsis"></a>개요
Azure Key Vault에서 서비스 주체 암호를 설정 합니다.

## <a name="syntax"></a>구문

```bash

set_secrets.sh [--region] <String> [--environment] <String> [--vault] <String> [--subscription] <String> [--spn_id] <String>
 [--spn_secret] <String> [--tenant_id] <String>
```

## <a name="description"></a>Description
배포 자동화에 필요한 Key Vault 암호를 설정 합니다.

## EXAMPLES

### <a name="example-1"></a>예제 1

```bash

set_secrets.sh --environment DEV                        \
    --region weeu                                       \
    --vault MGMTWEEUDEP00userABC                        \
    --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --spn_id yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy       \
    --spn_secret ************************               \
    --tenant_id zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz     
```

## <a name="parameters"></a>매개 변수

### `--region`
배포를 위한 Azure 지역의 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-r`

Required: True
```

### `--environment`
배포 환경의 이름을 설정 합니다.

```yaml
Type: String
```yaml
Type: String
Aliases: `-e`

Required: True
```

### `--vault`
키 자격 증명 모음의 이름을 설정 합니다.

```yaml
Type: String
Aliases: `-v`

Required: True
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

## <a name="notes"></a>참고
v 0.9-초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.
## <a name="related-links"></a>관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
