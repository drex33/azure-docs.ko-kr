---
title: Azure NetApp Files용 이중 프로토콜 볼륨 만들기 | Microsoft Docs
description: LDAP 사용자 매핑을 지원하는 이중 프로토콜(NFSv3과 SMB 또는 NFSv4.1과 SMB)을 사용하는 볼륨을 만드는 방법을 설명합니다.
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
ms.openlocfilehash: 33e01466a3e0629af9a691e33eb9161bf8098611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537107"
---
# <a name="create-a-dual-protocol-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기

Azure NetApp Files에서는 NFS(NFSv3 또는 NFSv4.1), SMB3 또는 이중 프로토콜(NFSv3과 SMB 또는 NFSv4.1과 SMB)을 사용하여 볼륨을 만들 수 있습니다. 이 문서에서는 LDAP 사용자 매핑을 지원하는 이중 프로토콜을 사용하는 볼륨을 만드는 방법을 보여 줍니다. 

NFS 볼륨을 만들려면 [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)를 참조하세요. SMB 볼륨을 만들려면 [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)를 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에 

* 용량 풀이 이미 만들어져 있어야 합니다.  
    [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)을 참조하세요.   
* Azure NetApp Files에 서브넷을 위임해야 합니다.  
    [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)을 참조하세요.

## <a name="considerations"></a>고려 사항

