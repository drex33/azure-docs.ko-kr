---
title: SSH를 사용하여 Windows VM에 커넥트
titleSuffix: Azure Bastion
description: Azure Bastion 사용하여 SSH를 사용하여 Windows VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 4e08aed1b1b91f55a12f5920dfff898cd0109bae
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278598"
---
# <a name="create-an-ssh-connection-to-a-windows-vm-using-azure-bastion-preview"></a>Azure Bastion 사용하여 Windows VM에 대한 SSH 연결 만들기(미리 보기)

이 문서에서는 Azure Portal 통해 Azure 가상 네트워크에 있는 Windows VM에 대한 RDP 연결을 안전하고 원활하게 만드는 방법을 보여 줍니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. RDP를 사용하여 Windows VM에 연결할 수도 있습니다. 자세한 내용은 [Windows VM에 대한 RDP 연결 만들기를 참조하세요.](bastion-connect-vm-rdp-windows.md)

Azure Bastion은 프로비저닝된 가상 네트워크의 모든 VM에 대한 안전한 연결을 제공합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다. 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

> [!NOTE]
> Windows VM에 대한 SSH 연결을 만들려면 표준 SKU를 사용하여 Azure Bastion 구성해야 합니다. 표준 SKU는 현재 미리 보기 상태입니다.
>

SSH를 사용하여 Windows 가상 머신에 연결할 때 인증에 사용자 이름/암호 및 SSH 키를 모두 사용할 수 있습니다. 다음 중 하나를 사용하면 SSH 키를 통해 VM에 연결할 수 있습니다.

* 수동으로 입력하는 프라이빗 키
* 프라이빗 키 정보가 포함된 파일

SSH 프라이빗 키는 `"-----BEGIN RSA PRIVATE KEY-----"`로 시작하고 `"-----END RSA PRIVATE KEY-----"`로 끝나는 형식이어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

VM이 있는 가상 네트워크에 대해 Azure Bastion 호스트를 설정했는지 확인합니다. 자세한 내용은 [Azure Bastion 호스트 만들기](tutorial-create-host-portal.md)를 참조하세요. Bastion 서비스가 가상 네트워크에 프로비전되고 배포되면 이를 사용하여 이 가상 네트워크의 모든 VM에 연결할 수 있습니다. 

Windows 가상 머신에 SSH하려면 다음 사항도 확인해야 합니다.
* Windows 가상 머신이 Windows Server 2019 이상에서 실행되고 있습니다.
* Windows 가상 머신에 OpenSSH Server가 설치되어 실행되고 있습니다. 이 작업을 수행하는 방법을 알아보려면 [OpenSSH 설치를](/windows-server/administration/openssh/openssh_install_firstuse)참조하세요.
* Azure Bastion 표준 SKU를 사용하도록 구성되었습니다.

### <a name="required-roles"></a>필요한 역할

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

SSH를 통해 Windows VM에 연결하려면 VM에서 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: SSH(22) *또는*
* 인바운드 포트: 사용자 지정 값(Azure Bastion 통해 VM에 연결할 때 이 사용자 지정 포트를 지정해야 합니다.)

### <a name="supported-configurations"></a>지원되는 구성

현재 Azure Bastion **OpenSSH를** 사용하여 SSH를 통해 Windows VM에 연결하는 것만 지원합니다.

## <a name="connect-using-username-and-password"></a><a name="username"></a>연결: 사용자 이름 및 암호 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="커넥트 선택된 Azure Portal 가상 머신에 대한 개요 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::

1. Bastion을 선택한 후 **Bastion 사용을** 선택합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. Azure Bastion **사용하여 커넥트** 페이지에서 **연결 설정** 섹션을 확장하고 **SSH를** 선택합니다. 표준 SSH 포트(22)와 다른 인바운드 포트를 사용하려는 경우 **포트** 를 입력합니다.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings.png" alt-text="연결 설정을 보여주는 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings.png":::

1. 사용자 **이름** 및 **암호** 를 입력한 **다음, 커넥트** 선택하여 VM에 연결합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication.png" alt-text="암호 인증의 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/authentication.png":::

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>연결: 수동으로 프라이빗 키 입력

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="스크린샷은 커넥트 선택된 Azure Portal 가상 머신에 대한 개요를 보여줍니다." lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. Azure Bastion **사용하여 커넥트** 페이지에서 **연결 설정** 섹션을 확장하고 **SSH를** 선택합니다. 표준 SSH 포트(22)와 다른 인바운드 포트를 사용하려는 경우 **포트** 를 입력합니다.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png" alt-text="연결 설정의 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png":::

