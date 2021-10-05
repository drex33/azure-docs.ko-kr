---
title: Azure NetApp Files 볼륨의 네트워크 기능 구성 | Microsoft Docs
description: 네트워크 기능 및 볼륨에 대 한 네트워크 기능 옵션을 구성 하는 방법에 대 한 옵션을 설명 합니다.
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
ms.date: 10/04/2021
ms.custom: references_regions
ms.author: b-juche
ms.openlocfilehash: 6350084ef916132d7b4d77da00853836daeabccd
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539020"
---
# <a name="configure-network-features-for-an-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨의 네트워크 기능 구성

**네트워크 기능** 기능을 공개 미리 보기에서 사용할 수 있습니다.  이 기능을 사용 하 여 Azure NetApp Files 볼륨에 VNet 기능을 사용할지 여부를 지정할 수 있습니다. 이 기능을 사용 하 여 옵션을 **_표준_*_ 또는 _*_기본_** 으로 설정할 수 있습니다. 새 NFS, SMB 또는 이중 프로토콜 볼륨을 만들 때 설정을 지정할 수 있습니다. 네트워크 기능에 대 한 자세한 내용은 [Azure NetApp Files 네트워크 계획에 대 한 지침](azure-netapp-files-network-topologies.md) 을 참조 하세요.

이 문서는 옵션을 이해 하 고 네트워크 기능을 구성 하는 방법을 보여 줍니다.

## <a name="options-for-network-features"></a>네트워크 기능에 대 한 옵션 

네트워크 기능에는 두 가지 설정을 사용할 수 있습니다. 

* ***Standard***  
    이 설정은 볼륨의 VNet 기능을 사용 하도록 설정 합니다.  

    [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md), [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md#user-defined)또는 추가 연결 패턴과 같은 더 높은 IP 제한이 나 VNet 기능이 필요한 경우 **네트워크 기능** 을 *표준* 으로 설정 해야 합니다.

* ***기본***  
    이 설정은 볼륨에 대 한 추가 VNet 기능 없이 제한 된 IP 제한 (<1000)을 제공 합니다.

    VNet 기능이 필요 하지 않은 경우 **네트워크 기능** 을 *기본* 으로 설정 해야 합니다.  

## <a name="supported-regions"></a>지원되는 지역 

현재 네트워크 기능 기능은 다음 지역에서 지원 됩니다. 

* 미국 중북부 

## <a name="considerations"></a>고려 사항

* 설정 하는 네트워크 기능 옵션 (*Standard* 또는 *Basic*)에 관계 없이 Azure VNet에는 azure netapp 파일에 위임 된 서브넷이 하나만 있을 수 있습니다. [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md#considerations)을 참조하세요. 
 
* 현재는 새 볼륨을 만드는 동안에만 네트워크 기능 설정을 지정할 수 있습니다. 기존 볼륨의 설정은 수정할 수 없습니다. 

* 해당 [Azure 지역에서 표준 볼륨 기능을 지 원하는](#supported-regions)경우에만 표준 네트워크 기능을 사용 하 여 볼륨을 만들 수 있습니다. 
    * 지역에 대해 표준 볼륨 기능이 지원 되는 경우 볼륨 만들기 페이지의 네트워크 기능 필드는 기본적으로 *Standard* 로 설정 됩니다. 이 설정은 *기본* 설정으로 변경할 수 있습니다. 
    * 지역에 대해 표준 볼륨 기능을 사용할 수 없는 경우 볼륨 만들기 페이지의 네트워크 기능 필드는 기본적으로 *기본* 으로 설정 되 고 설정을 수정할 수 없습니다.

* 원하는 네트워크 기능 유형과 호환 되는 저장소를 찾는 기능은 지정 된 VNet에 따라 다릅니다.  리소스가 부족 하 여 볼륨을 만들 수 없는 경우 호환 되는 저장소를 사용할 수 있는 다른 VNet을 사용해 볼 수 있습니다.
  
## <a name="register-the-feature"></a>기능 등록 

네트워크 기능 기능은 현재 공개 미리 보기로 제공 됩니다. 이 기능을 처음 사용하는 경우 먼저 기능을 등록해야 합니다.

1.  다음 명령을 실행 하 여 기능을 등록 합니다.

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

2. 기능 등록 상태를 확인합니다. 

    > [!NOTE]
    > **RegistrationState** 는 `Registered`로 변경되기 전까지 최대 60분 동안 `Registering` 상태일 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSDNAppliance

    Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowPoliciesOnBareMetal
    ```

[Azure CLI 명령](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

## <a name="set-the-network-features-option"></a>네트워크 기능 옵션 설정

이 섹션에서는 네트워크 기능 옵션을 설정 하는 방법을 보여 줍니다. 

1. 새 [NFS](azure-netapp-files-create-volumes.md), [SMB](azure-netapp-files-create-volumes-smb.md)또는 [이중 프로토콜](create-volumes-dual-protocol.md) 볼륨을 만드는 과정에서 볼륨 만들기 화면의 기본 탭 아래에서 **네트워크 기능** 옵션을 **기본** 또는 **표준** 으로 설정할 수 있습니다.

    다음 스크린샷은 표준 네트워크 기능 기능을 지 원하는 지역에 대 한 볼륨 만들기 예제를 보여 줍니다. 

    ![표준 네트워크 기능을 위한 볼륨 생성을 보여 주는 스크린샷](../media/azure-netapp-files/network-features-create-standard.png)

    다음 스크린샷은 표준 네트워크 기능 기능을 지원 *하지* 않는 지역에 대 한 볼륨 만들기 예제를 보여 줍니다. 

    ![기본 네트워크 기능을 위한 볼륨 생성을 보여 주는 스크린샷](../media/azure-netapp-files/network-features-create-basic.png)

2. 볼륨 만들기 프로세스를 완료 하기 전에 볼륨 만들기 화면의 **검토 + 만들기** 탭에서 지정 된 네트워크 기능 설정을 표시할 수 있습니다. **만들기** 를 클릭 하 여 볼륨 만들기를 완료 합니다.

    ![볼륨 만들기의 검토 및 만들기 탭을 보여 주는 스크린샷](../media/azure-netapp-files/network-features-review-create-tab.png)

3. **볼륨** 을 클릭 하 여 각 볼륨의 네트워크 기능 설정을 표시할 수 있습니다.

    [![네트워크 기능 설정을 표시 하는 볼륨 페이지를 보여 주는 스크린샷](../media/azure-netapp-files/network-features-volume-list.png)](../media/azure-netapp-files/network-features-volume-list.png#lightbox)

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files 네트워크 계획 지침](azure-netapp-files-network-topologies.md)
* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md) 