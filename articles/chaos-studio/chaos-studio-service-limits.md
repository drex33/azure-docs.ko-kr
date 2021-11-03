---
title: Azure 비정상 스튜디오 서비스 제한
description: Azure 비정상 스튜디오의 제한 및 사용 제한 이해
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.date: 11/01/2021
ms.topic: reference
ms.custom: ignite-fall-2021
ms.openlocfilehash: f9b5352597afb00b379363a2f151615a902dff8f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053748"
---
# <a name="azure-chaos-studio-service-limits"></a>Azure 비정상 스튜디오 서비스 제한
이 문서에서는 Azure 비정상 스튜디오의 서비스 제한을 제공 합니다. 
## <a name="experiment-and-target-limits"></a>실험 및 대상 제한

비정상 스튜디오는 개체 수, 작업 기간 및 데이터 보존에 제한을 적용 합니다.

| 제한 | 값 |
| -- | -- |
| 실험 당 작업 | 9 |
| 실험 당 분기 | 9 |
| 실험 당 단계 | 4 |
| 작업 기간 (시간) | 12 |
| 지역 및 구독 당 동시 실험 실행 | 5 |
| 총 실험 지속 시간 (시간) | 12 |
| 지역 및 구독 당 실험 수 | 500 |
| 작업 당 대상 수 | 50 |
| 대상 당 활성 에이전트 수 | 1,000 |
| 지역 및 구독 당 대상 수 | 10000 |

## <a name="api-throttling-limits"></a>API 제한 한도

비정상 스튜디오는 모든 ARM 작업에 제한을 적용 합니다. 한도를 초과 하는 요청은 제한 됩니다. 달리 지정 하지 않는 한 모든 요청 제한은 5 분 간격으로 적용 됩니다.

| 작업(Operation) | 요청 |
| -- | -- |
| Microsoft/감사/기록 | 100 |
| Microsoft. 감사/실험/읽기 | 300 |
| Microsoft/감사/삭제 | 100 |
| Microsoft. 감사/실험/시작/작업 | 20 |
| Microsoft. 감사 안/실험/취소/작업 | 100 |
| Microsoft. 감사/실험/상태/읽기 | 100 |
| Microsoft. n e t/실험/executionDetails/read | 100 |
| Microsoft. n e t/대상/쓰기 | 200 |
| Microsoft. n e t/대상/읽기 | 600 |
| Microsoft. n e t/대상/삭제 | 200 |
| Microsoft. n e t/대상/기능/쓰기 | 600 |
| Microsoft. n e t/대상/기능/읽기 | 1800 |
| Microsoft. n e t/대상/기능/삭제 | 600 |
| Microsoft. s e r/위치/targetTypes/read | 50 |
| Microsoft. 비정상/위치/targetTypes/capabilityTypes/read | 50 |
