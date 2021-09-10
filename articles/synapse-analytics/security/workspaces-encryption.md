---
title: Azure Synapse Analytics 암호화
description: Azure Synapse Analytics의 암호화를 설명하는 문서
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 07/14/2021
ms.author: nanditav
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: cc57f4af28aad79b9348cbbb4e939825daba06ea
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203565"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 작업 영역에 대한 암호화

이 문서는 다음을 설명합니다.
* Synapse Analytics 작업 영역에서 미사용 데이터의 암호화
* 고객 관리형 키를 사용하여 암호화를 설정하는 Synapse 작업 영역 구성
* 작업 영역에서 데이터를 암호화하는 데 사용되는 키 관리

## <a name="encryption-of-data-at-rest"></a>미사용 데이터 암호화

완전한 Encryption-at-Rest 솔루션이 암호화되지 않은 채 남아 있는 데이터가 없도록 보장합니다. 미사용 데이터를 이중으로 암호화하면 두 개의 암호화 레이어가 위협을 완화하여 모든 단일 레이어를 손상으로부터 보호할 수 있습니다. Azure Synapse Analytics는 고객 관리형 키와 함께 작업 영역에서 데이터에 대한 두 번째 암호화 레이어를 제공합니다. 고객 관리형 키는 키의 관리 및 회전에 대한 소유권을 가져올 수 있는 [Azure Key Vault](../../key-vault/general/overview.md)에서 보호합니다.

Azure 서비스에 대한 첫 번째 암호화 레이어는 플랫폼 관리형 키를 사용하여 설정합니다. 기본적으로 Azure Storage 계정의 Azure 디스크 및 데이터는 미사용 시 자동으로 암호화됩니다. Microsoft Azure의 암호화를 사용하는 방법에 대한 자세한 정보는 [Azure 암호화 개요](../../security/fundamentals/encryption-overview.md)를 참조하세요.

## <a name="azure-synapse-encryption"></a>Azure Synapse 암호화

이 섹션에서는 Synapse 작업 영역에서 고객 관리형 키 암호화를 설정하고 적용하는 방법에 대해 알아봅니다. 해당 암호화는 기존 키 또는 Azure Key Vault에서 생성된 새로운 키를 사용합니다. 단일 키를 사용하여 작업 영역에서 모든 데이터를 암호화합니다. Synapse 작업 영역은 2048 및 3072바이트 크기의 키를 사용하는 RSA 키를 지원합니다.

> [!NOTE]
> Synapse 작업 영역은 암호화에 ECC(타원 곡선 암호화) 키 사용을 지원하지 않습니다.

다음 Synapse 구성 요소의 데이터는 작업 영역 수준에서 구성된 고객 관리형 키를 사용하여 암호화됩니다.
* SQL 풀
 * 전용 SQL 풀
 * 서버리스 SQL 풀
* Apache Spark 풀
* Azure Data Factory 통합 런타임, 파이프라인, 데이터 세트.

## <a name="workspace-encryption-configuration"></a>작업 영역 암호화 구성

작업 영역을 만들 때 고객 관리형 키를 사용하여 이중 암호화를 설정하도록 작업 영역을 구성할 수 있습니다. 새 작업 영역을 만들 때 ‘보안’ 탭에서 ‘고객 관리형 키를 사용한 이중 암호화 설정’ 옵션을 선택합니다. 키 식별자 URI를 입력하거나 **동일 하위 지역** 의 키 자격 증명 모음 목록에서 작업 영역을 선택할 수 있습니다. Key Vault 자체는 **삭제 보호를 사용하도록 설정** 해야 합니다.

> [!IMPORTANT]
> 작업 영역을 만든 후에는 이중 암호화에 대한 구성 설정을 변경할 수 없습니다.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="이 다이어그램은 고객 관리 키로 이중 암호화에 대한 작업 영역을 설정하기 위해 선택해야 하는 옵션을 보여 줍니다." lightbox="./media/workspaces-encryption/workspaces-encryption.png":::

### <a name="key-access-and-workspace-activation"></a>키 액세스 및 작업 영역 활성화

