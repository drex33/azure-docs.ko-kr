---
title: ORC format 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 ORC format 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 973322c2ec7747fc5233eb897b302bfc910fee1b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391147"
---
# <a name="troubleshoot-the-orc-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse의 ORC format 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 ORC format 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-orcjavainvocationexception"></a>오류 코드: OrcJavaInvocationException

- **메시지**: `An error occurred when invoking Java, message: %javaException;.`
- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 “java.lang.OutOfMemory”, “Java 힙 공간” 및 “doubleCapacity”라는 문자열이 포함된 경우 일반적으로 이전 버전의 통합 런타임에 메모리 관리 문제가 있는 것입니다. | 자체 호스팅 통합 런타임을 사용 중인 경우 최신 버전으로 업그레이드하는 것이 좋습니다. |
    | 오류 메시지에 “java.lang.OutOfMemory”라는 문자열이 포함된 경우 통합 런타임에 파일을 처리할 수 있는 충분한 리소스가 없는 것입니다. | Integration Runtime의 동시 실행을 제한합니다. 자체 호스팅 IR의 경우 메모리가 8GB보다 크거나 같은 강력한 머신으로 스케일 업합니다. |
    |오류 메시지에 “NullPointerReference”라는 문자열이 포함된 경우 일시적인 오류가 원인일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |
    | 오류 메시지에 “BufferOverflowException”이라는 문자열이 포함된 경우 일시적인 오류가 원인일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |
    | 오류 메시지에 “java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can’t be cast to org.apache.hadoop.io.Text”라는 문자열이 포함된 경우 Java 런타임의 형식 전환 문제가 원인일 수 있습니다. 일반적으로 이는 Java 런타임에서 원본 데이터를 처리할 수 없음을 의미합니다. | 이 문제는 데이터 문제입니다. ORC 형식 데이터에서 char 또는 varchar 대신 문자열을 사용해 보세요. |

## <a name="error-code-orcdatetimeexceedlimit"></a>오류 코드: OrcDateTimeExceedLimit

- **메시지**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **원인**: datetime 값이 ‘0001-01-01 00:00:00’인 경우 [율리우스력과 그레고리력](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)의 차이가 원인일 수 있습니다.

- **권장 사항**: 틱 값을 확인하고 datetime 값 ‘0001-01-01 00:00:00’을 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
