---
title: CentOS VM을 Azure AD Domain Services에 조인하기 | Microsoft Docs
description: CentOS Linux 가상 머신을 Azure Active Directory Domain Services 관리되는 도메인에 구성하고 조인하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/17/2021
ms.author: justinha
ms.openlocfilehash: dc82c2c2793495009b9200ac0976c796ef2ccc9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603755"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>CentOS Linux 가상 머신을 Azure Active Directory Domain Services 관리되는 도메인에 조인

사용자가 단일 자격 증명 집합을 사용하여 Azure VM(가상 머신)에 로그인할 수 있도록 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 VM을 조인할 수 있습니다. Azure AD DS 관리되는 도메인에 VM을 조인하면 도메인의 사용자 계정과 자격 증명을 사용하여 서버에 로그인하고 관리할 수 있습니다. VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있도록 관리되는 도메인의 그룹 멤버 자격도 적용됩니다.

이 문서에서는 CentOS Linux VM을 관리되는 도메인에 조인하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* 관리되는 도메인의 일부인 사용자 계정입니다.
* Active Directory에서 충돌을 일으킬 수 있는 잘린 이름을 방지하기 위한 최대 15자의 고유한 Linux VM 이름

## <a name="create-and-connect-to-a-centos-linux-vm"></a>CentOS Linux VM 만들기 및 연결

Azure에 기존 CentOS Linux VM이 있는 경우 SSH를 사용하여 VM에 연결한 다음, 다음 단계를 계속 진행하여 [VM 구성을 시작](#configure-the-hosts-file)합니다.

CentOS Linux VM을 만들어야 하거나 이 문서에서 사용할 테스트 VM을 만들려는 경우 다음 방법 중 하나를 사용할 수 있습니다.

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM을 만들 때 가상 네트워크 설정에 주의하여 VM이 관리되는 도메인과 통신할 수 있는지 확인합니다.

* Azure AD Domain Services를 사용하도록 설정한 것과 동일하거나 피어링한 가상 네트워크에 VM을 배포합니다.
* VM을 관리되는 도메인과 다른 서브넷에 배포합니다.

VM이 배포되면 단계에 따라 SSH를 사용하여 VM에 연결합니다.

## <a name="configure-the-hosts-file"></a>hosts 파일 구성

VM 호스트 이름이 관리되는 도메인에 올바르게 구성되었는지 확인하려면 */etc/hosts* 파일을 편집하고 호스트 이름을 설정합니다.

```console
sudo vi /etc/hosts
```

*hosts* 파일에서 *localhost* 주소를 업데이트합니다. 다음 예제에서는

* *aaddscontoso.com* 은 관리되는 도메인의 DNS 도메인 이름입니다.
* *centos* 는 관리되는 도메인에 조인하려는 CentOS VM의 호스트 이름입니다.

해당 이름을 고유한 값으로 업데이트합니다.

```console
127.0.0.1 centos.aaddscontoso.com centos
```

완료되면 편집기의 `:wq` 명령을 사용하여 *hosts* 파일을 저장한 후 종료합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

VM을 관리되는 도메인에 조인하려면 VM에 몇 가지 추가 패키지가 필요합니다. 추가 패키지를 설치하고 구성하려면 `yum`을 사용하여 도메인 조인 도구를 업데이트하고 설치해야 합니다.

```console
sudo yum install adcli realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>VM을 관리되는 도메인에 조인

이제 필수 패키지가 VM에 설치되었으므로 VM을 관리되는 도메인에 조인합니다.

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
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 마지막으로 `realm join` 명령을 사용하여 관리되는 도메인에 VM을 조인합니다. `contosoadmin@AADDSCONTOSO.COM`와 같이 이전의 `kinit` 명령에서 지정한 관리되는 도메인의 일부인 동일한 사용자 계정을 사용합니다.

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --membership-software=adcli
    ```

VM을 관리되는 도메인에 조인하는 데는 몇 분 정도 걸립니다. 다음의 예제 출력에서는 VM이 관리되는 도메인에 성공적으로 조인되었음을 보여 줍니다.

```output
Successfully enrolled machine in realm
```

VM이 도메인 조인 프로세스를 성공적으로 완료할 수 없는 경우 VM의 네트워크 보안 그룹에서 관리되는 도메인의 가상 네트워크 서브넷에 대한 TCP + UDP 포트 464의 아웃바운드 Kerberos 트래픽을 허용하는지 확인합니다.

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

CentOS VM에 대한 ‘AAD DC 관리자’ 그룹 관리 권한을 구성원에게 부여하려면 */etc/sudoers* 에 항목을 추가합니다. 항목이 추가되면 *AAD DC Administrators* 그룹의 구성원이 CentOS VM에서 `sudo` 명령을 사용할 수 있습니다.

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

1. 콘솔에서 새 SSH 연결을 만듭니다. `ssh -l`와 같은 명령을 사용하여 관리되는 도메인에 속한 도메인 계정(예: `contosoadmin@aaddscontoso.com`)을 사용한 다음 VM의 주소(예: *centos.aaddscontoso.com*)를 입력합니다. Azure Cloud Shell을 사용하는 경우 내부 DNS 이름보다 VM의 공용 IP 주소를 사용합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>다음 단계

VM을 관리되는 도메인에 연결하거나 도메인 계정으로 로그인하는 데 문제가 있는 경우 [도메인 조인 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
