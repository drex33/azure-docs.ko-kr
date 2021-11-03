---
title: '빠른 시작: Synapse Studio를 사용하여 Data Explorer 풀 만들기(미리 보기)'
description: 이 가이드의 단계에 따라 Synapse Studio를 사용하여 Data Explorer 풀을 만듭니다.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: c52df34a4dea88a6e43d8d6fdf2a292d05130dc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101242"
---
# <a name="quickstart-create-a-data-explorer-pool-using-synapse-studio-preview"></a>빠른 시작: Synapse Studio를 사용하여 Data Explorer 풀 만들기(미리 보기)

Azure Synapse Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 대량의 데이터 스트리밍에 대한 실시간 분석을 제공하는 빠른 속도의 완전 관리형 데이터 분석 서비스입니다. Data Explorer를 사용하려면 먼저 Data Explorer 풀을 만듭니다.

이 빠른 시작에서는 Synapse Studio를 사용하여 Synapse 작업 영역에 Data Explorer 풀을 만드는 단계를 설명합니다.

> [!IMPORTANT]
> Data Explorer 인스턴스 요금은 인스턴스 사용 여부에 관계없이 분 단위 비례 배분 방식으로 청구됩니다. Data Explorer 인스턴스를 사용한 후 꼭 인스턴스를 종료하거나 짧은 시간 제한을 설정해야 합니다. 자세한 내용은 **리소스 정리** 를 참조하세요.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Synapse 작업 영역](../quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Synapse 작업 영역으로 이동

1. 검색 창에 서비스 이름(또는 리소스 이름)을 직접 입력하여 Data Explorer 풀이 생성될 때 Synapse 작업 영역으로 이동합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/goto-synapse-workspace.png" alt-text="Synapse 작업 영역이 입력된 Azure Portal 검색 창.":::

1. 작업 영역 목록에서 열려는 작업 영역의 이름(또는 이름의 일부)을 입력합니다. 이 예제에서는 **contosoanalytics** 라는 작업 영역을 사용합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/filter-synapse-workspace.png" alt-text="Contoso라는 이름이 포함된 작업 영역을 표시하도록 필터링된 Synapse 작업 영역 목록.":::

## <a name="launch-synapse-studio"></a>Synapse Studio 시작

작업 영역 개요에서 **작업 영역 웹 URL** 을 선택하여 Synapse Studio를 엽니다.

:::image type="content" source="media/create-data-explorer-pool-studio/launch-synapse-studio.png" alt-text="Synapse Synapse Studio 시작이 강조 표시된 Azure Portal 작업 영역 개요.":::

## <a name="create-a-new-data-explorer-pool"></a>새 Data Explorer 풀 만들기

1. Synapse Studio 홈 페이지의 왼쪽 탐색 영역에서 **관리** 아이콘을 선택하여 **관리 허브** 로 이동합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/select-synapse-studio-management-hub.png" alt-text="관리 허브 섹션이 강조 표시된 Synapse Studio 홈 페이지.":::

1. 관리 허브에서 **Data Explorer 풀** 섹션으로 이동하여 작업 영역에서 사용할 수 있는 Data Explorer 풀의 현재 목록을 확인합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/goto-data-explorer-pool-studio.png" alt-text="Data Explorer 풀 탐색이 선택된 Synapse Studio 관리 허브":::

1. **+새로 만들기** 를 선택합니다. 새 Data Explorer 풀 만들기 마법사가 나타납니다.

1. **기본 사항** 탭에서 다음 정보를 입력합니다.

    | 설정 | 제안 값 | 설명 |
    |--|--|--|
    | Data Explorer 풀 이름 | contosodataexplorer | Data Explorer 풀의 이름입니다. |
    | 작업 | 컴퓨팅 최적화 | 이 워크로드는 더 높은 CPU 대 SSD 스토리지 비율을 제공합니다. |
    | 노드 크기 | 작음(코어 4개) | 이 빠른 시작에서는 비용을 줄이기 위해 이 값을 가장 작은 크기로 설정합니다. |

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-basics-studio.png" alt-text="Synapse Studio 새 Data Explorer 풀의 기본 사항":::

    > [!IMPORTANT]
    > Data Explorer 풀이 사용할 수 있는 이름과 관련된 제한 사항이 있습니다. 이름은 소문자 및 숫자만 포함해야 하고 4~15자여야 하며 문자로 시작해야 합니다.

1. **다음: 추가 설정** 을 선택합니다. 다음 설정을 사용하고 나머지 설정은 기본값 그대로 둡니다.


    | 설정 | 제안 값 | 설명 |
    |--|--|--|
    | 자동 크기 조정 | 사용 안 함 | 이 빠른 시작에 자동 크기 조정이 필요하지 않습니다. |
    | 인스턴스 수 | 2 | 이 빠른 시작에서는 비용을 줄이기 위해 이 값을 가장 작은 크기로 설정합니다. |

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Synapse Studio 새 Data Explorer 풀의 고급 설정":::

1. **다음: 태그** 를 선택합니다. 태그를 추가하지 마세요.
1. **검토 + 만들기** 를 선택합니다.
1. 세부 정보가 올바른지 확인한 다음 **만들기** 를 선택합니다.

    Data Explorer 풀이 프로비전 프로세스를 시작합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-studio.png" alt-text="Synapse Studio 새 Data Explorer 풀 만들기":::

1. 프로비전이 완료되면 작업 영역으로 돌아가 목록에 새 Data Explorer 풀이 표시되는지 확인합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/verify-data-explorer-pool-studio.png" alt-text="Synapse Studio 새 Data Explorer 풀 목록":::

## <a name="clean-up-data-explorer-pool-resources-using-synapse-studio"></a>Synapse Studio를 사용하여 Data Explorer 풀 리소스 정리

Synapse Studio를 사용하여 작업 영역에서 Data Explorer 풀을 삭제하려면 다음 단계를 사용합니다.

> [!WARNING]
> Data Explorer 풀을 삭제하면 작업 영역에서 분석 엔진이 제거됩니다. 그러면 더 이상 풀에 연결할 수 없으며, 삭제된 풀을 사용하는 모든 쿼리, 파이프라인 및 Notebook은 더 이상 작동하지 않습니다.

### <a name="delete-the-data-explorer-pool"></a>Data Explorer 풀 삭제

1. 작업 영역에서 Data Explorer 풀로 이동합니다.
1. Data Explorer 풀(이 경우 **contosodataexplorer**)을 제거하려면 **자세히[...]**  > **삭제** 를 선택합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/list-data-explorer-pool-studio.png" alt-text="최근에 만든 풀이 선택된 Data Explorer 풀 목록.":::

1. 삭제를 확인하려면 삭제할 풀의 이름을 입력하고 **삭제** 를 선택합니다.

    :::image type="content" source="media/create-data-explorer-pool-studio/confirm-deletion-data-explorer-pool-studio.png" alt-text="최근에 만든 풀의 삭제를 확인합니다.":::

1. 프로세스가 성공적으로 완료되면 해당 풀이 더 이상 목록에 표시되지 않는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: Azure Portal을 사용하여 Data Explorer 풀 만들기](data-explorer-create-pool-portal.md)
