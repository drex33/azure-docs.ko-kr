---
title: Azure NetApp Files에 대한 NFS 볼륨 만들기 | Microsoft Docs
description: 이 문서에서는 Azure NetApp Files에서 NFS 볼륨을 만드는 방법을 보여줍니다. 사용할 버전 및 모범 사례와 같은 고려 사항에 대해 알아봅니다.
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
ms.openlocfilehash: e9d3f2443d73f75e96e0036d5c546d00238a58d8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614375"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFS 볼륨 만들기

Azure NetApp Files에서는 NFS(NFSv3 또는 NFSv4.1), SMB3 또는 이중 프로토콜(NFSv3 및 SMB 또는 NFSv4.1 및 SMB)을 사용하여 볼륨 만들기를 지원합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 

이 문서에서는 NFS 볼륨을 만드는 방법을 보여줍니다. SMB 볼륨에 대해서는 [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)를 참조하세요. 이중 프로토콜 볼륨의 경우 [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에 
* 용량 풀을 설정해야 합니다.  
    [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)을 참조하세요.   
* Azure NetApp Files에 서브넷을 위임해야 합니다.  
    [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)을 참조하세요.

## <a name="considerations"></a>고려 사항 

* 사용할 NFS 버전 결정  
  NFSv3는 다양한 사용 사례를 처리할 수 있으며 대부분의 엔터프라이즈 애플리케이션에 공통적으로 배포됩니다. 애플리케이션에 필요한 버전(NFSv3 또는 NFSv4.1)의 유효성을 검사하고 적절한 버전을 사용하여 볼륨을 만들어야 합니다. 예를 들어 [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)를 사용하는 경우 NFSv3 대신 NFSv4.1의 파일 잠금이 권장됩니다. 

* 보안  
  NFSv3 및 NFSv4.1에는 UNIX 모드 비트(읽기, 쓰기 및 실행)에 대한 지원이 제공됩니다. NFS 볼륨을 탑재하려면 NFS 클라이언트에서 루트 수준 액세스 권한이 필요합니다.

* NFSv4.1에 대한 로컬 사용자/그룹 및 LDAP 지원  
  현재 NFSv4.1에서는 볼륨에 대한 루트 액세스만 지원합니다. [Azure NetApp Files에 대한 NFSv4.1 기본 도메인 구성](azure-netapp-files-configure-nfsv41-domain.md)을 참조하세요. 

## <a name="best-practice"></a>모범 사례

* 볼륨에 대한 적절한 탑재 지침을 사용하고 있는지 확인합니다.  [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)를 참조하세요.

* NFS 클라이언트는 Azure NetApp Files 볼륨과 동일한 VNet 또는 피어링 VNet에 있어야 합니다. VNet 외부에서의 연결이 지원되지만 추가 대기 시간이 발생하고 전반적인 성능이 저하됩니다.

* NFS 클라이언트가 최신 상태이며 운영 체제에 대한 최신 업데이트를 실행 중인지 확인합니다.

## <a name="create-an-nfs-volume"></a>NFS 볼륨 만들기

1.  용량 풀 블레이드에서 **볼륨** 블레이드를 클릭합니다. **+ 볼륨 추가** 를 클릭하여 볼륨을 만듭니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  볼륨 만들기 창에서 **만들기** 를 클릭하고 기본 탭에서 다음 필드에 대한 정보를 제공합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 이름은 문자로 시작해야 합니다. 문자, 숫자, 밑줄(‘_’), 하이픈(‘-’)만 포함할 수 있습니다.

        `default` 또는 `bin`을 볼륨 이름으로 사용할 수 없습니다.

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
        
        서브넷을 위임하지 않은 경우에는 볼륨 만들기 페이지에서 **새로 만들기** 를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes** 를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 볼륨에 기존 스냅샷 정책을 적용하려면 **고급 섹션 표시** 를 클릭하여 확장하고, 스냅샷 경로를 숨길지 여부를 지정하고, 풀 다운 메뉴에서 스냅샷 정책을 선택합니다. 

        스냅샷 정책을 만드는 방법에 대한 자세한 내용은 [스냅샷 정책 관리](snapshots-manage-policy.md)를 참조하세요.

        ![고급 선택 표시](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **프로토콜** 을 클릭한 후, 다음 작업을 완료합니다.  
    * **NFS** 를 볼륨에 대한 프로토콜 유형으로 선택합니다.   

    * 볼륨의 고유 **파일 경로** 를 지정합니다. 이 경로는 탑재 대상을 만들 때 사용됩니다. 경로 요구 사항은 다음과 같습니다.   
        - 해당 지역의 각 서브넷 내에서 고유해야 합니다. 
        - 영문자로 시작해야 합니다.
        - 문자, 숫자 또는 대시(`-`)만 사용할 수 있습니다. 
        - 길이가 80자를 초과해서는 안 됩니다.

    * 볼륨의 **버전**(**NFSv3** 또는 **NFSv4.1**)을 선택합니다.  

    * NFSv4.1을 사용하는 경우 볼륨에 **Kerberos** 암호화를 사용할지 여부를 지정합니다.  

        NFSv4.1에서 Kerberos를 사용하는 경우 추가 구성이 필요합니다. [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)의 지침을 따릅니다.

    * Active Directory LDAP 사용자 및 확장 그룹(최대 1024개 그룹)을 사용하여 볼륨에 액세스하려면 **LDAP** 옵션을 선택합니다. [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)의 지침에 따라 필요한 구성을 완료합니다. 
 
    *  필요에 따라 **Unix 권한** 을 사용자 지정하여 탑재 경로에 대한 변경 권한을 지정합니다. 탑재 경로 아래에 있는 파일에는 설정이 적용되지 않습니다. 기본 설정은 `0770`입니다. 이 기본 설정은 소유자와 그룹에 읽기, 쓰기, 실행 권한을 부여하지만 다른 사용자에게는 권한이 부여되지 않습니다.     
        등록 요구 사항과 고려 사항은 **Unix 권한** 설정에 적용됩니다. [Unix 권한 구성 및 소유권 모드 변경](configure-unix-permissions-change-ownership-mode.md)의 지침을 따릅니다.   

    * 필요에 따라 [NFS 볼륨에 대한 내보내기 정책을 구성](azure-netapp-files-configure-export-policy.md)합니다.

    ![NFS 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. **검토 + 만들기** 를 클릭하여 볼륨 정보를 검토합니다.  그런 다음, **만들기** 를 클릭하여 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFSv4.1 기본 도메인 구성](azure-netapp-files-configure-nfsv41-domain.md)
* [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)
* [Azure NetApp Files용 TLS를 통한 ADDS LDAP 구성](configure-ldap-over-tls.md)
* [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)
* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [NFS 볼륨에 대한 내보내기 정책 구성](azure-netapp-files-configure-export-policy.md)
* [Unix 권한을 구성하고 소유권 모드를 변경](configure-unix-permissions-change-ownership-mode.md)합니다. 
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](../virtual-network/virtual-network-for-azure-services.md)
