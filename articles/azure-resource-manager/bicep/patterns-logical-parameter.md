---
title: 논리적 매개 변수 패턴
description: 논리적 매개 변수 패턴에 대해 설명합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: a45748e0a6ddc6e149826b0302bb900a29bbd4df
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386803"
---
# <a name="logical-parameter-pattern"></a>논리적 매개 변수 패턴

매개 변수를 사용하여 리소스 또는 여러 리소스의 논리적 정의를 지정합니다. Bicep 파일은 논리 매개 변수를 배포 가능한 리소스 정의로 변환합니다. 이 패턴을 따라 배포된 *내용을* 배포 *방법과* 구분할 수 있습니다.

## <a name="context-and-problem"></a>컨텍스트 및 문제점

복잡한 배포에서는 배열 매개 변수 및 루프를 사용하여 리소스 또는 리소스 속성 집합을 만듭니다. 리소스의 세부 정보를 정의하는 매개 변수를 만드는 경우 이해하고 사용하기 어려운 매개 변수를 만듭니다.

## <a name="solution"></a>해결 방법

간소화된 값을 허용하는 매개 변수 또는 Azure 리소스별 값 대신 비즈니스 도메인별 값을 정의합니다. Bicep 파일에 논리를 빌드하여 간소화된 값을 Azure 리소스 정의로 변환합니다.

[종종 루프를](loops.md) 사용하여 매개 변수에 정의된 논리 값을 리소스 정의에서 예상하는 속성 값으로 변환합니다.

이 패턴을 사용하면 리소스 간에 변경할 필요가 없는 속성의 기본값을 쉽게 적용할 수 있습니다. 변수 및 [함수를](variables.md) [](bicep-functions.md) 사용하여 고유한 비즈니스 규칙에 따라 리소스 속성의 값을 자동으로 결정할 수도 있습니다.

논리 매개 변수 패턴을 사용하면 템플릿의 매개 변수를 간단하고 쉽게 사용할 수 있도록 유지하면서 복잡한 리소스 집합을 배포할 수 있습니다.

## <a name="example-1-virtual-network-subnets"></a>예제 1: 가상 네트워크 서브넷

이 예제에서는 패턴을 사용하여 새 서브넷의 정의를 단순화하고 리소스의 구성을 결정하는 비즈니스 논리를 추가하는 방법을 보여 줍니다.

이 예제에서는 가상 네트워크에서 만들어야 하는 서브넷 목록을 지정하는 매개 변수를 정의합니다. 각 서브넷의 정의에는 라는 속성도 `allowRdp` 포함됩니다. 이 속성은 인바운드 원격 데스크톱 트래픽을 허용하는 네트워크 보안 그룹과 서브넷을 연결해야 하는지 여부를 나타냅니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/virtual-network.bicep" range="4-15" :::

그런 다음, Bicep 파일은 각 논리 서브넷 정의를 Azure에 필요한 서브넷 정의로 변환하는 변수를 정의합니다. 네트워크 보안 그룹은 속성이 로 설정된 경우 `allowRdp` 서브넷에 `true` 할당됩니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/virtual-network.bicep" range="17-25" :::

마지막으로 Bicep 파일은 가상 네트워크를 정의하고 변수를 사용하여 서브넷을 구성합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/virtual-network.bicep" range="51-62" highlight="10" :::

[전체 예제를 참조합니다.](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/patterns-logical-parameter/virtual-network.bicep?azure-portal=true)

## <a name="example-2-service-bus-queue"></a>예제 2: Service Bus 큐

이 예제에서는 패턴을 사용하여 매개 변수를 기반으로 정의된 여러 리소스에 일관된 구성 집합을 적용하는 방법을 보여 줍니다.

이 예제에서는 Azure Service Bus 큐에 대한 큐 이름 목록을 사용하여 매개 변수를 정의합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/service-bus.bicep" range="5-8" :::

그런 다음 루프를 사용하여 큐 리소스를 정의하고 모든 큐가 다른 중앙 집중식 큐에 데드 문자 메시지를 자동으로 전달하도록 구성합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/service-bus.bicep" range="30-32, 36-39" :::

[전체 예제를 참조합니다.](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/patterns-logical-parameter/service-bus.bicep?azure-portal=true)

## <a name="example-3-resources-for-a-multitenant-solution"></a>예제 3: 다중테넌트 솔루션에 대한 리소스

이 예제에서는 다중테넌트 솔루션을 빌드할 때 패턴을 사용하는 방법을 보여 줍니다. Bicep 배포는 테넌트 논리적 목록을 기반으로 복잡한 리소스 집합을 만들고 모듈을 사용하여 공유 및 테넌트별 리소스 만들기를 간소화합니다. 모든 테넌트는 Azure SQL 자체 데이터베이스와 Azure Front Door 구성된 자체 사용자 지정 도메인을 가져옵니다.

이 예제에서는 테넌트 목록을 지정하는 매개 변수를 정의합니다. 테넌트 정의는 단순히 테넌트 식별자 및 해당 사용자 지정 도메인 이름입니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/resources.bicep" range="15-24" :::

기본 Bicep 파일은 공유 리소스를 정의한 다음, 모듈을 사용하여 테넌트 반복을 수행합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-logical-parameter/all-tenant-resources.bicep" range="8-16" :::

모듈 내에서 테넌트별 리소스가 배포됩니다.

[전체 예제를 참조합니다.](https://github.com/Azure/azure-docs-bicep-samples/blob/main/samples/patterns-logical-parameter/all-tenant-resources.bicep?azure-portal=true)

## <a name="considerations"></a>고려 사항

- Bicep 파일을 개발하거나 디버깅하는 경우 리소스를 정의하지 않고 매핑 변수를 만드는 것이 좋습니다. 그런 다음, 매핑 결과를 볼 수 있도록 변수의 값으로 [출력을](outputs.md) 정의합니다.
- [조건을](conditional-resource-deployment.md) 사용하여 논리적 매개 변수 값에 따라 리소스를 선택적으로 배포할 수 있습니다.
- 리소스 이름을 동적으로 만들 때 이름이 [배포하는 리소스에 대한 요구 사항을](../management/resource-name-rules.md)충족하는지 확인합니다. 매개 변수를 통해 직접 이름을 수락하는 대신 일부 리소스에 대한 이름을 [생성해야](patterns-name-generation.md) 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[공유 변수 파일 패턴에 대해 알아봅니다.](patterns-shared-variable-file.md)
