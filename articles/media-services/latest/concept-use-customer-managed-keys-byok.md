---
title: Bring your own key(고객 관리형 키) 가져오기
description: Media Services에서 고객 관리형 키를 사용할 수 있습니다(즉, bring your own key).
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: ce91f89e8974517d56aa7f1de55d01b9353106e7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358569"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Media Services에서 BYOK(Bring Your Own Key) 또는 고객 관리형 키 사용

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

BYOK(Bring Your Own Key)는 고객이 워크로드를 클라우드로 이동할 수 있도록 도와주는 Azure 차원의 이니셔티브입니다. 고객 관리형 키를 사용하면 고객은 업계 규정을 준수하고 서비스의 테넌트 격리를 개선할 수 있습니다. 고객에게 암호화 키 제어를 제공하는 것은 불필요한 액세스 및 제어를 최소화하고 Microsoft 서비스에 대한 신뢰도를 구축하는 방법입니다.

## <a name="keys-and-key-management"></a>키 및 키 관리

Media Services 2020-05-01 이상 API를 사용하는 경우 Media Services 사용자 고유의 키를 사용할 수 있습니다. Media Services 소유의 시스템 키로 암호화된 모든 계정에 대해 기본 계정 키가 생성됩니다. 사용자 고유의 키를 사용하는 경우 계정 키는 사용자 키로 암호화됩니다. 콘텐츠 키는 계정 키에 의해 암호화됩니다. JobInputHttp URL 및 대칭 토큰 유효성 검사 키도 암호화됩니다.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="고객 관리형 키가 시스템 관리 키를 대체합니다.":::

Media Services는 Media Services 계정의 관리 ID를 사용하여 사용자가 소유한 Key Vault에서 키를 읽습니다. Media Services를 사용하려면 Key Vault를 계정과 동일한 지역에 있고 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다.

키는 2048, 3072 또는 4096 RSA 키일 수 있으며 HSM 및 소프트웨어 키가 모두 지원됩니다.

> [!NOTE]
> EC 키는 지원되지 않습니다.

키 이름 및 키 버전을 지정하거나 키 이름만 지정할 수 있습니다. 키 이름만 사용하는 경우 Media Services는 최신 키 버전을 사용합니다. 새 버전의 고객 키가 자동으로 검색되고 계정 키가 다시 암호화됩니다.

> [!WARNING]
> Media Services는 고객 키에 대한 액세스를 모니터링합니다. 사용자 키에 액세스할 수 없는 경우(예: 키가 삭제되었거나 Key Vault가 삭제되었거나 액세스 권한이 제거된 경우) Media Services는 계정을 고객 키에 액세스할 수 없는 상태로 전환합니다(사실상 계정을 사용하지 않도록 설정함). 그러나 이 상태에서는 계정을 삭제할 수 있습니다. 유일하게 지원되는 작업은 계정 GET, LIST 및 DELETE입니다. 다른 모든 요청(인코딩, 스트리밍 등)은 계정 키에 대한 액세스가 복원될 때까지 실패합니다.

## <a name="double-encryption"></a>이중 암호화

Media Services는 자동으로 이중 암호화를 지원합니다. 미사용 데이터의 경우 첫 번째 암호화 계층은 계정에 대한 `AccountEncryption` 설정에 따라 고객 관리형 키 또는 Microsoft 관리형 키를 사용합니다.  미사용 데이터에 대한 두 번째 암호화 계층은 별도의 Microsoft 관리형 키를 사용하여 자동으로 제공됩니다. 이중 암호화에 대한 자세한 내용은 [Azure 이중 암호화](../../security/fundamentals/double-encryption.md)를 참조하세요.

> [!NOTE]
> Media Services 계정에서 이중 암호화가 자동으로 설정됩니다. 그러나 스토리지 계정에 대해 고객 관리형 키 및 이중 암호화를 구성해야 합니다. 자세한 내용은 [스토리지 암호화](../../storage/common/storage-service-encryption.md)를 참조하세요.

## <a name="tutorials"></a>자습서

- [Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용](security-customer-managed-keys-portal-tutorial.md)
- [Media Services REST API에서 고객 관리형 키 또는 BYOK를 사용](security-customer-managed-keys-rest-postman-tutorial.md)합니다.

## <a name="next-steps"></a>다음 단계

[Media Services 동적 암호화를 사용하여 콘텐츠 보호](drm-content-protection-concept.md)
