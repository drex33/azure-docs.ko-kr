---
title: 유체 컨테이너 관리
description: Azure 유체 Relay 서비스에서 컨테이너를 관리 하는 방법에 대 한 개요입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 0129f22a2b9a1a22af4b7418a51bde74e9b5fdf0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663066"
---
# <a name="managing-fluid-containers"></a>유체 컨테이너 관리

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

Azure 유체 Relay 서비스에서 저장소의 원자성 단위는 컨테이너입니다. 컨테이너는 작업 및 스냅숏을 포함 하 여 유체 세션에서 저장 된 데이터를 나타냅니다. 유체 런타임은 컨테이너를 사용 하 여 유체 세션의 상태를 리하이드레이션 합니다.

## <a name="accessing-containers"></a>컨테이너 액세스

컨테이너는 containerID에서 참조 됩니다. 유효한 JWT를 사용 하는 모든 프로세스는 컨테이너에 액세스할 수 있습니다. 개발자는 컨테이너 액세스를 위해 JWTs를 생성 하 여 해당 시나리오에 적합 한 액세스를 제어 하는 비즈니스 논리를 제어 합니다. Azure 유체 Relay 서비스는 컨테이너에 대 한 액세스 권한이 있는 사용자에 대해 알지 못합니다.

> [!NOTE]
> JWT 필드 **documentID** 은 유체 컨테이너 ID에 해당 합니다.

## <a name="container-naming"></a>컨테이너 이름 지정

컨테이너는 컨테이너를 만들 때 Azure 유체 Relay 서비스에 의해 이름이 지정 됩니다. **만들기** 작업은 나중에 컨테이너를 열 때 사용 해야 하는 GUID 형식의 컨테이너 이름을 반환 합니다.

## <a name="container-discovery"></a>컨테이너 검색

개발자는 기존 컨테이너의 사용자 검색과 관련 된 모든 환경 및 비즈니스 논리를 담당 합니다. 이는 사용자가 유체 세션에 참여 하거나, 사용자 간에 컨테이너를 직접 공유 하거나, 기존 아티팩트 또는 프로세스에 컨테이너를 프로그래밍 방식으로 할당 하는 방법에 따라 검색 가능한 컨테이너 목록의 형태를 사용할 수 있습니다.

## <a name="container-tracking"></a>컨테이너 추적

대부분의 경우 개발자는 컨테이너에 대 한 액세스 권한이 있는 사용자에 대 한 정보 뿐만 아니라 컨테이너의 친숙 한 이름과 같은 메타 데이터에 대 한 정보를 포함 하는 컨테이너의 인벤토리를 관리 하려고 합니다.
