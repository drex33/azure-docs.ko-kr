---
title: Azure 디스크 풀(미리 보기)을 다른 구독으로 이동
description: Azure 디스크 풀을 다른 구독으로 이동하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 629ca5617bfc2166e10172863fc6d7560698919d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436939"
---
# <a name="move-a-disk-pool-preview-to-a-different-subscription"></a>디스크 풀(미리 보기)을 다른 구독으로 이동

Azure 디스크 풀(미리 보기)을 이동하려면 디스크 풀 자체, 디스크 풀에 포함된 디스크, 디스크 풀의 관리되는 리소스 그룹, 관리되는 리소스 그룹에 포함된 모든 리소스를 이동해야 합니다. 현재 Azure는 한 번에 여러 리소스 그룹을 다른 구독으로 이동하는 기능을 지원하지 않습니다. 

- 기존 디스크 풀의 템플릿을 내보냅니다.
- 이전 디스크 풀을 삭제합니다.
- 디스크 풀을 만드는 데 필요한 Azure 리소스를 이동합니다.
- 디스크 풀을 다시 배포합니다.

## <a name="export-your-existing-disk-pool-template"></a>기존 디스크 풀 템플릿 내보내기

재배포 프로세스를 간소화하기 위해 기존 디스크 풀에서 템플릿을 내보냅니다. 이 템플릿을 사용하여 선택한 구독에서 디스크 풀을 동일한 구성으로 다시 배포할 수 있습니다. 리소스에서 템플릿을 내보내는 방법을 알아보려면 [이 문서](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource)를 참조하세요.

## <a name="delete-the-old-disk-pool"></a>이전 디스크 풀 삭제

이제 템플릿을 내보냈으므로 이전 디스크 풀을 삭제합니다. 디스크 풀을 삭제하면 디스크 풀 리소스와 해당 관리되는 리소스 그룹이 제거됩니다. 디스크 풀을 삭제하는 방법에 대한 지침은 [이 문서](disks-pools-deprovision.md)를 참조하세요.

## <a name="move-your-disks-and-virtual-network"></a>디스크 및 가상 네트워크 이동

이제 디스크 풀이 삭제되었으므로 가상 네트워크와 디스크, 그리고 잠재적으로 클라이언트를 선택한 구독으로 이동할 수 있습니다. Azure 리소스를 다른 구독으로 이동하는 방법을 알아보려면 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.

## <a name="redeploy-your-disk-pool"></a>디스크 풀 다시 배포

다른 리소스를 구독으로 이동한 후에는 디스크, 가상 네트워크, 서브넷, 클라이언트에 대한 모든 참조가 이제 새로운 리소스 URI를 가리키도록 이전 디스크 풀의 템플릿을 업데이트합니다. 업데이트가 완료되면 새 구독에 템플릿을 다시 배포합니다. 템플릿을 편집하고 배포하는 방법을 알아보려면 [이 문서](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md#edit-and-deploy-the-template)를 참조하세요.

## <a name="next-steps"></a>다음 단계

디스크 풀을 관리하는 방법을 알아보려면 [디스크 풀 관리](disks-pools-manage.md)를 참조하세요.