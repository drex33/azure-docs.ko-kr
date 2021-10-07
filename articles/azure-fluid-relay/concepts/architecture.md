---
title: Azure 유체 Relay 아키텍처 개요
description: Azure 유체 Relay 아키텍처 개요
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/overview/
ms.openlocfilehash: 6b58c90692a7d4eba404e4d5da84b175d229f343
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663017"
---
# <a name="overview-of-azure-fluid-relay-architecture"></a>Azure 유체 Relay 아키텍처 개요

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

유체를 사용 하 여 응용 프로그램을 빌드할 때 세 가지 주요 개념을 이해 해야 합니다.

- 서비스
- 컨테이너
- 공유 개체

## <a name="service"></a>서비스

유체 클라이언트에는 연결 된 모든 클라이언트가 작업을 보내고 받기 위해 사용 하는 중앙 집중식 서비스가 필요 합니다. 응용 프로그램에서 유체를 사용 하는 경우 연결 하는 기본 서비스에 해당 하는 올바른 패키지를 사용 해야 합니다.

Azure 유체 Relay 서비스의 경우이 패키지는 **@fluidframework/azure-client** 입니다. 이 패키지는 Azure 유체 Relay를 통해 Azure에서 호스트 되는 유체 컨테이너를 만들고 로드 하는 데 도움이 됩니다.

## <a name="container"></a>컨테이너

**컨테이너** 는 액량에서 캡슐화의 기본 단위입니다. 이 클래스는 공유 개체의 컬렉션과 컨테이너 내의 개체 및 컨테이너의 수명 주기를 관리 하는 Api를 지원 합니다.

새 컨테이너를 만드는 작업은 클라이언트 기반 작업 이며 컨테이너 수명은 지원 서버에 저장 된 데이터에 바인딩됩니다. 기존 컨테이너를 가져올 때 컨테이너의 이전 상태를 고려 하는 것이 중요 합니다.

컨테이너에 대 한 자세한 내용은 fluidframework.com의 [컨테이너](https://fluidframework.com/docs/build/containers/) 를 참조 하세요.

## <a name="shared-objects"></a>공유 개체

**공유 개체** 는 특정 API를 노출 하 여 공동 작업 데이터를 제공 하는 개체 유형입니다. 많은 공유 개체는 컨테이너의 컨텍스트 내에 존재할 수 있으며 정적으로 또는 동적으로 만들 수 있습니다. **DDSes (분산 데이터 구조)** 및 **DataObjects** 는 둘 다 공유 개체 유형입니다.

자세한 내용은 fluidframework.com의 [데이터 모델링](https://fluidframework.com/docs/build/data-modeling/) 을 참조 하세요.

## <a name="package-structure"></a>패키지 구조

유체를 사용 하 여 빌드할 때 두 가지 기본 **패키지** 를 사용할 수 있습니다. **유체 프레임 워크** 패키지와 서비스별 클라이언트 패키지 (예: **azure-client)**

자세한 내용은 fluidframework.com의 [패키지](https://fluidframework.com/docs/build/packages/) 를 참조 하십시오.

### <a name="the-fluid-framework-package"></a>유체 프레임 워크 패키지

**유체 프레임 워크** 패키지는 응용 프로그램을 쉽게 빌드하고 사용할 수 있게 해 주는 핵심 유체 api 모음입니다. 이 패키지에는 모든 기본 공유 개체 뿐만 아니라 모든 공통 형식 정의가 포함 되어 있습니다.

### <a name="the-fluidframeworkazure-client-package"></a>@fluidframework/azure-client 패키지

**@fluidframework/azure-client** 패키지는 유체 컨테이너를 만들고 로드 하기 위해 Azure 유체 Relay 서비스 인스턴스에 연결 하기 위한 API를 제공 합니다. 이 API를 사용 하는 방법에 대 한 자세한 내용은 [방법: Azure 유체 Relay 서비스에 커넥트을](../how-tos/connect-fluid-azure-service.md) 참조 하세요.
