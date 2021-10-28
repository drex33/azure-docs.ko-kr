---
title: Azure AD Domain Services에 SLE VM 조인 | Microsoft Docs
description: SUSE Linux Enterprise Azure AD Domain Services 관리되는 도메인에 가상 머신을 구성하고 조인하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: 78a9db37a83c9e53ee336c0b1760b111a78dbcbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547014"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>SUSE Linux Enterprise 가상 머신을 Azure Active Directory Domain Services 관리되는 도메인에 조인

사용자가 단일 자격 증명 집합을 사용하여 Azure VM(가상 머신)에 로그인할 수 있도록 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 VM을 조인할 수 있습니다. Azure AD DS 관리되는 도메인에 VM을 조인하면 도메인의 사용자 계정과 자격 증명을 사용하여 서버에 로그인하고 관리할 수 있습니다. VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있도록 관리되는 도메인의 그룹 멤버 자격도 적용됩니다.

이 문서에서는 SLE(SUSE Linux Enterprise) VM을 관리되는 도메인에 조인하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* 관리되는 도메인의 일부인 사용자 계정
* Active Directory에서 충돌을 일으킬 수 있는 잘린 이름을 방지하기 위한 최대 15자의 고유한 Linux VM 이름

## <a name="create-and-connect-to-a-sle-linux-vm"></a>SLE Linux VM 만들기 및 연결

