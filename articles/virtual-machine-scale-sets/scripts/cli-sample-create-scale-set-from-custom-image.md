---
title: Azure CLI 샘플 - 사용자 지정 VM 이미지 사용
description: 이 Azure CLI 스크립트는 사용자 지정 VM 이미지를 VM 인스턴스의 원본으로 사용하는 가상 머신 확장 집합을 만듭니다.
author: mamccrea
ms.author: mamccrea
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: aaf58fafa757f0d601609682b70a5aa9946d6362
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223541"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli"></a>Azure CLI를 사용하여 사용자 지정 VM 이미지에서 가상 머신 확장 집합 만들기
이 스크립트는 사용자 지정 VM 이미지를 VM 인스턴스의 원본으로 사용하는 가상 머신 확장 집합을 만듭니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신 확장 집합 및 관련된 모든 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/ad/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vmss create](/cli/azure/vmss) | 가상 머신 확장 집합을 만들고 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 연결합니다. 부하 분산 장치도 여러 VM 인스턴스에 트래픽을 분산하기 위해 만듭니다. 또한 이 명령은 사용할 VM 이미지와 관리 자격 증명도 지정합니다.  |
| [az group delete](/cli/azure/ad/group) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계
Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/overview)를 참조하세요.
