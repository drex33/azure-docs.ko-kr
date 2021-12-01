---
title: 네이티브 Windows 클라이언트 및 Azure 방호를 사용 하는 VM에 커넥트
titleSuffix: Azure Bastion
description: 방호 및 네이티브 Windows 클라이언트를 사용 하 여 Windows 컴퓨터에서 VM에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d8146fface1997c90ea73c948381615d33dac79
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359819"
---
# <a name="connect-to-a-vm-using-bastion-and-the-native-client-on-your-windows-computer-preview"></a>Windows 컴퓨터에서 방호 및 native client를 사용 하 여 VM에 커넥트 (미리 보기)

이제 azure 방호는 Windows 워크스테이션에서 네이티브 RDP 또는 SSH 클라이언트를 사용 하 여 azure에서 대상 vm에 연결 하는 기능을 제공 합니다. 이 기능을 사용 하면 Azure CLI를 사용 하 여 방호를 통해 대상 Vm에 연결 하 고 로컬 SSH 키 쌍 및 Azure Active Directory (Azure AD)를 포함 하도록 로그인 옵션을 확장할 수 있습니다. 이 문서에서는 필요한 설정을 사용 하 여 방호를 구성한 다음 VNet의 VM에 연결 하는 방법을 설명 합니다. 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

> [!NOTE]
> 이 구성에는 Azure 방호의 표준 SKU가 필요 합니다.
>

현재이 기능에는 다음과 같은 제한 사항이 있습니다.

* Native client 지원은 아직 로컬 Linux 워크스테이션에서 사용할 수 없습니다. Linux 워크스테이션에서 대상 VM에 연결 하는 경우 Azure Portal 환경을 사용 하세요.

* 이 기능에서는 Azure Key Vault에 저장 된 SSH 개인 키를 사용 하 여 로그인을 지원 하지 않습니다. SSH 키 쌍을 사용 하 여 Linux VM에 로그인 하기 전에 로컬 컴퓨터의 파일에 개인 키를 다운로드 합니다.

## <a name="prerequisites"></a><a name="prereq"></a>필수 조건

시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 최신 버전의 CLI 명령 (버전 2.30 이상)이 설치 되어 있습니다. CLI 설치 명령에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.
* Azure 가상 네트워크
* 가상 네트워크의 가상 컴퓨터
* Azure AD 자격 증명을 사용 하 여 가상 머신에 로그인 하려면 다음 방법 중 하나를 사용 하 여 가상 머신이 설정 되었는지 확인 합니다.
    * [Windows vm](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md) 또는 [Linux vm](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)에 대해 Azure AD 로그인을 사용 하도록 설정 합니다.
    * [Windows VM을 Azure AD에 가입 하도록 구성](../active-directory/devices/concept-azure-ad-join.md)합니다.
    * [Windows VM을 하이브리드 Azure AD에 가입 하도록 구성](../active-directory/devices/concept-azure-ad-join-hybrid.md)합니다.

## <a name="configure-bastion"></a>Bastion 구성

사용자 환경에 관련 된 지침을 따르세요.

### <a name="to-modify-an-existing-bastion-host"></a>기존 요새 호스트를 수정 하려면

VNet에 대 한 방호를 이미 구성한 경우에는 다음 설정을 수정 합니다.

1. 요새 리소스에 대 한 **구성** 페이지로 이동 합니다. SKU가 **표준** 인지 확인 합니다. 그렇지 않은 경우 드롭다운에서 **표준** 으로 변경 합니다.
1. **Native Client 지원** 확인란을 선택 하 고 변경 내용을 적용 합니다.

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="기본 클라이언트 지원 확인란이 선택 된 기존 호스트를 업데이트 하기 위한 설정입니다." lightbox="./media/connect-native-client-windows/update-host-expand.png":::

### <a name="to-configure-a-new-bastion-host"></a>새 요새 호스트를 구성 하려면

