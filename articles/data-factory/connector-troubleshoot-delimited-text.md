---
title: 구분 기호로 분리 된 텍스트 형식 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 분리 된 텍스트 형식 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 42346a5922b0f607e467e495455166057248db5e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391122"
---
# <a name="troubleshoot-the-delimited-text-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 분리 된 텍스트 형식 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse에서 구분 된 텍스트 formatL 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-delimitedtextcolumnnamenotallownull"></a>오류 코드: DelimitedTextColumnNameNotAllowNull

- **메시지**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **원인**: 작업에서 ‘firstRowAsHeader’가 설정된 경우 첫 번째 행이 열 이름으로 사용됩니다. 이 오류는 첫 번째 행이 빈 값을 포함하고 있음을 의미합니다(예: ‘ColumnA, ColumnB’).

- **권장 사항**:  첫 번째 행을 확인하여 비어 있다면 값을 수정합니다.


## <a name="error-code-delimitedtextmorecolumnsthandefined"></a>오류 코드: DelimitedTextMoreColumnsThanDefined

- **메시지**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

  | 원인 분석                                               | 권장                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 문제가 있는 행의 열 개수가 첫 번째 행의 열 개수보다 큽니다. 데이터 문제 또는 잘못된 열 구분 기호나 따옴표 문자 설정으로 인한 문제일 수 있습니다. | 오류 메시지에서 행 개수를 확인하고 행의 열을 확인한 후 데이터를 수정합니다. |
  | 예상 열 개수가 오류 메시지에 “1”로 표시된다면 잘못된 압축 또는 형식 설정을 지정했기 때문에 파일을 잘못 구문 분석한 것일 수 있습니다. | 형식 설정이 원본 파일과 일치하는지 확인합니다. |
  | 원본이 폴더인 경우 지정된 폴더에 있는 파일의 스키마가 다를 수 있습니다. | 지정된 폴더의 파일이 동일한 스키마를 갖는지 확인합니다. |

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
