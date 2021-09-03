---
title: Azure Portal을 사용하여 GPU가 있는 Azure Stack Edge Pro에 관련된 활성화 오류 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro GPU 활성화 및 키 자격 증명 모음 관련 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/09/2021
ms.author: alkohli
ms.openlocfilehash: abb941fc84a0d4473ce03969e8c5718e8aeb41ce
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982361"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 활성화 문제 해결 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에서 활성화 문제를 해결하는 방법을 설명합니다. 


## <a name="activation-errors"></a>활성화 오류

다음 표에는 디바이스 활성화와 관련된 오류와 해당 권장 해결 방법이 요약되어 있습니다.

| 오류 메시지| 권장 해결 방법 |
|------------------------------------------------------|--------------------------------------|
| 디바이스가 활성화 키를 사용하여 활성화되기 전에 활성화에 사용되는 Azure Key Vault가 삭제되면 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | 키 자격 증명 모음이 삭제된 경우 자격 증명 모음이 제거 방지 기간에 있으면 키 자격 증명 모음을 복구할 수 있습니다. [키 자격 증명 모음 복구](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)의 단계를 수행합니다. <br>제거 방지 기간이 경과된 경우 키 자격 증명 모음을 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 디바이스가 활성화된 후 Azure Key Vault가 삭제된 경우 암호화와 관련된 작업을 수행하려고 하면(예: **사용자 추가**, **공유 추가** 또는 **컴퓨팅 구성**) 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | 키 자격 증명 모음이 삭제된 경우 자격 증명 모음이 제거 방지 기간에 있으면 키 자격 증명 모음을 복구할 수 있습니다. 키 자격 증명 모음 복구의 단계를 수행합니다. <br>제거 방지 기간이 경과된 경우 키 자격 증명 모음을 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| Azure Key Vault의 채널 무결성 키가 삭제된 경우 암호화와 관련된 작업을 수행하려고 하면(예: **사용자 추가**, **공유 추가** 또는 **컴퓨팅 구성**) 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | 키 자격 증명 모음의 채널 무결성 키가 삭제되었지만 키가 제거 기간 내에 있는 경우 [키 자격 증명 모음 키 제거 실행 취소](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)의 단계를 수행합니다. <br>제거 방지 기간이 경과되었고 키를 백업한 경우 백업에서 키를 복원할 수 있습니다. 이외의 경우에는 키를 복구할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 오류로 인해 활성화 키 생성에 실패하면 이 오류가 표시됩니다. 알림에는 추가적인 세부 정보가 포함됩니다. <br> ![키 자격 증명 모음 오류 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | 키 자격 증명 모음에 액세스할 수 있도록 [방화벽 뒤에 있는 Azure Key Vault 액세스](../key-vault/general/access-behind-firewall.md)에 지정된 포트와 URL이 방화벽에서 열려 있는지 확인합니다. 몇 분 정도 기다린 후 작업을 다시 시도합니다. 문제가 지속되면 Microsoft 지원에 문의하세요. |
| 읽기 전용 권한이 있는 사용자는 활성화 키를 생성할 수 없으며 이 오류가 표시됩니다. <br> ![키 자격 증명 모음 오류 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | 이는 적합한 액세스 권한이 없거나 *Microsoft.KeyVault* 가 등록되지 않았기 때문일 수 있습니다.<li>Azure Stack Edge 리소스에 사용되는 리소스 그룹 수준에서 소유자 또는 기여자 액세스 권한이 있는지 확인합니다.</li><li>Microsoft.KeyVault 리소스 공급자가 등록되어 있는지 확인합니다. 리소스 공급자를 등록하려면 Azure Stack Edge 리소스에 사용되는 구독으로 이동합니다. **리소스 공급자** 로 이동하고, *Microsoft.KeyVault* 를 검색하고, **등록** 을 선택합니다.</li> |

## <a name="next-steps"></a>다음 단계

- [GPU가 있는 Azure Stack Edge Pro 설치](azure-stack-edge-gpu-deploy-install.md)
