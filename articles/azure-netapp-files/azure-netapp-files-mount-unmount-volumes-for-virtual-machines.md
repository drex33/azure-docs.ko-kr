---
title: 가상 컴퓨터에 대한 Azure NetApp Files 볼륨 탑재
description: Azure에서 Windows 가상 머신 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제하는 방법에 대해 알아봅니다.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 78932885ee7c1ec3650de08efe340e998a54acf7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240037"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제 

필요에 따라 Windows 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제할 수 있습니다.  Linux 가상 머신에 대한 탑재 지침은 Azure NetApp Files에서 사용할 수 있습니다.  

## <a name="requirements"></a>요구 사항 

* NFS 볼륨에 액세스할 수 있으려면 내보내기 정책이 하나 이상 있어야 합니다.
* NFS 볼륨을 성공적으로 탑재하려면 클라이언트와 NFS 볼륨 간에 다음 NFS 포트가 열려 있는지 확인합니다.
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr`(NFSv3만 해당)
    * 4046 TCP/UDP = `status`(NFSv3만 해당)

## <a name="steps"></a>단계

1. **볼륨** 블레이드를 클릭한 다음 마운트할 볼륨을 선택합니다. 
2. 선택한 볼륨에서 **마운트 지침** 을 클릭한 다음 지침에 따라 볼륨을 마운트합니다. 

    ![NFS 마운트 지침](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![마운트 지침 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * NFS 볼륨을 마운트하는 경우 `mount` 명령에서 `vers` 옵션을 사용하여 마운트할 볼륨에 해당하는 NFS 프로토콜 버전을 지정해야 합니다. 
    * NFSv4.1을 사용하는 경우 다음 명령을 사용하여 파일 시스템을 마운트합니다. `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > NFSv4.1을 사용하고 사용 사례에 호스트 이름이 동일한 VM을 활용하는 것이 포함되는 경우(예: DR 테스트) [NFSv4.1 볼륨에 액세스하기 위해 동일한 호스트 이름으로 두개의 VM 구성](configure-nfs-clients.md#configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes)을 참조하세요.

3. Azure VM이 시작되거나 다시 부팅될 때 NFS 볼륨이 자동으로 탑재되도록 하려면 호스트의 `/etc/fstab` 파일에 항목을 추가합니다. 

    예: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`은 볼륨 속성 블레이드에있는 Azure NetApp Files 볼륨의 IP 주소입니다.
    * `$FILEPATH`은 Azure NetApp Files 볼륨의 내보내기 경로입니다.
    * `$MOUNTPOINT`은 NFS 내보내기를 마운트하는 데 사용되는 Linux 호스트에 생성된 디렉터리입니다.

4. NFS를 사용하여 Windows에 볼륨을 탑재하려는 경우:

    a. 먼저 Unix 또는 Linux VM에 볼륨을 탑재합니다.  
    b. 볼륨에 대해 `chmod 777` 또는 `chmod 775` 명령을 실행합니다.  
    다. Windows에서 NFS 클라이언트를 통해 볼륨을 탑재합니다.
    
5. NFS Kerberos 볼륨을 마운트하려는 경우 자세한 내용은 [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 NFSv4.1 기본 도메인 구성](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS FAQ](faq-nfs.md)
* [네트워크 파일 시스템 개요](/windows-server/storage/nfs/nfs-overview)
* [NFS Kerberos 볼륨 마운트](configure-kerberos-encryption.md#kerberos_mount)
* [NFSv4.1 볼륨에 액세스하기 위해 동일한 호스트 이름으로 두개의 VM 구성](configure-nfs-clients.md#configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes) 
