---
title: Azure NetApp Files에 대한 Active Directory 연결 만들기 및 관리 | Microsoft Docs
description: 이 문서에서는 Azure NetApp Files에 대한 Active Directory 연결을 만들고 관리하는 방법을 보여 줍니다.
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
ms.date: 08/17/2021
ms.author: b-juche
ms.openlocfilehash: 30b00320e9273ecb010239d66a3c056d3f95f332
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568057"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Azure NetApp Files에 대한 Active Directory 연결 만들기 및 관리

Azure NetApp Files의 여러 기능을 수행하려면 Active Directory에 연결해야 합니다.  예를 들어 [SMB 볼륨](azure-netapp-files-create-volumes-smb.md), [NFSv4.1 Kerberos 볼륨](configure-kerberos-encryption.md) 또는 [이중 프로토콜 볼륨](create-volumes-dual-protocol.md)을 만들려면 먼저 Active Directory에 연결해야 합니다.  이 문서에서는 Azure NetApp Files에 대한 Active Directory 연결을 만들고 관리하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에  

* 용량 풀을 설정해야 합니다. [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)을 참조하세요.   
* Azure NetApp Files에 서브넷을 위임해야 합니다. [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)을 참조하세요.

## <a name="requirements-and-considerations-for-active-directory-connections"></a><a name="requirements-for-active-directory-connections"></a>Active Directory 연결 요구 사항 및 고려 사항

