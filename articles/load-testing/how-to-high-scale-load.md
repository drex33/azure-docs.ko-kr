---
title: 대규모 부하 테스트에 대 한 Azure 부하 테스트 구성
titleSuffix: Azure Load Testing
description: 대량의 가상 사용자를 시뮬레이션 하 여 확장성이 높은 부하 테스트를 실행 하도록 Azure 부하 테스트를 구성 하는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 06113d7e36aa273e0fc6d5900d30e8c0f2b76ca2
ms.sourcegitcommit: 8152290a8817d0882035f7f3f1fd56b80f87dcda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133312743"
---
# <a name="configure-for-high-scale-load-testing"></a>확장성이 뛰어난 부하 테스트를 위한 구성

이 문서에서는 Azure 부하 테스트 미리 보기를 사용 하 여 대규모 부하에 대 한 부하 테스트를 설정 하는 방법을 알아봅니다. 많은 수의 가상 사용자를 시뮬레이트하려면 테스트 엔진 인스턴스를 구성 합니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  

- 기존 Azure 부하 테스트 리소스입니다. Azure 부하 테스트 리소스를 만들려면 빠른 시작 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md)을 참조 하세요.

## <a name="requests-per-second-rps"></a>초당 요청 수(RPS)

Azure 부하 테스트에서 생성할 수 있는 RPS ( *초당 요청* 수)는 응용 프로그램의 대기 시간 및 VU ( *가상 사용자* ) 수에 따라 달라 집니다. 

RPS = (VUs #) * (1/대기 시간) 수식을 적용할 수 있습니다.

예를 들어 응용 프로그램 대기 시간이 20 밀리초이 고 2000 VUs 부하를 생성 하는 경우 10만 RPS를 달성할 수 있습니다.

## <a name="test-engine-instances"></a>테스트 엔진 인스턴스

Azure 부하 테스트에서 *테스트 엔진* 인스턴스는 테스트 계획을 실행 하는 일을 담당 합니다. Apache JMeter 스크립트를 사용 하 여 테스트 계획을 만든 경우 각 테스트 엔진은 Apache JMeter 스크립트를 실행 합니다.

테스트 엔진 인스턴스는 병렬로 실행 됩니다. 응용 프로그램에 대 한 부하 테스트 실행을 확장 하는 방법을 정의 하는 데 사용할 수 있습니다.

Apache JMeter 스크립트에서 병렬 스레드 수를 정의 합니다. 이 숫자는 각 테스트 엔진 인스턴스가 병렬로 실행 하는 스레드 수를 나타냅니다. 각 스레드는 가상 사용자를 나타냅니다. 스레드의 수를 최대 250 미만으로 유지 하는 것이 좋습니다.

예를 들어 1000 스레드 (또는 가상 사용자)를 시뮬레이트하려면 Apache JMeter 스크립트의 스레드 수를 250로 설정 합니다. 그런 다음 4 개의 테스트 엔진 인스턴스 (4 x 250 스레드)를 사용 하 여 테스트를 구성 합니다.

> [!IMPORTANT]
> 공개 미리 보기의 경우 테스트 실행에 대해 최대 45 개의 엔진 인스턴스를 지원 합니다.

## <a name="configure-your-test-plan"></a>테스트 계획 구성

이 섹션에서는 부하 테스트의 크기 조정 설정을 구성 합니다.

1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 부하 테스트 리소스로 이동 하 고, 왼쪽 탐색에서 **테스트** 를 선택 하 여 테스트 목록을 봅니다. 그런 다음 확인란을 선택 하 여 목록에서 테스트를 선택 하 고 **편집** 을 선택 합니다.

    :::image type="content" source="media/how-to-high-scale-load/edit-test.png" alt-text="테스트 목록을 보여 주는 스크린샷":::

1. 테스트 세부 정보 페이지에서 테스트 구성을 수정할 수도 있습니다. **구성** 을 선택한 다음 **테스트** 를 선택 하 여 테스트를 편집 합니다.

    :::image type="content" source="media/how-to-high-scale-load/configure-test.png" alt-text="테스트 정보 페이지에서 테스트를 구성 하는 방법을 보여 주는 스크린샷":::

1. **테스트 편집** 페이지에서 **로드** 탭으로 이동 하 고 **엔진 인스턴스** 입력 필드를 구성 합니다.

    :::image type="content" source="media/how-to-high-scale-load/edit-test-load.png" alt-text="테스트를 편집할 때 로드 탭을 보여 주는 스크린샷":::

1. 테스트를 수정 하 고 테스트를 다시 실행할 때 새 구성을 사용 하려면 **적용** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- 테스트 결과를 비교 하는 방법에 대 한 자세한 내용은 [여러 테스트 결과 비교](./how-to-compare-multiple-test-runs.md)를 참조 하세요.

- 성능 테스트 자동화에 대 한 자세한 내용은 [자동화 된 성능 테스트 구성](./tutorial-cicd-azure-pipelines.md) 을 참조 하세요.
