---
title: SSH 키 쌍을 만드는 자세한 단계
description: Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 관리하는 단계를 자세히 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cynthn
ms.openlocfilehash: 9e5d50fa52bd2c4264835499f1bc3c10bee53264
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436228"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>자세한 단계: Azure에서 Linux VM 인증을 위해 SSH 키 만들기 및 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

SSH(Secure Shell) 키 쌍을 사용하면 인증에 SSH 키를 사용하는 Linux 가상 머신을 만들 수 있습니다. 이 문서에서는 SSH 클라이언트 연결을 위해 SSH RSA 퍼블릭 및 프라이빗 키 파일 쌍을 만들고 사용하는 방법을 보여 줍니다.

빠른 명령을 원하는 경우 [Azure에서 Linux VM용 SSH 퍼블릭 및 프라이빗 키 쌍을 만드는 방법](mac-create-ssh-keys.md)을 참조하세요.

SSH 키를 만들고 사용하여 **Windows** 컴퓨터에서 Linux VM에 연결하려면 [Azure에서 Windows SSH 키를 사용하는 방법을](ssh-from-windows.md)참조하세요. [Azure Portal](../ssh-keys-portal.md)을 사용하여 포털에서 VM 생성을 위한 SSH 키를 만들고 관리할 수도 있습니다.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>SSH 키 용도 및 이점

공개 키를 지정하여 Azure VM을 만들 경우 Azure는 VM에서 공개 키(`.pub` 형식의)를 `~/.ssh/authorized_keys` 폴더에 복사합니다. `~/.ssh/authorized_keys`의 SSH 키는 SSH 연결에서 일치하는 해당 프라이빗 키를 입력하라는 챌린지를 클라이언트에 표시하는 데 사용됩니다. 인증에 SSH 키를 사용하는 Azure Linux VM에서 Azure는 암호 로그인을 허용하지 않고 SSH 키만 허용하도록 SSHD 서버를 구성합니다. SSH 키를 사용하여 Azure Linux VM을 만들면 VM 배포 보안을 유지하고 `sshd_config` 파일에서 암호를 사용하지 않도록 설정하는 일반 사후 배포 구성 단계를 줄일 수 있습니다.

SSH 키를 사용하지 않지 않더라도 Linux VM을 설정해 암호 인증을 사용할 수 있습니다. VM이 인터넷에 노출되지 않는 경우에는 암호만 사용 하는 것으로도 충분할 수 있습니다. 그렇지만 지속적으로 각 Linux VM에 대한 암호를 관리하고, 최소 암호 길이, 정기 업데이트 등 정상적 암호 정책 및 사례들을 유지 관리해야 합니다. 

## <a name="generate-keys-with-ssh-keygen"></a>ssh-keygen을 사용하여 키 생성하기

키를 만들려면 기본 설정 명령은 `ssh-keygen`로서 Azure Cloud Shell, macOS 또는 Linux 호스트 및 Windows 10에서 OpenSSH 유틸리티를 사용할 수 있습니다. `ssh-keygen`은 일련의 사항을 질문한 다음, 프라이빗 키와 일치하는 공개 키를 작성합니다. 

SSH 키는 기본적으로 `~/.ssh` 디렉터리에 보관됩니다.  `~/.ssh` 디렉터리가 없는 경우 적절한 권한이 있는 사용자가 `ssh-keygen` 명령으로 해당 디렉터리를 만듭니다. SSH 키는 리소스로 만들어지고 나중에 사용하기 위해 Azure에 저장됩니다.

