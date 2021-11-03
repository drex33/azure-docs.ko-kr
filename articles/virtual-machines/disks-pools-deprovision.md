---
title: Azure 디스크 풀 프로비전 해제(미리 보기)
description: Azure 디스크 풀의 프로비전을 해제하고, 중지하고, 삭제하는 방법에 대해 알아봅니다.
author: roygara
ms.date: 11/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 36c301cb5575d7627a6179520aef79c01f8cd837
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022135"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>Azure 디스크 풀 프로비전 해제(미리 보기)

이 문서에서는 Azure 디스크 풀(미리 보기)에 대한 삭제 프로세스와 iSCSI 지원을 사용하지 않는 방법에 대해 설명합니다.

## <a name="stop-a-disk-pool"></a>디스크 풀 중지

디스크 풀을 중지하여 비용을 절감하고 모든 구성을 유지할 수 있습니다. 디스크 풀을 중지하면 더 이상 iSCSI를 통해 연결할 수 없습니다. 디스크 풀을 지원하기 위해 배포된 관리되는 리소스는 삭제되지 않습니다. 디스크 풀을 중지하기 전에 먼저 iSCSI 연결이 있는 모든 클라이언트와 디스크 풀의 연결을 끊어야 합니다. 언제든지 디스크 풀을 시작할 수 있습니다. 그러면 이 디스크 풀에 노출된 iSCSI 대상이 다시 활성화됩니다.
# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 디스크 풀로 이동하고 **중지를** 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name '<yourDiskPool>' -ResourceGroupName '<yourResourceGroup>'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool stop --name "<yourDiskPool>" --resource-group "<yourResourceGroup>"
```
---

## <a name="disable-iscsi-support"></a>iSCSI 지원 사용 안 함

디스크 풀에서 iSCSI 지원을 사용하지 않으면 더 이상 디스크 풀에 연결할 수 없습니다.

먼저 디스크 풀에서 iSCSI 지원을 사용하면 iSCSI 대상이 iSCSI 연결에 대한 엔드포인트로 생성됩니다. iSCSI 대상을 삭제하여 디스크 풀에서 iSCSI 지원을 사용하지 않을 수 있습니다. 각 디스크 풀에는 하나의 iSCSI 대상만 구성될 수 있습니다.

기존 디스크 풀에서 iSCSI 지원을 다시 사용할 수 있습니다. 디스크 풀에 대해 처리 중인 iSCSI 연결이 있는 경우 디스크 풀에서 iSCSI 지원을 사용하지 않을 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. **디스크 풀** 을 검색하고 디스크 풀을 선택합니다.
1. **설정** 아래에서 **iSCSI** 를 선택합니다.
1. **iSCSI 사용** 확인란을 선택 취소하고 **저장** 을 선택합니다.    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "<yourDiskpoolName>" -Name "<youriSCSITargetName>" -ResourceGroupName "yourResourceGroup>"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "<yourDiskPool>" --name "<yourIscsiTarget>" --resource-group "<yourResourceGroup>"
```
---

## <a name="delete-a-disk-pool"></a>디스크 풀 삭제

디스크 풀을 삭제하면 관리되는 리소스 그룹의 모든 리소스도 삭제됩니다. 디스크 풀에 대해 처리 중인 iSCSI 연결이 있는 경우 디스크 풀을 삭제할 수 없습니다. 먼저 iSCSI 연결이 있는 모든 클라이언트와 디스크 풀의 연결을 끊어야 합니다. 디스크 풀에 추가된 디스크는 삭제되지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **디스크 풀** 을 검색하고 선택한 다음, 삭제하려는 디스크 풀을 선택합니다.
1. 창의 위쪽에서 **삭제** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name "<yourDiskPoolName>" -ResourceGroupName "<yourResourceGroup>"

Remove-AzDiskPool -Name "<yourDiskPoolName>" -ResourceGroupName "<yourResourceGroup>
Remove-AzDiskPool -Name "<yourDiskpoolName>" -ResourceGroupName "<yourResourceGroup>"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool delete --name "<yourDiskPool>" --resource-group "<yourResourceGroup>"
```

---

## <a name="next-steps"></a>다음 단계

[Azure 관리 디스크](managed-disks-overview.md)에 대해 자세히 알아봅니다.
