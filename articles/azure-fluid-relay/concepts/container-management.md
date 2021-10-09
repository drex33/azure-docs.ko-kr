---
title: 유체 컨테이너 관리
description: Azure 유체 Relay 서비스에서 컨테이너를 관리 하는 방법에 대 한 개요입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 934122f4cb952a4be915c55c2555e336705a71c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710364"
---
# <a name="managing-fluid-containers"></a>유체 컨테이너 관리

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

컨테이너는 Azure 유체 Relay 서비스에서 저장소의 원자성 단위 이며 작업 및 스냅숏을 포함 하 여 유체 세션에서 저장 된 데이터를 나타냅니다. 유체 runtime은 컨테이너를 사용 하 여 사용자가 처음으로 조인 하거나 연결을 해제 한 후에 다시 연결할 때 유체 세션의 상태를 리하이드레이션 합니다.

Fluid Framework를 사용 하 여 응용 프로그램을 빌드할 때 컨테이너 만들기 및 관리와 관련 하 여이 다이어그램에 설명 된 것 처럼 몇 가지 사항을 고려해 야 합니다.

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="유체 service의 아키텍처와 개발자와 Microsoft가 소유 하는 파트에 대 한 그림입니다.":::

## <a name="key-concepts"></a>주요 개념

### <a name="container-permissions"></a>컨테이너 권한 

대부분의 경우 개발자는 컨테이너 및 컨테이너 권한의 인벤토리를 관리 하려고 합니다. 여기에는 컨테이너에 대 한 액세스 권한이 있는 사용자와 컨테이너의 친숙 한 이름 같은 메타 데이터에 대 한 정보가 포함 됩니다.

### <a name="accessing-containers"></a>컨테이너 액세스

컨테이너는 컨테이너 ID에서 참조 됩니다. 사용자가 컨테이너를 만들거나 열려면 먼저 유체 Runtime이 Azure 유체 Relay 서비스와 통신할 때 사용할 JWT를 요청 해야 합니다. 유효한 JWT를 사용 하는 모든 프로세스는 컨테이너에 액세스할 수 있습니다. 개발자는 컨테이너 액세스를 위해 JWTs를 생성 하 여 해당 시나리오에 적합 한 액세스를 제어 하는 비즈니스 논리를 제어 하는 책임이 있습니다. Azure 유체 Relay 서비스는 컨테이너에 대 한 액세스 권한이 있는 사용자에 대해 알지 못합니다. 이 항목에 대 한 자세한 내용은 [Azure 유체 Relay 토큰 계약](../how-tos/fluid-json-web-token.md) 을 참조 하세요.

> [!NOTE]
> JWT 필드 **documentID** 은 유체 컨테이너 ID에 해당 합니다.

### <a name="container-naming"></a>컨테이너 이름 지정

컨테이너는 컨테이너를 만들 때 Azure 유체 Relay 서비스에 의해 이름이 지정 됩니다. 만들기 작업은 나중에 컨테이너를 열 때 사용 해야 하는 GUID 형식의 컨테이너 이름을 반환 합니다. 대부분의 경우 개발자는 컨테이너 검색 흐름을 용이 하 게 하기 위해 친숙 한 이름과 함께이 컨테이너 ID GUID를 고유한 데이터 저장소에 저장 하려고 합니다. 

### <a name="container-discovery"></a>컨테이너 검색

개발자는 기존 컨테이너의 사용자 검색과 관련 된 모든 환경 및 비즈니스 논리를 담당 합니다. 이는 사용자가 유체 세션에 참여 하거나, 사용자 간에 컨테이너를 직접 공유 하거나, 기존 아티팩트 또는 프로세스에 컨테이너를 프로그래밍 방식으로 할당 하는 방법에 따라 검색 가능한 컨테이너 목록의 형태를 사용할 수 있습니다.

## <a name="see-also"></a>참고 항목

- [Azure 유체 Relay 아키텍처 개요](architecture.md)
- [분산 데이터 구조](data-structures.md)
- [Azure 유체 Relay 토큰 계약](../how-tos/fluid-json-web-token.md)
