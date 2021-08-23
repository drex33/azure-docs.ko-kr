---
title: Azure Data Factory 엔터티 이름 지정 규칙
description: 데이터 팩터리 엔터티에 대한 이름 지정 규칙을 설명합니다.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 0e469df360fed09924dbf6759bdd84c15671c7f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536289"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 이름 지정 규칙

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

다음 테이블에서 데이터 팩터리 아티팩트에 대한 이름 지정 규칙을 제공합니다.

| 이름 | 이름 고유성 | 유효성 검사 |
|:--- |:--- |:--- |
| Data Factory | Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. 즉, `MyDF` 및 `mydf`는 같은 데이터 팩터리를 의미합니다. |<ul><li>각 데이터 팩터리는 정확히 하나의 Azure 구독에 연결됩니다.</li><li>개체 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.</li><li>모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다. 컨테이너 이름에서 연속 대시는 허용되지 않습니다.</li><li>이름은 3-63자까지 가능합니다.</li></ul> |
| 연결된 서비스/데이터 세트/파이프라인 흐름 | 데이터 팩터리 내에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>경고 이름은 문자로 시작해야 합니다.</li><li>허용되지 않는 문자: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li><li>대시("-")는 연결된 서비스, 데이터 흐름 및 데이터 세트의 이름에서만 허용되지 않습니다.</li></ul>  |
| 통합 런타임 |데이터 팩터리 내에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>통합 런타임 이름에는 문자, 숫자 및 대시(-)만 사용할 수 있습니다.</li><li>첫 번째 및 마지막 자리는 문자 또는 숫자여야 합니다. 모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다.</li><li>통합 런타임 이름에는 연속해서 대시를 사용할 수 없습니다. </li></ul> |
| 데이터 흐름 변환 | 데이터 흐름 내에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | <ul><li>데이터 흐름 변환 이름에는 문자와 숫자만 사용할 수 있습니다.</li><li>첫 글자는 문자여야 합니다. </li></ul> |
| 리소스 그룹 |Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | 자세한 내용은 [Azure 명명 규칙 및 제한 사항](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)을 참조하세요. |
| 파이프라인 매개 변수 & 변수  |파이프라인 내에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | <ul><li>이전 버전과의 호환성 때문에 매개 변수 이름 및 변수 이름에 대한 유효성 검사는 고유성으로 제한됩니다.</li><li>매개 변수 또는 변수를 사용하여 엔터티 이름(예: 연결된 서비스)을 참조할 때 엔터티 명명 규칙이 적용됩니다.</li><li>파이프라인 매개 변수 및 변수의 이름을 지정하기 위해 데이터 흐름 변환 명명 규칙을 따르는 것이 좋습니다.</li></ul> |

## <a name="next-steps"></a>다음 단계

[빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md) 문서의 단계별 지침에 따라 데이터 팩터리를 만드는 방법을 알아봅니다. 
