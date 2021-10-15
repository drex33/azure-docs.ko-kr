---
title: Parquet format 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 Parquet format 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: b12666fdd66b3c85702b7222f2f2edca7136e323
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062761"
---
# <a name="troubleshoot-the-parquet-format-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse의 Parquet format 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 Parquet format 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-parquetjavainvocationexception"></a>오류 코드: ParquetJavaInvocationException

- **메시지**: `An error occurred when invoking java, message: %javaException;.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 “java.lang.OutOfMemory”, “Java 힙 공간” 및 “doubleCapacity”라는 문자열이 포함된 경우 일반적으로 이전 버전의 통합 런타임에 메모리 관리 문제가 있는 것입니다. | 자체 호스팅 IR을 사용하고 있으며 버전이 3.20.7159.1 이전 버전인 경우 최신 버전으로 업그레이드하는 것이 좋습니다. |
    | 오류 메시지에 “java.lang.OutOfMemory”라는 문자열이 포함된 경우 통합 런타임에 파일을 처리할 수 있는 충분한 리소스가 없는 것입니다. | Integration Runtime의 동시 실행을 제한합니다. 자체 호스팅 IR의 경우 메모리가 8GB보다 크거나 같은 강력한 머신으로 스케일 업합니다. |
    | 오류 메시지에 “NullPointerReference”라는 문자열이 포함된 경우 일시적인 오류가 원인일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |

## <a name="error-code-parquetinvalidfile"></a>오류 코드: ParquetInvalidFile

- **메시지**: `File is not a valid Parquet file.`

- **원인**: 이 문제는 Parquet 파일 문제입니다.

- **권장 사항**: 입력이 유효한 Parquet 파일인지 확인합니다.

## <a name="error-code-parquetnotsupportedtype"></a>오류 코드: ParquetNotSupportedType

- **메시지**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **원인**: Azure Data Factory 및 Synapse 파이프라인에서는 Parquet 형식이 지원되지 않습니다.

- **권장 사항**: [복사 작업을 통해 지원되는 파일 형식 및 압축 코덱](./supported-file-formats-and-compression-codecs.md)으로 이동하여 원본 데이터를 재차 확인합니다.

## <a name="error-code-parquetmisseddecimalprecisionscale"></a>오류 코드: ParquetMissedDecimalPrecisionScale

- **메시지**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **원인**: 숫자 전체 자릿수와 소수 자릿수가 구문 분석되었으나 해당 정보가 제공되지 않았습니다.

- **권장 사항**: 원본이 올바른 전체 자릿수 및 소수 자릿수 정보를 반환하지 않습니다. 문제 열에서 정보를 확인하세요.

## <a name="error-code-parquetinvaliddecimalprecisionscale"></a>오류 코드: ParquetInvalidDecimalPrecisionScale

- **메시지**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **원인**: 스키마가 유효하지 않습니다.

- **권장 사항**: 문제 열에서 전체 자릿수와 소수 자릿수를 확인합니다.

## <a name="error-code-parquetcolumnnotfound"></a>오류 코드: ParquetColumnNotFound

- **메시지**: `Column %column; does not exist in Parquet file.`

- **원인**: 원본 스키마가 싱크 스키마와 일치하지 않습니다.

- **권장 사항**: 작업에서 매핑을 확인합니다. 원본 열이 올바른 싱크 열과 매핑될 수 있는지 확인합니다.

## <a name="error-code-parquetinvaliddataformat"></a>오류 코드: ParquetInvalidDataFormat

- **메시지**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **원인**: 데이터를 mappings.source에 지정된 형식으로 변환할 수 없습니다.

- **권장 사항**: 원본 데이터를 재차 확인하거나 복사 작업 열 매핑에서 이 열에 올바른 데이터 형식을 지정합니다. 자세한 내용은 [복사 활동에서 지원되는 파일 형식 및 압축 코덱](./supported-file-formats-and-compression-codecs.md)을 참조하세요.

## <a name="error-code-parquetdatacountnotmatchcolumncount"></a>오류 코드: ParquetDataCountNotMatchColumnCount

- **메시지**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **원인**: 원본 열 개수와 싱크 열 개수가 일치하지 않습니다.

- **권장 사항**: ‘mapping’에서 원본 열 개수가 싱크 열 개수와 일치하는지 재차 확인합니다.

## <a name="error-code-parquetdatatypenotmatchcolumntype"></a>오류 코드: ParquetDataTypeNotMatchColumnType

- **메시지**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **원인**: 원본의 데이터를 싱크에 정의된 형식으로 변환할 수 없습니다.

- **권장 사항**: mapping.sink에 올바른 형식을 지정합니다.

## <a name="error-code-parquetbridgeinvaliddata"></a>오류 코드: ParquetBridgeInvalidData

- **메시지**: `%message;`

- **원인**: 데이터 값이 제한을 초과했습니다.

- **해결 방법**: 작업을 다시 시도합니다. 문제가 지속되면 지원으로 문의하세요.

## <a name="error-code-parquetunsupportedinterpretation"></a>오류 코드: ParquetUnsupportedInterpretation

- **메시지**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **원인**: 이 시나리오는 지원되지 않습니다.

- **권장 사항**:  'ParquetInterpretFor'는 'sparkSql'이 아니어야 합니다.

## <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>오류 코드: ParquetUnsupportFileLevelCompressionOption

- **메시지**: `File level compression is not supported for Parquet.`

- **원인**: 이 시나리오는 지원되지 않습니다.

- **권장 사항**: 페이로드에서 ‘CompressionType’을 제거합니다.

## <a name="error-code-usererrorjniexception"></a>오류 코드: UserErrorJniException

- **메시지**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **원인**: 일부 비정상(전역) 인수가 설정되어 있어 JVM(Java Virtual Machine)을 만들 수 없습니다.

- **권장 사항**: 자체 호스팅 IR의 ‘각 노드’를 호스트하는 머신에 로그인합니다. 시스템 변수가 `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`와 같이 올바르게 설정되었는지 확인합니다. 모든 IR 노드를 다시 시작한 다음 파이프라인을 다시 실행합니다.

## <a name="arithmetic-overflow"></a>산술 연산 오버플로

- **증상**: Parquet 파일을 복사할 때 `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`이라는 오류 메시지가 표시됩니다.

- **원인**: Oracle에서 Parquet로 파일을 복사하는 경우 현재 38개 이하의 소수 자릿수와 20자 이하의 정수 부분 길이만 지원됩니다. 

- **해결 방법**: 임시 방편으로 이 문제가 있는 모든 열을 VARCHAR2로 변환할 수 있습니다.

## <a name="no-enum-constant"></a>열거형 상수 없음

- **증상**: 데이터를 Parquet 형식으로 복사할 때 `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 또는 `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` 오류 메시지가 표시됩니다.

