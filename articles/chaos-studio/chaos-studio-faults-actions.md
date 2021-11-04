---
title: Azure Chaos Studio의 오류 및 작업
description: Azure Chaos Studio의 오류 및 작업을 이해합니다. 오류와 동작의 차이점은 무엇인가요? 오류를 정의하려면 어떻게 해야 할까요?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c4db4525c3535b0dda7bd8a21c46509f1203b748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102770"
---
# <a name="faults-and-actions-in-azure-chaos-studio"></a>Azure Chaos Studio의 오류 및 작업

Chaos Studio에서 실험의 일부로 발생하는 모든 활동을 **작업이라고** 하며 가장 일반적인 작업 유형은 **오류입니다.** 이 문서에서는 작업 및 오류와 각 작업의 속성을 설명합니다.

## <a name="experiment-actions"></a>실험 작업

동작은 비정상 사고 실험의 일부로 오케스트레이션되는 모든 활동입니다. 작업은 단계 및 분기로 구성되어 작업을 순차적으로 또는 병렬로 실행할 수 있습니다. 모든 작업에는 다음과 같은 속성이 있습니다.
* **이름:** 수행되는 특정 작업입니다. 이름은 일반적으로 작업에 대한 URN 형식을 취합니다(예: 'urn:
* **유형:** 작업이 실행되는 방식입니다. 작업은 *연속일* 수 있습니다. 즉, 작업이 일정 기간 동안 논스톱으로 실행(예: 10분 동안 CPU 압력 적용) *불연속* 는 작업이 한 번만 발생한다는 것을 의미합니다(예: Redis Cache 인스턴스 다시 부팅).

## <a name="types-of-actions"></a>작업 유형

Chaos Studio에는 두 가지 종류의 작업이 있습니다.
- **오류** - 이 작업을 수행하면 하나 이상의 리소스가 중단됩니다.
- **시간 지연** - 이 작업은 리소스에 영향을 주지 않고 "대기"합니다. 시스템 이전 오류의 영향을 받을 때까지 대기 하는 오류 사이 일시 중지 하는 데 유용 합니다.

## <a name="faults"></a>오류

오류는 Chaos Studio에서 가장 일반적인 작업입니다. 오류는 시스템에서 중단을 발생시켜 가용성에 영향을 주지 않고 시스템이 해당 중단을 효과적으로 처리하는지 확인할 수 있도록 합니다. 오류는 파괴적이거나(예: 프로세스 삭제), 압력 적용(예: 가상 메모리 압력 추가), 대기 시간 추가 또는 구성 변경이 발생할 수 있습니다. 이름 및 형식 외에도 오류에는 *지속 시간(* 연속인 경우) 및 *매개 변수* 가 있을 수 있습니다. 매개 변수는 오류를 적용하고 오류 이름과 관련되는 방법을 설명합니다. 예를 들어 Cosmos DB 장애 조치(failover) 오류에 대한 매개 변수는 쓰기 지역 실패 시 쓰기 지역으로 승격되는 읽기 지역입니다. 일부 매개 변수는 필수이지만 다른 매개 변수는 선택 사항입니다.

오류는 대상 유형에 *따라 에이전트 기반* 또는 서비스 *직접입니다.* 에이전트 기반 오류를 사용하려면 가상 머신 또는 가상 머신 확장 집합에 Chaos Studio 에이전트를 설치해야 합니다. 에이전트는 Windows 및 Linux 모두에 사용할 수 있지만 모든 오류를 두 운영 체제에서 모두 사용할 수 있는 것은 아닙니다. 각 운영 체제에서 지원되는 오류에 대한 자세한 내용은 오류 [라이브러리를](chaos-studio-fault-library.md) 참조하세요. 서비스 직접 오류에는 에이전트가 필요하지 않습니다. Azure 리소스에 대해 직접 실행됩니다.

오류에는 오류가 실행될 리소스를 설명하는 선택기의 이름도 포함됩니다. [비정상 상황에서의 실험에 대한 문서에서](chaos-studio-chaos-experiments.md)선택기에 대해 자세히 알아볼 수 있습니다. 오류는 리소스가 대상으로 온보딩되고 리소스에서 해당 오류 기능을 사용하도록 설정된 경우에만 리소스에 영향을 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 작업 및 오류를 이해했으며 다음을 수행할 준비가 되었습니다.
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct.md)