* [Active Directory 연결에 대한 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections)을 충족하는지 확인합니다. 
* AD(Active Directory)에 `pcuser` 계정을 만들고 계정을 사용할 수 있도록 설정합니다. 이 계정은 기본 사용자 역할을 합니다. NTFS 보안 스타일로 구성된 이중 프로토콜 볼륨에 액세스하기 위해 UNIX 사용자를 매핑하는 데 사용됩니다. `pcuser` 계정은 AD에 사용자가 없는 경우에만 사용됩니다. POSIX 특성이 설정된 AD에 사용자 계정이 있는 경우 해당 계정이 인증에 사용되고 `pcuser` 계정에 매핑되지 않습니다. 
* DNS 서버에 역방향 조회 영역을 만든 다음 이 역방향 조회 영역에 AD 호스트 컴퓨터의 포인터(PTR) 레코드를 추가합니다. 그러지 않으면 이중 프로토콜 볼륨 만들기가 실패합니다.
* Active Directory 연결의 **LDAP를 사용하는 로컬 NFS 사용자 허용** 옵션은 로컬 사용자에게 간헐적이고 일시적인 액세스를 제공하기 위한 것입니다. 이 옵션을 사용하도록 설정하면 LDAP 서버의 사용자 인증 및 조회는 중지됩니다. 따라서 로컬 사용자가 LDAP 사용 볼륨에 액세스해야 하는 경우를 제외하고 Active Directory 연결에서 이 옵션을 ‘사용 안 함’으로 유지해야 합니다. 해당 경우에는 볼륨에 대한 로컬 사용자 액세스가 더 이상 필요하지 않은 즉시 이 옵션을 사용하지 않도록 설정해야 합니다. 로컬 사용자 액세스를 관리하는 방법에 대한 자세한 내용은 [LDAP를 사용하는 로컬 NFS 사용자가 이중 프로토콜 볼륨에 액세스할 수 있도록 허용](#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume)을 참조하세요.
* NFS 클라이언트가 최신 상태이며 운영 체제에 대한 최신 업데이트를 실행 중인지 확인합니다.
* 이중 프로토콜 볼륨은 ADDS(Active Directory Domain Services)와 AADDS(Azure Active Directory Domain Services)를 모두 지원합니다. 
* 이중 프로토콜 볼륨은 AADDS에서 TLS를 통한 LDAP 사용을 지원하지 않습니다. [TLS를 통한 LDAP 고려 사항](configure-ldap-over-tls.md#considerations)을 참조하세요.
* 이중 프로토콜 볼륨에서 사용하는 NFS 버전은 NFSv3 또는 NFSv4.1입니다. 고려 사항은 다음과 같습니다.
    * 이중 프로토콜은 NFS 클라이언트의 Windows ACL 확장 특성 `set/get`을 지원하지 않습니다.
    * NFS 클라이언트는 NTFS 보안 스타일에 대한 권한을 변경할 수 없으며 Windows 클라이언트는 UNIX 스타일의 이중 프로토콜 볼륨에 대한 권한을 변경할 수 없습니다.   

        다음 표에서는 보안 스타일 및 이에 대한 영향을 설명합니다.  
        
        | 보안 스타일    | 사용 권한을 수정할 수 있는 클라이언트   | 클라이언트에서 사용할 수 있는 사용 권한  | 결과적으로 효과적인 보안 스타일    | 파일에 액세스할 수 있는 클라이언트     |
        |-  |-  |-  |-  |-  |
        | `Unix`    | NFS   | NFSv3 또는 NFSv4.1 모드 비트    | UNIX  | NFS 및 Windows   |
        | `Ntfs`    | Windows   | NTFS ACL     | NTFS  |NFS 및 Windows|

    * 이름 매핑이 발생하는 방향(Windows-UNIX 또는 UNIX-Windows)은 사용되는 프로토콜과 볼륨에 적용되는 보안 스타일에 따라 달라집니다. Windows 클라이언트에는 항상 Windows-UNIX 이름 매핑이 필요합니다. 사용자에게 검토 권한이 적용되는지 여부는 보안 스타일에 따라 달라집니다. 반대로, NTFS 보안 스타일을 사용 중인 경우 NFS 클라이언트는 UNIX-Windows 이름 매핑만 사용해야 합니다. 

        다음 표에서는 이름 매핑과 보안 스타일에 대해 설명합니다.  
    
        |     프로토콜          |     보안 스타일          |     이름 매핑 방향          |     적용되는 권한          |
        |-|-|-|-|
        |  SMB  |  `Unix`  |  Windows-UNIX  |  UNIX(모드 비트 또는 NFSv4.x ACL)  |
        |  SMB  |  `Ntfs`  |  Windows-UNIX  |  NTFS ACL(공유에 액세스하는 Windows SID 기준)  |
        |  NFSv3  |  `Unix`  |  없음  |  UNIX(모드 비트 또는 NFSv4.x ACL) <br><br>  NFSv4.x ACL은 NFSv4.x 관리 클라이언트를 사용하여 적용하고 NFSv3 클라이언트에 적용할 수 있습니다.  |
        |  NFS  |  `Ntfs`  |  UNIX-Windows  |  NTFS ACL(매핑된 Windows 사용자 SID 기준)  |

* 토폴로지가 크고 확장 그룹이 있는 LDAP 또는 이중 프로토콜 볼륨이 포함된 `Unix` 보안 스타일을 사용하는 경우 Azure NetApp Files는 토폴로지의 서버에 액세스하지 못할 수 있습니다.  이러한 상황이 발생하면 계정 팀에 문의하여 도움을 받으세요.  <!-- NFSAAS-15123 --> 
* 이중 프로토콜 볼륨을 만들기 위한 서버 루트 CA 인증서가 필요하지 않습니다. LDAP over TLS를 사용하는 경우에만 필요합니다.

## <a name="create-a-dual-protocol-volume"></a>이중 프로토콜 볼륨 만들기

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

        스냅샷 정책을 만드는 방법에 대한 자세한 내용은 [스냅샷 정책 관리](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)를 참조하세요.

        ![고급 선택 표시](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **프로토콜** 탭을 클릭하고 다음 작업을 완료합니다.  
    * 볼륨의 프로토콜 유형으로 **이중 프로토콜** 을 선택합니다.   

    * 사용할 **Active Directory** 연결을 지정합니다.

    * 고유한 **볼륨 경로** 를 지정합니다. 이 경로는 탑재 대상을 만들 때 사용됩니다. 경로 요구 사항은 다음과 같습니다.  

        - 해당 지역의 각 서브넷 내에서 고유해야 합니다. 
        - 영문자로 시작해야 합니다.
        - 문자, 숫자 또는 대시(`-`)만 사용할 수 있습니다. 
        - 길이가 80자를 초과해서는 안 됩니다.

    * 이중 프로토콜에 사용할 **버전** 을 지정합니다(**NFSv4.1과 SMB** 또는 **NFSv3과 SMB**).

        **NFSv4.1과 SMB** 이중 프로토콜을 사용하는 기능은 현재 미리 보기로 제공됩니다. 이 기능을 처음 사용하는 경우 기능을 등록해야 합니다.  

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```

        기능 등록 상태를 확인합니다. 

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경하기 전까지 최대 60분 동안 `Registering` 상태가 될 수 있습니다. 상태가 **Registered** 가 될 때까지 기다린 후에 계속하세요.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```
        [Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

    * 사용할 **보안 스타일** 을 NTFS(기본값) 또는 UNIX로 지정합니다.

    * 이중 프로토콜 볼륨에 대해 SMB3 프로토콜 암호화를 사용하도록 설정하려면 **SMB3 프로토콜 암호화 사용** 을 선택합니다.   

        이 기능을 통해 전송 중인 SMB3 데이터에 대한 암호화를 사용할 수 있습니다. 전송 중인 NFSv3 데이터는 암호화하지 않습니다. SMB3 암호화를 사용하지 않는 SMB 클라이언트는 이 볼륨에 액세스할 수 없습니다. 미사용 데이터는 이 설정에 관계없이 암호화됩니다. 자세한 내용은 [SMB 암호화](azure-netapp-files-smb-performance.md#smb-encryption)를 참조하세요. 

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

    * 이중 프로토콜 볼륨 버전으로 NFSv4.1과 SMB를 선택한 경우 볼륨에 대해 **Kerberos** 암호화를 사용하도록 설정할지 여부를 지정합니다.

        Kerberos를 사용하려면 추가 구성이 필요합니다. [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)의 지침을 따릅니다.

    *  필요에 따라 **Unix 권한** 을 사용자 지정하여 탑재 경로에 대한 변경 권한을 지정합니다. 탑재 경로 아래에 있는 파일에는 설정이 적용되지 않습니다. 기본 설정은 `0770`입니다. 이 기본 설정은 소유자와 그룹에 읽기, 쓰기, 실행 권한을 부여하지만 다른 사용자에게는 권한이 부여되지 않습니다.     
        등록 요구 사항과 고려 사항은 **Unix 권한** 설정에 적용됩니다. [Unix 권한 구성 및 소유권 모드 변경](configure-unix-permissions-change-ownership-mode.md)의 지침을 따릅니다.  

    * 필요에 따라 [볼륨에 대한 내보내기 정책을 구성](azure-netapp-files-configure-export-policy.md)합니다.

    ![이중 프로토콜 지정](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. **검토 + 만들기** 를 클릭하여 볼륨 정보를 검토합니다. 그런 다음, **만들기** 를 클릭하여 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>LDAP을 사용하는 로컬 NFS 사용자가 이중 프로토콜 볼륨에 액세스할 수 있도록 허용 

Active Directory 연결의 **LDAP를 사용하는 로컬 NFS 사용자 허용** 옵션을 사용하면 Windows LDAP 서버에 없는 로컬 NFS 클라이언트 사용자가 확장된 그룹을 사용할 수 있는 LDAP가 있는 이중 프로토콜 볼륨에 액세스할 수 있습니다. 

> [!NOTE] 
> 이 옵션을 사용하도록 설정하기 전에 [고려 사항](#considerations)을 이해해야 합니다.   
> **LDAP를 사용하여 로컬 NFS 사용자 허용** 옵션은 **확장 그룹을 사용하는 LDAP** 기능의 일부이며 등록이 필요합니다. 자세한 내용은 [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)을 참조하세요.

1. **Active Directory 연결** 을 클릭합니다.  기존 Active Directory 연결에서 컨텍스트 메뉴(점 3개 `…`)를 클릭하고 **편집** 을 선택합니다.  

2. 표시되는 **Active Directory 설정 편집** 창에서 **LDAP를 사용하여 로컬 NFS 사용자 허용** 옵션을 선택합니다.  

    ![LDAP를 사용하여 로컬 NFS 사용자 허용 옵션을 보여 주는 스크린샷](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX 특성 관리

Active Directory 사용자 및 컴퓨터 MMC 스냅인을 사용하여 UID, 홈 디렉터리 및 기타 값과 같은 POSIX 특성을 관리할 수 있습니다.  다음 예에서는 Active Directory 특성 편집기를 보여 줍니다.  

![Active Directory 특성 편집기](../media/azure-netapp-files/active-directory-attribute-editor.png) 

LDAP 사용자 및 LDAP 그룹에 대해 다음 특성을 설정해야 합니다. 
* LDAP 사용자에 필요한 특성:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* LDAP 그룹에 대한 필수 특성:   
    `objectClass: posixGroup`, `gidNumber: 555`
* 모든 사용자와 그룹에 각각 고유한 `uidNumber` 및 `gidNumber`가 있어야 합니다. 

AADDS(Azure Active Directory Domain Services)를 사용하는 경우 조직 AADDC 사용자 OU에 생성된 사용자와 그룹의 POSIX 특성을 수정할 수 없습니다. 해결 방법으로, 사용자 지정 OU를 만들고 사용자 지정 OU에 사용자와 그룹을 만들 수 있습니다.

Azure AD 테넌트의 사용자와 그룹을 AADDC 사용자 OU의 사용자와 그룹에 동기화하는 경우 사용자와 그룹을 사용자 지정 OU로 이동할 수 없습니다. 사용자 지정 OU에 생성된 사용자와 그룹은 AD 테넌트에 동기화되지 않습니다. 자세한 내용은 [AADDS 사용자 지정 OU 고려 사항 및 제한 사항](../active-directory-domain-services/create-ou.md#custom-ou-considerations-and-limitations)을 참조하세요.

### <a name="access-active-directory-attribute-editor"></a>Active Directory 특성 편집기 액세스 

Windows 시스템에서 다음과 같이 Active Directory 특성 편집기에 액세스할 수 있습니다.  

1. **시작** 을 클릭하고 **Windows 관리 도구** 로 이동한 다음, **Active Directory 사용자 및 컴퓨터** 를 클릭하여 Active Directory 사용자 및 컴퓨터 창을 엽니다.  
2.  보려는 도메인 이름을 클릭하고 콘텐츠를 펼칩니다.  
3.  고급 특성 편집기를 표시하려면 Active Directory 사용자 컴퓨터 **보기** 메뉴에서 **고급 기능** 옵션을 사용하도록 설정합니다.   
    ![특성 편집기 고급 기능 메뉴에 액세스하는 방법을 보여 주는 스크린샷](../media/azure-netapp-files/attribute-editor-advanced-features.png) 
4. 왼쪽 창에서 **사용자** 를 두 번 클릭하여 사용자 목록을 표시합니다.
5. 특정 사용자를 두 번 클릭하여 해당 **특성 편집기** 탭을 표시합니다.
 
## <a name="configure-the-nfs-client"></a>NFS 클라이언트 구성 

NFS 클라이언트를 구성하려면 [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md)의 지침을 따르세요.  

## <a name="next-steps"></a>다음 단계  

* [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)
* [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md)
* [Unix 권한 구성 및 소유권 모드 변경](configure-unix-permissions-change-ownership-mode.md) 
* [Azure NetApp Files용 TLS를 통한 ADDS LDAP 구성](configure-ldap-over-tls.md)
* [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)
* [SMB 또는 이중 프로토콜 볼륨 문제 해결](troubleshoot-dual-protocol-volumes.md)
* [LDAP 볼륨 문제 해결](troubleshoot-ldap-volumes.md)