아직 요새 호스트를 구성 하지 않은 경우에 [는 요새 호스트 만들기](tutorial-create-host-portal.md#createhost)를 참조 하세요. 요새 호스트를 구성 하는 경우 다음 설정을 지정 합니다.

1. **기본 사항** 탭에서 **인스턴스 세부 정보-> 계층** **을 선택 하 여 표준** SKU를 사용 하는 요새 호스트를 만듭니다.

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="표준 SKU가 선택 된 새 요새 호스트의 설정 합니다." lightbox="./media/connect-native-client-windows/standard.png":::
1. **고급** 탭에서 **Native Client 지원** 확인란을 선택 합니다.

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="Native Client 지원 확인란이 선택 된 새 요새 호스트에 대 한 설정입니다." lightbox="./media/connect-native-client-windows/new-host-expand.png":::

## <a name="verify-roles-and-ports"></a>역할 및 포트 확인

연결 하기 위해 다음 역할 및 포트가 구성 되어 있는지 확인 합니다.

### <a name="required-roles"></a>필요한 역할

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할
* Azure AD 로그인 방법을 사용 하는 경우 가상 머신 관리자 로그인 또는 가상 머신 사용자 로그인 역할 여기에 설명 된 프로세스를 사용 하 여 azure ad 로그인을 사용 하도록 설정 하는 경우에만이 작업을 수행 해야 합니다. [azure Windows vm 및 azure ad](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md) 또는 [azure Linux vm 및 azure ad](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)

### <a name="ports"></a>포트

Native client 지원을 사용 하 여 Linux VM에 연결 하려면 Linux VM에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: SSH (22) *또는*
* 인바운드 포트: 사용자 지정 값 (Azure 방호를 통해 VM에 연결할 때이 사용자 지정 포트를 지정 해야 합니다.)

native client 지원을 사용 하 여 Windows vm에 연결 하려면 Windows vm에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: RDP (3389) *또는*
* 인바운드 포트: 사용자 지정 값 (Azure 방호를 통해 VM에 연결할 때이 사용자 지정 포트를 지정 해야 합니다.)

## <a name="connect-to-a-vm"></a><a name="connect"></a>VM에 연결

이 섹션에서는 가상 머신에 연결 하는 방법을 설명 합니다. 연결 하려는 VM의 유형에 해당 하는 단계를 사용 합니다.

1. Azure 계정에 로그인 하 고, 방호 리소스가 포함 된 구독을 선택 합니다.

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

### <a name="connect-to-a-linux-vm"></a>Linux VM에 연결

1. 다음 옵션 중 하나를 사용 하 여 대상 Linux VM에 로그인 합니다.

   * Azure AD 로그인 사용 VM에 로그인 하는 경우 다음 명령을 사용 합니다. Azure AD를 사용 하 여 Azure Linux Vm에 로그인 하는 방법에 대 한 자세한 내용은 [Azure Linux vm 및 AZURE ad](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)를 참조 하세요.

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * SSH 키 쌍을 사용 하 여 로그인 하는 경우 다음 명령을 사용 합니다.

      ```azurecli-interactive
      az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```
   
   * 로컬 사용자 이름 및 암호를 사용 하 여 로그인 하는 경우 다음 명령을 사용 합니다. 그러면 대상 VM에 대 한 암호를 입력 하 라는 메시지가 표시 됩니다.

      ```azurecli-interactive
      az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "password" --username "<Username>"
      ```
> [!NOTE]
> 사용자 지정 포트 값을 지정 하려는 경우 선택 하는 로그인 명령에도--resource-port 필드를 포함 해야 합니다.
>

### <a name="connect-to-a-windows-vm"></a>Windows VM에 연결

1. 다음 명령을 사용 하 여 대상 Windows VM에 로그인 합니다. 그러면 자격 증명을 입력 하 라는 메시지가 표시 됩니다 .이 경우 로컬 사용자 이름 및 암호 또는 Azure AD 자격 증명을 제공할 수 있습니다. azure ad를 사용 하 여 azure Windows vm에 로그인 하는 방법에 대해 자세히 알아보려면 [azure Windows vm 및 azure ad](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)를 참조 하세요.

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>"
      ```
> [!NOTE]
> 사용자 지정 포트 값을 지정 하려는 경우에는 login 명령에--resource 포트 필드도 포함 해야 합니다.
>

1. 대상 VM에 로그인 하면 워크스테이션의 native client가 VM 세션 (RDP 세션의 경우 **mstsc** , ssh 세션의 경우 **ssh CLI 확장** )으로 열립니다.  SSH CLI 확장은 현재 미리 보기로 제공 됩니다.  "Az extension add--name ssh"를 실행 하 여 확장을 설치할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
