---
online version: https://github.com/Azure/sap-automation
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: update_sas_token.sh
description: Azure Key Vault SAP 라이브러리 SAS 토큰을 업데이트합니다.
ms.openlocfilehash: 27d1a15f49a00d78c027f445eb6f2308b9bbf078
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759500"
---
# <a name="update_sas_tokensh"></a>update_sas_token.sh

## <a name="synopsis"></a>개요
Azure Key Vault SAP 라이브러리 SAS 토큰을 업데이트합니다.

## <a name="syntax"></a>구문

```bash

update_sas_token.sh
```

## <a name="description"></a>Description
Azure Key Vault SAP 라이브러리 SAS 토큰을 업데이트합니다. SAP 라이브러리 스토리지 계정 이름 및 배포자 키 자격 증명 모음 이름을 묻는 메시지를 표시합니다.

## EXAMPLES

### <a name="example-1"></a>예제 1

SAP 라이브러리 스토리지 계정 이름 및 배포자 키 자격 증명 모음 이름을 묻는 메시지를 표시합니다.

```bash

update_sas_token.sh
```

### <a name="example-2"></a>예제 2

```bash

export SAP_LIBRARY_TF=mgmtweeusaplibXXX
export SAP_KV_TF=MGMTWEEUDEP00userYYY

update_sas_token.sh
```


## <a name="parameters"></a>매개 변수

없음

## <a name="notes"></a>참고
v0.9 - 초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.
## <a name="related-links"></a>관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임워크](https://github.com/Azure/sap-automation)
