---
title: Azure NetApp Files에 대한 NFS 클라이언트 구성 | Microsoft Docs
description: Azure NetApp Files에서 사용하도록 NFS 클라이언트를 구성하는 방법을 설명합니다.
services: azure-netapp-files
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
ms.date: 09/22/2021
ms.author: b-hchen
ms.openlocfilehash: 399003d0362dc480e027b78e58bc1c6e7a882f8a
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133545519"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Azure NetApp Files에 대한 NFS 클라이언트 구성

이 문서에 설명된 NFS 클라이언트 구성은 [NFSv4.1 Kerberos 암호화를 구성](configure-kerberos-encryption.md)하거나 [이중 프로토콜 볼륨을 만들](create-volumes-dual-protocol.md) 때 설정의 일부입니다. Azure NetApp Files와 함께 사용할 수 있는 다양한 Linux 배포판이 있습니다. 이 문서에서는 일반적으로 사용되는 두 가지 환경(RHEL 8 및 Ubuntu 18.04)에 대한 구성을 설명합니다. 

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항  

사용하는 Linux 버전에 관계없이 다음 구성이 필요합니다.

* 시간차 문제를 방지하도록 NTP 클라이언트를 구성합니다.
* 이름 확인을 위해 Linux 클라이언트의 DNS 항목을 구성합니다.  
    이 구성에는 "A"(정방향) 레코드와 PTR(역방향) 레코드가 포함되어야 합니다. 
* 도메인을 가입하려면 대상 Active Directory(realm join 명령 중에 만들어짐)에서 Linux 클라이언트에 대한 컴퓨터 계정을 만듭니다. 
    > [!NOTE] 
    > 아래 명령에 사용되는 `$SERVICEACCOUNT` 변수는 대상 조직 구성 단위에 컴퓨터 계정을 만들 수 있는 권한 또는 위임이 있는 사용자 계정이어야 합니다.

## <a name="rhel-8-configuration"></a>RHEL 8 구성 

이 섹션에서는 NFSv4.1 Kerberos 암호화 및 이중 프로토콜에 필요한 RHEL 구성에 대해 설명합니다.  

이 섹션의 예제에서는 다음 도메인 이름 및 IP 주소를 사용합니다.  

* 도메인 이름: `contoso.com`
* 개인 IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>NFSv 4.1 Kerberos 암호화를 사용하는 경우 RHEL 8 구성

1. 적절한 DNS 서버를 사용하여 `/etc/resolv.conf`를 구성합니다.  

    예를 들면 다음과 같습니다.  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. DNS 정방향 및 역방향 조회 영역에 대한 DNS 서버에 NFS 클라이언트 레코드를 추가합니다.

