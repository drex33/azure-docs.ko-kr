---
title: 포함 파일
description: 포함 파일
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: e9ba8efefd2238b1aadc2fbeaf17a286e0714bcd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484272"
---
Azure Policy의 각 개체 형식에 대한 최대 수가 있습니다. 정의의 경우 _범위_ 항목은 [관리 그룹](../articles/governance/management-groups/overview.md) 또는 구독을 의미합니다.
할당 및 예외의 경우 _범위_ 항목은 [관리 그룹](../articles/governance/management-groups/overview.md), 구독, 리소스 그룹 또는 개별 리소스를 의미합니다.

| Where | 대상 | 최대 수 |
|---|---|---|
| 범위 | 정책 정의 | 500 |
| 범위 | 이니셔티브 정의 | 200 |
| 테넌트 | 이니셔티브 정의 | 2,500 |
| 범위 | 정책 또는 이니셔티브 할당 | 200 |
| Scope | 예외 | 1000 |
| 정책 정의 | 매개 변수 | 20 |
| 이니셔티브 정의 | 정책 | 1000 |
| 이니셔티브 정의 | 매개 변수 | 300 |
| 정책 또는 이니셔티브 할당 | 제외(notScopes) | 400 |
| 정책 규칙 | 중첩된 조건부 | 512 |
| 수정 작업 | 리소스 | 500 |
