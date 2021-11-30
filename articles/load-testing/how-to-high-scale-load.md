---
title: 대규모 부하 테스트를 위한 Azure 부하 테스트 구성
titleSuffix: Azure Load Testing
description: 대량의 가상 사용자를 시뮬레이션하여 대규모 부하 테스트를 실행하도록 Azure Load Testing을 구성하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 790d6a909152cf6b323eb7b36ab3e442d6930580
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305616"
---
# <a name="configure-for-high-scale-load-testing"></a>대규모 부하 테스트 구성

이 문서에서는 Azure 부하 테스트 미리 보기를 사용하여 대규모 부하에 대한 부하 테스트를 설정하는 방법을 알아봅니다. 많은 수의 가상 사용자를 시뮬레이션하려면 테스트 엔진 인스턴스를 구성합니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  

- 기존 Azure Load Testing 리소스입니다. Azure 부하 테스트 리소스를 만들려면 빠른 시작 [부하 테스트 만들기 및 실행을](./quickstart-create-and-run-load-test.md)참조하세요.

## <a name="requests-per-second-rps"></a>초당 요청 수(RPS)

Azure Load Testing에서 생성할 수 있는 RPS(초당 *최대 요청* 수)는 애플리케이션의 대기 시간 및 VU(가상 *사용자)* 수에 따라 달라집니다. 

다음 수식을 적용할 수 있습니다. RPS = (VU 수) * (1/대기 시간).

예를 들어 애플리케이션 대기 시간이 20ms이고 2000VU 부하를 생성하는 경우 약 100,000 RPS를 달성할 수 있습니다.

## <a name="test-engine-instances"></a>테스트 엔진 인스턴스

Azure Load Testing에서 *테스트 엔진* 인스턴스는 테스트 계획 실행을 담당합니다. Apache JMeter 스크립트를 사용하여 테스트 계획을 만든 경우 각 테스트 엔진은 Apache JMeter 스크립트를 실행합니다.

테스트 엔진 인스턴스는 병렬로 실행됩니다. 이를 통해 애플리케이션에 대한 부하 테스트 실행을 스케일 아웃하는 방법을 정의할 수 있습니다.

Apache JMeter 스크립트에서 병렬 스레드 수를 정의합니다. 이 숫자는 각 테스트 엔진 인스턴스가 병렬로 실행되는 스레드 수를 나타냅니다. 각 스레드는 가상 사용자를 나타냅니다. 스레드 수를 최대 250개 미만으로 유지하는 것이 좋습니다.

예를 들어 1000개의 스레드(또는 가상 사용자)를 시뮬레이션하려면 Apache JMeter 스크립트의 스레드 수를 250으로 설정합니다. 그런 다음 네 개의 테스트 엔진 인스턴스(4 x 250 스레드)로 테스트를 구성합니다.

> [!IMPORTANT]
> 공개 미리 보기의 경우 테스트 실행에 대해 최대 100개의 엔진 인스턴스를 지원합니다.

## <a name="configure-your-test-plan"></a>테스트 계획 구성

이 섹션에서는 부하 테스트의 크기 조정 설정을 구성합니다.

1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 부하 테스트 리소스로 이동하고 왼쪽 탐색 창에서 **테스트를** 선택하여 테스트 목록을 봅니다. 다음으로, 확인란을 선택하여 목록에서 테스트를 선택한 다음, **편집을** 선택합니다.

    :::image type="content" source="media/how-to-high-scale-load/edit-test.png" alt-text="테스트 목록을 보여 주는 스크린샷":::

1. 테스트 세부 정보 페이지에서 테스트 구성을 수정할 수도 있습니다. **구성을** 선택한 **다음, 테스트를** 선택하여 테스트를 편집합니다.

    :::image type="content" source="media/how-to-high-scale-load/configure-test.png" alt-text="테스트 세부 정보 페이지에서 테스트를 구성하는 방법을 보여 주는 스크린샷":::

1. 테스트 **편집** 페이지에서 **로드** 탭으로 이동하여 엔진 인스턴스 입력 필드를 **구성합니다.**

    :::image type="content" source="media/how-to-high-scale-load/edit-test-load.png" alt-text="테스트를 편집할 때 로드 탭을 보여 주는 스크린샷.":::

1. **적용을** 선택하여 테스트를 수정하고 테스트를 다시 실행하면 새 구성을 사용합니다.

## <a name="next-steps"></a>다음 단계

- 테스트 결과를 비교하는 방법에 대한 자세한 내용은 [여러 테스트 결과 비교를 참조하세요.](./how-to-compare-multiple-test-runs.md)

- 성능 테스트 자동화에 대한 자세한 내용은 [자동화된 성능 테스트 구성을](./tutorial-cicd-azure-pipelines.md) 참조하세요.
