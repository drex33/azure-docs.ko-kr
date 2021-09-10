---
title: Azure Digital Twins 쿼리 언어 참조 - WHERE 절
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 쿼리 언어 WHERE 절에 대한 참조 설명서
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: f9be1425ae614c266f17a95668faf7fb9d21d2f1
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492832"
---
# <a name="azure-digital-twins-query-language-reference-where-clause"></a>Azure Digital Twins 쿼리 언어 참조: WHERE 절

이 문서에는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md)의 **WHERE 절** 에 대한 참조 정보가 포함되어 있습니다.

WHERE 절은 쿼리의 마지막 부분입니다. 특정 조건에 따라 반환되는 항목을 필터링하는 데 사용됩니다.

이 절은 쿼리하는 동안 선택 사항입니다.

## <a name="core-syntax-where"></a>핵심 구문: WHERE

WHERE 절은 부울 조건과 함께 쿼리 결과를 필터링하는 데 사용됩니다. 

조건은 부울 결과로 계산되는 [함수](reference-query-functions.md)일 수 있습니다. 비교 contains-type [연산자](reference-query-operators.md)와 함께 트윈 및 관계 속성(`.`로 액세스)을 사용하여 고유한 부울 문을 만들 수도 있습니다.

### <a name="syntax"></a>구문

속성 및 연산자 사용:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereSyntax":::

함수 사용:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionSyntax":::

### <a name="arguments"></a>인수

`Boolean` 값으로 평가하는 조건입니다.

### <a name="examples"></a>예

다음은 속성 및 연산자를 사용하는 예제입니다. 다음 쿼리는 WHERE 절에서 `$dtId` 값이 Room1인 트윈만 반환하도록 지정합니다.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereExample":::

다음은 함수를 사용하는 예제입니다. 다음 쿼리에서는 `IS_OF_MODEL` 함수를 사용하여 WHERE 절에서 `dtmi:sample:Room;1` 모델이 있는 트윈만 반환하도록 지정합니다. `IS_OF_MODEL` 함수에 대한 자세한 내용은 [Azure Digital Twins 쿼리 언어 참조: Functions](reference-query-functions.md#is_of_model)를 참조하세요.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionExample":::