---
title: Azure Bastion을 사용하여 Linux VM에 연결
description: Azure Bastion을 사용하여 Linux 가상 머신에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 56cbcf841d26cc7f8f26235728ef0dd1c596daa0
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289186"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure Bastion을 사용하여 Linux 가상 머신에 SSH를 통해 연결

이 문서에서는 Azure Virtual Network의 Linux VM에 SSH를 통해 안전하고 원활하게 연결하는 방법을 보여줍니다. Azure Portal에서 직접 VM에 연결할 수 있습니다. Azure Bastion을 사용하는 경우 VM에는 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. Azure Bastion에 대한 자세한 내용은 [개요](bastion-overview.md)를 참조하세요.

Azure Bastion을 사용하면 Linux 가상 머신에 SSH를 통해 연결할 수 있습니다. 인증에 사용자 이름/암호와 SSH 키를 모두 사용할 수 있습니다. 다음 중 하나를 사용하면 SSH 키를 통해 VM에 연결할 수 있습니다.

* 수동으로 입력하는 프라이빗 키
* 프라이빗 키 정보가 포함된 파일

SSH 프라이빗 키는 `"-----BEGIN RSA PRIVATE KEY-----"`로 시작하고 `"-----END RSA PRIVATE KEY-----"`로 끝나는 형식이어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

VM이 있는 가상 네트워크에 대해 Azure Bastion 호스트를 설정했는지 확인합니다. 자세한 내용은 [Azure Bastion 호스트 만들기](./tutorial-create-host-portal.md)를 참조하세요. Bastion 서비스가 가상 네트워크에 프로비전되고 배포되면 이를 사용하여 이 가상 네트워크의 모든 VM에 연결할 수 있습니다. 

Bastion을 사용하여 연결하는 경우 RDP를 사용하여 Windows VM에 연결하고 SSH를 사용하여 Linux VM에 연결한다고 가정합니다. Linux VM에 연결에 대한 자세한 내용은 [VM에 연결 - Windows](bastion-connect-vm-rdp.md)를 참조하세요.

### <a name="required-roles"></a>필요한 역할

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

SSH를 통해 Linux VM에 연결하려면 VM에 다음 포트가 열려 있어야 합니다.

* 인바운드 포트: SSH(22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>연결: 사용자 이름 및 암호 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택된 Azure Portal의 가상 머신에 대한 개요를 보여주는 스크린샷":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. **Azure Bastion을 사용하여 연결** 페이지에서 **사용자 이름** 과 **암호** 를 입력합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="암호 인증":::
1. **연결** 을 선택하여 VM에 연결합니다.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>연결: 수동으로 프라이빗 키 입력

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택된 Azure Portal의 가상 머신에 대한 개요를 보여주는 스크린샷":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. **Azure Bastion을 사용하여 연결** 페이지에서 **사용자 이름** 과 **SSH 프라이빗 키** 를 입력합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="SSH 프라이빗 키 인증":::
1. 텍스트 영역 **SSH 프라이빗 키** 에 프라이빗 키를 입력하거나 직접 붙여 넣습니다.
1. **연결** 을 선택하여 VM에 연결합니다.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>연결: 프라이빗 키 파일 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택된 Azure Portal의 가상 머신에 대한 개요를 보여주는 스크린샷":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. **Azure Bastion을 사용하여 연결** 페이지에서 **사용자 이름** 과 **로컬 파일의 SSH 프라이빗 키** 를 입력합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="SSH 프라이빗 키 파일":::

1. 파일을 찾은 다음, **열기** 를 선택합니다.
1. **연결** 을 선택하여 VM에 연결합니다. 연결을 클릭하면 이 가상 머신에 대한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 Bastion 서비스에서 포트 443을 사용하여 HTML5를 통해 연결됩니다.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>연결: Azure Key Vault에 저장된 프라이빗 키 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결하려는 가상 머신으로 이동한 다음, **연결** 을 클릭하고 드롭다운에서 **Bastion** 을 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="연결이 선택된 Azure Portal의 가상 머신에 대한 개요를 보여주는 스크린샷":::
1. Bastion을 선택한 후 **Bastion 사용** 을 클릭합니다. 가상 네트워크에 대해 Bastion을 프로비전하지 않은 경우 [Bastion 구성](./quickstart-host-portal.md)을 참조하세요.
1. **Azure Bastion을 사용하여 연결** 페이지에서 **사용자 이름** 을 입력하고 **Azure Key Vault의 SSH 프라이빗 키** 를 선택합니다.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Azure Key Vault의 SSH 프라이빗 키":::
1. **Azure Key Vault** 드롭다운을 선택하고 SSH 프라이빗 키를 저장한 리소스를 선택합니다. Azure Key Vault 리소스를 설정하지 않은 경우 [키 자격 증명 모음 만들기](../key-vault/secrets/quick-create-powershell.md)를 참조하고 SSH 프라이빗 키를 새 키 자격 증명 비밀의 값으로 저장합니다.

   >[!NOTE]
   >**PowerShell** 또는 **Azure CLI** 환경을 사용하여 SSH 프라이빗 키를 Azure Key Vault에 비밀로 저장하세요. Azure Key Vault 포털 환경을 통해 프라이빗 키를 저장하면 형식 지정을 방해하여 로그인에 실패합니다. 포털 환경을 사용하여 프라이빗 키를 비밀로 저장했고 더 이상 원본 프라이빗 키 파일에 액세스할 수 없는 경우 [SSH 키 업데이트](../virtual-machines/extensions/vmaccess.md#update-ssh-key)를 참조하여 새 SSH 키 쌍으로 대상 VM에 대한 액세스를 업데이트합니다.
   >

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   키 자격 증명 리소스에 저장된 비밀에 대한 **List** 및 **Get** 권한이 있는지 확인합니다. 키 자격 증명 모음 리소스에 대한 액세스 정책을 할당하고 수정하려면 [키 자격 증명 모음 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조하세요.
1. **Azure Key Vault Secret** 드롭다운을 선택하고 SSH 프라이빗 키의 값이 포함된 키 자격 증명 모음 비밀을 선택합니다.
3. **연결** 을 선택하여 VM에 연결합니다. **연결** 을 클릭하면 이 가상 머신에 대한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 Bastion 서비스에서 포트 443을 사용하여 HTML5를 통해 연결됩니다.

## <a name="next-steps"></a>다음 단계

Azure Bastion에 대한 자세한 내용은 [Bastion FAQ](bastion-faq.md)를 참조하세요.