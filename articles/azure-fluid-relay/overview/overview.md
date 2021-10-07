---
title: Azure 유체 Relay 개요
description: Fluid Framework 및 Azure 유체 Relay 개요.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 08/19/2021
ms.topic: how-to
ms.service: azure-fluid
ms.openlocfilehash: 30c0e289951555a5ffcd1d0776e0b273f8573da7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663064"
---
# <a name="azure-fluid-relay-overview"></a>Azure 유체 Relay 개요

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

[Fluid Framework](https://fluidframework.com/) 는 오픈 소스 플랫폼 독립적인 프레임 워크입니다. [Azure 유체 Relay](../overview/overview.md) 는 개발자가 실시간 공동 작업 환경을 구축 하 고 연결 된 JavaScript 클라이언트에서 실시간으로 상태를 복제 하는 데 도움이 되는 Fluid Framework에 대 한 관리 되는 제품입니다.

## <a name="what-is-the-fluid-framework"></a>Fluid Framework 이란?

Fluid Framework은 공유 상태를 배포 및 동기화 하기 위한 클라이언트 라이브러리의 컬렉션입니다. 이러한 라이브러리를 사용 하면 여러 클라이언트가 로컬 데이터 작업에 사용 되는 것과 유사한 코딩 패턴을 사용 하 여 공유 데이터 구조를 동시에 만들고 작업할 수 있습니다.

[FluidFramework.com](https://fluidframework.com)에 대 한 추가 설명서

## <a name="why-fluid"></a>액량?

짧은 대기 시간을 빌드하기 때문에 공동 작업 환경은 어렵습니다.

Fluid Framework 제공:

- 사용자 지정 서버 코드를 요구 하지 않아도 되는 데이터 지 속성을 사용 하는 클라이언트 중심 응용 프로그램 모델
- 친숙 한 프로그래밍 패턴을 포함 하는 분산 데이터 구조.
- 대기 시간이 매우 짧습니다.

Microsoft 개발자는 많은 응용 프로그램에 공동 작업을 구축 했지만, 공동 작업 환경을 관리 하는 데 필요한 응용 프로그램 관련 서버 쪽 논리가 많이 필요 합니다. Fluid Framework는 공동 작업 응용 프로그램을 만드는 복잡성을 줄이도록 Microsoft의 투자에 따른 결과입니다.

서버 코드에 투자 하지 않아도 되는 경우는 어떻게 되나요? 간단 하 고 저렴 한 비용으로 설계 된 범용 서버를 사용할 수 있는 경우 Imagine 합니다. 모든 개발이 클라이언트 환경에 초점을 맞추고 데이터 동기화가 처리 되었는지 Imagine 합니다. 이것은 유체의 약속입니다.

## <a name="focused-on-the-client-developer"></a>클라이언트 개발자에 게 집중

Fluid Framework를 사용 하 여 빌드된 응용 프로그램에는 텍스트 편집기를 통해 실시간으로 입력 하는 경우와 같은 정교한 데이터 동기화 시나리오를 사용할 수 있도록 서버에서 사용자 지정 코드가 필요 하지 않습니다. 클라이언트 개발자는 데이터를 동기화 된 상태로 유지 하는 작업을 수행할 수 있도록 하는 동시에 고객 환경에 집중할 수 있습니다.

Fluid Framework는 선택한 응용 프로그램 프레임 워크와 함께 작동 합니다. 간단한 JavaScript를 선호 하 든 프레임 워크를 사용 하 든, React, Angular, vue ue 등의 프레임 워크를 선호 하 든, 공동 작업 환경을 간단 하 고 유연 하 게

## <a name="how-fluid-works"></a>유체 작동 방법

유체는 매우 성능으로 공동 작업 환경을 제공 하도록 설계 되었습니다. 이러한 목표를 달성 하기 위해 팀은 서버 논리를 최대한 간단 하 고 간단 하 게 유지 합니다. 이 접근 방식은 클라이언트에서 서버 비용이 적은 거의 즉시 동기화 하는 데 도움이 됩니다.

서버를 단순하게 유지 하기 위해 각 유체 클라이언트는 자체 상태를 담당 합니다. 이전 시스템은 서버에서 원래 상태를 유지 하지만, 유체 service는 데이터 작업을 수행 하 고, 작업을 시퀀싱 하 고, 시퀀싱 된 작업을 클라이언트에 반환 하는 작업을 담당 합니다. 각 클라이언트는 해당 시퀀스를 사용 하 여 작업을 받는 순서에 관계 없이 해당 시퀀스를 독립적으로 사용 하 여 현재 상태를 정확 하 게 생성할 수 있습니다.

다음 단계는 일반적인 흐름입니다.

1. 클라이언트 코드는 데이터를 로컬에서 변경 합니다.
1. 유체 runtime은 해당 변경 내용을 유체 service로 보냅니다.
1. 유체 서비스는 해당 작업을 순서 대로 처리 하 고 모든 클라이언트에 브로드캐스트합니다.
1. 유체 runtime은 해당 작업을 로컬 데이터에 통합 하 고 "valueChanged" 이벤트를 발생 시킵니다.
1. 클라이언트 코드는 해당 이벤트 (업데이트 보기, 비즈니스 논리 실행)를 처리 합니다.

## <a name="getting-to-version-10"></a>버전 1.0로 가져오기

Fluid Framework 코어 기술은 완벽 하 고 안정적입니다. 그러나 해당 기초를 기반으로 하는 계층은 여전히 진행 중인 작업입니다. 향후 몇 개월 동안 Api를 개발 하 고, 새로운 기능을 추가 하 고, 프레임 워크를 사용 하 여 더욱 간소화 하는 작업을 수행 합니다. 이러한 변경 내용은 내부적으로 Microsoft의 유체 사용과 현재 액량에서 작성 하는 개발자 로부터 수집 하는 요구 사항에 따라 결정 됩니다.

Fluid Framework는 프로덕션 품질 솔루션을 아직 사용할 준비가 되지 않았습니다. 하지만 개발자에 게 피드백을 통해 피드백을 보내고 직접 참여 하는 기회를 제공 하기 위해 오픈 소스를 제공 하는 것이 좋습니다.
