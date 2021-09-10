---
title: SSH 키를 사용하여 Linux VM에 연결
description: Windows 컴퓨터에서 SSH 키를 생성하고 사용하여 Azure에서 Linux 가상 머신에 연결하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49357c5098a4e9a9dc35e3abd910a33efe6411ca
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691041"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure에서 Windows를 통해 SSH 키를 사용하는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합

이 문서는 Azure에서 *SSH*(Secure Shell) 키를 [생성](#create-an-ssh-key-pair)하고 사용하여 Linux VM(가상 머신)에 [연결](#connect-to-your-vm)하려는 Windows 사용자를 위한 것입니다. 포털에서 VM을 생성할 때 사용하기 위해 [Azure Portal에서 SSH 키를 생성하고 저장](../ssh-keys-portal.md)할 수도 있습니다.


Linux 또는 macOS 클라이언트에서 SSH 키를 사용하려면 [빠른 단계](mac-create-ssh-keys.md)를 참조하세요. SSH에 대한 자세한 개요는 [자세한 단계: Azure에서 Linux VM에 인증하기 위한 SSH 키 생성 및 관리](create-ssh-keys-detailed.md)를 참조하세요.

## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요

[SSH](https://www.ssh.com/ssh/)는 안전하지 않은 연결을 통해 안전하게 로그인할 수 있도록 암호화된 연결 프로토콜입니다. SSH는 Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH 자체에서 암호화된 연결을 제공하지만 SSH와 함께 암호를 사용하면 VM은 여전히 무차별 암호 대입 공격에 취약합니다. ‘SSH 키’라고도 하는 퍼블릭-프라이빗 키 쌍을 사용하여 SSH를 통해 VM에 연결하는 것이 좋습니다. 

퍼블릭-프라이빗 키 쌍은 현관 자물쇠와 같습니다. 자물쇠는 **퍼블릭** 형태로 공개되어 있으며, 알맞은 키를 가진 사람은 누구나 문을 열 수 있습니다. 키는 **프라이빗** 이며, 문을 여는 데 사용할 수 있기 때문에 신뢰하는 사람에게만 주어집니다. 

- ‘퍼블릭 키’는 VM을 만들 때 Linux VM에 배치됩니다. 

- *프라이빗 키* 는 로컬 시스템에 남아 있습니다. 이 프라이빗 키는 보호해야 하는 한편, 공유하면 안됩니다.

Linux VM에 연결하면 VM이 SSH 클라이언트를 테스트하여 올바른 프라이빗 키가 있는지 확인합니다. 클라이언트에 프라이빗 키가 있으면 VM에 액세스할 수 있습니다. 

조직의 보안 정책에 따라 단일 키 쌍을 재사용하여 여러 Azure VM 및 서비스에 액세스할 수 있습니다. 각 VM에 대해 별도의 키 쌍이 필요하지 않습니다. 

공개 키는 모두와 공유할 수 있지만, 사용자(또는 로컬 보안 인프라)만 프라이빗 키에 액세스할 수 있어야 합니다.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH 클라이언트

Windows 10의 최신 버전에는 SSH 키를 생성 및 사용하고 PowerShell 또는 명령 프롬프트에서 SSH 연결을 만드는 [OpenSSH 클라이언트 명령](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/)이 포함되어 있습니다. 이는 Windows 컴퓨터에서 Linux VM으로 SSH 연결을 만드는 가장 쉬운 방법입니다. 

또는 [Azure Cloud Shell](../../cloud-shell/overview.md)에서 Bash를 사용하여 VM에 연결할 수도 있습니다. [웹 브라우저](https://shell.azure.com/bash)에서, [Azure Portal](https://portal.azure.com)에서, 또는 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)을 사용하여 Visual Studio Code의 터미널로 Cloud Shell을 사용할 수 있습니다.

또한 [Linux용 Windows 하위 시스템](/windows/wsl/about)을 설치하여 SSH를 통해 VM에 연결하고 Bash 셸 내에서 다른 네이티브 Linux 도구를 사용할 수 있습니다.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

`ssh-keygen` 명령을 사용하여 SSH 키 쌍을 만듭니다. 파일 이름을 입력하거나 괄호 안에 표시된 기본값을 사용합니다(예: `C:\Users\username/.ssh/id_rsa`).  파일의 암호를 입력하거나 암호를 사용하지 않으려면 암호를 비워 두세요. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>키를 사용하여 VM 만들기

SSH 키를 사용해 인증하는 Linux VM을 만들려면 VM을 만들 때 SSH 공개 키를 제공하세요.

Azure CLI로 `az vm create`와 `--ssh-key-value` 매개 변수를 사용하여 공개 키의 경로 및 파일 이름을 지정합니다.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

PowerShell로 `New-AzVM`을 사용하여 SSH 키를 VM 구성에 추가합니다. 예를 들어 [빠른 시작: PowerShell을 사용하여 Azure에서 Linux 가상 머신 만들기](quick-create-powershell.md)를 참조하세요.

포털을 사용하여 많은 배포를 수행하는 경우 포털에서 VM을 만들 때 쉽게 선택할 수 있도록 공개 키를 Azure에 업로드하는 것이 좋습니다. 자세한 내용은 [SSH 키 업로드](../ssh-keys-portal.md#upload-an-ssh-key)를 참조하세요.


## <a name="connect-to-your-vm"></a>VM에 연결

Azure VM에서 배포된 공개 키 및 로컬 시스템에서 배포된 프라이빗 키를 통해 IP 주소 또는 VM의 DNS 이름을 사용하여 VM에 SSH를 사용합니다. 다음 명령에서 *azureuser* 및 *10.111.12.123* 을 관리자 사용자 이름, IP 주소(또는 정규화된 도메인 이름), 프라이빗 키의 경로로 바꿉니다.

```bash
ssh -i ~/.ssh/id_rsa azureuser@10.111.12.123
```

이 VM에 연결한 적이 없는 경우 호스트 지문을 확인하라는 메시지가 표시됩니다. 제공된 지문을 간단히 허용하려고 하지만, 이는 사용자를 중간에 공격 가능성이 있는 사람에게 노출됩니다. 항상 호스트 지문을 확인해야 합니다. 클라이언트에서 처음 연결할 때만 이 작업을 수행하면 됩니다. 포털을 통해 호스트 지문을 얻으려면 다음과 함께 실행 명령을 사용합니다. `ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub | awk '{print $2}'`.

:::image type="content" source="media/ssh-from-windows/run-command-validate-host-fingerprint.png" alt-text="Run 명령을 사용하여 호스트 지문의 유효성을 검사하는 방법을 보여 주는 스크린샷.":::

CLI를 사용하여 명령을 실행하려면 [`az vm run-command invoke` 명령](/cli/azure/vm/run-command)을 사용합니다.

키 쌍을 만들 때 암호를 구성한 경우 메시지가 표시되면 암호를 입력합니다.

VM이 Just-In-Time 액세스 정책을 사용하는 경우에는 액세스 권한을 요청해야 VM에 연결할 수 있습니다. Just-In-Time 정책과 관련된 자세한 내용은 [Just-In-Time 정책을 사용하여 가상 머신 액세스 관리](../../security-center/security-center-just-in-time.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- Azure Portal의 SSH 키에 대한 자세한 내용은 포털에서 VM을 만들 때 사용할 [Azure Portal의 SSH 키 생성 및 저장](../ssh-keys-portal.md)을 참조하세요.

- 자세한 단계, 옵션 및 SSH 키로 작업하는 고급 예제는 [SSH 키 쌍을 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조하세요.

- Azure Cloud Shell에서 PowerShell을 사용하여 Linux VM에 SSH 연결을 만들고 SSH 키를 생성할 수 있습니다. [PowerShell 빠른 시작](../../cloud-shell/quickstart-powershell.md#ssh)을 참조합니다.

- SSH를 사용하여 Linux VM을 연결하는 데 문제가 있으면 [Azure Linux VM에 대한 SSH 연결 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
