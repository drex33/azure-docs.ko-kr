---
title: Azure Portal을 사용하여 GPU가 있는 Azure Stack Edge Pro에 관련된 활성화 오류 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro GPU 활성화 및 키 자격 증명 모음 관련 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 1f6729fc0a723a21f66380a397a222bef23cba9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750319"
---
# <a name="troubleshoot-activation-or-secret-deletion-issues-on-azure-key-vault-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Azure Key Vault 활성화 또는 비밀 삭제 문제 해결 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에서 활성화 문제를 해결하는 방법을 설명합니다. 


## <a name="activation-errors"></a>활성화 오류

다음 표에는 디바이스 활성화와 관련된 오류와 해당 권장 해결 방법이 요약되어 있습니다.

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| 디바이스가 활성화 키를 사용하여 활성화되기 전에 활성화에 사용되는 Azure Key Vault가 삭제되면 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 키 자격 증명 모음이 삭제된 경우 자격 증명 모음이 제거 보호 기간에 있는 경우 키 자격 증명 모음을 복원할 수 있습니다. [키 자격 증명 모음 복구](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)의 단계를 수행합니다. <br>제거 보호 기간이 경과된 경우 키 자격 증명 모음 복구 블레이드를 통해 새 키 자격 증명  [모음을](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) 만들어야 합니다. |
| 디바이스가 활성화된 후 Azure Key Vault가 삭제된 경우 암호화와 관련된 작업을 수행하려고 하면(예: **사용자 추가**, **공유 추가** 또는 **컴퓨팅 구성**) 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 키 자격 증명 모음이 삭제된 경우 자격 증명 모음이 제거 보호 기간에 있는 경우 키 자격 증명 모음을 복원할 수 있습니다. [키 자격 증명 모음 복구](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)의 단계를 수행합니다. <br>제거 보호 기간이 경과된 경우 키 자격 증명 모음 복구 에 설명된 대로 새 키 자격 증명 [모음을](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)만들어야 합니다. |
| 오류로 인해 활성화 키 생성에 실패하면 이 오류가 표시됩니다. 알림에는 추가적인 세부 정보가 포함됩니다. <br> ![키 자격 증명 모음 오류 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 키 자격 증명 모음에 액세스할 수 있도록 [방화벽 뒤에 있는 Azure Key Vault 액세스](../key-vault/general/access-behind-firewall.md)에 지정된 포트와 URL이 방화벽에서 열려 있는지 확인합니다. 몇 분 정도 기다린 후 작업을 다시 시도합니다. 문제가 지속되면 Microsoft 지원에 문의하세요. |
| 읽기 전용 권한이 있는 사용자는 활성화 키를 생성할 수 없으며 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 이는 올바른 액세스 권한이 없거나 등록되지 않았기 때문일 수 `Microsoft.KeyVault` 있습니다.<li>Azure Stack Edge 리소스에 사용되는 리소스 그룹 수준에서 소유자 또는 기여자 액세스 권한이 있는지 확인합니다.</li><li>`Microsoft.KeyVault`리소스 공급자가 등록되어 있는지 확인합니다. 리소스 공급자를 등록하려면 Azure Stack Edge 리소스에 사용되는 구독으로 이동합니다. **리소스 공급자** 로 이동하고, *Microsoft.KeyVault* 를 검색하고, **등록** 을 선택합니다. 자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)을 참조하세요.</li> |


## <a name="unregistered-resource-provider-errors"></a>등록되지 않은 리소스 공급자 오류

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| Key Vault 리소스 공급자가 등록되지 않은 경우 활성화 키 생성 중에 키 자격 증명 모음을 만들 때 오류가 표시됩니다. <br> <!--![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 활성화 키가 생성되지 않습니다. <br>`Microsoft.KeyVault`리소스 공급자가 등록되어 있는지 확인합니다. 리소스 공급자를 등록하려면 Azure Stack Edge 리소스에 사용되는 구독으로 이동합니다. **리소스 공급자** 로 이동하고, *Microsoft.KeyVault* 를 검색하고, **등록** 을 선택합니다. <br>자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)을 참조하세요.</li> |
| Storage 리소스 공급자가 등록되지 않은 경우 감사 로그에 대한 스토리지 계정을 만들 때 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 이 오류는 차단 오류가 아니며 활성화 키가 생성됩니다. <br>Storage 리소스 공급자는 일반적으로 자동으로 등록되지만 그렇지 않은 경우 [리소스 공급자 등록의](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers) 단계에 따라 `Microsoft.Storage` 구독에 등록합니다.  |

