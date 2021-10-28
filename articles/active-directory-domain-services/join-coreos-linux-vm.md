---
title: Azure AD Domain Services에 CoreOS VM 가입 | Microsoft Docs
description: Azure AD Domain Services 관리되는 도메인에 CoreOS 가상 머신을 구성하고 가입하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 4de85e8cf62ed2b8e5726d2c569396bcba1cb968
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590103"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>CoreOS 가상 머신을 Azure AD Domain Services 관리되는 도메인에 가입

사용자가 단일 자격 증명 집합을 사용하여 Azure VM(가상 머신)에 로그인할 수 있도록 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 VM을 가입할 수 있습니다. Azure AD DS 관리되는 도메인에 VM을 조인하면 도메인의 사용자 계정과 자격 증명을 사용하여 서버에 로그인하고 관리할 수 있습니다. VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있도록 관리되는 도메인의 그룹 멤버 자격도 적용됩니다.

이 문서에서는 관리되는 도메인에 CoreOS VM을 가입하는 방법을 보여줍니다.

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

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>CoreOS Linux VM에 만들기 및 연결

Azure에 기존 CoreOS Linux VM이 있는 경우 Secure Shell을 사용하여 연결한 후 다음 단계로 계속 진행하여 [VM 구성을 시작](#configure-the-hosts-file)합니다.

CoreOS Linux VM을 만들거나 이 문서에서 사용할 테스트 VM을 만들려면 다음 방법 중 하나를 사용할 수 있습니다:

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
* *coreos* 는 관리되는 도메인에 가입하는 CoreOS VM의 호스트 이름입니다.

해당 이름을 고유한 값으로 바꿉니다:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

완료되면 편집기의 `:wq` 명령을 사용하여 *hosts* 파일을 저장한 후 종료합니다.

## <a name="configure-the-sssd-service"></a>SSSD 서비스 구성

*/etc/sssd/sssd.conf* SSSD 구성을 업데이트합니다.

```console
sudo vi /etc/sssd/sssd.conf
```

다음 매개 변수에 대해 고유한 관리되는 도메인 이름을 지정합니다:

* 모두 대문자인 *domains*
* *[domain/AADDSCONTOSO]* 여기서 AADDSCONTOSO는 모두 대문자입니다
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* 모두 대문자인 *krb5_realm*

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>VM을 관리되는 도메인에 조인

SSSD 구성 파일이 업데이트되면 이제 가상 머신을 관리되는 도메인에 가입합니다.

1. 먼저 `adcli info` 명령을 사용하여 관리되는 도메인에 대한 정보를 볼 수 있는지 확인합니다. 다음 예제에서는 도메인 *AADDSCONTOSO.COM* 에 대한 정보를 가져옵니다. 관리되는 도메인 이름을 모두 대문자로 지정합니다:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   `adcli info` 명령에서 관리되는 도메인을 찾을 수 없는 경우 다음의 문제 해결 단계를 검토합니다.

    * VM에서 도메인에 연결할 수 있는지 확인합니다. `ping aaddscontoso.com`을 시도하여 긍정적인 응답이 반환되는지 확인합니다.
    * VM이 관리되는 도메인을 사용할 수 있는 것과 동일하거나 피어링한 가상 네트워크에 배포되었는지 확인합니다.
    * 가상 네트워크의 DNS 서버 설정이 관리되는 도메인의 도메인 컨트롤러를 가리키도록 업데이트되었는지 확인합니다.

1. 이제 `adcli join` 명령을 사용하여 관리되는 도메인에 VM을 가입합니다. 관리되는 도메인의 일부인 사용자를 지정합니다. 필요한 경우 [Azure AD의 그룹에 사용자 계정을 추가합니다](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    다시 한번 강조하지만 관리되는 도메인 이름은 모두 대문자로 입력해야 합니다. 다음 예제에서 `contosoadmin@aaddscontoso.com` 이라는 이름의 계정은 Kerberos를 초기화하는데 사용됩니다. 관리되는 도메인의 일부인 본인의 사용자 계정을 입력합니다.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    `adcli join` 명령은 VM이 관리되는 도메인에 성공적으로 가입한 경우 정보를 반환하지 않습니다.

1. 도메인 가입 구성을 적용하려면 SSSD 서비스를 시작합니다:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용하여 VM에 로그인

VM이 관리되는 도메인에 성공적으로 조인되었는지 확인하려면 도메인 사용자 계정을 사용하여 새 SSH 연결을 시작합니다. 홈 디렉터리가 생성되었고 도메인의 그룹 멤버 자격이 적용되었는지 확인합니다.

1. 콘솔에서 새 Secure Shell 연결을 만듭니다. `contosoadmin@aaddscontoso.com`와 같은 `ssh -l` 명령을 사용하는 관리되는 도메인에 포함되는 도메인 계정을 사용한 다음 *coreos.aaddscontoso.com* 와 같은 VM 주소를 입력합니다. Azure Cloud Shell을 사용하는 경우 내부 DNS 이름보다 VM 공용 IP 주소를 사용합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. 이제 그룹 멤버 자격이 올바르게 확인되는지 확인합니다:

    ```console
    id
    ```

    관리되는 도메인의 그룹 멤버 자격이 표시되어야 합니다.

## <a name="next-steps"></a>다음 단계

VM을 관리되는 도메인에 연결하거나 도메인 계정으로 로그인하는 데 문제가 있는 경우 [도메인 조인 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
