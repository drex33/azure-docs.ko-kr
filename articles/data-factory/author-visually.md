---
title: 시각적 개체 작성
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 시각적 작성을 사용하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: authoring
ms.custom: synapse
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 09/09/2021
ms.openlocfilehash: a9ee177015896e314d140166c2477e92fa9edf8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788082"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 및 Synapse Analytics UI(사용자 인터페이스) 환경을 사용하면 코드를 작성하지 않고도 데이터 팩터리 또는 Synapse 파이프라인에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다.

현재 UI는 Microsoft Edge 및 Google Chrome에서만 지원됩니다.

## <a name="authoring-canvas"></a>제작 캔버스

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
**제작 캔버스** 를 열려면 연필 아이콘을 클릭합니다. 

:::image type="content" source="media/author-visually/authoring-canvas.png" alt-text="제작 캔버스":::

여기서는 팩터리로 구성된 파이프라인, 활동, 데이터 세트 및 데이터 흐름을 작성합니다. 마찬가지로 연결된 서비스, 트리거 및 통합 런타임은 [관리 탭에서](#management-hub)편집할 수 있습니다. 제작 캔버스를 사용하여 파이프라인 빌드를 시작하려면 [복사 활동을 사용하여 데이터 복사를](tutorial-copy-data-portal.md)참조하세요. 

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
**제작 캔버스** 를 열려면 파이프라인/통합 아이콘을 클릭합니다. 

:::image type="content" source="media/author-visually/authoring-canvas-synapse.png" alt-text="제작 캔버스":::

여기서는 Synapse에서 파이프라인 및 활동을 작성합니다. 마찬가지로 파이프라인에서 사용하는 데이터 흐름은 개발 탭에서 편집할 수 있으며 연결된 서비스, 트리거 및 연결된 통합 런타임은 [관리 탭에서](#management-hub)편집할 수 있습니다.  제작 캔버스를 사용하여 파이프라인 빌드를 시작하려면 [복사 활동을 사용하여 데이터 복사를](tutorial-copy-data-portal.md)참조하세요. 

---

기본 시각적 작성 환경은 서비스를 직접 작업하는 것입니다. Azure Repos Git 또는 GitHub 통합은 파이프라인에 대한 작업에 대한 소스 제어 및 협업을 허용하도록 지원됩니다. 이러한 제작 환경 간의 차이점에 대한 자세한 내용은 [소스 제어를 참조하세요.](source-control.md)

### <a name="properties-pane"></a>속성 창

파이프라인, 데이터 세트 및 데이터 흐름과 같은 최상위 리소스의 경우 캔버스 오른쪽에 있는 속성 창에서 상위 수준 속성을 편집할 수 있습니다. 속성 창에는 이름, 설명, 주석 및 기타 고급 속성 등의 속성이 포함됩니다. 파이프라인 활동 및 데이터 흐름 변환과 같은 하위 리소스는 캔버스 하단의 패널을 사용하여 편집됩니다. 

:::image type="content" source="media/author-visually/properties-pane.png" alt-text="속성 창":::

속성 창은 기본적으로 리소스 생성 시에만 열립니다. 편집하려면 캔버스의 오른쪽 위 모서리에 있는 속성 창 아이콘을 클릭합니다.

### <a name="related-resources"></a>관련 참고 자료

속성 창에서 **관련** 탭을 선택하여 선택한 리소스에 종속된 리소스를 확인할 수 있습니다. 현재 리소스를 참조하는 모든 리소스가 여기에 나열됩니다.

:::image type="content" source="media/author-visually/related-resources.png" alt-text="관련 참고 자료":::

예를 들어 위의 이미지에서는 파이프라인 하나와 데이터 흐름 두 개가 현재 선택된 데이터 세트를 사용합니다.

## <a name="management-hub"></a>관리 허브

UI의 관리 탭에서 액세스하는 *관리* 허브는 서비스에 대한 글로벌 관리 작업을 호스트하는 포털입니다. 여기에서 데이터 저장소 및 외부 컴퓨팅, 소스 제어 구성 및 트리거 설정에 대한 연결을 관리할 수 있습니다. 자세한 내용은 [관리 허브](author-management-hub.md)의 기능에 대해 알아보세요.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="연결된 서비스 관리":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="연결된 서비스 관리":::

---

## <a name="expressions-and-functions"></a>식 및 함수

정적 값 대신 식과 함수를 사용하여 서비스 내에서 많은 속성을 지정할 수 있습니다.

속성 값에 대한 식을 지정하려면 **동적 콘텐츠 추가** 를 선택하거나 필드에 초점을 맞춘 상태에서 **Alt + P** 를 클릭합니다.

:::image type="content" source="media/author-visually/dynamic-content-1.png" alt-text="동적 콘텐츠 추가":::

그러면 식 **작성기에서** 지원되는 시스템 변수, 작업 출력, 함수 및 사용자 지정 변수 또는 매개 변수에서 식을 작성할 수 있습니다. 

:::image type="content" source="media/author-visually/dynamic-content-2.png" alt-text="식 작성기":::

식 언어에 대한 자세한 내용은 [식 및 함수를 참조하세요.](control-flow-expression-language-functions.md)

## <a name="provide-feedback"></a>피드백 제공

기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백** 을 선택하세요.

:::image type="content" source="media/author-visually/provide-feedback.png" alt-text="사용자 의견":::

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