> [!NOTE]
> SSH 키 [생성 및 저장에](../ssh-keys-azure-cli.md)설명된 대로 [az sshkey create](/cli/azure/sshkey#az_sshkey_create) 명령을 사용하여 [Azure CLI](/cli/azure) 키를 만들 수도 있습니다.

### <a name="basic-example"></a>기본 예제

다음 `ssh-keygen` 명령은 기본적으로 `~/.ssh` 디렉터리에 4,096비트 SSH RSA 퍼블릭 및 프라이빗 키 파일을 생성합니다. SSH 키 쌍이 현재 위치에 있으면 이러한 파일은 덮어쓰여집니다.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>자세한 예제
다음 예제에서는 SSH RSA 키 쌍을 만드는 추가 명령 옵션을 표시합니다. SSH 키 쌍이 현재 위치에 있으면 이러한 파일은 덮어쓰여집니다. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**설명된 명령**

`ssh-keygen` = 키를 만드는 데 사용한 프로그램

`-m PEM` = 키의 형식을 PEM으로 지정

`-t rsa` = 이 경우 RSA 형식으로 만들 키 유형

`-b 4096` = 이 경우 키의 비트 수는 4096

`-C "azureuser@myserver"` = 쉽게 식별할 수 있도록 공개 키 파일의 끝에 추가된 주석 일반적으로 이메일 주소는 주석으로 사용되지만 인프라에 가장 적합한 것을 사용합니다.

`-f ~/.ssh/mykeys/myprivatekey` = 기본 이름을 사용하지 않으려는 경우 프라이빗 키 파일의 파일이름입니다. `.pub`에 추가된 해당 공개 키 파일은 동일한 디렉터리에 생성됩니다. 디렉터리가 있어야 합니다.

`-N mypassphrase` = 프라이빗 키 파일에 액세스하는 데 사용된 추가 암호입니다. 

### <a name="example-of-ssh-keygen"></a>ssh-keygen 예제

```bash
ssh-keygen -t rsa -m PEM -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>저장된 키 파일

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

이 문서에 대한 키 쌍 이름. `id_rsa`라는 키 쌍을 기본값으로 가지고 일부 도구는 `id_rsa` 프라이빗 키 파일 이름을 예상하므로 하나 있는 것이 좋습니다. 디렉터리 `~/.ssh/` 은 SSH 키 쌍 및 SSH 구성 파일에 대한 기본 위치입니다. 전체 경로를 지정하지 않으면 `ssh-keygen`에서 `~/.ssh` 기본 디렉터리가 아니라 현재 작업 디렉터리에 키를 만듭니다.

#### <a name="list-of-the-ssh-directory"></a>`~/.ssh` 디렉터리의 목록

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>키 암호

`Enter passphrase (empty for no passphrase):`

프라이빗 키에 암호를 추가하는 것이 *가장* 좋습니다. 키 파일을 보호하는 암호가 없는 경우 키 파일을 소유한 모든 사용자가 해당 공개 키가 있는 모든 서버에 로그인하는 데 해당 파일을 사용할 수 있습니다. 암호를 추가하면 보호 기능을 제공합니다. 이 경우에 프라이빗 키 파일에 대한 액세스 권한을 얻을 수 있으며 키를 변경할 시간을 제공합니다.

## <a name="generate-keys-automatically-during-deployment"></a>배포 동안 키를 자동으로 생성

VM을 만들기 위해 [Azure CLI](/cli/azure)를 사용하는 경우 `--generate-ssh-keys` 옵션으로 [az vm create](/cli/azure/vm) 명령을 실행하여 SSH 공개 및 프라이빗 키 파일을 선택적으로 생성할 수 있습니다. 키는 ~/.ssh 디렉터리에 저장됩니다. 이미 해당 위치에 있는 경우 이 명령 옵션은 키를 덮어쓰지 않습니다.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>VM을 배포하는 경우 SSH 공개 키 제공

인증을 위해 SSH 키를 사용하는 Linux VM을 만들려면 Azure Portal, CLI, Resource Manager 템플릿 또는 기타 방법을 사용하여 VM을 만들 때 SSH 공개 키를 제공합니다. 포털을 사용하는 경우 공개 키 자체를 입력합니다. 기존 공개 키를 사용하여 VM을 만들기 위해 [Azure CLI](/cli/azure)을 사용하는 경우 `--ssh-key-value` 옵션으로 [az vm create](/cli/azure/vm) 명령을 실행하여 이 공개 키의 위치나 값을 지정합니다. 

SSH 공개 키의 형식을 잘 모르는 경우 다음과 같이 `cat`을 실행하여 공개 키를 확인할 수 있습니다. 이때 `~/.ssh/id_rsa.pub`를 사용자 고유의 공개 키 파일 위치로 대체합니다.

```bash
cat ~/.ssh/id_rsa.pub
```

출력은 다음과 비슷합니다(여기서 수정됨).

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Azure Portal 또는 Resource Manager 템플릿에 공개 키 파일의 내용을 복사하여 붙여넣으려는 경우 추가 공백을 복사하거나 줄 바꿈을 더 추가하면 안 됩니다. 예를 들어 macOS를 사용하는 경우 공개 키 파일(기본적으로 `~/.ssh/id_rsa.pub`)을 **pbcopy** 로 파이프하여 내용을 복사할 수 있습니다. `xclip` 등의 기타 Linux 프로그램을 통해 같은 작업을 수행할 수 있습니다.

여러 줄 형식으로 된 공개 키 사용을 선호하는 경우 이전에 만든 공개 키에서 pem 컨테이너에 RFC4716 형식 키를 생성할 수 있습니다.

기존 SSH 공개 키에서 RFC4716 형식 키를 만들려면 다음을 수행합니다.

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH 클라이언트를 통해 VM에 SSH를 사용
Azure VM에서 배포된 공개 키 및 로컬 시스템에서 배포된 프라이빗 키를 통해 IP 주소 또는 VM의 DNS 이름을 사용하여 VM에 SSH를 사용합니다. 다음 명령에서 *azureuser* 및 *myvm.westus.cloudapp.azure.com* 을 관리자 사용자 이름 및 정규화된 도메인 이름(또는 IP 주소)으로 바꿉니다.

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

키 쌍을 만들 때 암호를 제공한 경우 로그인 프로세스 중에 메시지가 표시되면 암호를 입력합니다. (서버가 `~/.ssh/known_hosts` 폴더에 추가되고 Azure VM에서 공개 키가 변경되거나 서버 이름이 `~/.ssh/known_hosts`에서 제거될 까지 다시 연결할지 묻는 메시지가 표시되지 않습니다.)

VM이 Just-In-Time 액세스 정책을 사용하는 경우에는 액세스 권한을 요청해야 VM에 연결할 수 있습니다. Just-In-Time 정책과 관련된 자세한 내용은 [Just-In-Time 정책을 사용하여 가상 머신 액세스 관리](../../security-center/security-center-just-in-time.md)를 참조하세요.

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>ssh 에이전트를 사용하여 프라이빗 키 암호 저장

모든 SSH 로그인에서 프라이빗 키 파일 암호를 입력하지 않아도 되도록 하려는 경우 `ssh-agent`를 사용하여 프라이빗 키 파일 암호를 캐시합니다. Mac을 사용하는 경우 `ssh-agent`를 호출할 때 macOS 키 집합은 프라이빗 키 암호를 안전하게 저장합니다.

`ssh-agent` 및 `ssh-add`를 확인하고 사용하여 암호를 대화형으로 사용할 필요가 없도록 SSH 시스템에 키 파일을 알려줍니다.

```bash
eval "$(ssh-agent -s)"
```

이제 `ssh-add` 명령을 사용하여 프라이빗 키를 `ssh-agent`에 추가합니다.

```bash
ssh-add ~/.ssh/id_rsa
```

프라이빗 키 암호는 이제 `ssh-agent`에 저장됩니다.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>키를 기존 VM에 복사하는 데 ssh-copy-id 사용

VM을 이미 만든 경우 `ssh-copy-id`를 사용하여 Linux VM에 새 SSH 퍼블릭 키를 추가할 수 있습니다.

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH 구성 파일 만들기 및 구성

SSH 클라이언트 동작을 최적화하고 로그인 속도를 높이기 위해 SSH 구성 파일(`~/.ssh/config`)을 만들고 구성할 수 있습니다. 

다음 예제에서는 기본 SSH 프라이빗 키를 사용하여 사용자로 특정 VM에 신속하게 로그인하는 데 사용할 수 있는 간단한 구성을 보여 줍니다. 

파일을 만듭니다.

```bash
touch ~/.ssh/config
```

파일을 편집하여 새 SSH 구성 추가

```bash
vim ~/.ssh/config
```

VM 호스트에 대해 적절한 구성 설정을 추가합니다. 이 예제에서 VM 이름은 *myvm* 이고 계정 이름은 *azureuser* 입니다.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

자체 전용된 키 쌍을 사용하기 위해 각 호스트를 사용하도록 설정하려면 추가 호스트에 대한 구성을 추가할 수 있습니다. 고급 구성 옵션은 [SSH 구성 파일](https://www.ssh.com/ssh/config/)을 참조합니다.

SSH 키 쌍 및 구성된 SSH 구성 파일이 있으므로 빠르고 안전하게 Linux VM에 로그인할 수 있습니다. 다음 명령을 실행하면 SSH는 SSH 구성 파일의 `Host myvm` 차단에서 모든 설정을 찾아 로드합니다.

```bash
ssh myvm
```

SSH 키를 사용하는 서버에 처음 로그인하면 명령은 해당 키 파일의 암호를 입력하라는 메시지를 표시합니다.

## <a name="next-steps"></a>다음 단계

다음으로 새 SSH 공개 키를 사용하여 Azure Linux VM을 만듭니다. 기본 로그인 방법(암호)을 사용하여 만든 VM보다 SSH 공개 키를 로그인으로 사용하여 만든 Azure VM의 보안성이 더 우수합니다.

* [Azure Portal을 사용하여 Linux 가상 머신 만들기](quick-create-portal.md)
* [Azure CLI를 사용하여 Linux 가상 머신 만들기](quick-create-cli.md)
* [Azure 템플릿을 사용하여 Linux VM 만들기](create-ssh-secured-vm-from-template.md)
