---
title: SMB 다중 채널 사용
description: Azure 프리미엄 파일 공유에서 SMB 다중 채널을 사용하도록 설정하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7bbfba257126b741751d169af9390615bec6917c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459682"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>FileStorage 계정에서 SMB 다중 채널 사용(미리 보기) 

Azure FileStorage 계정은 프리미엄 파일 공유에 대한 여러 네트워크 연결을 설정하여 SMB 3.x 클라이언트의 성능을 향상시키는 SMB 다중 채널(미리 보기)을 지원합니다. 이 문서에서는 기존 스토리지 계정에서 SMB 다중 채널을 사용하도록 설정하는 단계별 지침을 제공합니다. Azure Files SMB 다중 채널에 대한 자세한 내용은  [SMB 다중 채널 성능](storage-files-smb-multichannel-performance.md)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="limitations"></a>제한 사항

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [FileStorage 계정을 만듭니다](./storage-how-to-create-file-share.md).

## <a name="getting-started"></a>시작

PowerShell 창을 열고 Azure 구독에 로그인합니다. 여기에서 다음 명령을 사용하여 SMB 다중 채널 미리 보기에 등록할 수 있습니다.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> 등록은 최대 1시간 정도 걸릴 수 있습니다.

### <a name="verify-that-feature-registration-is-complete"></a>기능 등록이 완료되었는지 확인

스토리지 계정에서 이 기능을 사용하도록 설정하는 데 최대한 시간이 걸릴 수 있기 때문에 다음 명령을 사용하여 해당 기능이 구독에 등록되어 있는지 확인하는 것이 좋습니다.

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>SMB 다중 채널 사용 
FileStorage 계정을 만든 후에는 지침에 따라 스토리지 계정에 대한 SMB 다중 채널 설정을 업데이트할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
1. Azure Portal에 로그인하여 SMB 다중 채널을 구성하려는 FileStorage 스토리지 계정으로 이동합니다.
1. **파일 서비스** 에서 **파일 공유** 를 선택한 다음 **파일 공유 설정** 을 선택합니다.
1. **SMB 다중 채널** 을 **사용**(또는 **사용 안 함**)으로 설정하고 **저장** 을 선택합니다.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="SMB 다중 채널을 사용하도록 설정된 스토리지 계정의 스크린샷."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

**파일 공유 설정** 에서 SMB 다중 채널 옵션이 표시되지 않거나 구성을 업데이트하는 동안 업데이트 실패 오류가 발생하는 경우 구독이 등록되어 있는지 그리고 지원되는 계정 유형 및 복제를 사용하여 계정이 지원되는 [지역](#regional-availability) 중 하나에 있는지 확인하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이러한 PowerShell 명령을 실행하기 전에 `$resourceGroupName` 및 `$storageAccountName` 변수를 리소스 그룹 및 스토리지 계정으로 설정합니다.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI는 아직 SMB 다중 채널 구성을 지원하지 않습니다. 스토리지 계정에서 SMB 다중 채널을 구성하는 방법에 대해서는 포털 지침을 참조하세요.

---

> [!NOTE]
> SMB 다중 채널 구성 설정에 대한 모든 변경 내용은 스토리지 계정의 모든 파일 공유에 적용됩니다. 그러나 변경 내용을 적용하려면 클라이언트에서 공유를 다시 탑재해야 합니다.


## <a name="next-steps"></a>다음 단계 

- SMB 다중 채널을 활용하도록 [파일 공유](storage-how-to-use-files-windows.md)를 다시 탑재합니다.
- [SMB 다중 채널과 관련하여 발생한 문제를 해결합니다](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- 향상된 기능에 대해 자세한 내용은 [SMB 다중 채널 성능](storage-files-smb-multichannel-performance.md) 참조
- Windows SMB 다중 채널 기능에 대해 자세히 알아보려면 [SMB 다중 채널 관리](/azure-stack/hci/manage/manage-smb-multichannel)를 참조하세요.
