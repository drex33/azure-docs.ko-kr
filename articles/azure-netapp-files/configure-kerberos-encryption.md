---
title: Azure NetApp Files에 NFSv4.1 Kerberos 암호화 구성 | Microsoft Docs
description: Azure NetApp Files에 NFSv4.1 Kerberos 암호화를 구성하는 방법과 그 성능 영향을 설명합니다.
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
ms.date: 07/15/2021
ms.author: b-juche
ms.openlocfilehash: a12df50ac6fe2e1dc2bf793316538a90ba0f901d
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361338"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFSv4.1 Kerberos 암호화 구성

Azure NetApp Files는 AES-256 암호화를 사용하는 Kerberos 모드(krb5, krb5i 및 krb5p)에서 NFS 클라이언트 암호화를 지원합니다. 이 문서에서는 Kerberos 암호화와 함께 NFSv4.1 볼륨을 사용하는 데 필요한 구성을 설명합니다.

## <a name="requirements"></a>요구 사항

NFSv4.1 클라이언트 암호화에는 다음 요구 사항이 적용됩니다. 

* Kerberos 티켓을 지원하도록 AD DS(Active Directory Domain Services) 또는 AADDS(Azure Active Directory Domain Services) 연결 
* 클라이언트 및 Azure NetApp Files NFS 서버 IP 주소 모두에 대한 DNS A/PTR 레코드 만들기
* Linux 클라이언트: 이 문서에서는 RHEL 및 Ubuntu 클라이언트에 대한 지침을 제공합니다.  다른 클라이언트는 유사한 구성 단계로 작동합니다. 
* NTP 서버 액세스: 일반적으로 사용되는 AD DC(Active Directory 도메인 컨트롤러) 도메인 컨트롤러 중 하나를 사용할 수 있습니다.
* 사용자 계정의 사용자 계정 이름이 `$` 기호(예: user$@REALM.COM)로 끝나지 *않도* 록 합니다. <!-- Not using 'contoso.com' in this example; per Mark, A customers REALM namespace may be different from their AD domain name space. -->   
    [gMSA(그룹 관리 서비스 계정)](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)의 경우 계정을 Azure NetApp Files Kerberos 기능과 함께 사용하려면 먼저 사용자 계정 이름에서 후행 `$`를 제거해야 합니다.


## <a name="create-an-nfs-kerberos-volume"></a>NFS Kerberos 볼륨 만들기

1.  [Azure NetApp Files용 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)의 단계를 수행하여 NFSv4.1 볼륨을 만듭니다.   

    볼륨 만들기 페이지에서 NFS 버전을 **NFSv4.1** 로 설정하고 Kerberos를 **사용** 으로 설정합니다.

    > [!IMPORTANT] 
    > 볼륨을 만든 후에는 Kerberos 사용 선택 항목을 수정할 수 없습니다.

    ![NFSv4.1 Kerberos 볼륨 만들기](../media/azure-netapp-files/create-kerberos-volume.png)  