- **원인**: 

    이 문제는 열 이름에 공백 또는 지원되지 않는 특수 문자(예: ;{}()\n\t=)가 있는 경우 발생할 수 있습니다. Parquet에서는 이러한 형식을 지원하지 않습니다. 

    예를 들어, *contoso(test)* 와 같은 열 이름은 [코드](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java)에 있는 괄호 안의 형식을 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");`과 같이 구문 분석합니다. 이러한 “test” 형식이 없어서 오류가 throw된 것입니다.

    지원되는 형식을 확인하려면 GitHub [apache/parquet-mr site](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)를 참조하세요.

- **해결 방법**: 

    다음을 재차 확인합니다.
    - 싱크 열 이름에 공백이 있습니다.
    - 공백이 있는 첫 번째 행이 열 이름으로 사용되었습니다.
    - OriginalType 형식이 지원됩니다. `,;{}()\n\t=`와 같은 특수 문자는 사용하지 않습니다. 

## <a name="error-code-parquetdatetimeexceedlimit"></a>오류 코드: ParquetDateTimeExceedLimit

- **메시지**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **원인**: datetime 값이 ‘0001-01-01 00:00:00’인 경우 율리우스력과 그레고리력의 차이가 원인일 수 있습니다. 자세한 내용은 [율리우스력 날짜와 그레고리력 날짜 간의 차이](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)를 참조하세요.

- **해결 방법**: 틱 값을 확인하고 datetime 값 ‘0001-01-01 00:00:00’을 사용하지 않습니다.

## <a name="error-code-parquetinvalidcolumnname"></a>오류 코드: ParquetInvalidColumnName

- **메시지**: `The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **원인**: 열 이름에 잘못된 문자가 있습니다.

- **해결 방법**: 열 매핑을 추가하거나 수정하여 싱크 열 이름을 유효하게 만듭니다.

## <a name="the-file-created-by-the-copy-data-activity-extracts-a-table-that-contains-a-varbinary-max-column"></a>데이터 복사 작업을 통해 만들어진 파일은 varbinary (max) 열이 포함 된 테이블을 추출 합니다.

- **증상**: 데이터 복사 작업에서 만든 Parquet 파일은 varbinary (max) 열이 포함 된 테이블을 추출 합니다.

- **원인**:이 문제는 대량 열을 읽는 Parquet mr 라이브러리 버그로 인해 발생 합니다. 

- **해결** 방법: 파일당 1000 행의 제한이 있는 작은 파일 (크기 < 1g)을 생성 하려고 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
