---
title: Azure AD Domain Services에 Ubuntu VM 조인 | Microsoft Docs
description: Ubuntu Linux 가상 머신을 Azure AD Domain Services 관리되는 도메인에 조인하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 10/11/2021
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: ea50fc8bfbaaf7383eb71b433fee97729967c061
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856983"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ubuntu Linux 가상 머신을 Azure Active Directory Domain Services 관리되는 도메인에 조인

사용자가 단일 자격 증명 집합을 사용하여 Azure VM(가상 머신)에 로그인할 수 있도록 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 VM을 조인할 수 있습니다. Azure AD DS 관리되는 도메인에 VM을 조인하면 도메인의 사용자 계정과 자격 증명을 사용하여 서버에 로그인하고 관리할 수 있습니다. VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있도록 관리되는 도메인의 그룹 멤버 자격도 적용됩니다.

이 문서에서는 Ubuntu Linux VM을 관리되는 도메인에 조인하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* 관리되는 도메인의 일부인 사용자 계정 사용자의 SAMAccountName 특성이 자동으로 생성되지 않았는지 확인합니다. Azure AD 테넌트의 여러 사용자 계정에 동일한 mailNickname 특성이 있는 경우 각 사용자에 대한 SAMAccountName 특성이 자동으로 생성됩니다. 자세한 내용은 [Azure Active Directory Domain Services 관리형 도메인에서 개체 및 자격 증명을 동기화하는 방법](synchronization.md)을 참조하세요.
* Active Directory에서 충돌을 일으킬 수 있는 잘린 이름을 방지하기 위한 최대 15자의 고유한 Linux VM 이름

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux VM 만들기 및 연결

