---
title: 분산 데이터 구조
description: 분산 데이터 구조는 유체 애플리케이션의 구성 요소입니다.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/data-structures/overview/
ms.openlocfilehash: c79fea26de7cda6d3097c2a8c9403420451fe665
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663038"
---
# <a name="distributed-data-structures"></a>분산 데이터 구조

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

이 Fluid Framework 연결된 각 클라이언트가 동일한 상태에 액세스할 수 있도록 자동으로 보장하는 DDS(분산 데이터 구조)를 개발자에게 제공합니다. DDSes에서 제공하는 API는 이전에 공통 데이터 구조를 사용한 프로그래머에게 친숙하도록 설계되었습니다.

> [!NOTE]
> 이 문서에서는 사용자가 fluidframework.com [분산 데이터 구조 소개에](https://fluidframework.com/docs/build/dds/) 익숙하다고 가정합니다.

분산 데이터 구조는 로컬 데이터 구조처럼 동작합니다. 코드는 데이터를 추가하고, 데이터를 제거하고, 업데이트할 수 있습니다. 그러나 DDS는 로컬 개체가 아닙니다. DDS의 동일한 부모 컨테이너를 노출하는 다른 클라이언트에서 DDS를 변경할 수도 있습니다. 사용자는 동일한 DDS를 동시에 변경할 수 있으므로 데이터 모델링에 사용할 DDS를 고려해야 합니다.

> [!NOTE]
> **'동시에'의 의미**
>
> 서버에서 다른 클라이언트의 변경 내용을 수신하기 전에 각각 변경하면 둘 이상의 클라이언트가 *동시에* 변경된다고 합니다.

시나리오에 맞는 올바른 데이터 구조를 선택하면 애플리케이션의 성능 및 코드 구조가 향상될 수 있습니다.

DDS는 다음과 같은 세 가지 특성에 따라 다릅니다.

- **기본 데이터 구조:** 예를 들어 키-값 쌍, 시퀀스 또는 큐입니다.
- **클라이언트 자율성:** *낙관적* DDS를 사용하면 모든 클라이언트가 값을 적극적으로 변경할 수 있으며 새 값이 다른 모든 클라이언트에 릴레이됩니다. 그러나 *합의* DDS는 합의 프로세스에서 다른 클라이언트가 수락하는 경우에만 변경을 허용합니다.
- **병합 정책:** 클라이언트에서 충돌하는 변경 내용을 해결하는 방법을 결정하는 정책입니다.

아래에서는 데이터 구조를 열거하고 가장 유용한 경우에 대해 설명했습니다.

## <a name="key-value-data"></a>키-값 데이터

이러한 DDS는 키-값 데이터를 저장하는 데 사용됩니다. 낙관적이며 최종 작성자-승 병합 정책을 사용합니다. 쌍의 값은 복합 개체일 수 있지만 지정된 쌍의 값은 직접 편집할 수 없습니다. 전체 값은 원하는 편집 내용이 포함된 전체 전체 값을 포함하는 새 값으로 바꾸어야 합니다.

- **SharedMap**: 기본 키-값 데이터 구조입니다.

### <a name="key-value-scenarios"></a>키-값 시나리오

키-값 데이터 구조는 많은 시나리오에서 가장 일반적인 선택입니다.

- 사용자 기본 설정 데이터입니다.
- 설문 조사의 현재 상태입니다.
- 뷰의 구성입니다.

### <a name="common-issues-and-best-practices-for-key-value-ddses"></a>키-값 DDS에 대한 일반적인 문제 및 모범 사례

- SharedMap에 카운터를 저장하면 예기치 않은 동작이 있습니다. 대신 SharedCounter를 사용합니다.
- 배열, 목록 또는 로그를 키-값 항목에 저장하면 사용자가 한 항목의 일부를 공동으로 수정할 수 없으므로 예기치 않은 동작이 발생할 수 있습니다. 배열 또는 목록 데이터를 SharedSequence 또는 SharedInk에 저장해 보세요.
- 하나의 키-값 항목에 많은 데이터를 저장하면 성능 또는 병합 문제가 발생할 수 있습니다. 각 업데이트는 두 업데이트를 병합하는 대신 전체 값을 업데이트합니다. 데이터를 여러 키로 분할해 보세요.

## <a name="sequences"></a>시퀀스

이러한 DDS는 순차적 데이터를 저장하는 데 사용됩니다. 낙관적입니다. 시퀀스 데이터 구조는 목록 또는 배열의 지정된 인덱스에서 데이터를 추가하거나 제거해야 하는 경우에 유용합니다. 키-값 데이터 구조와 달리 시퀀스는 순차적 순서를 가지며 여러 사용자의 동시 삽입을 처리할 수 있습니다.

- **SharedNumberSequence:** 숫자 시퀀스입니다.
- **SharedObjectSequence:** 일반 개체의 시퀀스입니다.

### <a name="sequence-scenarios"></a>시퀀스 시나리오

- 목록
- 타임라인

### <a name="common-issues-and-best-practices-for-sequence-ddses"></a>시퀀스 DDSes에 대한 일반적인 문제 및 모범 사례

- 시퀀스의 항목으로는변변할 수 없는 데이터만 저장합니다. 항목의 값을 변경하는 유일한 방법은 먼저 시퀀스에서 제거한 다음 이전 값이 있던 위치에 새 값을 삽입하는 것입니다. 그러나 다른 클라이언트는 삽입 및 제거할 수 있으므로 새 값을 원하는 위치로 신뢰할 수 있는 방법이 없습니다.

## <a name="strings"></a>문자열

SharedString DDS는 공동으로 편집할 수 있는 구조화되지 않은 텍스트 데이터에 사용됩니다. 낙관적입니다.

- `SharedString` -- 공동 작업 텍스트를 처리하기 위한 데이터 구조입니다.

### <a name="string-scenarios"></a>문자열 시나리오

- 텍스트 편집기

## <a name="see-also"></a>참조

DDS 및 DDS를 사용하는 방법에 대한 자세한 내용은 fluidframework.com 다음 섹션을 참조하세요.

- [분산 데이터 구조 를 소개합니다.](https://fluidframework.com/docs/build/dds/)
- [분산 데이터 구조의 형식입니다.](https://fluidframework.com/docs/data-structures/overview/)
