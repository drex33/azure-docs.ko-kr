---
title: Azure NetApp Files에 대한 SMB 볼륨 만들기 | Microsoft Docs
description: 이 문서에서는 Azure NetApp Files에서 SMB3 볼륨을 만드는 방법을 보여줍니다. Active Directory 연결 및 도메인 서비스에 대한 요구 사항을 알아봅니다.
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
ms.author: b-juche
ms.openlocfilehash: 738e3d6cb3b27180cd8337e09a72fbe3a0639edf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240056"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 SMB 볼륨 만들기

Azure NetApp Files에서는 NFS(NFSv3 또는 NFSv4.1), SMB3 또는 이중 프로토콜(NFSv3 및 SMB 또는 NFSv4.1 및 SMB)을 사용하여 볼륨 만들기를 지원합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 

이 문서에서는 SMBv3 볼륨을 만드는 방법을 보여줍니다. NFS 볼륨에 대해서는 [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)를 참조하세요. 이중 프로토콜 볼륨의 경우 [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에 

* 용량 풀을 설정해야 합니다. [용량 풀 만들기를](azure-netapp-files-set-up-capacity-pool.md)참조 하세요.     
* Azure NetApp Files에 서브넷을 위임해야 합니다. [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)을 참조하세요.

## <a name="configure-active-directory-connections"></a>Active Directory 연결 구성 

SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Azure NetApp 파일에 대해 Active Directory 연결을 구성하지 않은 경우 [Active Directory 연결 만들기 및 관리](create-active-directory-connections.md)에 설명된 지침을 따르세요.

## <a name="add-an-smb-volume"></a>SMB 볼륨 추가

1. 용량 풀 블레이드에서 **볼륨** 블레이드를 클릭합니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가** 를 클릭하여 볼륨을 만듭니다.  
    볼륨 만들기 창이 나타납니다.

3. 볼륨 만들기 창에서 **만들기** 를 클릭하고 기본 사항 탭 아래에 있는 다음 필드에 정보를 제공합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 이름은 문자로 시작해야 합니다. 문자, 숫자, 밑줄('_') 및 하이픈('-')만 포함할 수 있습니다. 

        `default` 또는 `bin`은 볼륨 이름으로 사용할 수 없습니다.

    * **용량 풀**  
        볼륨을 만들 용량 풀을 지정합니다.

    * **할당량**  
        볼륨에 할당되는 논리 스토리지의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **처리량(MiB/S)**    
        볼륨이 수동 QoS 용량 풀에 만들어지면 볼륨에 대해 원하는 처리량을 지정합니다.   

        볼륨이 자동 QoS 용량 풀에 만들어지면 이 필드에 표시되는 값은 (할당량 x 서비스 수준 처리량)입니다.   

    * **가상 네트워크**  
        볼륨에 액세스하려는 Microsoft Azure Virtual Network(VNet)를 지정합니다.  

        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 영역에 있는 VNet에서만 액세스할 수 있습니다. Express Route를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우 볼륨 만들기 페이지에서 **새로 만들기** 를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes** 를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * **네트워크 기능**  
        지원 되는 지역에서는 볼륨에 대 한 **기본** 또는 **표준** 네트워크 기능을 사용할지 여부를 지정할 수 있습니다. 자세한 내용은 [볼륨의 네트워크 기능 구성](configure-network-features.md) 및 [Azure NetApp Files 네트워크 계획에 대 한 지침](azure-netapp-files-network-topologies.md) 을 참조 하세요.

    * 볼륨에 기존 스냅샷 정책을 적용하려면 **고급 섹션 표시** 를 클릭하여 확장하고, 스냅샷 경로를 숨길지 여부를 지정하고, 풀 다운 메뉴에서 스냅샷 정책을 선택합니다. 

        스냅샷 정책을 만드는 방법에 대한 자세한 내용은 [스냅샷 정책 관리](snapshots-manage-policy.md)를 참조하세요.

        ![고급 선택 표시](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. **프로토콜** 을 클릭하고 다음 정보를 입력합니다.  
    * 볼륨의 프로토콜 유형으로 **SMB** 를 선택합니다.  

    * 드롭다운 목록에서 **Active Directory** 연결을 선택합니다.  
    
    * 볼륨의 고유한 **공유 이름** 을 지정합니다. 이 공유 이름은 탑재 대상을 만들 때 사용됩니다. 공유 이름 요구 사항은 다음과 같습니다.   
        - 해당 지역의 각 서브넷 내에서 고유해야 합니다. 
        - 영문자로 시작해야 합니다.
        - 문자, 숫자 또는 대시(`-`)만 사용할 수 있습니다. 
        - 길이가 80자를 초과해서는 안 됩니다.   
        
    * <a name="smb3-encryption"></a>SMB3에 대한 암호화를 사용하도록 설정하려면 **SMB3 프로토콜 암호화 사용** 을 선택합니다.   
        이 기능을 통해 전송 중인 SMB3 데이터에 대한 암호화를 사용할 수 있습니다. SMB3 암호화를 사용하지 않는 SMB 클라이언트는 이 볼륨에 액세스할 수 없습니다.  미사용 데이터는 이 설정에 관계없이 암호화됩니다.  
        자세한 내용은 [SMB 암호화](azure-netapp-files-smb-performance.md#smb-encryption)를 참조하세요. 

        **SMB3 프로토콜 암호화** 기능은 현재 미리 보기 상태입니다. 이 기능을 처음 사용하는 경우 사용하기 전에 등록합니다. 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        기능 등록 상태를 확인합니다. 

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경되기 전 최대 60분 동안 `Registering` 상태에 있을 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        [Azure CLI 명령](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다.  
    * <a name="continuous-availability"></a>SMB 볼륨에 대한 지속적인 가용성을 사용하도록 설정하려면 **지속적인 가용성 사용** 을 선택합니다.    

        > [!IMPORTANT]   
        > SMB 지속적인 가용성 기능은 현재 공개 미리 보기로 제공됩니다. **[Azure NetApp Files SMB 지속적인 가용성 공유 공개 미리 보기 대기 목록 제출 페이지](https://aka.ms/anfsmbcasharespreviewsignup)** 를 통해 기능에 액세스하기 위한 대기 목록 요청을 제출해야 합니다. 지속적인 가용성 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 메일을 기다리세요.   
        > 
        SQL Server 및 [FSLogix 사용자 프로필 컨테이너](../virtual-desktop/create-fslogix-profile-container.md)에 대해서만 지속적인 가용성을 사용하도록 설정해야 합니다. SQL Server 및 FSLogix 사용자 프로필 컨테이너 이외의 워크로드에는 SMB 지속적인 가용성 공유를 사용할 수 *없습니다*. 이 기능은 현재 Windows SQL Server에서 지원됩니다. Linux SQL Server는 현재 지원되지 않습니다. 비관리자(도메인) 계정을 사용하여 SQL Server를 설치하는 경우 계정에 필요한 보안 권한이 할당되어 있어야 합니다. 도메인 계정에 필요한 보안 권한(`SeSecurityPrivilege`)이 없고 도메인 수준에서 권한을 설정할 수 없는 경우에는 Active Directory 연결의 **보안 권한 사용자** 필드를 사용하여 계정에 권한을 부여하면 됩니다. [Active Directory 연결 만들기](create-active-directory-connections.md#create-an-active-directory-connection)를 참조하세요.

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![SMB 볼륨 만들기의 프로토콜 탭을 설명하는 스크린샷입니다.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **검토 + 만들기** 를 클릭하여 볼륨 정보를 검토합니다.  그런 다음, **만들기** 를 클릭하여 SMB 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="control-access-to-an-smb-volume"></a>SMB 볼륨에 대한 액세스 제어  

SMB 볼륨에 대한 액세스는 사용 권한을 통해 관리합니다. 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 파일 및 폴더 사용 권한  

Windows SMB 클라이언트에서 개체 속성의 **보안** 탭을 사용하여 파일 또는 폴더에 대한 사용 권한을 설정할 수 있습니다.
 
![파일 및 폴더 사용 권한 설정](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>다음 단계  

* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files에 대해 TLS를 통한 ADDS LDAP 구성](configure-ldap-over-tls.md) 
* [기존 SMB 볼륨에서 지속적인 가용성 사용](enable-continuous-availability-existing-SMB.md)
* [SMB 암호화](azure-netapp-files-smb-performance.md#smb-encryption)
* [Azure NetApp Files 볼륨 오류 문제 해결](troubleshoot-volumes.md)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](../virtual-network/virtual-network-for-azure-services.md)
* [Azure 명령줄 인터페이스를 사용하여 새 Active Directory 포리스트 설치](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
