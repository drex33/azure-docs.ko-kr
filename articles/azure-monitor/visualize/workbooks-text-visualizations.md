---
title: Azure Monitor 통합 문서 텍스트 시각화
description: 모든 Azure Monitor 통합 문서 텍스트 시각화에 대해 알아봅니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 9b58d29bc46f086d10534572a27e83f5eba59f84
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131660"
---
# <a name="text-visualizations"></a>텍스트 시각화

통합 문서를 사용하면 작성자가 해당 통합 문서에 텍스트 블록을 포함할 수 있습니다. 텍스트는 원격 분석에 대한 사람의 분석, 사용자가 데이터를 해석하는 데 도움이 되는 정보, 섹션 제목 등일 수 있습니다.

[![Apdex 텍스트 표 스크린샷](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

텍스트는 전체 형식 지정 컨트롤을 제공하는 Markdown 컨트롤을 통해 추가됩니다. 여기에는 다양한 제목 및 글꼴 스타일, 하이퍼링크, 테이블 등이 포함됩니다.

편집 모드:

![편집 모드 텍스트 단계의 스크린샷.](./media/workbooks-text-visualizations/text-edit-mode.png)

미리 보기 모드:

![미리 보기 탭의 편집 모드 텍스트 단계 스크린샷.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>텍스트 컨트롤 추가

1. **편집** 도구 모음 항목을 클릭하여 통합 문서를 편집 모드로 전환합니다.
2. **텍스트 추가** 링크를 사용하여 통합 문서에 텍스트 컨트롤을 추가합니다.
3. 편집기 필드에 Markdown을 추가합니다.
4. 텍스트 스타일 옵션을 사용하여 일반 markdown과 Azure Portal의 표준 정보/경고/성공/오류 스타일로 래핑된 markdown 간에 전환할 수 있습니다.
5. **미리 보기** 탭을 사용하여 콘텐츠가 어떻게 표시되는지 확인할 수 있습니다. 편집하는 동안 미리 보기는 스크롤 막대 영역 내에 콘텐츠를 표시하여 크기를 제한합니다. 그러나 런타임에 markdown 콘텐츠는 스크롤 막대 없이 필요한 공간을 채우도록 확장됩니다.
6. **편집 완료** 단추를 선택하여 단계 편집을 완료합니다.

> [!TIP]
> 이 [Markdown 치트 시트](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)를 사용하여 다양한 서식 옵션에 대해 알아보세요.

## <a name="text-styles"></a>텍스트 스타일

텍스트 단계에 사용할 수 있는 텍스트 스타일은 다음과 같습니다.

| 스타일     | 설명                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | 추가 서식이 적용되지 않습니다.                                                      |
| `info`    | `ℹ` 또는 유사한 아이콘과 일반적으로 파란색 배경을 사용하는 포털의 ‘info’ 스타일입니다.      |
| `error`   | `❌` 또는 유사한 아이콘과 일반적으로 빨간색 배경을 사용하는 포털의 ‘error’ 스타일입니다.     |
| `success` | `✔` 또는 유사한 아이콘과 일반적으로 녹색 배경을 사용하는 포털의 ‘success’ 스타일입니다.  |
| `upsell`  | `🚀` 또는 유사한 아이콘과 일반적으로 보라색 배경을 사용하는 포털의 ‘upsell’ 스타일입니다. |
| `warning` | `⚠` 또는 유사한 아이콘과 일반적으로 파란색 배경을 사용하는 포털의 ‘warning’ 스타일입니다.   |

특정 스타일을 선택하는 대신 스타일의 원본으로 텍스트 매개 변수를 선택할 수도 있습니다. 매개 변수 값은 위의 텍스트 값 중 하나여야 합니다. 값이 없거나 값을 인식할 수 없으면 `plain` 스타일로 처리됩니다.

info 스타일 예:

![info 스타일의 모양을 보여 주는 스크린샷.](./media/workbooks-text-visualizations/text-preview-info-style.png)

warning 스타일 예:

![warning 스타일의 모양을 보여 주는 스크린샷.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>다음 단계

* [통합 문서에서 차트](workbooks-chart-visualizations.md)를 만드는 방법을 알아봅니다.
* [통합 문서에서 그리드](workbooks-grid-visualizations.md)를 만드는 방법을 알아봅니다.