1. 사용자 **이름** 및 **SSH 프라이빗 키 를 입력합니다.** 텍스트 영역 **SSH 프라이빗 키** 에 프라이빗 키를 입력하거나 직접 붙여 넣습니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-manual.png" alt-text="SSH 키 인증의 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/authentication-manual.png":::

1. **연결** 을 선택하여 VM에 연결합니다.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>연결: 프라이빗 키 파일 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="커넥트 선택한 Azure Portal 가상 머신에 대한 개요를 스크린샷" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. Azure Bastion **사용하여 커넥트** 페이지에서 **연결 설정** 섹션을 확장하고 **SSH를** 선택합니다. 표준 SSH 포트(22)와 다른 인바운드 포트를 사용하려는 경우 **포트** 를 입력합니다.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png" alt-text="연결 설정을 보여줍니다." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png":::

1. 로컬 파일 에서 **사용자 이름** 및 **SSH 프라이빗 키를** 입력합니다. 파일을 찾은 다음, **열기** 를 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-file.png" alt-text="SSH 키 파일을 스크린샷으로 표현합니다." lightbox="./media/bastion-connect-vm-ssh-windows/authentication-file.png":::

1. **연결** 을 선택하여 VM에 연결합니다. 연결을 클릭하면 이 가상 머신에 대한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 Bastion 서비스에서 포트 443을 사용하여 HTML5를 통해 연결됩니다.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>연결: Azure Key Vault에 저장된 프라이빗 키 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="스크린샷은 커넥트 선택된 Azure Portal 가상 머신에 대한 개요입니다." lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. Azure Bastion **사용하여 커넥트** 페이지에서 **연결 설정** 섹션을 확장하고 **SSH를** 선택합니다. 표준 SSH 포트(22)와 다른 인바운드 포트를 사용하려는 경우 **포트** 를 입력합니다.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png" alt-text="연결 설정을 보여주는 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png":::

1. 사용자 **이름을** 입력하고 **Azure Key Vault SSH 프라이빗 키를** 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/ssh-key-vault.png" alt-text="Azure Key Vault SSH 프라이빗 키를 보여주는 스크린샷.":::
1. **Azure Key Vault** 드롭다운을 선택하고 SSH 프라이빗 키를 저장한 리소스를 선택합니다. 

   * Azure Key Vault 리소스를 설정하지 않은 경우 [키 자격 증명 모음 만들기](../key-vault/secrets/quick-create-powershell.md)를 참조하고 SSH 프라이빗 키를 새 키 자격 증명 비밀의 값으로 저장합니다.
   * 키 자격 증명 리소스에 저장된 비밀에 대한 **List** 및 **Get** 권한이 있는지 확인합니다. 키 자격 증명 모음 리소스에 대한 액세스 정책을 할당하고 수정하려면 [키 자격 증명 모음 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조하세요.

      >[!NOTE]
      >**PowerShell** 또는 **Azure CLI** 환경을 사용하여 SSH 프라이빗 키를 Azure Key Vault에 비밀로 저장하세요. Azure Key Vault 포털 환경을 통해 프라이빗 키를 저장하면 형식 지정을 방해하여 로그인에 실패합니다. 포털 환경을 사용하여 프라이빗 키를 비밀로 저장했고 더 이상 원본 프라이빗 키 파일에 액세스할 수 없는 경우 [SSH 키 업데이트](../virtual-machines/extensions/vmaccess.md#update-ssh-key)를 참조하여 새 SSH 키 쌍으로 대상 VM에 대한 액세스를 업데이트합니다.
      >

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/private-key-stored.png" alt-text="Azure Key Vault 보여주는 스크린샷." lightbox="./media/bastion-connect-vm-ssh-windows/private-key-stored.png":::

1. **Azure Key Vault Secret** 드롭다운을 선택하고 SSH 프라이빗 키의 값이 포함된 키 자격 증명 모음 비밀을 선택합니다.
1. **연결** 을 선택하여 VM에 연결합니다. **연결** 을 클릭하면 이 가상 머신에 대한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 Bastion 서비스에서 포트 443을 사용하여 HTML5를 통해 연결됩니다.

## <a name="next-steps"></a>다음 단계

Azure Bastion에 대한 자세한 내용은 [Bastion FAQ](bastion-faq.md)를 참조하세요.