3. DNS를 확인하려면 NFS 클라이언트에서 다음 명령을 사용합니다.   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. 패키지 설치:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  NTP 클라이언트를 구성합니다.  

    RHEL 8은 기본적으로 chrony를 사용합니다. [도구 모음을 사용하여 `Chrony`NTP 구성](https://access.redhat.com/documentation/en-us/red-hat-enterprise-linux/8/guide/6c230de2-39f1-455a-902d-737eea31ad34)의 구성 지침을 따릅니다.

6.  Active Directory 도메인에 가입합니다.  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    예를 들면 다음과 같습니다. 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    `default_realm`이 `/etc/krb5.conf`에서 제공된 보안영역으로 설정되어 있는지 확인합니다.  그렇지 않은 경우 다음 예제와 같이 파일의 `[libdefaults]` 섹션 아래에 추가합니다.
    
    ```
    [libdefaults]
        default_realm = CONTOSO.COM
        default_tkt_enctypes = aes256-cts-hmac-sha1-96
        default_tgs_enctypes = aes256-cts-hmac-sha1-96
        permitted_enctypes = aes256-cts-hmac-sha1-96
    [realms]
        CONTOSO.COM = {
            kdc = dc01.contoso.com
            admin_server = dc01.contoso.com
            master_kdc = dc01.contoso.com
            default_domain = contoso.com
        }
    [domain_realm]
        .contoso.com = CONTOSO.COM
        contoso.com = CONTOSO.COM
    [logging]
        kdc = SYSLOG:INFO
        admin_server = FILE=/var/kadm5.log
    ```

7. 모든 NFS 서비스를 다시 시작합니다.  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    다시 시작하면 Kerberos 탑재 중에 `“mount.nfs: an incorrect mount option was specified”` 오류 조건이 방지됩니다.

8. 사용자 계정으로 `kinit` 명령을 실행하여 티켓을 가져옵니다. 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    예를 들면 다음과 같습니다.   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>이중 프로토콜을 사용하는 경우 RHEL 8 구성

다음 단계는 선택 사항입니다. NFS 클라이언트에서 사용자 매핑을 사용하는 경우에만 이 단계를 수행해야 합니다. 

1. [NFSv4.1 Kerberos 암호화를 사용하는 경우 RHEL 8 구성](#rhel8_nfsv41_kerberos) 섹션에서 설명하는 모든 단계를 완료합니다.   

2. SSSD 구성 파일에서 IP 주소를 사용하는 대신, AD의 FQDN(정규화된 도메인 이름)을 사용하도록 /etc/hosts 파일에 고정 DNS 레코드를 추가합니다.  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. 도메인에 대한 추가 섹션을 추가하여 AD LDAP 서버에서 식별자를 확인합니다.    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
    
    `[domain/contoso-ldap]`위의 구성에서 다음을 수행 합니다.
    * `id_provider` 는로 설정 되 `ldap` 고는 그렇지 않습니다 `ad` .
    * 검색에 대 한 검색 기준 및 사용자 및 그룹 클래스가 구성에 지정 되어 있습니다.
    * `ldap_sasl_authid` 는의 컴퓨터 계정 이름입니다 `klist -kte` .
    * `use_fully_qualified_names`이 `false`로 설정됩니다.  이 설정은 짧은 이름을 사용할 때이 구성이 사용 됨을 의미 합니다.
    * `ldap_id_mapping` 가 지정 되지 않은 경우 기본값은 `false` 입니다.

    `realm join`구성은 클라이언트에서 생성 되며 다음과 같습니다.
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   
    
    `[domain/contoso.com]`위의 구성에서 다음을 수행 합니다.
    * `id_provider`이 `ad`로 설정됩니다.
    * `ldap_id_mapping`이 `true`로 설정됩니다. SSSD 생성 Id를 사용 합니다. 또는 `false` 모든 사용자 이름 스타일에 대해 POSIX uid를 사용 하려는 경우이 값을로 설정할 수 있습니다. 클라이언트 구성에 따라 값을 결정할 수 있습니다. 
    * `use_fully_qualified_names`은 `true`입니다. 이 설정은 `user@CONTOSO.COM` 이 구성을 사용 함을 의미 합니다.

4. `/etc/nsswitch.conf`에 `sss` 항목이 있는지 확인합니다.   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. `sssd` 서비스를 다시 시작하고 캐시를 지웁니다.   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. 클라이언트가 LDAP 서버와 통합되었는지 확인하기 위해 테스트합니다.   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu 구성   

이 섹션에서는 NFSv4.1 Kerberos 암호화 및 이중 프로토콜에 필요한 Ubuntu 구성에 대해 설명합니다. 

이 섹션의 예제에서는 다음 도메인 이름 및 IP 주소를 사용합니다.  

* 도메인 이름: `contoso.com`
* 개인 IP: `10.6.1.4`

1. 적절한 DNS 서버를 사용하여 `/etc/resolv.conf`를 구성합니다.

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. DNS 정방향 및 역방향 조회 영역에 대한 DNS 서버에 NFS 클라이언트 레코드를 추가합니다.
 
    DNS를 확인하려면 NFS 클라이언트에서 다음 명령을 사용합니다.

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. 패키지를 설치합니다.
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    메시지가 표시되면 `$DOMAIN.NAME`(예: `CONTOSO.COM`과 같이 대문자 사용)를 기본 Kerberos 보안영역으로 입력합니다.

4. `rpc-gssd.service` 서비스를 다시 시작합니다. 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18.04는 기본적으로 chrony를 사용합니다. [Ubuntu Bionic: chrony를 사용하여 NTP 구성](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)의 구성 지침을 따릅니다.

6. Active Directory 도메인에 가입합니다.   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    예를 들면 다음과 같습니다.    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. 티켓을 얻으려는 사용자로 `kinit`를 실행합니다. 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    예를 들면 다음과 같습니다.    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>이중 프로토콜을 사용하는 경우 Ubuntu 구성  

다음 단계는 선택 사항입니다.  NFS 클라이언트에서 사용자 매핑을 사용하려는 경우에만 이 단계를 수행해야 합니다.  

1. 다음 명령을 실행하여 설치된 패키지를 업그레이드합니다.   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    다음 예제에서는 샘플 값을 사용합니다. 명령에서 입력하라는 메시지가 표시되면 사용자 환경에 따라 입력을 제공해야 합니다. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. `/etc/nsswitch.conf` 파일에 다음 `ldap` 항목이 있는지 확인합니다.   
    `passwd:    compat systemd ldap`   
    `group:     compat systemd ldap`

3. 다음 명령을 실행하여 서비스를 다시 시작하고 사용합니다.

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

다음 예제에서는 LDAP 사용자 `‘hari1’`에 대한 Ubuntu LDAP 클라이언트에서 AD LDAP 서버를 쿼리합니다. 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes"></a>NFSv 4.1 볼륨에 액세스하도록 호스트 이름이 같은 두 VM 구성 

이 섹션에서는 Azure NetApp Files NFSv4.1 볼륨에 액세스하도록 호스트 이름이 같은 두 개의 VM을 구성하는 방법을 설명합니다. 이 절차는 DR(재해 복구) 테스트를 수행하고 기본 DR 시스템과 동일한 호스트 이름의 테스트 시스템을 필요로 하는 경우에 유용할 수 있습니다. 이 절차는 동일한 Azure NetApp Files 볼륨에 액세스하는 두 VM에 동일한 호스트 이름이 있는 경우에만 필요합니다.  

NFSv4.x에서는 각 클라이언트가 *고유* 문자열로 서버에 대해 자신을 식별해야 합니다. 한 클라이언트와 한 서버 간에 공유되는 파일 열기 및 잠금 상태는 이 ID와 연결됩니다. 강력한 NFSv4.x 상태 복구 및 투명 상태 마이그레이션을 지원하려면 이 ID 문자열이 클라이언트를 다시 부팅하는 동안 변경되지 않아야 합니다.

1. 다음 명령을 사용하여 VM 클라이언트에 `nfs4_unique_id` 문자열을 표시합니다.
    
    `# systool -v -m nfs | grep -i nfs4_unique`     
    `    nfs4_unique_id      = ""`

    동일한 호스트 이름(예: DR 시스템)을 사용하여 추가 VM에 동일한 볼륨을 탑재하려면 Azure NetApp Files NFS 서비스에 대해 고유하게 식별할 수 있도록 `nfs4_unique_id`를 만듭니다.  이 단계를 통해 서비스는 동일한 호스트 이름으로 두 VM을 구분하고 두 VM 모두에서 NFSv 4.1 볼륨을 탑재할 수 있습니다.  

    테스트 DR 시스템에서만 이 단계를 수행해야 합니다. 일관성을 위해 관련된 각 가상 머신에 고유한 설정을 적용하는 것이 좋습니다.

2. 테스트 DR 시스템에서 일반적으로 `/etc/modprobe.d/`에 있는 `nfsclient.conf` 파일에 다음 행을 추가합니다.

    `options nfs nfs4_unique_id=uniquenfs4-1`  

    `uniquenfs4-1` 문자열은 서비스에 연결할 VM 전체에서 고유하다면 영숫자 문자열이 될 수 있습니다.

    NFS 클라이언트 설정을 구성하는 방법에 대한 배포 설명서를 확인합니다.

    변경 사항을 적용하려면 VM을 다시 부팅합니다.

3. 테스트 DR 시스템에서 VM 다시 부팅 후 `nfs4_unique_id`가 설정되었는지 확인합니다.       

    `# systool -v -m nfs | grep -i nfs4_unique`   
    `   nfs4_unique_id      = "uniquenfs4-1"`   

4. 정상적으로 두 VM에 [NFSv4.1 볼륨을 탑재](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)합니다.

    이제 호스트 이름이 같은 두 VM이 모두 NFSv 4.1 볼륨을 탑재하고 액세스할 수 있습니다.  

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)
* [Windows 또는 Linux 가상 머신용 볼륨 탑재](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 
