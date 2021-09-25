---
title: ID 모델
titleSuffix: An Azure Communication Services concept
description: ID 및 액세스 토큰에 대해 알아보기
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: e19ea511a74f95e3c3c9877226751c1933879c46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671966"
---
# <a name="identity-model"></a>ID 모델

Azure Communication Services는 ID에 구애받지 않는 서비스입니다. 이러한 설계는 다음과 같은 여러 이점을 제공합니다.

- ID 관리 시스템에서 기존 ID 재사용
- 통합 시나리오에 유연성 제공
- Azure Communication Services에서 ID를 비공개로 유지

시스템에서 정보를 복제하는 대신 비즈니스 사례에 필요한 매핑 관계를 유지 관리합니다. 예를 들어 1:1, 1: N, N:1, N:M으로 ID를 매핑할 수 있습니다. 전화 번호, 사용자, 디바이스, 애플리케이션 및 GUID와 같은 외부 식별자는 Azure Communication Services의 ID로 사용할 수 없습니다. Azure Communication Services ID용으로 생성된 액세스 토큰은 채팅 또는 통화와 같은 기본 서비스에 액세스하는 데 사용됩니다.

## <a name="identity"></a>ID

Azure Communication Services ID 라이브러리를 사용하여 ID를 만들 수 있습니다. ID는 대화에서 식별자 역할을 하며 액세스 토큰을 만드는 데 사용됩니다. 동일한 ID로 다양한 디바이스의 여러 세션에 동시에 참여할 수 있습니다. ID에 여러 활성 액세스 토큰이 동시에 있을 수 있습니다.

ID, 리소스 또는 구독을 삭제하면 모든 액세스 토큰이 무효화됩니다. 또한 이 작업은 해당 ID로 저장한 모든 데이터를 삭제합니다. 삭제된 ID는 새 액세스 토큰을 만들거나 이전에 저장된 데이터에 액세스할 수 없습니다(예: 채팅 메시지).

보유한 ID의 개수에 따라 요금이 청구되지는 않으며 그 대신 기본 서비스의 사용에 대한 요금이 청구됩니다. ID 개수로 인해 애플리케이션의 ID를 Azure Communication Services ID에 매핑하는 방식을 제한할 필요는 없습니다.

매핑의 자유에는 개인 정보 보호에 대한 책임이 따릅니다. 사용자를 시스템에서 삭제하려는 경우 해당 사용자와 연결된 모든 ID를 삭제해야 합니다.

Azure Communication Services는 익명 사용자에게 특수 ID를 제공하지 않습니다. 이는 사용자와 ID 간의 매핑을 유지하지 않으며, ID가 익명인지 여부를 확인할 수 없습니다. 사용자의 요구에 맞게 ID 개념을 디자인할 수 있습니다. 애플리케이션마다 각 익명 사용자를 위한 새 ID를 만드는 것이 좋습니다.

