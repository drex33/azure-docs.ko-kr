---
title: Azure 유체 Relay의 데이터 암호화
description: 유체 Relay 서버의 데이터 암호화에 대 한 이해 향상
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 3af5ba0580b0cb662c9ab87c73afac965a8c066f
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129731922"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Azure 유체 Relay의 데이터 암호화

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

Microsoft Azure 유체 Relay 서버는 [Azure Kubernetes](/azure/aks/enable-host-encryption), [Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest) 및 [Azure Blob Storage](/azure/storage/common/storage-service-encryption)의 미사용 암호화 기능을 활용 합니다. AFRS와 이러한 리소스 간의 서비스 간 통신은 TLS로 암호화 되며, 네트워크 보안 규칙에 따라 외부 간섭 으로부터 보호 되는 Azure Virtual Network 경계로 묶입니다.

아래 다이어그램에서는 Azure 유체 Relay 서버를 구현 하는 방법 및 데이터 저장소를 처리 하는 방법을 간략하게 보여 줍니다.

:::image type="content" source="../images/data-encryption.png" alt-text="Azure 유체 Relay의 데이터 저장소 다이어그램":::

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>암호화를 사용 하는 경우 Azure 유체 Relay 서버 비용은 얼마나 되나요?

미사용 암호화는 기본적으로 사용 하도록 설정 되어 있습니다. 추가 비용은 없습니다.

### <a name="who-manages-the-encryption-keys"></a>암호화 키는 누가 관리하나요?

키는 Microsoft에서 관리합니다.

### <a name="how-often-are-encryption-keys-rotated"></a>얼마나 자주 암호화 키가 순환되나요?

Microsoft에는 암호화 키 회전에 대 한 일련의 내부 지침이 있습니다. Azure 유체 Relay 서버는 다음과 같습니다. 특정 지침은 게시되지 않습니다. Microsoft는 내부 지침의 하위 집합으로 간주되고 개발자를 위한 유용한 모범 사례가 있는 [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx)을 게시합니다.

### <a name="can-i-use-my-own-encryption-keys"></a>나만의 암호화 키를 사용할 수 있나요?

아니요,이 기능은 아직 사용할 수 없습니다. 이에 대 한 추가 업데이트를 확인 하세요. 

### <a name="what-regions-have-encryption-turned-on"></a>암호화가 켜져 있는 Azure 지역은 어디인가요?

모든 Azure 유체 Relay 서버 지역에는 모든 사용자 데이터에 대 한 암호화가 설정 되어 있습니다.

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>암호화는 성능 대기 시간 및 처리량 SLA에 영향을 주나요?

A: 미사용 암호화를 사용 하는 경우 성능 Sla에 대 한 영향 또는 변경 내용이 없습니다.

## <a name="see-also"></a>참고 항목

- [Azure 유체 Relay 아키텍처 개요](architecture.md)
- [Azure 유체 Relay 토큰 계약](../how-tos/fluid-json-web-token.md)
- [앱의 인증 및 권한 부여](authentication-authorization.md)
