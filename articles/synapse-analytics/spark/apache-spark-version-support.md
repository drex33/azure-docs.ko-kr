---
title: Apache Spark 버전 지원
description: 지원되는 Spark, Scala, Python, .NET 버전
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/22/2021
ms.author: midesa
ms.openlocfilehash: 9f13a0797caa13f0d4367357308809c97bde0b17
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577773"
---
# <a name="azure-synapse-runtimes"></a>Azure Synapse 런타임
Azure Synapse용 Apache Spark 풀은 런타임을 사용하여 필수 구성 요소 버전, Azure Synapse 최적화, 패키지 및 커넥터를 특정 Apache Spark 버전과 연결합니다. 이러한 런타임은 새로운 향상된 기능 및 패치를 포함하도록 주기적으로 업그레이드됩니다. 

서버리스 Apache Spark 풀을 만들 때 해당 Apache Spark 버전을 선택할 수 있습니다. 이에 따라 풀은 연결된 런타임 구성 요소 및 패키지와 함께 미리 설치됩니다. 이러한 런타임에는 다음과 같은 이점이 있습니다.

- 더 빠른 세션 시작 시간
- 특정 Apache Spark 버전과의 호환성 테스트
- 인기 있는 호환 커넥터 및 오픈 소스 패키지에 대한 액세스

> [!NOTE]
> - 유지 관리 업데이트는 지정된 서버리스 Apache Spark 풀의 새 세션에 자동으로 적용됩니다. 
> - 새 런타임 버전을 사용할 때 애플리케이션이 제대로 실행되는지 테스트하고 유효성을 검사해야 합니다.

## <a name="supported-azure-synapse-runtime-releases"></a>지원되는 Azure Synapse 런타임 릴리스 
다음 표에서는 지원되는 Azure Synapse 런타임 릴리스의 런타임 이름, Apache Spark 버전 및 릴리스 날짜를 나열합니다.

|  런타임 이름  | 릴리스 날짜 |  릴리스 스테이지 |
| ----- | ----- | ----- |
| [Apache Spark 2.4용 Azure Synapse 런타임](./apache-spark-24-runtime.md) | 2020년 12월 15일 | GA|
| [Apache Spark 3.1용 Azure Synapse 런타임](./apache-spark-3-runtime.md) | 2021년 5월 26일 | GA |

## <a name="runtime-release-stages"></a>런타임 릴리스 단계

## <a name="preview-runtimes"></a>런타임 미리 보기
AzureSynapse Analytic에서 GA(일반 공급)되기 전에 기능에 대한 피드백을 평가하고 공유할 수 있는 미리 보기를 제공합니다. 런타임은 미리 보기에서 사용할 수 있지만 새 의존성 및 구성 요소 버전이 도입될 수 있습니다. 지원 SLA는 미리 보기 런타임에 적용되지 않습니다. 

## <a name="generally-available-runtimes"></a>일반 사용 가능한 런타임
GA(일반 공급) 런타임은 모든 고객에게 공개되며 프로덕션용으로 사용할 수 있습니다. 일반적으로 런타임을 사용할 수 있게 되면 보안 수정과 안정성 향상을 백포트할 수 있습니다. 또한 기본 종속성 또는 구성 요소 버전을 변경하지 않는 경우에만 새 구성 요소가 도입됩니다. 