고객 관리형 키를 사용하는 Azure Synapse 암호화 모델은 필요에 따라 Azure Key Vault에서 키에 액세스하여 암호화하고 암호를 해독하는 작업 영역을 포함합니다. 액세스 정책 또는 [Azure Key Vault RBAC 액세스](../../key-vault/general/rbac-guide.md)를 통해 작업 영역에서 키에 액세스할 수 있습니다. Azure Key Vault 액세스 정책을 통해 사용 권한을 부여하는 경우, 정책을 만드는 동안 [‘애플리케이션 전용’](../../key-vault/general/security-features.md#key-vault-authentication-options) 옵션을 선택합니다(작업 영역의 관리 ID를 선택하고 권한 있는 애플리케이션으로 추가하지 않음).

 작업 영역을 활성화하려면 먼저 자격 증명 모음에 필요한 사용 권한을 작업 영역 관리 ID에 부여해야 합니다. 작업 영역 활성화에 대한 단계적 접근 방식은 작업 영역의 데이터가 고객 관리형 키를 통해 암호화되도록 합니다. 전용 SQL 풀에 대해 암호화를 사용하거나 사용하지 않도록 설정할 수 있으며, 각 풀은 기본적으로 암호화를 사용하지 않도록 설정되어 있습니다.

#### <a name="permissions"></a>사용 권한

미사용 데이터를 암호화하거나 암호를 해독하려면 작업 영역 관리 ID에 다음 사용 권한이 있어야 합니다.
* WrapKey(새 키를 만들 때 Key Vault에 키를 삽입)
* UnwrapKey(암호 해독을 위한 키 가져오기)
* Get(키의 공개된 부분 읽기)

#### <a name="workspace-activation"></a>작업 영역 활성화

작업 영역(이중 암호화를 사용하는 경우)을 만든 후 활성화가 성공할 때까지 ‘보류 중’ 상태로 유지됩니다. 모든 기능을 완전히 사용하려면 작업 영역을 활성화해야 합니다. 예를 들어, 일단 활성화가 성공하면 새 전용 SQL 풀만 만들 수 있습니다. 작업 영역에 자격 증명 모음에 대한 관리 ID 액세스 권한을 부여하고 작업 영역 Azure Portal 배너에서 활성화 링크를 클릭합니다. 활성화가 성공적으로 완료되면 고객 관리형 키로 모든 데이터를 보호할 수 있는 작업 영역을 사용할 준비가 됩니다. 앞에서 설명한 것처럼 활성화를 성공적으로 수행하려면 자격 증명 모음에 삭제 보호를 설정해야 합니다.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="이 다이어그램은 작업 영역에 대한 배너의 활성화 링크를 보여 줍니다." lightbox="./media/workspaces-encryption/workspace-activation.png":::


### <a name="manage-the-workspace-customer-managed-key"></a>고객 관리형 키 작업 영역 관리 

Azure Portal의 **암호화** 페이지에서 데이터를 암호화하는 데 사용되는 고객 관리형 키를 변경할 수 있습니다. 여기서는 키 식별자를 사용하여 새 키를 선택할 수도 있고, 동일한 하위 지역의 액세스 권한이 있는 Key Vault에서 작업 영역을 선택할 수 있습니다. 이전에 사용한 것과 다른 자격 증명 모음에서 키를 선택하는 경우, 새 자격 증명 모음에 작업 영역 관리 ID ‘Get’, ‘Wrap’, ‘Unwrap’ 권한을 부여합니다. 작업 영역은 새 자격 증명 모음에 대한 액세스의 유효성을 검사하고 작업 영역의 모든 데이터는 새 키로 다시 암호화됩니다.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="이 다이어그램은 Azure Portal의 작업 영역 암호화 섹션을 보여 줍니다." lightbox="./media/workspaces-encryption/workspace-encryption-management.png":::

>[!IMPORTANT]
>작업 영역의 암호화 키를 변경하는 경우, 작업 영역에서 새 키로 변경할 때까지 해당 키를 유지합니다. 이는 새 키로 다시 암호화되기 전에 이전 키를 사용하여 데이터의 암호를 해독할 수 있도록 하기 위한 것입니다.

키의 자동, 주기적 회전 또는 키 동작에 대한 Azure Key Vault 정책을 통해 새로운 키 버전을 만들 수 있습니다. 작업 영역의 모든 데이터를 최신 버전의 활성 키로 다시 암호화하도록 선택할 수 있습니다. 다시 암호화하기 위해서는 Azure Portal의 키를 임시 키로 변경한 다음 암호화에 사용할 키로 다시 전환합니다. 예를 들어, 동적 키인 Key1의 최신 버전을 사용하여 데이터 암호화를 업데이트하려면 고객 관리형 키 작업 영역을 임시 키인 Key2로 변경합니다. Key2를 사용하여 암호화를 마칠 때까지 기다립니다. 그런 다음, 고객 관리형 키 작업 영역을 다시 Key1로 전환하면 작업 영역의 데이터는 최신 버전의 Key1로 다시 암호화됩니다.

> [!NOTE]
> Azure Synapse Analytics는 새로운 키 버전이 만들어질 때 자동으로 데이터를 다시 암호화하는 기능을 추가하기 위해 활발하게 작업하고 있습니다. 이 기능을 사용할 수 있을 때까지 작업 영역에서 일관성 유지를 위해 위에서 설명한 프로세스대로 데이터를 다시 암호화해야 합니다.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>서비스 관리형 키를 사용한 SQL 투명한 데이터 암호화

TDE(SQL 투명한 데이터 암호화)는 이중 암호화에 사용하도록 설정 *되지 않은* 작업 영역의 전용 SQL 풀에 사용할 수 있습니다. 이와 같은 형식의 작업 영역에서 서비스 관리형 키를 사용하여 전용 SQL 풀의 데이터에 이중 암호화를 제공합니다. 서비스 관리형 키로 TDE는 전용 SQL 풀에 대해 암호화를 사용하거나 사용하지 않도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[기본 제공 Azure 정책을 사용하여 Synapse 작업 영역에 대한 암호화 보호 구현하기](../policy-reference.md)

