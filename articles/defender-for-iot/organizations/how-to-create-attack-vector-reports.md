---
title: 공격 벡터 보고서 만들기
description: 공격 벡터 보고서는 악용 가능한 디바이스의 취약성 체인을 그래픽으로 표시합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: a100363cd974bd820e859ca2456fcfa5c347e7eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293291"
---
# <a name="attack-vector-reporting"></a>공격 벡터 보고

## <a name="about-attack-vector-reports"></a>공격 벡터 보고서 정보

공격 벡터 보고서는 악용 가능한 디바이스의 취약성 체인을 그래픽으로 표시합니다. 이러한 취약성으로 인해 주요 네트워크 디바이스에 공격자가 액세스할 수 있습니다. 공격 벡터 시뮬레이터는 공격 벡터를 실시간으로 계산하고 특정 대상의 모든 공격 벡터를 분석합니다.

공격 벡터를 사용하면 공격 시퀀스에서 완화 작업의 효과를 평가할 수 있습니다. 예를 들어 시스템 업그레이드가 공격 체인을 차단하여 공격자의 경로를 중단하는지 여부 또는 대체 공격 경로가 유지되는지 여부를 평가할 수 있습니다. 이 정보는 수정 및 위험 완화 작업의 우선 순위를 지정하는 데 도움이 됩니다.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="제어 센터에서 경고 보기.":::

> [!NOTE]
> 관리자 및 보안 분석가는 이 섹션에서 설명된 절차를 수행할 수 있습니다.

## <a name="create-an-attack-vector-report"></a>공격 벡터 보고서 만들기

공격 벡터 시뮬레이션을 만들려면:

1. 측면 메뉴에서 :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="더하기 기호":::를 선택하여 시뮬레이션을 추가합니다.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="공격 벡터 시뮬레이션.":::

2. 시뮬레이션 속성을 입력합니다.

   - **이름**: 시뮬레이션 이름입니다.

   - **최대 벡터**: 단일 시뮬레이션의 최대 벡터 수입니다.

   - **디바이스 맵에 표시**: 공격 벡터를 디바이스 맵에 필터로 표시합니다.

   - **모든 원본 디바이스**: 공격 벡터는 모든 디바이스를 공격 원본으로 간주합니다.

   - **공격 원본**: 공격 벡터는 지정된 디바이스만 공격 원본으로 간주합니다.

   - **모든 대상 디바이스**: 공격 벡터는 모든 디바이스를 공격 대상으로 간주합니다.

   - **공격 대상**: 공격 벡터는 지정된 디바이스만 공격 대상으로 간주합니다.

   - **디바이스 제외**: 지정된 디바이스가 공격 벡터 시뮬레이션에서 제외됩니다.

   - **서브넷 제외**: 지정된 서브넷이 공격 벡터 시뮬레이션에서 제외됩니다.

3. **시뮬레이션 추가** 를 선택합니다. 시뮬레이션이 시뮬레이션 목록에 추가됩니다.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="새 시뮬레이션 추가":::

4. 시뮬레이션을 편집하려면 :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::을 선택합니다.

   시뮬레이션을 삭제하려면 :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::를 선택합니다.

   시뮬레이션을 즐겨찾기로 표시하려면 :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::를 선택합니다.

5. 공격 벡터 목록이 나타나고, 벡터 점수(100점 기준), 공격 원본 디바이스, 공격 대상 디바이스가 포함됩니다. 공격 벡터를 그래픽으로 표시할 특정 공격을 선택합니다.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="공격 벡터":::

## <a name="see-also"></a>참고 항목

[공격 벡터 보고](how-to-create-attack-vector-reports.md)
