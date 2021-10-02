---
title: Azure Stack Edge 리소스 및 디바이스 활성화와 Azure Key Vault 통합
description: Azure Stack Edge Pro 디바이스를 활성화하는 동안 Azure Key Vault 비밀 관리와 연결되는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 67cddd1839e666d4908706a1bbdaccbbd3565704
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351430"
---
# <a name="manage-azure-stack-edge-secrets-using-azure-key-vault"></a>Azure Key Vault 사용하여 Azure Stack Edge 비밀 관리 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault 비밀 관리를 위해 Azure Stack Edge 리소스와 통합됩니다. 이 문서에서는 디바이스를 활성화하는 동안 Azure Stack Edge 리소스에 대해 Azure Key Vault 만든 다음 비밀 관리에 사용되는 방법에 대해 자세히 설명합니다. 


## <a name="about-key-vault-and-azure-stack-edge"></a>키 자격 증명 모음 및 Azure Stack Edge 정보

Azure Key Vault 클라우드 서비스는 토큰, 암호, 인증서, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 제어하는 데 사용됩니다. 또한 Key Vault 데이터를 암호화하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있습니다. 

Azure Stack Edge 서비스의 경우 Key Vault와 통합하면 다음과 같은 이점이 제공됩니다.

- 고객 비밀을 저장합니다. Azure Stack Edge 서비스에 사용되는 비밀 중 하나는 CIK(채널 무결성 키)입니다. 이 키를 사용하면 비밀을 암호화할 수 있으며 키 자격 증명 모음에 안전하게 저장됩니다. BitLocker 복구 키 및 BMC(베이스보드 관리 컨트롤러) 사용자 암호와 같은 디바이스 비밀도 키 자격 증명 모음에 저장됩니다. 

    자세한 내용은 [비밀 및 키 안전하게 저장을 참조하세요.](../key-vault/general/overview.md#securely-store-secrets-and-keys)
- 암호화된 고객 비밀을 디바이스에 전달합니다.
- 디바이스가 다운된 경우 쉽게 액세스할 수 있도록 디바이스 비밀을 표시합니다.


## <a name="generate-activation-key-and-create-key-vault"></a>활성화 키 생성 및 키 자격 증명 모음 만들기

활성화 키 생성 프로세스 중에 Azure Stack Edge 리소스에 대한 키 자격 증명 모음이 만들어집니다. 키 자격 증명 모음은 Azure Stack Edge 리소스가 있는 동일한 리소스 그룹에 만들어집니다. 키 자격 증명 모음에 대한 기여자 권한이 필요합니다. 

### <a name="prerequisites-for-key-vault"></a>키 자격 증명 모음에 대한 필수 조건

활성화하는 동안 키 자격 증명 모음을 만들기 전에 다음 필수 구성을 충족해야 합니다.

- Azure Stack Edge 리소스를 만들기 전에 *Microsoft.KeyVault* 리소스 공급자를 등록합니다. 구독에 대한 소유자 또는 기여자 액세스 권한이 있는 경우 리소스 공급자가 자동으로 등록됩니다. 키 자격 증명 모음은 Azure Stack Edge 리소스와 동일한 구독 및 리소스 그룹에 만들어집니다. 

- Azure Stack Edge 리소스를 만들 때 리소스의 수명 동안 유지되고 클라우드의 리소스 공급자와 통신하는 시스템 할당 관리 ID도 만들어집니다. 

    관리 ID를 사용하도록 설정하면 Azure는 Azure Stack Edge 리소스에 대한 신뢰할 수 있는 ID를 만듭니다.

### <a name="key-vault-creation"></a>키 자격 증명 모음 만들기

리소스를 만든 후에는 디바이스를 사용하여 리소스를 활성화해야 합니다. 이렇게 하려면 Azure Portal 활성화 키를 생성합니다. 

활성화 키를 생성하면 다음 이벤트가 발생합니다. 

![활성화 키 생성 흐름](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-1.png)

- Azure Portal 활성화 키를 요청합니다. 그런 다음, 요청이 키 자격 증명 모음 리소스 공급자에게 전송됩니다. 
- 액세스 정책이 있는 표준 계층 키 자격 증명 모음이 만들어지고 기본적으로 잠겨 있습니다. 
    - 이 키 자격 증명 모음은 사용자가 지정한 기본 이름 또는 3~24자 길이의 사용자 지정 이름을 사용합니다. 이미 사용 중인 키 자격 증명 모음은 사용할 수 없습니다. 
    - 키 자격 증명 모음 세부 정보는 서비스에 저장됩니다. 이 키 자격 증명 모음은 비밀 관리에 사용되며 Azure Stack Edge 리소스가 있는 동안 유지됩니다. 

        ![Key Vault 키 생성 중에 생성됩니다.](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)
- 실수로 삭제되는 것을 방지하기 위해 키 자격 증명 모음에서 리소스 잠금을 사용하도록 설정됩니다. 실수로 삭제된 경우 키 자격 증명 모음을 90일 이내에 복원할 수 있도록 하는 일시 삭제도 키 자격 증명 모음에서 사용하도록 설정됩니다. 자세한 내용은 [Azure Key Vault 일시 삭제 개요](../key-vault/general/soft-delete-overview.md)를 참조하세요.
- 이제 Azure Stack Edge 리소스를 만들 때 만든 시스템 할당 관리 ID를 사용할 수 있습니다.
- CIK(채널 무결성 키)가 생성되어 키 자격 증명 모음에 배치됩니다. CIK 세부 정보가 서비스에 표시됩니다.
- ZRS(영역 중복 스토리지 계정)도 Azure Stack Edge 리소스와 동일한 범위에서 만들어지고 계정에 잠금이 배치됩니다. 
    - 이 계정은 감사 로그를 저장하는 데 사용됩니다. 
    - 스토리지 계정 만들기는 장기 실행 프로세스이며 몇 분 정도 걸립니다.
    - 스토리지 계정에는 키 자격 증명 모음 이름으로 태그가 지정됩니다.
- 진단 설정이 키 자격 증명 모음에 추가되고 로깅이 활성화됩니다. 
- 디바이스가 키 자격 증명 모음을 사용하여 비밀을 저장하고 검색하므로 관리 ID가 키 자격 증명 모음 액세스 정책에 추가되어 키 자격 증명 모음에 대한 액세스를 허용합니다. 
- 키 자격 증명 모음은 관리 ID를 사용하여 요청을 인증하여 활성화 키를 생성합니다. 활성화 키가 Azure Portal 반환됩니다. 그런 다음, 이 키를 복사하여 로컬 UI에서 사용하여 디바이스를 활성화할 수 있습니다.

> [!NOTE]
> - Azure Key Vault Azure Stack Edge 리소스와 통합되기 전에 기존 Azure Stack Edge 리소스가 있는 경우 영향을 받지 않습니다. 기존 Azure Stack Edge 리소스를 계속 사용할 수 있습니다.
> -  키 자격 증명 모음 및 스토리지 계정을 만들면 전체 리소스 비용이 추가됩니다. 허용되는 트랜잭션 및 해당 요금에 대한 자세한 내용은 [Azure Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/) 및 [Storage 계정의 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/storage/blobs/)

키 자격 증명 모음 및 디바이스 활성화와 관련된 문제가 발생하는 경우 [디바이스 활성화 문제 해결을](azure-stack-edge-gpu-troubleshoot-activation.md)참조하세요.


## <a name="view-key-vault-properties"></a>키 자격 증명 모음 속성 보기

활성화 키가 생성되고 키 자격 증명 모음이 생성된 후 키 자격 증명 모음에 액세스하여 비밀, 액세스 정책, 진단 및 인사이트를 볼 수 있습니다. 다음 절차에서는 이러한 각 작업에 대해 설명합니다.

### <a name="view-secrets"></a>비밀 보기

활성화 키가 생성되고 키 자격 증명 모음이 생성된 후 키 자격 증명 모음에 액세스하는 것이 좋습니다. 

키 자격 증명 모음에 액세스하고 비밀을 보려면 다음 단계를 수행합니다.

1. Azure Stack Edge 리소스에 대한 Azure Portal **보안으로** 이동합니다. 
1. 오른쪽 창의 **보안** 아래에서 **비밀을** 볼 수 있습니다. 
1. Azure Stack Edge 리소스와 연결된 키 자격 증명 모음으로 이동할 수도 있습니다. **키 자격 증명 모음 이름** 을 선택합니다. 

    ![디바이스 키 자격 증명 모음으로 이동](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. 키 자격 증명 모음에 저장된 비밀을 보려면 **비밀** 로 이동합니다. 채널 무결성 키, BitLocker 복구 키 및 BMC(베이스보드 관리 컨트롤러) 사용자 암호는 키 자격 증명 모음에 저장됩니다. 디바이스가 다운되면 포털에서 BitLocker 복구 키 및 BMC 사용자 암호에 쉽게 액세스할 수 있습니다.
    
    ![키 자격 증명 모음에서 디바이스 비밀 보기](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-secrets-1.png)

### <a name="view-managed-identity-access-policies"></a>관리 ID 액세스 정책 보기

키 자격 증명 모음 및 관리 ID에 대한 액세스 정책에 액세스하려면 다음 단계를 수행합니다.

1. Azure Stack Edge 리소스에 대한 Azure Portal **보안으로** 이동합니다. 
1. **Key Vault 이름에** 해당하는 링크를 선택하여 Azure Stack Edge 리소스와 연결된 키 자격 증명 모음으로 이동합니다. 

    ![디바이스 키 자격 증명 모음으로 이동](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. 키 자격 증명 모음과 연결된 액세스 정책을 보려면 **액세스 정책** 으로 이동합니다. 관리 ID에 액세스 권한이 부여된 것을 볼 수 있습니다. **비밀 권한** 을 선택합니다. 관리 ID 액세스가 **비밀의 Get** 및 **Set으로만** 제한되는 것을 볼 수 있습니다. 
    
    ![키 자격 증명 모음에 대한 액세스 정책 보기](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-access-policies-1.png)

    
### <a name="view-audit-logs"></a>감사 로그 보기

키 자격 증명 모음에 액세스하고 진단 설정 및 감사 로그를 보려면 다음 단계를 수행합니다.

1. Azure Stack Edge 리소스에 대한 Azure Portal **보안으로** 이동합니다. 
1. **Key Vault 이름에** 해당하는 링크를 선택하여 Azure Stack Edge 리소스와 연결된 키 자격 증명 모음으로 이동합니다. 

    ![디바이스 키 자격 증명 모음으로 이동](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. 키 자격 증명 모음과 연결된 진단 설정을 보려면 **진단 설정** 으로 이동합니다. 이 설정을 사용하면 키 자격 증명 모음에 액세스하는 방법과 시기 및 대상을 모니터링할 수 있습니다. 진단 설정이 만들어진 것을 볼 수 있습니다. 로그도 생성된 스토리지 계정으로 흐릅니다. 감사 이벤트는 키 자격 증명 모음에도 생성됩니다.
    
    ![키 자격 증명 모음에 대한 진단 설정 보기](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-diagnostics-settings-1.png)

키 자격 증명 모음의 로그에 대해 다른 스토리지 대상을 구성한 경우 해당 스토리지 계정에서 직접 로그를 볼 수 있습니다.

### <a name="view-insights"></a>인사이트 보기 

키 자격 증명 모음에서 수행된 작업을 포함하여 키 자격 증명 모음 인사이트에 액세스하려면 다음 단계를 수행합니다.

1. Azure Stack Edge 리소스에 대한 Azure Portal **보안으로** 이동합니다. 
1. **Key vault 진단** 에 해당 하는 링크를 선택 합니다. 

    ![장치 키 자격 증명 모음으로 이동](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

1. **Insights** 블레이드는 키 자격 증명 모음에서 수행 되는 작업의 개요를 제공 합니다.

    ![주요 자격 증명 모음에 대 한 정보 보기](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-insights-1.png) 

### <a name="view-managed-identity-status"></a>관리 id 상태 보기

Azure Stack Edge 리소스와 연결 된 시스템 할당 관리 id의 상태를 보려면 다음 단계를 수행 합니다.

1. Azure Stack에 지 리소스에 대 한 Azure Portal **보안** 으로 이동 합니다. 
1. 오른쪽 창에서 시스템 할당 관리 **id** 로 이동 하 여 시스템 할당 관리 id가 사용 하도록 설정 되었는지 여부를 확인 합니다.

    ![장치 키 자격 증명 모음으로 이동](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)

### <a name="view-key-vault-locks"></a>주요 자격 증명 모음 잠금 보기

키 자격 증명 모음에 액세스 하 고 잠금을 보려면 다음 단계를 수행 합니다.

1. Azure Stack에 지 리소스에 대 한 Azure Portal **보안** 으로 이동 합니다. 
1. **키 자격 증명 모음 이름** 에 해당 하는 링크를 선택 하 여 Azure Stack Edge 리소스와 연결 된 주요 자격 증명 모음으로 이동 합니다. 

    ![장치 키 자격 증명 모음으로 이동](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-name-1.png)
1. 키 자격 증명 모음에 대 한 잠금을 보려면 **잠금** 으로 이동 합니다. 실수로 인 한 삭제를 방지 하기 위해 키 자격 증명 모음에서 리소스 잠금이 사용 됩니다. 
    
    ![주요 자격 증명 모음에 대 한 잠금 보기](media/azure-stack-edge-gpu-activation-key-vault/view-key-vault-locks-1.png)


## <a name="regenerate-activation-key"></a>활성화 키 다시 생성

특정 인스턴스에서 활성화 키를 다시 생성 해야 할 수 있습니다. 활성화 키를 다시 생성 하면 다음과 같은 이벤트가 발생 합니다.

1. Azure Portal에서 활성화 키를 다시 생성 하도록 요청 합니다. 
1. 정품 인증 키가 Azure Portal 반환 됩니다. 그런 다음이 키를 복사 하 여 사용할 수 있습니다. 

활성화 키를 다시 생성 하면 키 자격 증명 모음에 액세스할 수 없습니다. 

## <a name="recover-device-secrets"></a>장치 비밀 복구

CIK가 실수로 삭제 되거나 키 자격 증명 모음에서 비밀 (예: BMC 사용자 암호)이 유효 하지 않은 경우, 키 자격 증명 모음 암호를 업데이트 하기 위해 장치에서 비밀을 푸시 해야 합니다. 

장치 비밀을 동기화 하려면 다음 단계를 수행 합니다.  

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **보안** 으로 이동 합니다.
1. 오른쪽 창의 맨 위 명령 모음에서 **장치 비밀 동기화** 를 선택 합니다.
1. 키 자격 증명 모음에 암호를 복원 하거나 업데이트 하기 위해 키 자격 증명 모음에 장치 비밀이 푸시됩니다. 동기화가 완료 되 면 알림이 표시 됩니다.

    ![키 자격 증명 모음에서 장치 비밀 동기화](media/azure-stack-edge-gpu-activation-key-vault/sync-device-secrets-1.png)

## <a name="delete-key-vault"></a>키 자격 증명 모음 삭제

Azure Stack Edge 리소스와 연결 된 키 자격 증명 모음을 삭제 하는 방법에는 두 가지가 있습니다.

- Azure Stack Edge 리소스를 삭제 하 고 연결 된 키 자격 증명 모음을 동시에 삭제 하도록 선택 합니다.
- 실수로 키 자격 증명 모음을 직접 삭제 했습니다.

Azure Stack Edge 리소스가 삭제 되 면 해당 리소스와 함께 키 자격 증명 모음도 삭제 됩니다. 확인하라는 메시지가 표시됩니다. 이 주요 자격 증명 모음에 다른 키를 저장 하 고이 키 자격 증명 모음을 삭제 하지 않으려는 경우 동의를 제공 하지 않도록 선택할 수 있습니다. 키 자격 증명 모음을 그대로 유지 하는 Azure Stack에 지 리소스만 삭제 됩니다. 

다음 단계를 수행 하 여 Azure Stack Edge 리소스 및 연결 된 키 자격 증명 모음을 삭제 합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **개요** 로 이동 합니다.
1. 오른쪽 창에서 **삭제** 를 선택 합니다. 이 작업은 Azure Stack Edge 리소스를 삭제 합니다.

   ![Azure Stack Edge 리소스 및 연결 된 key vault 삭제](media/azure-stack-edge-gpu-activation-key-vault/delete-azure-stack-edge-resource-1.png)  

1. 확인 블레이드가 표시 됩니다. Azure Stack Edge 리소스 이름을 입력 합니다. 연결 된 키 자격 증명 모음이 삭제 되었는지 확인 하려면 **예** 를 입력 합니다.

    ![Azure Stack Edge 리소스 및 연결 된 key vault 삭제 확인](media/azure-stack-edge-gpu-activation-key-vault/confirm-delete-azure-stack-edge-resource-1.png)   
1. **삭제** 를 선택합니다.

Azure Stack Edge 리소스와 key vault가 삭제 됩니다.

Azure Stack Edge 리소스가 사용 중일 때 키 자격 증명 모음을 실수로 삭제할 수 있습니다. 이런 경우에는 **보안** 페이지에서 Azure Stack Edge 리소스에 대 한 중요 한 알림이 발생 합니다. 이 페이지로 이동 하 여 key vault를 복구할 수 있습니다. 


## <a name="recover-key-vault"></a>키 자격 증명 모음 복구

실수로 삭제 하거나 제거 하는 경우 Azure Stack Edge 리소스와 연결 된 key vault를 복구할 수 있습니다. 이 키 자격 증명 모음을 사용 하 여 다른 키를 저장 한 경우에는 키 자격 증명 모음을 복원 하 여 해당 키를 복구 해야 합니다.

- 90 일 이내에 삭제 된 key vault를 복원할 수 있습니다.
- 90 일의 제거 보호 기간이 이미 경과 된 경우에는 key vault를 복원할 수 없습니다. 대신 새 키 자격 증명 모음을 만들어야 합니다.

90 일 이내에 다음 단계에 따라 주요 자격 증명 모음을 복구 합니다.

- Azure Portal에서 Azure Stack에 지 리소스의 **보안** 페이지로 이동 합니다. 리소스와 연결 된 키 자격 증명 모음이 삭제 된 결과에 대 한 알림이 표시 됩니다. 알림을 선택 하거나 **보안 기본 설정** 아래의 키 자격 증명 모음 이름에 대해 **다시 구성** 을 선택 하 여 key vault를 복구할 수 있습니다.

    ![보안 페이지로 이동](media/azure-stack-edge-gpu-activation-key-vault/security-page-1.png) 

- **키 자격 증명 모음 복구** 블레이드에서 **구성** 을 선택 합니다. 복구의 일부로 수행 되는 작업은 다음과 같습니다.  

    ![복구 단계](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-2.png) 

    - 키 자격 증명 모음이 동일한 이름으로 복구 되 고 키 자격 증명 모음 리소스에 잠금이 배치 됩니다. 
    
        > [!NOTE]
        > 키 자격 증명 모음을 삭제 하 고 90 일의 제거 보호 기간이 경과 되지 않은 경우 해당 기간에 키 자격 증명 모음 이름을 사용 하 여 새 key vault를 만들 수 없습니다.
    - 감사 로그를 저장 하는 저장소 계정이 만들어집니다. 
    - 시스템 할당 관리 id에는 키 자격 증명 모음에 대 한 액세스 권한이 부여 됩니다.
    - 장치 비밀이 키 자격 증명 모음에 푸시됩니다. 
    
    **구성** 을 선택합니다. 
 
    ![키 자격 증명 모음 복구 블레이드](media/azure-stack-edge-gpu-activation-key-vault/recover-key-vault-1.png)  

    키 자격 증명 모음이 복구 되며 복구가 완료 되 면 해당 효과에 대 한 알림이 표시 됩니다.

키 자격 증명 모음이 삭제 되 고 90 일의 제거 보호 기간이 경과한 경우 위에 설명 된 [키 복구 절차](#recover-key-vault) 를 통해 새 key vault를 만드는 옵션이 제공 됩니다. 이 경우 키 자격 증명 모음에 대 한 새 이름을 제공 합니다. 새 저장소 계정이 만들어지고, 관리 되는 id에이 키 자격 증명 모음에 대 한 액세스 권한이 부여 되며,이 키 자격 증명 모음에 장치 비밀이 푸시됩니다.
  
<!--To recover the key vault using the follow these steps to [Recover your key vault](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates).-->

## <a name="recover-managed-identity-access"></a>관리 id 액세스 복구

시스템 할당 관리 id 액세스 정책을 삭제 하면 장치에서 키 자격 증명 모음 비밀을 다시 동기화 할 수 없는 경우 경고가 발생 합니다. 관리 id에 키 자격 증명 모음에 대 한 액세스 권한이 없으면 장치 경고가 다시 발생 합니다. 각 경우에서 경고를 선택 하 여 **key Vault 복구 블레이드** 를 열고 다시 구성 합니다. 이 프로세스는 관리 되는 id 액세스를 복원 해야 합니다. 

![Key vault 흐름에 관리 되는 id 액세스 권한 부여](media/azure-stack-edge-gpu-activation-key-vault/activation-key-generation-flow-2.png)


## <a name="next-steps"></a>다음 단계

- [활성화 키를 생성](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)하는 방법에 대해 자세히 알아보세요.
- Azure Stack Edge 장치에서 [키 자격 증명 모음 오류 문제를 해결](azure-stack-edge-gpu-troubleshoot-activation.md) 합니다.
