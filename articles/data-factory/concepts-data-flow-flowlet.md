---
title: 매핑 데이터 흐름의 Flowlet | Microsoft Docs
itleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름의 Flowlet 개념 알아보기
author: joshuha-msft
ms.author: joowen
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 7c71c394ca3ba2e6b3d9e7b2e31cc692a4508d94
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133098185"
---
# <a name="flowlets-in-mapping-data-flow"></a>매핑 데이터 흐름의 Flowlet

## <a name="what-is-a-flowlet"></a>flowlet이란?

 flowlet은 기존 매핑 데이터 흐름에서 만들거나 처음부터 시작할 수 있는 다시 사용할 수 있는 활동 컨테이너입니다. 패턴을 다시 사용하면 논리 중복을 방지하고 여러 매핑 데이터 흐름에서 동일한 논리를 적용할 수 있습니다.
 
 flowlet을 사용하면 주소 정리 또는 문자열 트리밍과 같은 작업을 수행하는 논리를 만들 수 있습니다. 그런 다음 동적 코드 재사용 환경을 위해 호출 데이터 흐름의 열에 입력 및 출력을 매핑할 수 있습니다.

> [!NOTE] 
> 매핑 데이터 흐름의 Flowlet 기능은 현재 공개 미리 보기로 제공됩니다.

## <a name="getting-started"></a>시작
flowlet을 만들려면 매핑 데이터 흐름 메뉴 옵션에서 새 flowlet 작업을 클릭합니다.

![flowlet을 만드는 방법을 보여주는 스크린샷](./media/data-flow-flowlet/flowlet-new-menu.png)

그러면 입력, 출력 및 변환 활동에 추가할 수 있는 새 flowlet이 생성됩니다.

## <a name="flowlet-design-surface"></a>Flowlet 디자인 표면
flowlet 디자인 표면은 매핑 데이터 흐름 디자인 표면과 비슷합니다. 주요 차이점은 아래에 설명된 입력, 출력 및 디버깅 환경입니다.

![flowlet 디자인 화면 및 매핑 데이터 흐름과의 유사성을 보여 주는 스크린샷.](./media/data-flow-flowlet/flowlet-design.png)

### <a name="flowlet-input"></a>Flowlet 입력

flowlet의 입력은 호출 매핑 데이터 흐름에서 예상되는 입력 열을 정의합니다. 이 호출 매핑 데이터 흐름은 스트림의 열을 입력에서 정의한 열에 매핑합니다. 이렇게 하면 flowlet이 열에 재사용 가능한 논리를 수행하는 동시에 flowlet이 적용되는 열에 대한 호출 매핑 데이터 흐름을 유연하게 수행할 수 있습니다.

![flowlet 입력 구성 속성 패널을 보여주는 스크린샷.](./media/data-flow-flowlet/flowlet-input.png)

### <a name="flowlet-output"></a>Flowlet 출력

flowlet의 출력은 호출 매핑 데이터 흐름으로 내보낸 것으로 예상할 수 있는 출력 열을 정의합니다. flowlet에서 변환을 수행한 후에는 결과 출력 열이 호출 매핑 데이터 흐름으로 전송됩니다.

![flowlet 출력 구성 속성 패널을 보여주는 스크린샷.](./media/data-flow-flowlet/flowlet-output.png)

### <a name="debugging-a-flowlet"></a>Flowlet 디버깅
flowlet을 디버깅하는 경우 매핑 데이터 흐름 디버그 환경과는 몇 가지 차이점이 있습니다. 

첫째, 미리 보기 데이터는 flowlet의 출력에서만 사용할 수 있습니다. 데이터를 미리 보려면 흐름 출력을 선택한 다음 데이터 미리 보기 탭을 선택해야 합니다.

![flowlet의 출력에 대한 미리 보기 데이터를 보여주는 스크린샷.](./media/data-flow-flowlet/flowlet-debug.png)

둘째, flowlet이 입력에 동적으로 매핑되기 때문에 flowlet을 디버그하기 위해 사용자가 flowlet을 통해 보낼 테스트 데이터를 입력할 수 있습니다. 디버그 설정 아래에 입력 열과 일치하는 테스트 데이터로 채울 표가 표시됩니다. 열 수가 많은 입력의 경우 전체 화면 아이콘에서 선택해야 할 수 있습니다.

![디버그 설정 디버깅을 위해 테스트 데이터를 입력하는 방법을 보여주는 스크린샷](./media/data-flow-flowlet/flowlet-debug-settings.png)

## <a name="other-methods-for-creating-a-flowlet"></a>Flowlet을 만드는 다른 방법
기존 매핑 데이터 흐름에서 Flowlet을 만들 수도 있습니다. 이렇게 하면 사용자가 이미 만든 논리를 빠르게 다시 사용할 수 있습니다.

단일 변환 활동의 경우 매핑 데이터 흐름 활동을 마우스 오른쪽 단추로 클릭하고 새 flowlet 만들기를 선택할 수 있습니다. 그러면 해당 작업 및 입력이 있는 flowlet이 생성되어 활동의 입력과 일치합니다.

![마우스 오른쪽 단추 클릭 메뉴 옵션을 사용하여 기존 활동에서 flowlet을 만드는 것을 보여주는 스크린샷.](./media/data-flow-flowlet/flowlet-context-create.png)

다중 선택을 설정한 경우 여러 매핑 데이터 흐름 활동을 선택할 수도 있습니다. 이 작업은 여러 활동을 선택하기 위해 사각형을 그리거나 shift+select를 사용하여 여러 활동을 선택하여 여러 활동을 올가미로 지정하여 수행할 수 있습니다. 그런 다음, 마우스 오른쪽 단추를 클릭하고 새 flowlet 만들기를 선택합니다.

![기존 활동에서 여러 항목을 선택하는 스크린샷.](./media/data-flow-flowlet/flowlet-context-multi.png)


## <a name="running-a-flowlet-inside-of-a-mapping-data-flow"></a>매핑 데이터 흐름 내에서 flowlet 실행
flowlet이 만들어지면 flowlet 변환을 통해 매핑 데이터 흐름 작업에서 flowlet을 실행할 수 있습니다. 

자세한 내용은 [매핑 데이터 흐름 | Flowlet 변환을 참조하세요. Microsoft Docs](data-flow-flowlet.md)