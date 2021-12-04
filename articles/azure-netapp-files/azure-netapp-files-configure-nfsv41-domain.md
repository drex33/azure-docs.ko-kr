---
title: Azure NetApp Files | 대한 NFSv4.1 도메인 구성 Microsoft Docs
description: Azure NetApp Files NFSv4.1을 사용하기 위해 NFSv4.1 도메인을 구성하는 방법에 대해 설명합니다.
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/11/2021
ms.author: b-hchen
ms.openlocfilehash: 84bd599b61920161aacc2a411ddf197a958b51d8
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542557"
---
# <a name="configure-nfsv41-domain-for-azure-netapp-files"></a>Azure NetApp Files 대한 NFSv4.1 도메인 구성

NFSv4는 인증 도메인의 개념을 도입합니다. 현재 Azure NetApp Files는 서비스에서 NFS 클라이언트로 루트 전용 사용자 매핑을 지원합니다. Azure NetApp Files에서 NFSv 4.1 기능을 사용하려면 NFS 클라이언트를 업데이트해야 합니다.

## <a name="default-behavior-of-usergroup-mapping"></a>사용자/그룹 매핑의 기본 동작

NFSv4 도메인은 기본적으로 `localdomain`로 설정되어 있으므로 루트 매핑은 `nobody` 사용자를 기본값으로 사용됩니다. Azure NetApp Files NFSv4.1 볼륨을 루트로 탑재하면 다음과 같이 파일 권한이 표시됩니다.  

![NFSv4.1에 대한 사용자/그룹 매핑의 기본 동작](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

위의 예제에서 볼 수 있듯이, `file1`의 사용자는 `root`여야 하지만 기본적으로 `nobody`에 매핑됩니다.  이 문서에서는 `idmap Domain` 설정을 `defaultv4iddomain.com`로 변경하여 `file1` 사용자를 `root`로 설정하는 방법을 보여 줍니다.  

## <a name="configure-nfsv41-domain"></a>NFSv4.1 도메인 구성  

1. NFS 클라이언트에서 `/etc/idmapd.conf` 파일을 편집합니다.   
    줄의 압축을 `#Domain` `#` 풀고(즉, 줄에서 을 제거) 다음과 같이 값을 `localdomain` 변경합니다.

    * 볼륨이 LDAP에 대해 사용하도록 설정되지 않은 경우 를 `Domain = defaultv4iddomain.com` 설정합니다.
    * 볼륨이 [LDAP에 대해 사용하도록 설정된](configure-ldap-extended-groups.md)경우 `Domain` 을 NetApp 계정의 Active Directory 연결에 구성된 도메인으로 설정합니다.
        예를 들어 가 `contoso.com` NetApp 계정에서 구성된 도메인인 경우 를 `Domain = contoso.com` 설정합니다.

    다음 예제에서는 변경 전에 의 초기 구성을 보여 줍니다. `/etc/idmapd.conf`

    ```
    [General]
    Verbosity = O 
    Pipefs—Directory = /run/rpc_pipefs 
    # set your own domain here, if it differs from FQDN minus hostname 
    # Domain = localdomain 
     
    [Mapping] 
    Nobody-User = nobody 
    Nobody-Group = nogroup 
    ```

    다음 예제에서는 *LDAP가 아닌* NFSv4.1 볼륨의 업데이트된 구성을 보여 줍니다.

    ```
    [General]
    Verbosity = O 
    Pipefs—Directory = /run/rpc_pipefs 
    # set your own domain here, if it differs from FQDN minus hostname 
    Domain = defaultv4iddomain.com 
 
    [Mapping] 
    Nobody-User = nobody 
    Nobody-Group = nogroup 
    ```

    다음 예제에서는 *LDAP 사용* NFSv4.1 볼륨의 업데이트된 구성을 보여 줍니다. 이 예제에서 `contoso.com` 는 NetApp 계정에서 구성된 도메인입니다.

    ```
    [General]
    Verbosity = O 
    Pipefs—Directory = /run/rpc_pipefs 
    # set your own domain here, if it differs from FQDN minus hostname 
    Domain = contoso.com
    
    [Mapping] 
    Nobody-User = nobody 
    Nobody-Group = nogroup 
    ```

2. 현재 탑재된 NFS 볼륨을 모두 분리합니다.
3. `/etc/idmapd.conf` 파일을 업데이트합니다.
4. 호스트(`service rpcbind restart`)에서 `rpcbind` 서비스를 다시 시작하거나 단순히 호스트를 다시 부팅합니다.
5. 필요에 따라 NFS 볼륨을 탑재합니다.   

    [Windows 또는 Linux VM용 볼륨 탑재를 참조하세요.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 

다음 예제에서는 결과로 생성된 사용자/그룹 변경 내용을 보여 줍니다. 

![결과로 생성된 사용자/그룹 변경 내용의 예제 스크린샷](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

예제에서 볼 수 있듯이, 사용자/그룹이 이제 `nobody`에서 `root`로 변경되었습니다.

## <a name="behavior-of-other-non-root-users-and-groups"></a>루트가 아닌 다른 사용자 및 그룹의 동작

Azure NetApp Files는 NFSv4.1 볼륨의 파일 또는 폴더와 연결된 사용 권한이 있는 로컬 사용자(호스트에서 로컬로 생성된 사용자)를 지원합니다. 그러나 이 서비스는 현재 여러 노드 간 사용자/그룹 매핑을 지원하지 않습니다. 따라서 한 호스트에서 만든 사용자가 다른 호스트에서 만든 사용자에게 기본적으로 매핑되지 않습니다. 

다음 예제에서 `Host1`에는 기존 테스트 사용자 계정 3개(`testuser01`, `testuser02`, `testuser03`)가 있습니다. 

![Host1에 기존 테스트 사용자 계정 3개가 있음을 보여 주는 스크린샷](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`에서는 테스트 사용자 계정이 생성되지 않았지만 두 호스트에서 모두 동일한 볼륨이 탑재된 것을 확인합니다.

![NFSv4.1에 대한 결과 구성](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>다음 단계 

* [Windows 또는 Linux VM용 볼륨 탑재](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)

