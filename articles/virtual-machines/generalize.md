---
title: 이미지를 만들기 전에 VM 일반화
description: 이미지를 만들기 전에 VM을 일반화하여 머신 관련 정보를 제거했습니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: 7b733043a6b9a8e68aad94aaa57baf5c4d3fd2aa
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128915"
---
# <a name="remove-machine-specific-information-by-generalizing-a-vm-before-creating-an-image"></a>이미지를 만들기 전에 VM을 일반화하여 머신 관련 정보 제거

특별히 일반화된 이미지를 만들려는 경우가 아니면 [Shared Image Gallery](shared-image-galleries.md#generalized-and-specialized-images)에서 이미지를 만들기 위해 VM을 일반화할 필요가 없습니다. 갤러리 외부에서 관리형 이미지를 만드는 경우에는 일반화가 필요합니다.

일반화하면 머신 관련 정보가 제거되므로 이미지를 사용하여 여러 VM을 만들 수 있습니다. VM이 일반화되면 VM이 일반화되어 부팅 시퀀스를 올바르게 설정할 수 있음을 플랫폼에 알려야 합니다. VM이 일반화되면 VM을 다시 시작하면 안 됩니다.


## <a name="linux"></a>Linux

Azure VM 에이전트를 사용하여 VM의 프로비전을 해제하여 머신별 파일 및 데이터를 삭제합니다. 원본 Linux VM에서 `-deprovision+user` 매개 변수와 함께 `waagent` 명령을 사용합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](./extensions/agent-linux.md)를 참조하세요. 이 프로세스는 되돌릴 수 없습니다.

1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
2. SSH 창에서 다음 명령을 입력합니다.
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 명령이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다. 또한 `+user` 매개 변수는 마지막 프로비전된 사용자 계정을 제거합니다. VM에 사용자 계정 자격 증명을 유지하려면 `-deprovision`만 사용합니다.
 
3. 계속하려면 **y** 를 입력합니다. `-force` 매개 변수를 추가하여 이 확인 단계를 피할 수 있습니다.
4. 명령이 완료되면 **종료** 를 입력하여 SSH 클라이언트를 닫습니다.  이 시점에서 VM은 계속 실행되고 있습니다.


그런 다음, VM을 플랫폼에서 일반화됨으로 표시해야 합니다. 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## <a name="windows"></a>Windows 

Sysprep은 모든 개인 계정 및 보안 정보를 제거한 다음 이미지로 사용할 컴퓨터를 준비합니다. Sysprep에 대한 자세한 내용은 [Sysprep 개요](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조하세요.

가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) 및 [지원되지 않는 시나리오](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)를 참조하세요. 

> [!IMPORTANT]
> VM에서 Sysprep을 실행하고 나면 해당 VM은 *일반화* 된 것으로 간주되므로 다시 시작할 수 없습니다. VM 일반화 프로세스는 되돌릴 수 없습니다. 원래 VM을 작동하는 상태로 유지해야 하는 경우에는 [VM의 복사본](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm)을 만들고 복사본을 일반화해야 합니다. 
>
> Sysprep을 사용하려면 드라이브의 암호를 완전히 해독해야 합니다. VM에서 암호화를 사용하도록 설정한 경우 Sysprep을 실행하기 전에 암호화를 사용하지 않도록 설정합니다.
>
> Azure에 VHD(가상 하드 디스크)를 처음으로 업로드하기 전에 Sysprep을 실행하려는 경우 [VM을 준비](./windows/prepare-for-upload-vhd-image.md)해야 합니다.  
> 
> 

Windows VM을 일반화하려면 다음 단계를 수행합니다.

1. Windows VM에 로그인합니다.
   
2. 관리자로 명령 프롬프트 창을 엽니다. 

3. Panther 디렉터리(C:\Windows\Panther)를 삭제합니다. 디렉터리를 %windir%\system32\sysprep으로 변경한 다음, `sysprep.exe`를 실행합니다.
   
4. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 시작** 을 선택하고 **일반화** 확인란을 선택합니다.
   
5. **종료 옵션** 에서 **종료** 를 선택합니다.
   
6. **확인** 을 선택합니다.
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. Sysprep은 작업을 완료하면 VM을 종료합니다. VM을 다시 시작하지 않습니다.

> [!TIP]
> **선택적** [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options)을 사용하여 이미지를 최적화하고 VM의 첫 번째 부팅 시간을 줄입니다.
>
> 이미지를 최적화하려면 Windows 탐색기에서 VHD를 두 번 클릭하여 탑재한 다음 `/optimize-image` 매개 변수를 사용하여 DISM을 실행합니다.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> 여기서 D:는 탑재된 VHD의 경로입니다.
>
> `DISM /optimize-image`를 실행하는 경우 VHD를 마지막으로 수정해야 합니다. 배포하기 전에 VHD를 변경하는 경우 `DISM /optimize-image`를 다시 실행해야 합니다.

Sysprep이 완료되면 가상 머신의 상태를 **일반화됨** 으로 설정합니다.
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