2. **정책 내보내기** 를 선택하여 볼륨에 대해 원하는 액세스 권한 수준과 보안 옵션(kerberos 5, kerberos 5i 또는 kerberos 5p)을 일치시킵니다.   

    Kerberos가 성능에 미치는 영향은 [NFSv4.1에서 Kerberos가 성능에 미치는 영향](#kerberos_performance)을 참조하세요.  

    Azure NetApp Files 탐색 창에서 정책 내보내기를 클릭하여 볼륨의 Kerberos 보안 방법을 수정할 수도 있습니다.

3.  **검토 + 만들기** 를 클릭하여 NFSv4.1 볼륨을 만듭니다.

## <a name="configure-the-azure-portal"></a>Azure Portal 구성 

1.  [Active Directory 연결 만들기](create-active-directory-connections.md)의 지침을 따릅니다.  

    Kerberos를 사용하려면 Active Directory에서 컴퓨터 계정을 최소 하나 이상 만들어야 합니다. 제공하는 계정 정보는 SMB *및* NFSv4.1 Kerberos 볼륨 모두에 대한 계정을 만드는 데 사용됩니다. 이 컴퓨터는 볼륨을 만드는 동안 자동으로 생성됩니다.

2.  **Kerberos 영역** 아래에 **AD 서버 이름** 과 **KDC IP** 주소를 입력합니다.

    AD 서버와 KDC IP는 동일한 서버일 수 있습니다. 이 정보는 Azure NetApp Files에서 사용하는 SPN 컴퓨터 계정을 만드는 데 사용됩니다. 컴퓨터 계정을 만든 후 Azure NetApp Files는 DNS 서버 레코드를 사용하여 필요에 따라 추가 KDC 서버를 찾습니다. 

    ![Kerberos 영역](../media/azure-netapp-files/kerberos-realm.png)
 
3.  **조인** 을 클릭하여 구성을 저장합니다.

## <a name="configure-active-directory-connection"></a>Active Directory 연결 구성 

NFSv4.1 Kerberos 구성에서 Active Directory에 컴퓨터 계정 2개를 만듭니다.
* SMB 공유의 컴퓨터 계정
* NFSv4.1의 컴퓨터 계정--접두사 `NFS-`를 통해 이 계정을 식별할 수 있습니다. 

첫 번째 NFSv4.1 Kerberos 볼륨을 만든 후 다음 PowerShell 명령을 사용하여 컴퓨터 계정의 암호화 형식을 설정합니다.

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>NFS 클라이언트 구성 

NFS 클라이언트를 구성하려면 [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md)의 지침을 따르세요.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>NFS Kerberos 볼륨 탑재

1. **볼륨** 페이지에서 탑재할 NFS 볼륨을 선택합니다.

2. 볼륨에서 **탑재 지침** 을 선택하여 지침을 표시합니다.

    예를 들면 다음과 같습니다. 

    ![Kerberos 볼륨 탑재 지침](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 새 볼륨에 대한 디렉터리(탑재 지점)를 만듭니다.  

4. 컴퓨터 계정의 기본 암호화 형식을 AES 256으로 설정합니다.  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 컴퓨터 계정마다 이 명령을 한 번만 실행해야 합니다.
    * 도메인 컨트롤러 또는 [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems)가 설치된 PC에서 이 명령을 실행할 수 있습니다. 
    * `$NFSCOMPUTERACCOUNT` 변수는 Kerberos 볼륨을 배포할 때 Active Directory에서 생성된 컴퓨터 계정입니다. 이 계정에는 접두사 `NFS-`가 있습니다. 
    * `$ANFSERVICEACCOUNT` 변수는 컴퓨터 계정이 생성된 조직 구성 단위에 대한 위임된 컨트롤이 있는 권한 없는 Active Directory 사용자 계정입니다. 

5. 호스트에 볼륨을 탑재합니다. 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT` 변수는 탑재 명령에서 찾은 `host:/export` 경로입니다.
    * `$ANFMOUNTPOINT` 변수는 Linux 호스트에서 사용자가 만든 폴더입니다.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>NFSv4.1에서 Kerberos의 성능 영향 

NFSv4.1 볼륨, 테스트된 성능 벡터 및 kerberos의 예상 성능 영향에 사용할 수 있는 보안 옵션을 이해해야 합니다. 자세한 내용은 [NFSv4.1 볼륨에서 Kerberos 성능 영향](performance-impact-kerberos.md)을 참조하세요.  

## <a name="next-steps"></a>다음 단계  

* [NFSv4.1 볼륨에서 Kerberos의 성능 영향](performance-impact-kerberos.md)
* [NFSv4.1 Kerberos 볼륨 문제 해결](troubleshoot-nfsv41-kerberos-volumes.md)
* [Azure NetApp Files에 대한 FAQ](azure-netapp-files-faqs.md)
* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Active Directory 연결 만들기](create-active-directory-connections.md)
* [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md) 
