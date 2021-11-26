---
title: 네이티브 Windows 클라이언트 및 Azure Bastion 사용하여 VM에 커넥트
titleSuffix: Azure Bastion
description: Bastion 및 네이티브 Windows 클라이언트를 사용하여 Windows 컴퓨터에서 VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: e22dcfcc0fe6a6378e2cd1f7de679254e59504f0
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133110273"
---
# <a name="connect-to-a-vm-using-bastion-and-the-native-client-on-your-windows-computer-preview"></a>Windows 컴퓨터에서 Bastion 및 네이티브 클라이언트를 사용하여 VM에 커넥트(미리 보기)

이제 Azure Bastion Windows 워크스테이션에서 네이티브 RDP 또는 SSH 클라이언트를 사용하여 Azure의 대상 VM에 연결할 수 있도록 지원합니다. 이 기능을 사용하면 Azure CLI 사용하여 Bastion을 통해 대상 VM에 연결할 수 있으며, 로그인 옵션을 확장하여 로컬 SSH 키 쌍 및 azure AD(Azure Active Directory)를 포함할 수 있습니다. 이 문서는 필요한 설정을 사용하여 Bastion을 구성한 다음, VNet의 VM에 연결하는 데 도움이 됩니다. 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

> [!NOTE]
> 이 구성에는 Azure Bastion 표준 SKU가 필요합니다.
>

현재 이 기능에는 다음과 같은 제한이 있습니다.

* 네이티브 클라이언트 지원은 아직 로컬 Linux 워크스테이션에서 사용할 수 없습니다. Linux 워크스테이션에서 대상 VM에 연결하는 경우 Azure Portal 환경을 사용하세요.

* 사용자 지정 포트 또는 프로토콜을 사용하여 대상 VM에 로그인하는 것은 아직 네이티브 클라이언트 지원에서 사용할 수 없습니다. 사용자 지정 포트 또는 프로토콜을 사용하여 Bastion을 통해 대상 VM에 로그인하려면 Azure Portal 환경을 사용합니다.

* 로컬 사용자 이름 및 암호를 사용하여 대상 VM에 로그인하는 것은 아직 지원되지 않습니다. 로컬 사용자 이름 및 암호 자격 증명을 사용하여 Bastion을 통해 대상 VM에 로그인하려면 Azure Portal 환경을 사용합니다.

* Azure Key Vault 저장된 SSH 프라이빗 키를 사용하여 로그인하는 것은 이 기능에서 지원되지 않습니다. SSH 키 쌍을 사용하여 Linux VM에 로그인하기 전에 로컬 컴퓨터의 파일에 프라이빗 키를 다운로드합니다.

## <a name="prerequisites"></a><a name="prereq"></a>필수 조건

시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 최신 버전의 CLI 명령(버전 2.30 이상)이 설치되어 있습니다. CLI 설치 명령에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.
* Azure 가상 네트워크
* 가상 네트워크의 가상 머신.
* Azure AD 자격 증명을 사용하여 가상 머신에 로그인하려는 경우 다음 방법 중 하나를 사용하여 가상 머신이 설정되었는지 확인합니다.
    * [Windows VM](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md) 또는 [Linux VM에](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)대해 Azure AD 로그인을 사용하도록 설정합니다.
    * [Windows VM을 Azure AD 조인으로 구성합니다.](../active-directory/devices/concept-azure-ad-join.md)
    * [Windows VM을 하이브리드 Azure AD 조인으로 구성합니다.](../active-directory/devices/concept-azure-ad-join-hybrid.md)

## <a name="configure-bastion"></a>Bastion 구성

사용자 환경과 관련된 지침을 따릅니다.

### <a name="to-modify-an-existing-bastion-host"></a>기존 요새 호스트를 수정하려면

VNet에 대해 Bastion을 이미 구성한 경우 다음 설정을 수정합니다.

