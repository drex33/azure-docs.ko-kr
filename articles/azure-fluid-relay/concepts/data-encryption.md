---
title: Azure Fluid Relay의 데이터 암호화
description: 유동 릴레이 서버의 데이터 암호화 이해
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 8a818ea51e1d4a3f2df03ae1c4bafcf16da35b13
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261009"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Azure Fluid Relay의 데이터 암호화

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

Microsoft Azure Fluid Relay Server는 [Azure Kubernetes](../../aks/enable-host-encryption.md), [Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest) 및 [Azure Blob Storage](../../storage/common/storage-service-encryption.md)미사용 암호화 기능을 활용합니다. AFRS와 이러한 리소스 간의 서비스 간 통신은 TLS로 암호화되며 네트워크 보안 규칙의 외부 간섭으로부터 보호되는 Azure Virtual Network 경계로 묶입니다.

아래 다이어그램은 Azure Fluid Relay Server가 구현되는 방법과 데이터 스토리지를 처리하는 방법을 간략하게 보여 줍니다.

:::image type="content" source="../images/data-encryption.png" alt-text="Azure Fluid Relay의 데이터 스토리지 다이어그램":::

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>암호화를 사용하는 경우 Azure Fluid Relay 서버의 비용은 얼마인가요?

미사용 암호화는 기본적으로 사용하도록 설정됩니다. 추가 비용은 없습니다.

### <a name="who-manages-the-encryption-keys"></a>암호화 키는 누가 관리하나요?

키는 Microsoft에서 관리합니다.

### <a name="how-often-are-encryption-keys-rotated"></a>얼마나 자주 암호화 키가 순환되나요?

Microsoft에는 Azure Fluid Relay Server가 따르는 암호화 키 회전에 대한 일련의 내부 지침이 있습니다. 특정 지침은 게시되지 않습니다. Microsoft는 내부 지침의 하위 집합으로 간주되고 개발자를 위한 유용한 모범 사례가 있는 [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx)을 게시합니다.

### <a name="can-i-use-my-own-encryption-keys"></a>나만의 암호화 키를 사용할 수 있나요?

아니요, 이 기능은 아직 사용할 수 없습니다. 이에 대한 추가 업데이트를 주시하세요. 

### <a name="what-regions-have-encryption-turned-on"></a>암호화가 켜져 있는 Azure 지역은 어디인가요?

모든 Azure Fluid Relay Server 지역에는 모든 사용자 데이터에 대해 암호화가 설정되어 있습니다.

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>암호화는 성능 대기 시간 및 처리량 SLA에 영향을 주나요?

A: 미사시 암호화를 사용하도록 설정된 성능 SLA에는 영향을 주거나 변경하지 않습니다.

## <a name="see-also"></a>추가 정보

- [Azure Fluid Relay 아키텍처 개요](architecture.md)
- [Azure Fluid Relay 토큰 계약](../how-tos/fluid-json-web-token.md)
- [앱의 인증 및 권한 부여](authentication-authorization.md)