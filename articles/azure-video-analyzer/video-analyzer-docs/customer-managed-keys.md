---
title: 고객 관리형 키 - Azure Video Analyzer
description: Azure Video Analyzer에서 고객 관리형 키(즉, Bring Your Own Key)를 사용할 수 있습니다.
author: anilmur
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: e86d2eb8883efb887a598f93d191ae9070ef2b67
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403001"
---
# <a name="customer-managed-keys-with-azure-video-analyzer"></a>Azure Video Analyzer를 사용한 고객 관리형 키

BYOK(Bring Your Own Key)는 고객이 워크로드를 클라우드로 이동할 수 있도록 도와주는 Azure 차원의 이니셔티브입니다. 고객 관리형 키를 사용하면 고객은 업계 규정을 준수하고 서비스의 테넌트 격리를 개선할 수 있습니다. 고객에게 암호화 키 제어를 제공하는 것은 불필요한 액세스 및 제어를 최소화하고 Microsoft 서비스에 대한 신뢰도를 구축하는 방법입니다.

## <a name="keys-and-key-management"></a>키 및 키 관리

모든 Video Analyzer 계정에 대한 계정 키가 만들어집니다. 기본적으로 이 계정 키는 Video Analyzer가 소유한 시스템 키(즉, 시스템 관리형 키)로 암호화됩니다. 대신 Azure Video Analyzer에서 사용자 고유의 키를 사용할 수 있습니다. 이 경우 계정 키가 사용자의 키로 암호화됩니다. 액세스 정책 및 비디오 리소스 메타데이터는 계정 키를 사용하여 암호화됩니다.

Video Analyzer는 사용자 할당 관리 ID를 사용하여 사용자가 소유한 Key Vault에서 키를 읽습니다. 비디오 분석기 계정을 만들거나 업데이트할 때 사용자 할당 관리 Id를 제공 하 고 Key Vault에 적절 한 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md) 를 할당 해야 합니다. Video Analyzer를 사용하려면 Key Vault가 계정과 동일한 지역에 있어야 하며 일시 삭제 및 제거 보호가 사용하도록 설정되어 있어야 합니다.

키는 2048, 3072 또는 4096 RSA 키일 수 있으며 HSM 및 소프트웨어 키가 모두 지원됩니다.

> [!NOTE]
> EC 키는 지원되지 않습니다.

키 이름 및 키 버전을 지정하거나 키 이름만 지정할 수 있습니다. 키 이름만 사용하는 경우 Video Analyzer는 최신 키 버전을 사용합니다. 새 버전의 고객 키가 자동으로 검색되고 계정 키가 다시 암호화됩니다.

> [!WARNING]
> Video Analyzer는 고객 키에 대한 액세스를 모니터링합니다. 고객 키에 액세스할 수 없는 경우(예: 키가 삭제되었거나 Key Vault가 삭제되었거나 액세스 권한이 제거된 경우) Video Analyzer는 계정을 고객 키에 액세스할 수 없음 상태로 전환합니다(결과적으로 계정이 사용할 수 없게 설정됨). 계정을 삭제하거나 계정 키를 복원하여 액세스를 다시 시작할 수 있습니다.

## <a name="double-encryption"></a>이중 암호화

Video Analyzer는 이중 암호화를 Azure 표준 사례로 사용하여 중요한 데이터를 보호합니다. [Azure 이중 암호화](../../security/fundamentals/double-encryption.md)를 참조하세요. 미사용 데이터의 경우 첫 번째 암호화 계층은 계정에 대한 `encryption` 설정에 따라 고객 관리형 키 또는 Microsoft 관리형 키를 사용합니다. 미사용 데이터에 대한 두 번째 암호화 계층은 별도의 Microsoft 관리형 키를 사용하여 자동으로 제공됩니다.

> [!NOTE]
> Video Analyzer 계정에서 이중 암호화가 자동으로 사용하도록 설정됩니다. 그러나 스토리지 계정에 대해 고객 관리형 키 및 이중 암호화를 구성해야 합니다. [스토리지 암호화](../../storage/common/storage-service-encryption.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

[관리 ID](managed-identity.md) 검토
