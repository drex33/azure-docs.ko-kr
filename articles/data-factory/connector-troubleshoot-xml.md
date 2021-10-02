---
title: XML 형식 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 XML 형식 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d4a6d8c26b9441663cf67a1ea9a837091a51a624
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391153"
---
# <a name="troubleshoot-the-xml-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 XML 형식 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse에서 XML 형식 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-xmlsinknotsupported"></a>오류 코드: XmlSinkNotSupported

- **메시지**: `Write data in XML format is not supported yet, choose a different format!`

- **원인**: 복사 작업에서 XML 데이터 세트가 싱크 데이터 세트로 사용되었습니다.

- **권장 사항**: 싱크 데이터 세트가 아닌 다른 형식으로 데이터 세트를 사용합니다.


## <a name="error-code-xmlattributecolumnnameconflict"></a>오류 코드: XmlAttributeColumnNameConflict

- **메시지**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **원인**: 특성 접두사가 사용되었으며 이로 인해 충돌이 발생했습니다.

- **권장 사항**: “attributePrefix” 속성에 다른 값을 설정합니다.


## <a name="error-code-xmlvaluecolumnnameconflict"></a>오류 코드: XmlValueColumnNameConflict

- **메시지**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **원인**: 자식 요소 이름 중 하나가 요소 값의 열 이름으로 사용되었습니다.

- **권장 사항**: “valueColumn” 속성에 다른 값을 설정합니다.


## <a name="error-code-xmlinvalid"></a>오류 코드: XmlInvalid

- **메시지**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **원인**: 입력 XML 파일의 형식이 잘못되었습니다.

- **권장 사항**: XML 파일의 형식을 올바르게 수정합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
