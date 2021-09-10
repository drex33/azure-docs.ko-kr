---
title: Azure NetApp Files NFS 볼륨 액세스를 위한 확장 그룹을 사용하는 ADDS LDAP 구성 | Microsoft Docs
description: Azure NetApp Files를 사용하여 NFS 볼륨을 만들 때 확장 그룹이 포함된 LDAP를 사용하도록 설정하는 고려 사항 및 단계에 대해 설명합니다.
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
ms.date: 07/19/2021
ms.author: b-juche
ms.openlocfilehash: 01d8f23331525443a9f83245b8eec2b402e92d6e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443025"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성

[NFS 볼륨을 생성](azure-netapp-files-create-volumes.md)할 때 볼륨에 대해 확장 그룹 기능이 포함된 LDAP를 사용하는 옵션(**LDAP** 옵션)이 있습니다. 이 기능을 사용하면 Active Directory LDAP 사용자 및 확장 그룹(최대 1024개 그룹)을 통해 볼륨에 액세스할 수 있습니다. NFSv4.1 및 NFSv3 볼륨 모두에서 확장 그룹이 포함된 LDAP를 사용할 수 있습니다. 

이 문서에서는 NFS 볼륨을 만들 때 확장 그룹이 포함된 LDAP를 사용하도록 설정하는 고려 사항 및 단계에 대해 설명합니다.  

## <a name="considerations"></a>고려 사항

* 볼륨을 만드는 동안에만 확장 그룹 기능으로 LDAP를 사용하도록 설정할 수 있습니다. 기존 볼륨에서는 이 기능을 소급해서 사용할 수 없습니다.  

* 확장 그룹을 사용하는 LDAP는 ADDS(Active Directory Domain services) 또는 AADDS(Azure Active Directory Domain services)에서만 지원됩니다. OpenLDAP 또는 기타 타사 LDAP 디렉터리 서비스는 지원되지 않습니다. 

* AADDS(Azure Active Directory Domain Services)를 사용하는 경우 LDAP over TLS를 사용하지 *않아야* 합니다.  

* 볼륨을 만든 후에는 LDAP 옵션 설정(사용 또는 사용 안 함)을 수정할 수 없습니다.  

* 다음 표에서는 LDAP 캐시의 TTL(Time to Live) 설정에 대해 설명합니다. 클라이언트를 통해 파일이나 디렉터리에 액세스하기 전에 캐시가 새로 고쳐질 때까지 기다려야 합니다. 그러지 않으면 액세스 거부 메시지가 클라이언트에 표시됩니다. 

    |     오류 조건    |     해결 방법    |
    |-|-|
    | 캐시 |  기본 시간 제한 |
    | 그룹 구성원 목록  | 24시간 TTL  |
    | Unix 그룹  | 24시간 TTL, 1분 부정 TTL  |
    | Unix 사용자  | 24시간 TTL, 1분 부정 TTL  |

    캐시에 *TTL(Time to Live)* 이라는 특정 제한 시간이 있습니다. 시간 초과 기간이 지나면 항목이 만료되므로 오래된 항목이 유지되지 않습니다. *부정 TTL* 값에는 존재하지 않을 수 있는 개체에 대한 LDAP 쿼리로 인한 성능 문제를 방지할 수 있도록 실패한 조회가 상주합니다.”        

## <a name="steps"></a>단계

1. 확장 그룹을 사용하는 LDAP 기능은 현재 미리 보기로 제공됩니다. 이 기능을 처음 사용하기 전에 기능을 등록해야 합니다.  

    1. 기능을 등록합니다.   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. 기능 등록 상태를 확인합니다. 

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경되기 전 최대 60분 동안 `Registering` 상태에 있을 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    [Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

2. LDAP 볼륨에는 LDAP 서버 설정에 대한 Active Directory 구성이 필요합니다. [Active Directory 연결 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections) 및 [Active Directory 연결 만들기](create-active-directory-connections.md#create-an-active-directory-connection)의 지침에 따라 Azure Portal에서 Active Directory 연결을 구성합니다.  

    > [!NOTE]
    > Active Directory 연결 설정을 구성했는지 확인합니다. 컴퓨터 계정은 Active Directory 연결 설정에 지정된 OU(조직 구성 단위)에 만들어집니다. 이 설정은 LDAP 클라이언트에서 Active Directory로 인증하는 데 사용됩니다.

3. Active Directory LDAP 서버가 Active Directory 실행 중인지 확인합니다. 

4. LDAP NFS 사용자에게는 LDAP 서버에 특정 POSIX 특성이 있어야 합니다. 다음과 같이 LDAP 사용자 및 LDAP 그룹에 대한 속성을 설정합니다. 

    * LDAP 사용자에 대한 필수 특성:   
        `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: user`
    * LDAP 그룹에 대한 필수 특성:   
        `objectClass: group`, `gidNumber: 555`

    Active Directory 사용자 및 컴퓨터 MMC 스냅인을 사용하여 POSIX 특성을 관리할 수 ​​있습니다. 다음 예에서는 Active Directory 특성 편집기를 보여 줍니다.  

    ![Active Directory 특성 편집기](../media/azure-netapp-files/active-directory-attribute-editor.png) 

    자세한 내용은 [Access Active Directory 특성 편집기 액세스](create-volumes-dual-protocol.md#access-active-directory-attribute-editor)를 참조하세요.  

5. LDAP 통합 NFSv4.1 Linux 클라이언트를 구성하려면 [Azure NetApp Files용 NFS 클라이언트 구성](configure-nfs-clients.md)을 참조하세요.

6.  [Azure NetApp Files용 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)의 단계에 따라 NFS 볼륨을 만듭니다. 볼륨 생성 프로세스 중에 **프로토콜** 탭에서 **LDAP** 옵션을 사용하도록 설정합니다.   

    ![LDAP 옵션을 사용하여 볼륨 페이지 만들기를 보여 주는 스크린샷](../media/azure-netapp-files/create-nfs-ldap.png)  

7. 선택 사항 - Windows LDAP 서버에 없는 로컬 NFS 클라이언트 사용자가 확장 그룹을 사용하는 LDAP가 있는 NFS 볼륨에 액세스할 수 있도록 설정할 수 있습니다. 이렇게 하려면 다음과 같이 **LDAP를 사용하여 로컬 NFS 사용자 허용** 옵션을 사용하도록 설정합니다.
    1. **Active Directory 연결** 을 클릭합니다.  기존 Active Directory 연결에서 컨텍스트 메뉴(점 3개 `…`)를 클릭하고 **편집** 을 선택합니다.  
    2. 표시되는 **Active Directory 설정 편집** 창에서 **LDAP를 사용하여 로컬 NFS 사용자 허용** 옵션을 선택합니다.  

    ![LDAP를 사용하여 로컬 NFS 사용자 허용 옵션을 보여 주는 스크린샷](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Active Directory 연결 만들기 및 관리](create-active-directory-connections.md)
* [LDAP 볼륨 문제 해결](troubleshoot-ldap-volumes.md)