1. Bastion 리소스의 **구성** 페이지로 이동합니다. SKU가 **표준인지 확인합니다.** 그렇지 않은 경우 드롭다운에서 **표준으로** 변경합니다.
1. Native Client **지원** 확인란을 확인하고 변경 내용을 적용합니다.

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="Native Client 지원 상자가 선택된 기존 호스트를 업데이트하는 설정." lightbox="./media/connect-native-client-windows/update-host-expand.png":::

### <a name="to-configure-a-new-bastion-host"></a>새 요새 호스트를 구성하려면

배스천 호스트가 구성되지 않은 경우 [배스천 호스트 만들기를](tutorial-create-host-portal.md#createhost)참조하세요. 배스천 호스트를 구성할 때 다음 설정을 지정합니다.

1. 기본 **탭의** 인스턴스 **세부 정보 -> 계층에서** 표준을 선택하여 **표준** SKU를 사용하여 배스천 호스트를 만듭니다.

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="표준 SKU가 선택된 새 요새 호스트에 대한 설정." lightbox="./media/connect-native-client-windows/standard.png":::
1. **고급** 탭에서 Native Client **지원** 확인란을 선택합니다.

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="Native Client 지원 상자가 선택된 새 요새 호스트에 대한 설정." lightbox="./media/connect-native-client-windows/new-host-expand.png":::

## <a name="verify-roles-and-ports"></a>역할 및 포트 확인

연결하기 위해 다음 역할 및 포트가 구성되어 있는지 확인합니다.

### <a name="required-roles"></a>필요한 역할

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할
* Azure AD 로그인 방법을 사용하는 경우 Virtual Machine 관리자 로그인 또는 Virtual Machine 사용자 로그인 역할 Azure Windows VM, Azure AD 또는 Azure Linux [VM 및 Azure AD에](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md) 설명된 프로세스를 사용하여 Azure AD 로그인을 사용하도록 설정하는 경우에만 이 작업을 수행해야 [합니다.](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)

### <a name="ports"></a>포트

네이티브 클라이언트 지원을 사용하여 Linux VM에 연결하려면 Linux VM에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: SSH(22)

네이티브 클라이언트 지원을 사용하여 Windows VM에 연결하려면 Windows VM에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: RDP(3389)

사용자 지정 포트를 사용하여 대상 VM에 연결하려면 대신 Azure Portal 지침을 사용합니다.

## <a name="connect-to-a-vm"></a><a name="connect"></a>VM에 연결

이 섹션은 가상 머신에 연결하는 데 도움이 됩니다. 연결하려는 VM 유형에 해당하는 단계를 사용합니다.

1. Azure 계정에 로그인하고 Bastion 리소스가 포함된 구독을 선택합니다.

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

### <a name="connect-to-a-linux-vm"></a>Linux VM에 연결

1. 다음 옵션 중 하나를 사용하여 대상 Linux VM에 로그인합니다.

   * Azure AD 로그인 사용 VM에 로그인하는 경우 다음 명령을 사용합니다. Azure AD를 사용하여 Azure Linux VM에 로그인하는 방법에 대한 자세한 내용은 [Azure Linux VM 및 Azure AD를](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md)참조하세요.

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * SSH 키 쌍을 사용하여 로그인하는 경우 다음 명령을 사용합니다.

      ```azurecli-interactive
      az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```

### <a name="connect-to-a-windows-vm"></a>Windows VM에 연결

1. Azure AD 로그인과 함께 다음 명령을 사용하여 대상 Windows VM에 로그인합니다. Azure AD를 사용하여 Azure Windows VM에 로그인하는 방법에 대한 자세한 내용은 [Azure Windows VM 및 Azure AD를](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)참조하세요.

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>"
      ```

1. 대상 VM에 로그인하면 워크스테이션의 네이티브 클라이언트가 VM 세션(RDP 세션의 **경우 mstsc,** SSH 세션의 경우 **ssh CLI** 확장)으로 열립니다.

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
