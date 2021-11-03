---
title: Azure HPC Cache에 대한 Azure CLI 사전 요구 사항
description: Azure CLI를 사용하여 Azure HPC Cache를 만들거나 수정하기 전의 설정 단계
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: femila
ms.openlocfilehash: 90d139cf2e839544eb94545f010b2fa0d917e8cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016115"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure HPC Cache용 Azure CLI 설치

Azure CLI를 사용하여 Azure HPC Cache를 만들거나 관리하기 전에 다음 단계를 수행하여 사용자 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache에는 Azure CLI 버전 2.7 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="set-default-resource-group-optional"></a>기본 리소스 그룹 설정(선택 사항)

대부분의 hpc-cache 명령을 사용하려면 캐시의 리소스 그룹을 전달해야 합니다. [az config](/cli/azure/reference-index#az_config)를 사용하여 기본 리소스 그룹을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure CLI 확장을 설치하고 로그인한 후 Azure CLI를 사용하여 Azure HPC Cache 시스템을 만들고 관리할 수 있습니다.

* [Azure HPC Cache 만들기](hpc-cache-create.md)
* [Azure CLI hpc-cache 설명서](/cli/azure/hpc-cache)
