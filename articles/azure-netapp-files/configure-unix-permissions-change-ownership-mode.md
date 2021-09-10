---
title: Azure NetApp Files NFS 및 이중 프로토콜 볼륨에 대해 Unix 권한 및 소유권 모드 변경 구성 | Microsoft Docs
description: Azure NetApp Files NFS 및 이중 프로토콜 볼륨에 대해 Unix 권한 및 소유권 모드 변경 옵션을 설정하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/06/2021
ms.author: b-juche
ms.openlocfilehash: bfb794b1344dbc4723ca699f572b487b54d60c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567393"
---
# <a name="configure-unix-permissions-and-change-ownership-mode-for-nfs-and-dual-protocol-volumes"></a>NFS 및 이중 프로토콜 볼륨에 대해 Unix 권한 및 소유권 모드 변경 구성

`Unix` 보안 스타일을 사용하는 Azure NetApp Files NFS 볼륨 또는 이중 프로토콜 볼륨에 대해 **Unix 권한** 및 **소유권 모드 변경**( **`Chown Mode`** ) 옵션을 설정할 수 있습니다. 볼륨을 만드는 동안 또는 볼륨을 만든 후에 이러한 설정을 지정할 수 있습니다. 

## <a name="unix-permissions"></a>Unix 권한   

Azure NetApp Files **Unix 권한** 기능을 사용하면 탑재 경로에 대한 변경 권한을 지정할 수 있습니다. 탑재 경로 아래에 있는 파일에는 설정이 적용되지 않습니다.   

Unix 권한 설정은 기본적으로 `0770`으로 설정되어 있습니다. 이 기본 설정은 소유자와 그룹에 읽기, 쓰기, 실행 권한을 부여하지만 다른 사용자에게는 권한이 부여되지 않습니다. 

 사용자 지정 Unix 권한 값(예: `0755`)을 지정하여 소유자, 그룹 또는 다른 사용자에게 원하는 권한을 부여할 수 있습니다.  

## <a name="change-ownership-mode"></a>소유권 모드 변경   

소유권 모드 변경( **`Chown Mode`** ) 기능을 사용하면 파일 및 디렉터리의 소유권 관리 기능을 설정할 수 있습니다.  볼륨의 내보내기 정책에서 설정을 지정하거나 수정할 수 있습니다. **`Chown Mode`** 에는 두 가지 옵션을 사용할 수 있습니다.   

* `Restricted`(기본값) - 루트 사용자만 파일 및 디렉터리의 소유권을 변경할 수 있습니다.
* `Unrestricted` - 루트가 아닌 사용자가 자신이 소유하는 파일 및 디렉터리의 소유권을 변경할 수 있습니다.

## <a name="considerations"></a>고려 사항  

* 지정한 Unix 권한은 볼륨 탑재 지점(루트 디렉터리)에만 적용됩니다.  
* 지역 간 복제 구성에 있는 원본 또는 대상 볼륨에 대한 Unix 권한은 수정할 수 없습니다. 

## <a name="steps"></a>단계

1. Unix 권한 및 소유권 모드 변경 기능은 현재 미리 보기로 제공됩니다. 처음으로 이러한 기능을 사용하려면 먼저 기능을 등록해야 합니다.   

    1. **Unix 권한** 기능을 등록합니다.   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions
        ```

    2.  **소유권 모드 변경** 기능을 등록합니다.    

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```

    3. 기능 등록 상태를 확인합니다.    

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경되기 전까지 최대 60분 동안 `Registering` 상태일 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions  
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```
        
    [Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

2. [NFS 볼륨을 만들거나](azure-netapp-files-create-volumes.md) [이중 프로토콜 볼륨을 만들](create-volumes-dual-protocol.md) 때 **프로토콜** 탭에서 **Unix 권한** 및 소유권 모드 변경( **`Chown Mode`** ) 설정을 지정할 수 있습니다. 

    다음 예제에서는 NFS 볼륨에 대한 볼륨 만들기 화면을 보여 줍니다. 

    ![NFS에 대한 볼륨 만들기 화면을 보여 주는 스크린샷](../media/azure-netapp-files/unix-permissions-create-nfs-volume.png)

3. 기존 NFS 또는 이중 프로토콜 볼륨의 경우 다음과 같이 **Unix 권한** 및 **소유권 모드 변경** 을 설정하거나 수정할 수 있습니다.  

    1. Unix 권한을 수정하려면 **볼륨** 을 마우스 오른쪽 단추로 클릭하고 **편집** 을 선택합니다. 편집 창이 표시되면 **Unix 권한** 에 대한 값을 지정합니다.  
        ![Unix 권한에 대한 편집 화면을 보여 주는 스크린샷](../media/azure-netapp-files/unix-permissions-edit.png)

    2. 소유권 모드 변경을 수정하려면 **볼륨** 을 클릭하고 **정책 내보내기** 를 클릭한 다음, **`Chown Mode`** 설정을 수정합니다.  
        ![정책 내보내기 화면을 보여 주는 스크린샷](../media/azure-netapp-files/chown-mode-edit.png)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md) 
* [내보내기 정책 구성](azure-netapp-files-configure-export-policy.md)