Azure에 기존 Ubuntu Linux VM이 있는 경우 SSH를 사용하여 연결하고 다음 단계를 계속 진행하여 [VM 구성을 시작](#configure-the-hosts-file)합니다.

Ubuntu Linux VM을 만들거나 이 문서에서 사용할 테스트 VM을 만들려는 경우 다음 방법 중 하나를 사용할 수 있습니다.

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
* *ubuntu* 는 관리되는 도메인에 조인하려는 Ubuntu VM의 호스트 이름입니다.

해당 이름을 고유한 값으로 업데이트합니다.

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

완료되면 편집기의 `:wq` 명령을 사용하여 *hosts* 파일을 저장한 후 종료합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

VM을 관리되는 도메인에 조인하려면 VM에 몇 가지 추가 패키지가 필요합니다. 추가 패키지를 설치하고 구성하려면 `apt-get`를 사용하여 도메인 조인 도구를 업데이트하고 설치합니다.

Kerberos 설치 중에 *krb5-user* 패키지는 영역 이름을 모두 대문자로 입력하라는 메시지를 표시합니다. 예를 들어 관리되는 도메인의 이름이 *aaddscontoso.com* 인 경우 *AADDSCONTOSO.COM* 를 영역으로 입력합니다. 설치는 */etc/krb5.conf* 의 `[realm]` 및 `[domain_realm]` 섹션을 구성 파일에 기록합니다. 영역을 모두 대문자로 지정해야 합니다.

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>NTP(Network Time Protocol) 구성

도메인 통신이 제대로 작동하려면 Ubuntu VM의 날짜 및 시간이 관리되는 도메인과 동기화되어야 합니다. 관리되는 도메인의 NTP 호스트 이름을 */etc/ntp.conf* 파일에 추가합니다.

1. 편집기를 사용하여 *ntp.conf* 파일을 엽니다.

    ```console
    sudo vi /etc/ntp.conf
    ```

1. *ntp.conf* 파일에서 관리되는 도메인의 DNS 이름을 추가할 줄을 만듭니다. 다음 예제에서는 *aaddscontoso.com* 에 대한 항목을 추가합니다. 사용자 고유의 DNS 이름 사용:

    ```console
    server aaddscontoso.com
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 *ntp.conf* 파일을 저장하고 종료합니다.

1. VM이 관리되는 도메인과 동기화되었는지 확인하려면 다음 단계를 수행해야 합니다.

    * NTP 서버 중지
    * 관리되는 도메인에서 날짜 및 시간 업데이트
    * NTP 서비스 시작

    다음 명령을 실행하여 단계를 완료합니다. `ntpdate` 명령을 사용하여 고유의 DNS 이름을 사용합니다.

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>VM을 관리되는 도메인에 조인

이제 필수 패키지를 VM에 설치하고 NTP를 구성했으므로 VM을 관리되는 도메인에 조인합니다.

1. `realm discover` 명령을 사용하여 관리되는 도메인을 검색합니다. 다음 예제에서는 보안영역 *AADDSCONTOSO.COM* 을 검색합니다. 관리되는 도메인 이름을 모두 대문자로 지정합니다.

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   `realm discover` 명령에서 관리되는 도메인을 찾을 수 없는 경우 다음의 문제 해결 단계를 검토합니다.

    * VM에서 도메인에 연결할 수 있는지 확인합니다. `ping aaddscontoso.com`을 시도하여 긍정적인 응답이 반환되는지 확인합니다.
    * VM이 관리되는 도메인을 사용할 수 있는 것과 동일하거나 피어링한 가상 네트워크에 배포되었는지 확인합니다.
    * 가상 네트워크의 DNS 서버 설정이 관리되는 도메인의 도메인 컨트롤러를 가리키도록 업데이트되었는지 확인합니다.

1. 이제 `kinit` 명령을 사용하여 Kerberos를 초기화합니다. 관리되는 도메인의 일부인 사용자를 지정합니다. 필요한 경우 [Azure AD의 그룹에 사용자 계정을 추가합니다](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    다시 한번 강조하지만 관리되는 도메인 이름은 모두 대문자로 입력해야 합니다. 다음 예제에서 `contosoadmin@aaddscontoso.com`이라는 이름의 계정은 Kerberos를 초기화하는 데 사용됩니다. 관리되는 도메인의 일부인 고유의 사용자 계정을 입력합니다.

    ```console
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. 마지막으로 `realm join` 명령을 사용하여 관리되는 도메인에 VM을 조인합니다. `contosoadmin@AADDSCONTOSO.COM`와 같이 이전의 `kinit` 명령에서 지정한 관리되는 도메인의 일부인 동일한 사용자 계정을 사용합니다.

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

VM을 관리되는 도메인에 조인하는 데는 몇 분 정도 걸립니다. 다음의 예제 출력에서는 VM이 관리되는 도메인에 성공적으로 조인되었음을 보여 줍니다.

```output
Successfully enrolled machine in realm
```

VM이 도메인 조인 프로세스를 성공적으로 완료할 수 없는 경우 VM의 네트워크 보안 그룹에서 관리되는 도메인의 가상 네트워크 서브넷에 대한 TCP + UDP 포트 464의 아웃바운드 Kerberos 트래픽을 허용하는지 확인합니다.

’지정되지 않은 GSS 오류입니다. 보조 코드가 추가 정보를 제공할 수 있습니다(Kerberos 데이터베이스에서 서버를 찾을 수 없음)’ 오류가 표시되면 */etc/krb5.conf* 파일을 연 후 다음 `[libdefaults]` 섹션에 코드를 추가하고 다시 시도하세요.

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>SSSD 구성 업데이트

이전 단계에서 설치된 패키지 중 하나는 SSSD(시스템 보안 서비스 디먼)용입니다. 사용자가 도메인 자격 증명을 사용하여 VM에 로그인하려고 하면 SSSD는 인증 공급자에게 요청을 릴레이합니다. 이 시나리오에서 SSSD는 Azure AD DS를 사용하여 요청을 인증합니다.

1. 편집기를 사용하여 *sssd.conf* 파일을 엽니다.

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 다음과 같이 *use_fully_qualified_names* 에 대한 줄을 주석으로 처리합니다.

    ```console
    # use_fully_qualified_names = True
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 *sssd.conf* 파일을 저장하고 종료합니다.

1. 변경 내용을 적용하려면 SSSD 서비스를 다시 시작합니다.

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>사용자 계정 및 그룹 설정 구성

VM이 관리되는 도메인에 조인되고 인증을 위해 구성되었으면 몇 가지 사용자 구성 옵션을 완료할 수 있습니다. 구성 변경에는 암호 기반 인증 허용과 도메인 사용자가 처음으로 로그인할 때 로컬 VM에 홈 디렉터리 자동 생성이 포함됩니다.

### <a name="allow-password-authentication-for-ssh"></a>SSH에 대한 암호 인증 허용

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
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>자동 홈 디렉터리 생성 구성

사용자가 처음으로 로그인할 때 홈 디렉터리의 자동 생성을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 편집기에서 */etc/pam.d/common-session* 파일을 엽니다.

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. 기존 `session optional pam_sss.so` 줄 아래에 다음 줄을 추가합니다.

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 *공통 세션* 파일을 저장하고 종료합니다.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

*AAD DC 관리자* 그룹 구성원에게 Ubuntu VM에 대한 관리 권한을 부여하려면 */etc/sudoers* 에 항목을 추가합니다. 일단 추가되면 *AAD DC 관리자* 그룹의 구성원이 Ubuntu VM에서 `sudo` 명령을 사용할 수 있습니다.

1. 편집할 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */etc/sudoers* 파일의 끝에 다음 항목을 추가합니다.

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    완료되면 `Ctrl-X` 명령을 사용하여 편집기를 저장하고 종료합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용하여 VM에 로그인합니다.

VM이 관리되는 도메인에 성공적으로 조인되었는지 확인하려면 도메인 사용자 계정을 사용하여 새 SSH 연결을 시작합니다. 홈 디렉터리가 생성되었고 도메인의 그룹 멤버 자격이 적용되었는지 확인합니다.

1. 콘솔에서 새 SSH 연결을 만듭니다. `contosoadmin@aaddscontoso.com`과 같은 `ssh -l` 명령을 사용하는 관리되는 도메인에 속한 도메인 계정을 사용한 다음, *ubuntu.aaddscontoso.com* 과 같은 VM 주소를 입력합니다. Azure Cloud Shell을 사용하는 경우 내부 DNS 이름보다 VM의 공용 IP 주소를 사용합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. VM에 성공적으로 연결되면 홈 디렉터리가 올바르게 초기화되었는지 확인합니다.

    ```console
    pwd
    ```

    사용자 계정과 일치하는 고유한 디렉터리가 있는 */home* 디렉터리에 있어야 합니다.

1. 이제 그룹 멤버 자격이 올바르게 확인되는지 확인합니다.

    ```console
    id
    ```

    관리되는 도메인에 그룹 멤버 자격이 표시되어야 합니다.

1. ‘AAD DC 관리자’ 그룹 멤버로 VM에 로그인한 경우 `sudo` 명령을 올바르게 사용할 수 있는지 확인합니다.

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>다음 단계

VM을 관리되는 도메인에 연결하거나 도메인 계정으로 로그인하는 데 문제가 있는 경우 [도메인 조인 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
