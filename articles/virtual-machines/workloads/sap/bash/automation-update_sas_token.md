---
online version: https://github.com/Azure/sap-hana
schema: 2.0.0
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 10/21/2021
ms.topic: reference
ms.service: virtual-machines-sap
title: update_sas_token sh
description: Azure Key Vault에서 SAP 라이브러리 SAS 토큰을 업데이트 합니다.
ms.openlocfilehash: 09b9506c54e2595679d5fcd7189548453472d6a1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730633"
---
# <a name="update_sas_tokensh"></a>update_sas_token sh

## <a name="synopsis"></a>개요
Azure Key Vault에서 SAP 라이브러리 SAS 토큰을 업데이트 합니다.

## <a name="syntax"></a>구문

```bash

update_sas_token.sh
```

## <a name="description"></a>Description
Azure Key Vault에서 SAP 라이브러리 SAS 토큰을 업데이트 합니다. SAP 라이브러리 저장소 계정 이름 및 배포자 key vault 이름을 묻는 메시지를 표시 합니다.

## EXAMPLES

### <a name="example-1"></a>예제 1

SAP 라이브러리 저장소 계정 이름 및 배포자 key vault 이름을 묻는 메시지를 표시 합니다.

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
v 0.9-초기 버전


Copyright (c) Microsoft Corporation.
MIT 라이선스에 따라 사용이 허가됩니다.
## <a name="related-links"></a>관련 링크

[GitHub 리포지토리: SAP 배포 자동화 프레임 워크](https://github.com/Azure/sap-hana)
