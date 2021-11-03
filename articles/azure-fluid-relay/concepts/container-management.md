---
title: 유체 컨테이너 관리
description: Azure Fluid Relay 서비스에서 컨테이너를 관리하는 방법에 대한 개요입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 38999f706cdec3b27f41b9408c1ffcf5c689c41b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039491"
---
# <a name="managing-fluid-containers"></a>유체 컨테이너 관리

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

컨테이너는 Azure Fluid Relay 서비스의 스토리지 원자 단위이며 작업 및 스냅샷을 포함하여 유체 세션에서 저장된 데이터를 나타냅니다. 유체 런타임은 컨테이너를 사용하여 사용자가 처음으로 조인하거나 퇴사한 후 다시 조인할 때 Fluid 세션의 상태를 리하이드레이션합니다.

Fluid Framework 사용하여 애플리케이션을 빌드할 때 이 다이어그램에 요약된 대로 컨테이너 만들기 및 관리와 관련하여 고려해야 할 몇 가지 사항이 있습니다.

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="유체 서비스의 아키텍처와 개발자와 Microsoft가 소유하는 부분을 보여주는 일러스트레이션.":::

## <a name="key-concepts"></a>주요 개념

### <a name="container-permissions"></a>컨테이너 권한 

대부분의 경우 개발자는 컨테이너 및 컨테이너 권한의 인벤토리를 관리하려고 합니다. 여기에는 컨테이너에 대한 액세스 권한이 있는 사람에 대한 정보와 컨테이너의 친숙한 이름과 같은 메타데이터가 포함됩니다.

### <a name="accessing-containers"></a>컨테이너 액세스

컨테이너는 컨테이너 ID로 참조됩니다. 사용자가 컨테이너를 만들거나 열려면 먼저 Azure Fluid Relay 서비스와 통신할 때 Fluid Runtime에서 사용할 JWT를 요청해야 합니다. 유효한 JWT를 가진 모든 프로세스는 컨테이너에 액세스할 수 있습니다. 개발자는 컨테이너 액세스에 대한 JWT를 생성하여 비즈니스 논리를 제어하여 해당 시나리오에 맞게 액세스를 제어합니다. Azure Fluid Relay 서비스는 어떤 사용자가 컨테이너에 액세스할 수 있어야 하는지 알지 않습니다. 이 항목에 대한 자세한 내용은 [Azure Fluid Relay 토큰 계약을 참조하세요.](../how-tos/fluid-json-web-token.md)

> [!NOTE]
> JWT 필드 **documentID는** 유체 컨테이너 ID에 해당합니다.

### <a name="container-naming"></a>컨테이너 이름 지정

컨테이너는 컨테이너를 만들 때 Azure Fluid Relay 서비스에 의해 명명됩니다. 만들기 작업은 나중에 컨테이너를 여는 데 사용해야 하는 GUID 형식의 컨테이너 이름을 반환합니다. 대부분의 경우 개발자는 컨테이너 검색 흐름을 용이하게 하기 위해 친숙한 이름과 함께 이 컨테이너 ID GUID를 자체 데이터 저장소에 저장하려고 합니다. 

### <a name="container-discovery"></a>컨테이너 검색

개발자는 기존 컨테이너의 사용자 검색과 관련된 모든 환경 및 비즈니스 논리를 담당합니다. 이는 유체 세션의 사용자 참여, 사용자 간의 컨테이너 직접 공유 또는 기존 아티팩트 또는 프로세스에 대한 컨테이너의 프로그래밍 방식 할당에 따라 검색 가능한 컨테이너 목록의 형태를 사용할 수 있습니다.

## <a name="example-container-creation-flow"></a>컨테이너 만들기 흐름 예제

:::image type="content" source="../images/container-creation-flow.jpg" lightbox="../images/container-creation-flow-lightbox.jpg" alt-text="컨테이너 만들기 프로세스 데이터 흐름을 설명하는 다이어그램":::

이 예제에서 앱/페이지는 클라이언트 앱이 새 컨테이너를 만들 때 사용할 일반 JWT(특정 컨테이너에 바인딩되지 않음)와 함께 로드됩니다.

클라이언트 쪽 앱은 Fluid Framework API를 사용하여 Azure Fluid Relay 서비스에서 새 컨테이너를 만듭니다. 이 경우 새로 할당된 컨테이너 ID가 있는 컨테이너 개체가 생성됩니다. 컨테이너와의 추가 상호 작용에는 컨테이너 ID를 포함하는 새 JWT가 필요합니다.

클라이언트가 새 컨테이너를 만든 후에는 컨테이너와 사용자를 매핑하여 사용 권한을 관리하는 일부 시스템에 컨테이너 ID를 저장합니다. 이 시스템은 개발자가 사용자를 위해 만들려는 모든 컨테이너 검색/검색 환경을 구동합니다.

컨테이너와 상호 작용하기 전에 클라이언트는 Fluid Framework 런타임에서 Azure Fluid Relay 서비스로의 후속 호출에 사용할 컨테이너별 JWT를 요청합니다. 

## <a name="exporting-container-content"></a>컨테이너 콘텐츠 내보내기

애플리케이션이 최종 사용자가 내보내야 할 수 있는 데이터를 저장하는 경우 애플리케이션 개발자는 컨테이너에 정의된 분산 데이터 구조로 표현된 유체 컨테이너의 현재 상태를 사용하여 해당 내보내기 기능을 애플리케이션으로 빌드할 책임이 있습니다. Fluid 컨테이너에 연결하고 여는 데 대한 자세한 내용은 [컨테이너(fluidframework.com)를](https://fluidframework.com/docs/build/containers/)참조하세요. 컨트롤 플레인 API를 사용하여 컨테이너를 나열하고 삭제하는 자세한 내용은 [Microsoft Azure Fluid Relay 서버에서 유체 컨테이너 삭제를 참조하세요.](../how-tos/container-deletion.md)

## <a name="see-also"></a>참고 항목

- [Azure Fluid Relay 아키텍처 개요](architecture.md)
- [분산 데이터 구조](data-structures.md)
- [Azure Fluid Relay 토큰 계약](../how-tos/fluid-json-web-token.md)