* 구독 및 지역별로 하나의 AD(Active Directory) 연결만 구성할 수 있습니다.   

    AD 연결이 다른 NetApp 계정에 있는 경우에도 Azure NetApp Files는 단일 *지역* 에서 여러 AD 연결을 지원하지 않습니다. 그러나 AD 연결이 서로 다른 지역에 있는 경우에는 여러 AD 연결을 단일 구독에 사용할 수 있습니다. 여러 AD 연결이 단일 지역에 필요한 경우 별도의 구독을 사용하여 이 작업을 수행할 수 있습니다.  

    AD 연결은 생성된 NetApp 계정을 통해서만 볼 수 있습니다. 그러나 공유 AD 기능을 사용하도록 설정하여 동일한 구독 및 동일한 지역에 있는 NetApp 계정이 NetApp 계정 중 하나에서 만든 AD 서버를 사용하도록 허용할 수 있습니다. [동일한 구독 및 지역의 여러 NetApp 계정을 하나의 AD 연결에 매핑](#shared_ad)을 참조하세요. 이 기능을 활성화할 경우 동일한 구독 및 동일한 지역에 있는 모든 NetApp 계정에서 AD 연결이 표시됩니다. 

* 사용하는 관리자 계정은 사용자가 지정할 OU(조직 구성 단위) 경로에 머신 계정을 만들 수 있어야 합니다.  

* Azure NetApp Files에서 사용되는 Active Directory 사용자 계정의 암호를 변경하는 경우 [Active Directory 연결](#create-an-active-directory-connection)에 구성된 암호를 업데이트해야 합니다. 그러지 않으면 새 볼륨을 생성할 수 없으며 설정에 따라 기존 볼륨에 대한 액세스도 영향을 받을 수 있습니다.  

* 해당하는 Windows Active Directory(AD) 서버에 적절한 포트가 열려 있어야 합니다.  
    필요한 포트는 다음과 같습니다. 

    |     서비스           |     포트     |     프로토콜     |
    |-----------------------|--------------|------------------|
    |    AD 웹 서비스    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    해당 없음       |    Echo Reply    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 이름       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* 대상 Active Directory Domain Services에 대한 사이트 토폴로지는 지침, 특히 Azure NetApp Files가 배포되는 Azure VNet 관련 지침을 준수해야 합니다.  

    Azure NetApp Files에서 연결할 수 있는 도메인 컨트롤러가 있는 신규 또는 기존 Active Directory 사이트에 Azure NetApp Files가 배포되는 가상 네트워크의 주소 공간을 추가해야 합니다. 

* 지정된 DNS 서버는 Azure NetApp Files의 [위임된 서브넷](./azure-netapp-files-delegate-subnet.md)에서 연결할 수 있어야 합니다.  

    지원되는 네트워크 토폴로지는 [Azure NetApp Files 네트워크 계획 지침](./azure-netapp-files-network-topologies.md)을 참조하세요.

    NSG(네트워크 보안 그룹)와 방화벽에 Active Directory 및 DNS 트래픽 요청을 허용하는 규칙이 적절히 구성되어 있어야 합니다. 

* Azure NetApp Files에 위임된 서브넷은 모든 로컬 및 원격 도메인 컨트롤러를 포함하여 도메인의 모든 Active Directory Domain Services(ADDS) 도메인 컨트롤러에 연결할 수 있어야 합니다. 그렇지 않으면 서비스 중단이 발생할 수 있습니다.  

    Azure NetApp Files에 위임된 서브넷에서 연결할 수 없는 도메인 컨트롤러가 있는 경우 Active Directory 연결을 만드는 동안 Active Directory 사이트를 지정할 수 있습니다.  Azure NetApp Files가 Azure NetApp Files에 위임된 서브넷 주소 공간이 있는 사이트의 도메인 컨트롤러와만 통신해야 합니다.

    AD 사이트 및 서비스에 대한 [사이트 토폴로지 디자인](/windows-server/identity/ad-ds/plan/designing-the-site-topology)을 참조하세요. 
    
* [Active Directory 조인](#create-an-active-directory-connection) 창에서 **AES 암호화** 확인란을 선택하여 AD 인증에 대한 AES 암호화를 사용하도록 설정할 수 있습니다. Azure NetApp Files는 DES, Kerberos AES 128 및 Kerberos AES 256 암호화 유형(가장 덜 안전한 방식부터)을 지원합니다. AES 암호화를 사용하도록 설정하는 경우 Active Directory를 조인하는 데 사용되는 사용자 자격 증명에 Active Directory에 대해 사용하도록 설정된 기능과 일치하는 가장 높은 계정 옵션을 사용해야 합니다.    

    예를 들어, Active Directory에 AES-128 기능만 있는 경우 사용자 자격 증명에 대해 AES-128 계정 옵션을 사용하도록 설정해야 합니다. Active Directory에 AES-256 기능이 있는 경우 AES-256 계정 옵션을 사용하도록 설정해야 합니다(AES-128도 지원). Active Directory에 Kerberos 암호화 기능이 없을 경우 Azure NetApp Files는 기본적으로 DES를 사용합니다.  

    Active Directory 사용자 및 컴퓨터 MMC(Microsoft Management Console)의 속성에서 계정 옵션을 사용하도록 설정할 수 있습니다.   

    ![Active Directory 사용자 및 컴퓨터 MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files는 이제 Azure NetApp Files 서비스와 타기팅된 [Active Directory Domain Services 도메인 컨트롤러](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 간의 보안 LDAP 트래픽 전송을 허용하는 [LDAP 서명](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server)을 지원합니다. LDAP 서명에 대한 Microsoft 권고 [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023)의 지침을 따르는 경우 [Active Directory 조인](#create-an-active-directory-connection) 창에서 **LDAP 서명** 확인란을 선택하여 Azure NetApp Files에서 LDAP 서명 기능을 사용하도록 설정해야 합니다. 

    [LDAP 채널 바인딩](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) 구성만으로는 Azure NetApp Files 서비스에 영향을 주지 않습니다. 그러나 LDAP 채널 바인딩과 보안 LDAP를 모두 사용하는 경우(예: LDAPS 또는 `start_tls`) SMB 볼륨 만들기가 실패합니다.

* 비 AD 통합 DNS의 경우 "친숙한 이름"을 사용하여 Azure NetApp Files가 작동할 수 있도록 DNS A/PTR 레코드를 추가해야 합니다. 

## <a name="decide-which-domain-services-to-use"></a>사용할 도메인 서비스 결정 

Azure NetApp Files는 AD 연결을 위해 [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)(ADDS)와 Azure Active Directory Domain Services(AADDS)를 모두 지원합니다.  AD 연결을 만들기 전에 ADDS를 사용할지 아니면 AADDS를 사용할지 결정해야 합니다.  

자세한 내용은 [자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교](../active-directory-domain-services/compare-identity-solutions.md)를 참조하세요. 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Azure NetApp Files에 기본 [Active Directory 사이트 및 서비스](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) 범위를 사용할 수 있습니다. 이 옵션을 사용하면 [Azure NetApp Files에서 액세스할 수 있는](azure-netapp-files-network-topologies.md) Active Directory Domain Services(ADDS) 도메인 컨트롤러에 대한 읽기 및 쓰기가 가능합니다. 또한 이 옵션은 지정된 Active Directory 사이트 및 서비스 사이트에 없는 도메인 컨트롤러와 서비스가 통신하는 것을 방지합니다. 

ADDS를 사용할 때 사이트 이름을 찾기 위해 Active Directory Domain Services를 담당하는 조직의 관리 그룹에 문의할 수 있습니다. 아래 예에서는 사이트 이름이 표시되는 Active Directory 사이트 및 서비스 플러그 인을 보여줍니다. 

![Active Directory 사이트 및 서비스](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Azure NetApp Files에 대한 AD 연결을 구성하는 경우 **AD 사이트 이름** 필드의 범위에 사이트 이름을 지정합니다.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services(AADDS) 구성 및 지침은 [Microsoft Azure Active Directory Domain Services 설명서](../active-directory-domain-services/index.yml)를 참조하세요.

Azure NetApp Files에는 다음과 같은 추가 AADDS 고려 사항이 적용됩니다. 

* AADDS가 배포되는 VNet 또는 서브넷이 Azure NetApp Files 배포와 동일한 Azure 지역에 있는지 확인합니다.
* Azure NetApp Files가 배포되는 지역에서 다른 VNet을 사용하는 경우 두 VNet 간에 피어링을 만들어야 합니다.
* Azure NetApp Files는 `user` 및 `resource forest` 형식을 지원합니다.
* 동기화 유형에 대해 `All` 또는 `Scoped`를 선택할 수 있습니다.   
    `Scoped`를 선택하는 경우 SMB 공유에 액세스하기 위해 올바른 Microsoft Azure Active Directory 그룹을 선택했는지 확인합니다.  확실하지 않은 경우 `All` 동기화 유형을 사용할 수 있습니다.

Active Directory 연결을 만들 때 AADDS에 대한 다음 사항에 유의해야 합니다.

* AADDS 메뉴에서 **기본 DNS**, **보조 DNS** 및 **AD DNS 도메인 이름** 에 대한 정보를 찾을 수 있습니다.  
DNS 서버의 경우 Active Directory 연결 구성에 2개의 IP 주소가 사용됩니다. 
* **조직 구성 단위 경로** 는 `OU=AADDC Computers`입니다.  
이 설정은 **Active Directory 연결** 의 **NetApp 계정** 에서 구성합니다.

  ![조직 구성 단위 경로](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **사용자 이름** 자격 증명은 Microsoft Azure Active Directory 그룹 **Microsoft Azure Active Directory DC 관리자** 의 구성원인 사용자일 수 있습니다.


## <a name="create-an-active-directory-connection"></a>Active Directory 연결 만들기

1. NetApp 계정에서 **Active Directory 연결** 을 클릭하고 **조인** 을 클릭합니다.  

    Azure NetApp Files는 동일한 지역 및 동일한 구독 내에서 하나의 Active Directory 연결만 지원합니다. 동일한 구독 및 지역의 다른 NetApp 계정에서 Active Directory를 이미 구성한 경우 NetApp 계정에서 다른 Active Directory를 구성하고 조인할 수 없습니다. 그러나 동일한 구독 및 동일한 지역 내의 여러 NetApp 계정에서 Active Directory 구성을 공유할 수 있도록 공유 AD 기능을 사용하도록 설정할 수 있습니다. [동일한 구독 및 지역의 여러 NetApp 계정을 하나의 AD 연결에 매핑](#shared_ad)을 참조하세요.

    ![Active Directory 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Active Directory 조인 창에서 사용하려는 도메인 서비스에 따라 다음 정보를 제공합니다.  

    사용하는 도메인 서비스에 대한 자세한 내용은 [사용할 도메인 서비스 결정](#decide-which-domain-services-to-use)을 참조하세요. 

    * **기본 DNS**  
        Active Directory 도메인 가입 및 SMB 인증 작업에 필요한 DNS입니다. 
    * **보조 DNS**   
        중복 이름 서비스 확인을 위한 보조 DNS 서버입니다. 
    * **AD DNS 도메인 이름**  
        가입하려는 Active Directory Domain Services의 도메인 이름입니다.
    * **AD 사이트 이름**  
        도메인 컨트롤러 검색이 제한될 사이트 이름입니다. Active Directory 사이트 및 서비스의 사이트 이름과 일치해야 합니다.
    * **SMB 서버(컴퓨터 계정) 접두사**  
        Azure NetApp Files에서 새 계정을 만드는 데 사용할 Active Directory의 머신 계정에 대한 명명 접두사입니다.

        예를 들어 조직에서 파일 서버에 사용하는 명명 표준이 NAS-01, NAS-02..., NAS-045인 경우 접두사로 "NAS"를 입력합니다. 

        필요에 따라 Active Directory에 추가 머신 계정이 만들어집니다.

        > [!IMPORTANT] 
        > Active Directory 연결을 만든 후 SMB 서버 접두사의 이름을 바꾸면 작업이 중단됩니다. SMB 서버 접두사의 이름을 바꾼 후에는 기존 SMB 공유를 다시 탑재해야 합니다.

    * **조직 구성 단위 경로**  
        SMB 서버 머신 계정이 만들어질 OU(조직 구성 단위)의 LDAP 경로(예: OU=두 번째 수준,OU=첫 번째 수준)입니다. 

        Azure Active Directory Domain Services와 함께 Azure NetApp Files를 사용하는 경우 NetApp 계정에 대해 Active Directory를 구성할 때 조직 구성 단위 경로는 `OU=AADDC Computers`입니다.

        ![Active Directory 조인](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES 암호화**   
        AD 인증에 AES 암호화를 활성화하거나 [SMB 볼륨에 대한 암호화](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)가 필요한 경우 이 확인란을 선택합니다.   
        
        요구 사항에 대해서는 [Active Directory 연결에 대한 요구 사항](#requirements-for-active-directory-connections)을 참조하세요.  
  
        ![Active Directory AES 암호화](../media/azure-netapp-files/active-directory-aes-encryption.png)

        **AES 암호화** 기능은 현재 미리 보기로 제공됩니다. 이 기능을 처음 사용하는 경우 사용하기 전에 등록합니다. 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        기능 등록 상태를 확인합니다. 

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경되기 전 최대 60분 동안 `Registering` 상태에 있을 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        [Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

    * **LDAP 서명**   
        LDAP 서명을 사용하도록 설정하려면 이 확인란을 선택합니다. 이 기능은 Azure NetApp Files 서비스와 사용자 지정 [Active Directory Domain Services 도메인 컨트롤러](/windows/win32/ad/active-directory-domain-services) 간의 보안 LDAP 조회를 지원합니다. 자세한 내용은 [ADV190023 | LDAP 채널 바인딩 및 LDAP 서명을 사용하도록 설정하기 위한 Microsoft 지침](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023)을 참조하세요.  

        ![Active Directory LDAP 서명](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        **LDAP 서명** 기능은 현재 미리 보기 상태입니다. 이 기능을 처음 사용하는 경우 사용하기 전에 등록합니다. 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        기능 등록 상태를 확인합니다. 

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경되기 전 최대 60분 동안 `Registering` 상태에 있을 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        [Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

     * **보안 권한 사용자**   <!-- SMB CA share feature -->   
        Azure NetApp Files 볼륨에 액세스하기 위해 상승된 권한이 필요한 사용자에게 보안 권한(`SeSecurityPrivilege`)을 부여할 수 있습니다. 지정된 사용자 계정은 기본적으로 도메인 사용자에게 할당되지 않은 보안 권한이 필요한 Azure NetApp Files SMB 공유에서 특정 작업을 수행할 수 있습니다.   

        예를 들어 특정 시나리오에서 SQL Server를 설치하는 데 사용되는 사용자 계정에는 상승된 보안 권한을 부여해야 합니다. 비관리자(도메인) 계정을 사용하여 SQL Server를 설치하고 계정에 할당된 보안 권한이 없는 경우 계정에 보안 권한을 추가해야 합니다.  

        > [!IMPORTANT]
        > **보안 권한 사용자** 기능을 사용하려면 **[Azure NetApp Files SMB 지속적인 가용성 공유 공개 미리 보기 대기 목록 제출 페이지](https://aka.ms/anfsmbcasharespreviewsignup)** 를 통해 대기 목록 요청을 제출해야 합니다. 이 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 이메일을 기다리세요.        
        > 
        > 이 기능을 사용하는 것은 선택 사항이며 SQL Server에 대해서만 지원됩니다. SQL Server를 설치하는 데 사용되는 도메인 계정을 이미 존재해야만 **보안 권한 사용자** 필드에 추가할 수 있습니다. SQL Server 설치 관리자의 계정을 **보안 권한 사용자** 에게 추가하면 Azure NetApp Files 서비스에서 도메인 컨트롤러에 연결하여 계정의 유효성을 검사할 수 있습니다. 도메인 컨트롤러에 연결할 수 없는 경우 명령이 실패할 수 있습니다.  

        `SeSecurityPrivilege` 및 SQL Server에 대한 자세한 내용은 [설치 계정에 특정 사용자 권한이 없는 경우 SQL Server 설치 실패](/troubleshoot/sql/install/installation-fails-if-remove-user-right)를 참조하세요.

        ![Active Directory 연결 창의 보안 권한 사용자 상자를 보여 주는 스크린샷](../media/azure-netapp-files/security-privilege-users.png) 

     * **백업 정책 사용자**  
        Azure NetApp Files에 사용하기 위해 만든 컴퓨터 계정에 대한 높은 권한이 필요한 추가 계정을 포함할 수 있습니다. 지정된 계정은 파일 또는 폴더 수준에서 NTFS 권한을 변경할 수 있습니다. 예를 들어 Azure NetApp Files에서 SMB 파일 공유로 데이터를 마이그레이션하는 데 사용되는 권한 없는 서비스 계정을 지정할 수 있습니다.  

        ![Active Directory 백업 정책 사용자](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        **백업 정책 사용자** 기능은 현재 미리 보기 상태입니다. 이 기능을 처음 사용하는 경우 사용하기 전에 등록합니다. 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        기능 등록 상태를 확인합니다. 

        > [!NOTE]
        > **RegistrationState** 는 `Registered`로 변경되기 전 최대 60분 동안 `Registering` 상태에 있을 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        [Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다.  

    * **사용자 이름** 과 **암호** 를 포함한 자격 증명

        ![Active Directory 자격 증명](../media/azure-netapp-files/active-directory-credentials.png)

3. **조인** 을 클릭합니다.  

    만든 Active Directory 연결이 나타납니다.

    ![Active Directory 연결이 만들어짐](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="map-multiple-netapp-accounts-in-the-same-subscription-and-region-to-an-ad-connection"></a><a name="shared_ad"></a>동일한 구독 및 지역의 여러 NetApp 계정을 하나의 AD 연결에 매핑  

공유 AD 기능을 사용하면 모든 NetApp 계정이 동일한 구독 및 동일한 지역에 속하는 NetApp 계정 중 하나로 만든 AD(Active Directory) 연결을 공유할 수 있습니다. 예를 들어, 이 기능을 사용하면 동일한 구독 및 지역의 모든 NetApp 계정이 공통 AD 구성을 사용하여 [SMB 볼륨](azure-netapp-files-create-volumes-smb.md), [NFSv4.1 Kerberos 볼륨](configure-kerberos-encryption.md) 또는 [이중 프로토콜 볼륨](create-volumes-dual-protocol.md)을 만들 수 있습니다. 이 기능을 사용할 경우 동일한 구독 및 동일한 지역에 있는 모든 NetApp 계정에 AD 연결이 표시됩니다.   

이 기능은 현재 미리 보기로 제공됩니다. 이 기능을 처음 사용하기 전에 등록해야 합니다. 등록 후 기능이 활성화되고 백그라운드에서 작동합니다. UI 컨트롤이 필요하지 않습니다. 

1. 다음 기능을 등록합니다. 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```

2. 기능 등록 상태를 확인합니다. 

    > [!NOTE]
    > **RegistrationState** 는 `Registered`로 변경되기 전 최대 60분 동안 `Registering` 상태에 있을 수 있습니다. 상태가 **Registered** 가 될 때까지 기다린 후에 계속하세요.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```
[Azure CLI 명령](/cli/azure/feature) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 
 
## <a name="next-steps"></a>다음 단계  

* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
* [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)
* [Azure 명령줄 인터페이스를 사용하여 새 Active Directory 포리스트 설치](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
