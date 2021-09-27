---
title: 변수 작업 추가
titleSuffix: Azure Data Factory & Azure Synapse
description: 변수 추가 활동을 설정하여 Data Factory 또는 Synapse Analytics 파이프라인에 정의된 기존 배열 변수에 값을 추가하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: 9d2a081535f571b139a5cb15cdb85b37e2c3af97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811703"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics 변수 추가 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
변수 추가 활동을 사용하여 Data Factory 또는 Synapse Analytics 파이프라인에 정의된 기존 배열 변수에 값을 추가합니다.

## <a name="type-properties"></a>형식 속성

속성 | 설명 | 필수
-------- | ----------- | --------
name | 파이프라인의 작업 이름 | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | 아니요
type | 활동 유형은 AppendVariable입니다. | 예
값 | 지정된 변수에 추가하는 데 사용되는 문자열 리터럴 또는 표현식 개체 값 | 예
variableName | 활동별로 수정될 변수의 이름, 변수는 ‘Array’ 유형이어야 합니다. | 예

## <a name="next-steps"></a>다음 단계
관련 제어 흐름 활동에 대해 알아봅니다. 

- [변수 설정 작업](control-flow-set-variable-activity.md)
