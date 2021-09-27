---
title: 매핑 데이터 흐름 변환 개요
titleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름에서 사용할 수 있는 다양한 변환 개요
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 393e6dbc0f30e4a541408119db6f606f24023d5d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059742"
---
# <a name="mapping-data-flow-transformation-overview"></a>매핑 데이터 흐름 변환 개요

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

다음은 현재 매핑 데이터 흐름에서 지원되는 변환 목록입니다. 각 변환을 클릭하여 구성 세부 정보를 알아보세요.

| 속성 | 범주 | Description |
| ---- | -------- | ----------- |
| [집계](data-flow-aggregate.md) | 스키마 한정자 | 기존 열 또는 계산 열로 그룹화된 SUM, MIN, MAX, COUNT와 같은 다양한 유형의 집계를 정의합니다. | 
| [행 변경](data-flow-alter-row.md) | 행 한정자 | 행에 대한 삽입, 삭제, 업데이트 및 upsert 정책을 설정합니다. |
| [조건부 분할](data-flow-conditional-split.md) | 다중 입력/출력 | 일치 조건에 따라 데이터 행을 서로 다른 스트림으로 라우팅합니다. |
| [파생 열](data-flow-derived-column.md) | 스키마 한정자 | 데이터 흐름 식 언어를 사용하여 새 열을 생성하거나 기존 필드를 수정합니다. | 
| [Exists](data-flow-exists.md) | 다중 입력/출력 | 데이터가 다른 원본 또는 스트림에 있는지 확인합니다. | 
| [Filter](data-flow-filter.md) | 행 한정자 | 조건에 따라 행을 필터링합니다. |
| [평면화(flatten)](data-flow-flatten.md) | 스키마 한정자 |  JSON과 같은 계층 구조 내부의 배열 값을 가져와서 개별 행으로 언롤합니다. |
| [Join](data-flow-join.md) | 다중 입력/출력 |  두 원본 또는 스트림의 데이터를 결합합니다. |
| [조회](data-flow-lookup.md) | 다중 입력/출력 | 다른 원본에서 데이터를 참조합니다. |
| [새 분기](data-flow-new-branch.md) | 다중 입력/출력 | 동일한 데이터 스트림에 대해 여러 작업 및 변환 세트를 적용합니다. |
| [Parse](data-flow-new-branch.md) | 포맷터 | 데이터 스트림에서 JSON, 구분된 텍스트, 또는 XML 형식 텍스트의 문자열인 텍스트 열을 구문 분석합니다. |
| [피벗](data-flow-pivot.md) | 스키마 한정자 | 하나 이상의 그룹화 열이 개별 열로 변환된 고유한 행 값을 갖는 집계입니다. |
| [Rank](data-flow-rank.md) | 스키마 한정자 | 정렬 조건을 기준으로 정렬된 순위를 생성합니다. |
| [Select](data-flow-select.md) | 스키마 한정자 | 열 및 스트림 이름의 별칭을 지정하고, 열을 삭제하거나 다시 정렬합니다. |
| [싱크](data-flow-sink.md) | - | 데이터의 최종 대상입니다. |
| [Sort](data-flow-sort.md) | 행 한정자 | 현재 데이터 스트림에서 들어오는 행을 정렬합니다. |
| [원본](data-flow-source.md) | - | 데이터 흐름에 대한 데이터 원본입니다. |
| [서로게이트 키](data-flow-surrogate-key.md) | 스키마 한정자 | 증가하는 업무 외 임의 키 값을 추가합니다. |
| [Union](data-flow-union.md) | 다중 입력/출력 | 여러 데이터 스트림을 수직으로 결합합니다. |
| [피벗 해제](data-flow-unpivot.md) | 스키마 한정자 | 열을 행 값으로 피벗합니다. |
| [창](data-flow-window.md) | 스키마 한정자 |  데이터 스트림에서 창 기반 열 집계를 정의합니다. |
| [Parse](data-flow-parse.md) | 스키마 한정자 |  Json 또는 구분된 텍스트로 열 데이터를 구문 분석합니다. |
