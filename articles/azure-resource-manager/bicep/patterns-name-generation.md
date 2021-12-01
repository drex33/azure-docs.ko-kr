---
title: 이름 생성 패턴
description: 이름 생성 패턴에 대해 설명합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: cd8bf8bad424fac128af06450c67df47f71999f2
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386852"
---
# <a name="name-generation-pattern"></a>이름 생성 패턴

Bicep 파일 내에서 문자열 보간 및 Bicep 함수를 사용하여 배포하는 각 환경에 대해 고유하고 결정적이며 의미 있고 다른 리소스 이름을 만듭니다.

## <a name="context-and-problem"></a>컨텍스트 및 문제점

Azure에서는 리소스에 제공하는 이름이 중요합니다. 이름은 사용자와 팀이 리소스를 식별하는 데 도움이 됩니다. 대부분의 서비스에서 리소스 이름은 리소스에 액세스하는 데 사용하는 DNS 이름의 일부를 형성합니다. 리소스를 만든 후에는 이름을 쉽게 변경할 수 없습니다.

리소스 이름을 계획할 때는 다음을 확인해야 합니다.

- **고유:** Azure 리소스 이름은 고유해야 하지만 고유성의 범위는 리소스에 따라 달라집니다.
- **결정적:** 기존 리소스를 다시 만들지 않고 Bicep 파일을 반복적으로 배포할 수 있어야 합니다. 동일한 매개 변수를 가진 Bicep 파일을 다시 배포하는 경우 리소스는 동일한 이름을 유지해야 합니다.
- **의미:** 이름은 리소스의 용도에 대한 중요한 정보를 사용자와 팀에 제공합니다. 가능한 경우 리소스의 용도를 나타내는 이름을 사용합니다.
- **각 환경에 대해 고유:** 일반적으로 출시 프로세스의 일부로 테스트, 스테이징 및 프로덕션과 같은 여러 환경에 배포합니다.
- **특정 리소스에 유효:** [각 Azure 리소스에는 유효한 리소스 이름을 만들 때 따라야 하는 지침 집합이 있습니다.](../management/resource-name-rules.md) 여기에는 최대 길이, 허용되는 문자 및 리소스 이름이 문자로 시작해야 하는지 여부가 포함됩니다.

> [!NOTE]
> 조직에 따라야 하는 고유한 명명 규칙이 있을 수도 있습니다. Azure 클라우드 채택 프레임워크 조직에 대한 명명 전략을 만드는 방법에 대한 [지침을 제공합니다.](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming) 따라야 하는 엄격한 명명 규칙이 있고 생성되는 이름이 고유하고 고유한 경우 이 패턴을 따르지 않아도 됩니다.

## <a name="solution"></a>해결 방법

Bicep의 [문자열 보간을](bicep-functions-string.md#concat) 사용하여 리소스 이름을 [변수로 생성합니다.](variables.md) 리소스에 전역적으로 고유한 이름이 필요한 경우 [uniqueString() 함수를](bicep-functions-string.md#uniquestring) 사용하여 리소스 이름의 일부를 생성합니다. 리소스를 쉽게 식별할 수 있도록 의미 있는 정보를 추가하거나 추가합니다.

> [!NOTE]
> Azure RBAC 역할 정의 및 역할 할당과 같은 일부 Azure 리소스에는 해당 이름으로 전역적으로 고유한 식별자(GUID)가 있어야 합니다. [guid() 함수를](bicep-functions-string.md#guid) 사용하여 이러한 리소스에 대한 이름을 생성합니다.

재사용 가능한 Bicep 코드를 만드는 경우 이름을 [매개 변수로](parameters.md)정의하는 것이 좋습니다. 기본 [매개 변수 값을](parameters.md#default-value) 사용하여 재정의할 수 있는 기본 이름을 정의합니다. 기본값은 Bicep 파일을 더 재사용 가능하게 만들어 파일 사용자가 다른 명명 규칙을 따라야 하는 경우 자신의 이름을 정의할 수 있도록 하는 데 도움이 됩니다.

## <a name="example-1-organizational-naming-convention"></a>예제 1: 조직 명명 규칙

다음 예제에서는 App Service 앱 및 계획에 대한 이름을 생성합니다. 리소스 종류 코드( `app` 또는 ), 애플리케이션 또는 `plan` 워크로드 이름( `contoso` ), 환경 이름(매개 변수로 지정) 및 `uniqueString()` 리소스 그룹 ID의 시드 값과 함께 함수를 사용하여 고유성을 보장하는 문자열을 포함하는 조직 규칙을 따릅니다.

App Service 계획에는 전역적으로 고유한 이름이 필요하지 않지만 계획 이름은 조직의 정책을 준수하기 위해 동일한 형식으로 구성됩니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-name-generation/app-service.bicep" range="1-8, 13-17, 23" highlight="3-4" :::

## <a name="example-2"></a>예제 2

다음 예제에서는 명명 규칙 없이 다른 조직에 대한 두 스토리지 계정의 이름을 생성합니다. 이 예제에서는 `uniqueString()` 리소스 그룹의 ID와 함께 함수를 다시 사용합니다. 두 스토리지 계정 각각에 고유한 이름이 있는지 확인하기 위해 생성된 이름 앞에 짧은 문자열이 추가됩니다. 이렇게 하면 이름이 스토리지 계정의 요구 사항인 문자로 시작하는 데도 도움이 됩니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-name-generation/storage.bicep" range="3-8, 14-18, 24" highlight="1-2" :::

## <a name="considerations"></a>고려 사항

- 리소스 이름의 고유성 범위를 확인해야 합니다. [uniqueString() 함수에](bicep-functions-string.md#uniquestring) 적절한 시드 값을 사용하여 Azure 리소스 그룹 및 구독에서 Bicep 파일을 다시 사용할 수 있는지 확인합니다.
  > [!TIP]
  > 대부분의 경우 정규화된 리소스 그룹 ID는 함수의 시드 값에 적합한 옵션입니다. `uniqueString`
  >
  > ```bicep
  > var uniqueNameComponent = uniqueString(resourceGroup().id)
  > ```
  > 
  > 리소스 그룹( )의 이름은 `resourceGroup().name` 구독 간에 파일을 다시 사용할 수 있을 정도로 고유하지 않을 수 있습니다.
- 리소스가 배포된 후 함수의 시드 값을 변경하지 `uniqueString()` 마십시오. 시드 값을 변경하면 새 이름이 생성되며 프로덕션 리소스에 영향을 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계

[공유 변수 파일 패턴에 대해 알아봅니다.](patterns-shared-variable-file.md)