Azure에 기존 SLE Linux VM이 있는 경우 SSH를 사용하여 연결하고 다음 단계를 계속 진행하여 [VM 구성을 시작](#configure-the-hosts-file)합니다.

SLE Linux VM을 만들어야 하거나 이 문서에서 사용할 테스트 VM을 만들려는 경우 다음 방법 중 하나를 사용할 수 있습니다.

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM을 만들 때 가상 네트워크 설정에 주의하여 VM이 관리되는 도메인과 통신할 수 있는지 확인합니다.

* Azure AD Domain Services를 사용하도록 설정한 것과 동일하거나 피어링한 가상 네트워크에 VM을 배포합니다.
* Azure AD Domain Services 관리되는 도메인과 다른 서브넷에 VM을 배포합니다.

VM이 배포되면 단계에 따라 SSH를 사용하여 VM에 연결합니다.

## <a name="configure-the-hosts-file"></a>hosts 파일 구성

VM 호스트 이름이 관리되는 도메인에 올바르게 구성되었는지 확인하려면 */etc/hosts* 파일을 편집하고 호스트 이름을 설정합니다.

```console
sudo vi /etc/hosts
```

*hosts* 파일에서 *localhost* 주소를 업데이트합니다. 다음 예제에서는

* *aaddscontoso.com* 은 관리되는 도메인의 DNS 도메인 이름입니다.
* *linux-q2gr* 은 관리되는 도메인에 조인하려는 SLE VM의 호스트 이름입니다.

해당 이름을 고유한 값으로 업데이트합니다.

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

완료되면 편집기의 `:wq` 명령을 사용하여 *hosts* 파일을 저장한 후 종료합니다.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>SSSD를 사용하여 VM을 관리되는 도메인에 조인

**SSSD** 및 YaST의 ‘사용자 로그온 관리’ 모듈을 사용하여 관리되는 도메인에 조인하려면 다음 단계를 완료합니다.

1. ‘사용자 로그온 관리’ YaST 모듈을 설치합니다.

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. YaST를 엽니다.

1. 나중에 DNS 자동 검색을 성공적으로 사용하려면 관리되는 도메인 IP 주소(‘Active Directory 서버’)를 클라이언트의 이름 서버로 구성합니다.

    YaST에서 **시스템 > 네트워크 설정** 을 선택합니다.

1. *Hostname/DNS* 탭을 선택한 다음, ‘이름 서버 1’ 텍스트 상자에 관리되는 도메인의 IP 주소를 입력합니다. IP 주소는 관리되는 도메인에 대한 Azure Portal의 ‘속성’ 창에 표시됩니다(예: *10.0.2.4* 및 *10.0.2.5*).

    관리되는 고유 도메인 IP 주소를 추가한 다음 **확인** 을 선택합니다.

1. YaST 주 창에서 *Network Services*  >  ‘사용자 로그온 관리’를 선택합니다.

    다음 예제 스크린샷에 나타난 것처럼 모듈에는 컴퓨터의 여러 네트워크 속성 및 현재 사용 중인 인증 방법을 보여 주는 개요가 열립니다.

    ![YaST의 사용자 로그인 관리 창에 대한 예시 스크린샷](./media/join-suse-linux-vm/overview-window.png)

    편집을 시작하려면 **설정 변경** 을 선택합니다.

VM을 관리되는 도메인에 조인하려면 다음 단계를 완료합니다.

1. 대화 상자에서 **도메인 추가** 를 선택합니다.

1. *aaddscontoso.com* 과 같은 올바른 ‘도메인 이름’을 지정하고, ID 데이터 및 인증에 사용할 서비스를 지정합니다. 두 가지 모두에 대해 *Microsoft Active Directory* 를 선택합니다.

    ‘도메인 사용’ 옵션이 선택되어 있는지 확인합니다.

1. 준비되면 **확인** 을 선택합니다.

1. 다음 대화 상자에서 기본 설정을 적용한 다음 **확인** 을 선택합니다.

1. VM은 필요에 따라 추가 소프트웨어를 설치하고 관리되는 도메인을 사용할 수 있는지 확인합니다.

    모든 항목이 올바르면 VM이 관리되는 도메인을 검색했지만 *아직 등록되지 않았음* 을 나타내는 다음 예제 대화 상자가 표시됩니다.

    ![YaST의 Active Directory 등록 창의 예제 스크린샷](./media/join-suse-linux-vm/enroll-window.png)

1. 대화 상자에서 관리되는 도메인에 속한 사용자의 ‘사용자 이름’ 및 ‘암호’를 지정합니다.  필요한 경우 [Azure AD의 그룹에 사용자 계정을 추가합니다](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    현재 도메인이 Samba에 대해 사용하도록 설정되어 있는지 확인하려면 ‘이 AD에서 작동하도록 Samba 구성 덮어쓰기’를 활성화합니다.

1. 등록하려면 **확인** 을 선택합니다.

1. 성공적으로 등록되었는지 확인하는 메시지가 표시됩니다. 완료하려면 **확인** 을 선택합니다.

VM이 관리되는 도메인에 등록되면 다음 예제 스크린샷과 같이 ‘도메인 사용자 로그온 관리’를 사용하여 클라이언트를 구성합니다.

![YaST의 도메인 사용자 로그온 관리 창을 보여 주는 예제 스크린샷](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. 관리되는 도메인에서 제공하는 데이터를 사용하여 로그인을 허용하려면 ‘도메인 사용자 로그온 허용’ 확인란을 선택합니다.

1. 필요에 따라 ‘도메인 데이터 원본 사용’에서 사용자 환경에 필요한 추가 데이터 원본을 선택합니다. 관련 옵션에는 **sudo** 를 사용할 수 있는 사용자 또는 사용 가능한 네트워크 드라이브가 포함됩니다.

1. 관리되는 도메인의 사용자가 VM에 홈 디렉터리를 가질 수 있도록 하려면 ‘홈 디렉터리 만들기’ 확인란을 선택합니다.

1. 사이드바에서 **서비스 옵션 › 이름 스위치** 를 선택한 다음 ‘확장 옵션’을 선택합니다. 해당 창에서 *fallback_homedir* 또는 *override_homedir* 을 선택한 다음, **추가** 를 선택합니다.

1. 홈 디렉터리 위치에 대한 값을 지정합니다. 홈 디렉터리가 */home/USER_NAME* 형식을 따르게 하려면 */home/%u* 를 사용합니다. 가능한 변수에 대한 자세한 내용은 sssd.conf 기본 페이지(`man 5 sssd.conf`) 섹션 *override_homedir* 를 참조하세요.

1. **확인** 을 선택합니다.

1. 변경 내용을 저장하려면 **확인** 을 선택합니다. 그런 다음, 이제 표시된 값이 올바른지 확인합니다. 대화 상자를 나가려면 **취소** 를 선택합니다.

1. SSSD와 Winbind를 동시에 실행하려는 경우(예: SSSD를 통해 조인한 다음 Samba 파일 서버를 실행하는 경우) Samba 옵션 ‘kerberos 메서드’를 smb.conf에서 ‘비밀 및 keytab’으로 설정해야 합니다.  SSSD 옵션 *ad_update_samba_machine_account_password* 도 sssd.conf에서 *true* 로 설정해야 합니다. 이 옵션은 시스템 keytab이 동기화되지 않는 것을 방지합니다.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Winbind를 사용하여 VM을 관리되는 도메인에 조인

**Winbind** 및 YaST의 ‘Windows 도메인 멤버 자격’ 모듈을 사용하여 관리되는 도메인에 조인하려면 다음 단계를 완료합니다.

1. YaST에서 **Network Services > Windows 도메인 멤버 자격** 을 선택합니다.

1. ‘Windows 도메인 멤버 자격’ 화면의 ‘도메인 또는 작업 그룹’에서 조인할 도메인을 입력합니다.  관리되는 도메인 이름(예: *aaddscontoso.com*)을 입력합니다.

    ![YaST의 Windows 도메인 멤버 자격 창을 보여 주는 예제 스크린샷](./media/join-suse-linux-vm/samba-client-window.png)

1. Linux 인증에 SMB 원본을 사용하려면 ‘Linux 인증에 SMB 정보 사용’ 옵션을 선택합니다.

1. VM에서 관리되는 도메인 사용자에 대한 로컬 홈 디렉터리를 자동으로 만들려면 ‘로그인 시 홈 디렉터리 만들기’ 옵션을 선택합니다.

1. 관리되는 도메인을 일시적으로 사용할 수 없는 경우에도 도메인 사용자가 로그인할 수 있도록 하려면 ‘오프라인 인증’ 옵션을 선택합니다.

1. Samba 사용자 및 그룹에 대한 UID 및 GID 범위를 변경하려면 ‘전문가 설정’을 선택합니다.

1. *NTP 구성* 을 선택하여 관리되는 도메인에 대한 NTP(Network Time Protocol) 시간 동기화를 구성합니다. 관리되는 도메인의 IP 주소를 입력합니다. IP 주소는 관리되는 도메인에 대한 Azure Portal의 ‘속성’ 창에 표시됩니다(예: *10.0.2.4* 및 *10.0.2.5*).

1. **확인** 을 선택하고 메시지가 표시되면 도메인 가입을 확인합니다.

1. 관리되는 도메인 관리자의 암호를 입력하고 **확인** 을 선택합니다.

    ![SLE VM을 관리되는 도메인에 조인하는 경우 표시되는 인증 대화 상자를 보여 주는 예제 스크린샷](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

관리되는 도메인에 조인한 후에는 데스크톱 또는 콘솔의 디스플레이 관리자를 사용하여 워크스테이션에서 해당 도메인에 로그인할 수 있습니다.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>YaST 명령줄 인터페이스에서 Winbind를 사용하여 VM을 관리되는 도메인에 조인

**winbind** 및 *YaST 명령줄 인터페이스* 를 사용하여 관리되는 도메인에 조인하려면 다음을 수행합니다.

* 도메인에 조인합니다.

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>터미널에서 Winbind를 사용하여 VM을 관리되는 도메인에 조인

**winbind** 및 ‘`samba net` 명령’을 사용하여 관리되는 도메인에 조인하려면 다음을 수행합니다.

1. kerberos 클라이언트 및 samba-winbind를 설치합니다.

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. 구성 파일을 편집합니다.

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/security/pam_winbind.conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Azure AD 및 Linux의 날짜와 시간이 동기화되어 있는지 확인합니다. NTP 서비스에 Azure AD 서버를 추가하여 이 작업을 수행할 수 있습니다.
   
   1. /etc/ntp.conf에 다음 줄을 추가합니다.
     
      ```console
      server aaddscontoso.com
      ```

   1. NTP 서비스를 다시 시작합니다.
     
      ```console
      sudo systemctl restart ntpd
      ```

4. 도메인에 조인합니다.

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Linux PAM(플러그형 인증 모듈)에서 winbind를 로그인 원본으로 사용하도록 설정합니다.

   ```console
   pam-config --add --winbind
   ```

6. 사용자가 로그인할 수 있도록 홈 디렉터리 자동 생성을 사용하도록 설정합니다.

   ```console
   pam-config -a --mkhomedir
   ```

7. Winbind 서비스를 시작하고 사용하도록 설정합니다.

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>SSH에 대한 암호 인증 허용

기본적으로 사용자는 SSH 퍼블릭 키 기반 인증을 사용하여 VM에만 로그인할 수 있습니다. 암호 기반 인증이 실패합니다. VM을 관리되는 도메인에 조인할 때 해당 도메인 계정은 암호 기반 인증을 사용해야 합니다. 다음과 같이 SSH 구성을 업데이트하여 암호 기반 인증을 허용합니다.

1. 편집기를 사용하여 *sshd_conf* 파일을 엽니다.

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *PasswordAuthentication* 줄을 ‘예’로 업데이트합니다.

    ```console
    PasswordAuthentication yes
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 *sshd_conf* 파일을 저장한 후 종료합니다.

1. 변경 내용을 적용하고 사용자가 암호를 사용하여 로그인하게 하려면 SSH 서비스를 다시 시작합니다.

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

‘AAD DC 관리자’ 그룹 구성원에게 SLE VM에 대한 관리 권한을 부여하려면 */etc/sudoers* 에 항목을 추가합니다. 추가된 후에는 ‘AAD DC 관리자’ 그룹의 구성원이 SLE VM에서  *명령을 사용할 수 있습니다.* `sudo`

1. 편집할 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */etc/sudoers* 파일에 다음 항목을 추가합니다. *AAD DC 관리자* 그룹 이름에 공백이 포함되어 있으므로 그룹 이름에 백슬래시 이스케이프 문자를 포함합니다. *aaddscontoso.com* 와 같은 고유한 도메인 이름을 추가합니다.

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 저장한 후 종료합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용하여 VM에 로그인

VM이 관리되는 도메인에 성공적으로 조인되었는지 확인하려면 도메인 사용자 계정을 사용하여 새 SSH 연결을 시작합니다. 홈 디렉터리가 생성되었고 도메인의 그룹 멤버 자격이 적용되었는지 확인합니다.

1. 콘솔에서 새 SSH 연결을 만듭니다. `ssh -l` 명령을 사용하여 관리되는 도메인에 속하는 도메인 계정을 사용하고(예: `contosoadmin@aaddscontoso.com`) VM의 주소를 입력합니다(예: *linux-q2gr.aaddscontoso.com*). Azure Cloud Shell을 사용하는 경우 내부 DNS 이름보다 VM의 공용 IP 주소를 사용합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. VM에 성공적으로 연결되면 홈 디렉터리가 올바르게 초기화되었는지 확인합니다.

    ```console
    pwd
    ```

    사용자 계정과 일치하는 고유한 디렉터리가 있는 */home* 디렉터리에 있어야 합니다.

3. 이제 그룹 멤버 자격이 올바르게 확인되는지 확인합니다.

    ```console
    id
    ```

    관리되는 도메인에 그룹 멤버 자격이 표시되어야 합니다.

4. ‘AAD DC 관리자’ 그룹 멤버로 VM에 로그인한 경우 `sudo` 명령을 올바르게 사용할 수 있는지 확인합니다.

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>다음 단계

VM을 관리되는 도메인에 연결하거나 도메인 계정으로 로그인하는 데 문제가 있는 경우 [도메인 조인 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
