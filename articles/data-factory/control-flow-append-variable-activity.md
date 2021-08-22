---
title: Azure Data Factory의 변수 추가 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory 파이프라인에 정의된 기존 배열 변수에 값을 추가하기 위해 변수 추가 작업을 설정하는 방법 알아보기
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 4157ad2fc1cf3619b1fa4c55709f8828415f0dca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642566"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory의 변수 추가 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
변수 추가 작업을 사용하여 Data Factory 파이프라인에 정의된 기존 배열 변수에 값을 추가할 수 있습니다.

## <a name="type-properties"></a>형식 속성

속성 | 설명 | 필수
-------- | ----------- | --------
name | 파이프라인의 작업 이름 | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | 아니요
type | 활동 유형은 AppendVariable입니다. | 예
값 | 지정된 변수에 추가하는 데 사용되는 문자열 리터럴 또는 표현식 개체 값 | 예
variableName | 활동별로 수정될 변수의 이름, 변수는 ‘Array’ 유형이어야 합니다. | 예

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 설정 작업](control-flow-set-variable-activity.md)