유효한 액세스 토큰을 보유한 사용자는 현재 ID 내용에 액세스할 수 있습니다. 예를 들어 사용자는 보낸 채팅 메시지에 액세스할 수 있습니다. 액세스는 액세스 토큰에 속하는 범위로만 제한됩니다. 자세한 내용은 이 문서의 [액세스 토큰](#access-tokens) 섹션을 참조하세요.

### <a name="identity-mapping"></a>ID 매핑

Azure Communication Services는 Azure ID 관리 시스템의 기능을 복제하지 않으며 고객이 고객과 관련된 ID를 사용할 수 있는 수단은 제공하지 않습니다. 예를 들어 고객은 전화 번호 또는 메일 주소를 사용할 수 없습니다. 그 대신 Azure Communication Services는 고유 식별자를 제공합니다. 애플리케이션의 ID에 이러한 고유 식별자를 할당할 수 있습니다. Azure Communication Services는 사용자의 실제 ID를 확인할 수 있는 어떤 종류의 정보도 저장하지 않습니다.

시스템에서 정보가 중복되지 않도록 하려면 사용자를 ID 도메인에서 Azure Communication Services ID로 매핑하는 방법을 계획합니다. 어떠 종류의 패턴을 따라도 무방합니다. 예를 들어 1:1, 1: N, N:1 또는 M:N을 사용할 수 있습니다. 단일 사용자가 단일 ID 또는 여러 ID에 매핑되는지 여부를 결정합니다.

새 ID가 만들어지면 애플리케이션의 사용자에게 해당 매핑을 저장합니다. 기본 서비스를 사용하려면 ID로 액세스 토큰을 요구해야 하므로 애플리케이션의 사용자가 ID를 알고 있어야 합니다.

관계형 데이터베이스를 사용하여 사용자 정보를 저장하는 경우 매핑 시나리오에 따라 디자인을 조정할 수 있습니다. 1:1 또는 N:1을 매핑하는 시나리오의 경우 테이블에 `CommunicationServicesId` 열을 추가하여 Azure Communication Services ID를 저장하는 것이 좋습니다. 1: N 또는 N:M 관계를 사용하는 시나리오에서는 관계형 데이터베이스에 별도의 테이블을 만들 수도 있습니다.

## <a name="access-tokens"></a>액세스 토큰

액세스 토큰은 Azure Communication Service의 기본 서비스에 액세스하는 데 사용할 수 있는 JWT(JSON Web Token)입니다. 발급된 액세스 토큰에는 무결성 보호가 적용됩니다. 즉, 발급된 후에는 해당 클레임을 변경할 수 없습니다. 따라서 ID, 만료, 범위 등의 속성을 수동으로 변경하면 액세스 토큰이 무효화됩니다. 무효화된 토큰으로 기본 서비스를 사용하는 경우 기본 서비스에 대한 액세스가 거부됩니다.

액세스 토큰의 속성은 다음과 같습니다.
* ID.
* Expiration.
* 범위.

액세스 토큰은 항상 24시간 동안 유효합니다. 만료되면 액세스 토큰이 무효화되고 기본 서비스에 액세스하는 데 사용할 수 없습니다.

ID에는 서버 쪽 서비스에서 새 액세스 토큰을 요청하는 방법이 필요합니다. *scope* 매개 변수는 사용 가능하며 비어 있지 않은 기본 서비스의 집합을 정의합니다. Azure Communication Services는 액세스 토큰에 대해 다음과 같은 범위를 지원합니다.

|이름|설명|
|---|---|
|채팅|  채팅에 참여할 수 있는 기능 부여|
|VoIP|  ID와 전화 번호를 호출하는 기능을 부여합니다.|


만료 시간 전에 액세스 토큰을 해지하려면 Azure Communication Services ID 라이브러리를 사용합니다. 토큰 해지는 즉시 이루어지지 않으며 전파하는 데 최대 15분이 걸립니다. ID, 리소스 또는 구독을 제거하면 모든 액세스 토큰이 해지됩니다.

특정 기능에 액세스하는 사용자의 권한을 제거하려면 모든 액세스 토큰을 해지합니다. 그런 다음 범위 집합이 보다 제한된 새 액세스 토큰을 발급합니다.

Azure Communication Services에서 액세스 키를 회전하면 이전 액세스 키를 사용하여 만든 모든 활성 액세스 토큰이 해지됩니다. 모든 ID는 Azure Communication Services에 대한 액세스 권한을 상실하므로 새 액세스 토큰을 발급해야 합니다.

클라이언트의 애플리케이션이 아닌 서버 쪽 서비스에서 액세스 토큰을 발급하는 것이 좋습니다. 이를 위해서는 발급에 액세스 키 또는 관리 ID가 필요합니다. 보안상의 이유로, 클라이언트 애플리케이션과 액세스 키를 공유하지 않는 것이 좋습니다.

클라이언트 애플리케이션은 클라이언트 인증이 가능한 신뢰할 수 있는 서비스 엔드포인트를 사용해야 합니다. 엔드포인트는 클라이언트 대신 액세스 토큰을 발급해야 합니다. 자세한 내용은 [클라이언트 및 서버 아키텍처](./client-and-server-architecture.md)를 참조하세요.

백업 저장소에 액세스 토큰을 캐시하는 경우 암호화를 사용하는 것이 좋습니다. 액세스 토큰은 중요한 데이터입니다. 보호되지 않는 경우 악의적인 활동에 사용될 수 있습니다. 액세스 토큰이 있는 사용자는 SDK를 시작하고 API에 액세스할 수 있습니다. 액세스 가능한 API는 액세스 토큰에 포함된 범위에 따라 제한됩니다. 필요한 범위만 포함하는 액세스 토큰을 발급하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 액세스 토큰 관리에 대한 소개는 [액세스 토큰 만들기 및 관리](../quickstarts/access-tokens.md)를 참조하세요.
* 인증에 대한 소개는 [Azure Communication Services에 인증](./authentication.md)을 참조하세요.
* 데이터 보존 및 개인 정보에 대한 소개는 [지역 가용성 및 데이터 보존](./privacy.md)을 참조하세요.
* 테스트용 ID를 빠르게 만드는 방법에 대한 자세한 내용은 [ID 빨리 만들기 빠른 시작](../quickstarts/identity/quick-create-identity.md)을 참조하세요.
