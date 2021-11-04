---
title: 디자이너에서 내게 필요한 옵션 기능 사용
titleSuffix: Azure Machine Learning
description: 디자이너에서 사용할 수 있는 바로 가기 키 및 화면 읽기 프로그램의 내게 필요한 옵션 기능에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: lagayhar
author: lgayhardt
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: dfa3b5630b09e2a3e3411afc7d77b76f1b651e72
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555004"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>키보드를 통해 Azure Machine Learning 디자이너 사용

키보드 및 화면 읽기 프로그램을 통해 Azure Machine Learning 디자이너를 사용하는 방법을 알아봅니다. Azure Portal의 모든 위치에서 작동하는 바로 가기 키 목록은 [Azure Portal의 바로 가기 키](../azure-portal/azure-portal-keyboard-shortcuts.md)를 참조하세요.

이 워크플로는 [내레이터](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) 및 [JAWS](https://www.freedomscientific.com/products/software/jaws/)에서 테스트되었지만 다른 표준 화면 읽기 프로그램과 함께 작동합니다.

## <a name="navigate-the-pipeline-graph"></a>파이프라인 그래프 탐색

파이프라인 그래프는 중첩된 목록으로 구성됩니다. 외부 목록은 파이프라인 그래프의 모든 구성 요소를 설명하는 구성 요소 목록입니다. 내부 목록은 특정 구성 요소의 모든 연결을 설명하는 연결 목록입니다.  

1. 구성 요소 목록에서 화살표 키를 사용하여 구성 요소를 전환합니다.
1. 탭을 사용하여 대상 구성 요소에 대한 연결 목록을 엽니다.
1. 화살표 키를 사용하여 구성 요소의 연결 포트 간에 전환합니다.
1. "G"를 사용하여 대상 구성 요소로 이동합니다.

## <a name="edit-the-pipeline-graph"></a>파이프라인 그래프 편집

### <a name="add-a-component-to-the-graph"></a>그래프에 구성 요소 추가

1. Ctrl+F6을 사용하여 캔버스에서 구성 요소 트리로 포커스를 전환합니다.
1. 표준 treeview 컨트롤을 사용하여 구성 요소 트리에서 원하는 구성 요소를 찾습니다.

### <a name="edit-a-component"></a>구성 요소 편집

구성 요소를 다른 구성 요소에 연결하려면 다음을 수행합니다.

1. 구성 요소 목록의 구성 요소를 대상으로 지정하여 연결 도우미를 열 때 Ctrl + Shift + H를 사용합니다.
1. 구성 요소에 대한 연결 포트를 편집합니다.

구성 요소 속성을 조정하려면 다음을 수행합니다.

1. 구성 요소를 대상으로 지정하는 경우 Ctrl + Shift + E를 사용하여 구성 요소 속성을 엽니다.
1. 구성 요소 속성을 편집합니다.

## <a name="navigation-shortcuts"></a>탐색 바로 가기

| 키 입력 | Description |
|-|-|
| Ctrl + F6 | 캔버스와 구성 요소 트리 간에 포커스 전환 |
| Ctrl + F1   | 구성 요소 트리의 노드에 초점을 맞출 때 정보 카드 열기 |
| Ctrl + Shift + H | 노드에 포커스가 있을 때 연결 도우미 열기 |
| Ctrl + Shift + E | 포커스가 노드에 있을 때 구성 요소 속성 열기 |
| Ctrl + G | 파이프라인 실행에 실패한 경우 실패한 첫 번째 노드로 포커스 이동 |

## <a name="action-shortcuts"></a>작업 바로 가기

액세스 키와 함께 다음 바로 가기를 사용합니다. 액세스 키에 대한 자세한 내용은 https://en.wikipedia.org/wiki/Access_key 를 참조하세요.

| 키 입력 | 작업 |
|-|-|
| 액세스 키 + R | 다음을 실행합니다. |
| 액세스 키 + P | 게시 |
| 액세스 키 + C | 복제 |
| 액세스 키 + D | 배포 |
| 액세스 키 + I | 유추 파이프라인 만들기/업데이트 |
| 액세스 키 + B | 일괄 처리 유추 파이프라인 만들기/업데이트 |
| 액세스 키 + K | "유추 파이프라인 만들기" 드롭다운 목록 열기 |
| 액세스 키 + U | "유추 파이프라인 업데이트" 드롭다운 목록 열기 |
| 액세스 키 + M | 자세히(...) 드롭다운 목록 열기 |

## <a name="next-steps"></a>다음 단계

- [고대비 켜기 및 테마 변경](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Microsoft의 내게 필요한 옵션 관련 도구](https://www.microsoft.com/accessibility)
