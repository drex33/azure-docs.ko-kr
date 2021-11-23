---
title: '`arcdata` 확장 설치'
description: Azure(az) CLI용 `arcdata` 확장을 설치합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: c33dea26b8d989e46e10af55403cb663188b243e
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132957103"
---
# <a name="install-arcdata-azure-cli-extension"></a>`arcdata` Azure CLI 확장 설치

> [!IMPORTANT]
> 새 릴리스로 업데이트하는 경우 최신 버전의 Azure CLI 및 `arcdata` 확장으로도 업데이트해야 합니다.


## <a name="install-latest-azure-cli"></a>최신 Azure CLI 설치 

최신 Azure CLI를 가져오려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.


## <a name="add-arcdata-extension"></a>`arcdata` 확장 추가

확장을 추가하려면 다음 명령을 실행합니다. 

```azurecli
az extension add --name arcdata 
```

[Azure CLI 확장에 대해 자세히 알아보세요](/cli/azure/azure-cli-extensions-overview).

## <a name="update-arcdata-extension"></a>`arcdata` 확장 업데이트

확장이 이미 있는 경우 다음 명령을 사용하여 업데이트할 수 있습니다.

```azurecli
az extension update --name arcdata
```

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)
