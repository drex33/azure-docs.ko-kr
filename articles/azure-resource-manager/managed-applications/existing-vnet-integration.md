---
title: 기존 가상 네트워크에 배포
description: 관리되는 애플리케이션의 사용자가 기존 가상 네트워크를 선택할 수 있도록 설정하는 방법을 설명합니다. 가상 네트워크는 관리되는 애플리케이션 외부에 있을 수 있습니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: 0f35e12b62b0efd9f79f83a1832c317d7eabe99b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648338"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Azure Managed Application에서 기존 가상 네트워크 사용

이 문서에서는 소비자 구독에서 기존 가상 네트워크와 통합하는 Azure Managed Application을 정의하는 방법을 보여 줍니다. 관리되는 애플리케이션을 통해 소비자는 새 가상 네트워크를 만들지 아니면 기존 네트워크를 사용할지 결정할 수 있습니다. 기존 가상 네트워크는 관리되는 리소스 그룹 외부에 있을 수 있습니다.

## <a name="main-template"></a>기본 템플릿

먼저 _mainTemplate.json_ 파일을 살펴보겠습니다. 가상 머신 및 관련 리소스를 배포하기 위한 전체 템플릿은 다음과 같습니다. 나중에 기존 가상 네트워크 사용과 관련된 템플릿 부분을 좀더 자세히 살펴보겠습니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

가상 네트워크는 [조건부로 배포](../templates/conditional-resource-deployment.md)됩니다. 소비자는 관리되는 애플리케이션을 통해 소비자는 새 가상 네트워크를 만들지 아니면 기존 네트워크를 사용할지를 나타내는 매개 변수 값을 전달합니다. 소비자가 새 가상 네트워크를 선택하면 리소스가 배포됩니다. 그렇지 않으면 리소스는 배포하는 동안 건너뜁니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

가상 네트워크 ID에 대한 변수에는 두 개의 속성이 있습니다. 한 속성은 새 가상 네트워크가 배포될 때 리소스 ID를 반환합니다. 다른 속성은 기존 가상 네트워크가 사용될 때 리소스 ID를 반환합니다. 기존 가상 네트워크의 리소스 ID에는 가상 네트워크를 포함하는 리소스 그룹의 이름이 포함됩니다.

서브넷 ID는 가상 네트워크 ID 값에서 구성됩니다. 소비자 선택과 일치하는 값을 사용합니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

네트워크 인터페이스가 서브넷 ID 변수로 설정됩니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI 정의

이제 _createUiDefinition.json_ 파일을 살펴보겠습니다. 전체 파일은 다음과 같습니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

파일에는 가상 네트워크 요소가 포함됩니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

이 요소를 통해 소비자는 새 가상 네트워크 또는 기존 가상 네트워크를 선택할 수 있습니다.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="새 가상 네트워크 또는 기존 가상 네트워크":::

출력에 소비자가 새 가상 네트워크 또는 기존 가상 네트워크를 선택했는지 여부를 나타내는 값을 포함합니다. 관리 ID 값도 있습니다.

> [!NOTE]
> 관리 ID의 출력 값 이름을 **managedIdentity** 로 지정해야 합니다.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>다음 단계

UI 정의 파일을 만드는 방법에 대한 자세한 내용은 [Azure 관리되는 애플리케이션의 만들기 환경을 위한 CreateUiDefinition.json](create-uidefinition-overview.md) 파일을 참조하세요.