## <a name="key-vault-or-secret-deletion-errors"></a>키 자격 증명 모음 또는 비밀 삭제 오류

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| Azure Key Vault 채널 무결성 키가 삭제되고 암호화와 관련된 작업(예: **사용자 추가,** **공유 추가** 또는 컴퓨팅 구성)을  수행하려고 하면 이 오류가 발생합니다.  |키 자격 증명 모음의 채널 무결성 키가 삭제되었지만 키가 제거 기간 내에 있는 경우 키 자격 증명 모음 [키 제거 실행 취소의](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)단계를 수행합니다. <br>제거 방지 기간이 경과되었고 키를 백업한 경우 백업에서 키를 복원할 수 있습니다. 이외의 경우에는 키를 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
<!--|Noopur를 사용하여 확인한 후 이를 제거합니다. 고객이 아무 것도 수행할 필요가 없습니다. 디바이스를 활성화하기 전에 Azure Key Vault 채널 무결성 키가 삭제되고 활성화 키가 다시 생성되면 이 오류가 발생합니다. <br> ![키 자격 증명 모음 오류 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png) | 채널 무결성 키가 다시 만들어지고 메타데이터가 업데이트됩니다.
|-->

## <a name="audit-logging-errors"></a>감사 로깅 오류

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| 키 자격 증명 모음에 대한 진단 설정 만들기가 실패하면 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 이는 차단 오류가 아니며 활성화 키가 생성됩니다. <br> 진단 설정을 수동으로 [만들어 감사 로그를 저장할 수 있습니다.](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs) |
| 예를 들어 지정한 이름의 계정이 이미 있으므로 스토리지 계정을 만들 수 없는 경우 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 스토리지 계정을 수동으로 만들고 키 자격 증명 모음의 진단 설정에 연결할 수 있습니다. 이 계정은 감사 로그를 저장하는 데 사용됩니다. <br> 자세한 내용은 [로그에 대한 스토리지 계정 만들기를 참조하세요.](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)  |
|Azure Stack Edge 리소스에 대해 시스템이 할당한 관리 ID가 삭제되면 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Azure Stack Edge 리소스에 대한 보안 블레이드에 경고가 표시됩니다. 키 자격 증명 모음 [복구 블레이드를 통해 새 관리 ID를 만들려면](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) 이 경고를 선택합니다.  |
| 관리 ID에 키 자격 증명 모음에 대한 액세스 권한이 없는 경우 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Azure Stack Edge 리소스에 대한 보안 블레이드에 경고가 표시됩니다. 키 자격 증명 모음 복구 블레이드를 통해 키 자격 증명 [모음에 대한 관리 ID 액세스 권한을 부여하려면](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)이 경고를 선택합니다.  |

## <a name="resource-move-errors"></a>리소스 이동 오류

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| 키 자격 증명 모음 리소스가 리소스 그룹 또는 구독 간에 이동되면 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 키 자격 증명 모음 리소스 이동은 키 자격 증명 모음 삭제와 동일한 방식으로 처리됩니다. 자격 증명 모음이 제거 보호 기간에 있는 경우 키 자격 증명 모음을 복원할 수 있습니다. 제거 보호 기간이 경과된 경우 새 키 자격 증명 모음을 만들어야 합니다. 위의 사례 중 하나에 대한 자세한 내용은 키 자격 증명 모음 [복구를 참조하세요.](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)    |
| 사용 중인 구독이 테넌트 간에 이동되면 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 관리 ID를 다시 구성하고 새 키 자격 증명 모음을 만듭니다. 키 자격 증명 모음 리소스를 이동할 수도 있습니다. 이 경우 관리 ID만 다시 구성해야 합니다. 위의 각 경우에서 키 자격 증명 [모음 복구를 참조하세요.](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)   |
| 감사 로그에 사용되는 스토리지 계정 리소스가 리소스 그룹 또는 구독 간에 이동되면 오류가 표시되지 않습니다.  | 새 [스토리지 계정을 만들고 감사 로그를 저장하도록 구성할](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)수 있습니다. |

## <a name="other-errors"></a>다른 오류

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| 키 자격 증명 모음에 대해 네트워크 제한이 구성된 경우 이 오류가 표시됩니다. <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | 이 서비스는 네트워크 제한으로 인해 키 자격 증명 모음 삭제 또는 키 자격 증명 모음에 액세스할 수 없는 시나리오를 구분할 수 없습니다. 각각의 경우 키 자격 증명 모음 [복구 블레이드로](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) 안내됩니다.     |


## <a name="next-steps"></a>다음 단계

- [GPU가 있는 Azure Stack Edge Pro 설치](azure-stack-edge-gpu-deploy-install.md)
