---
title: 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics의 커넥터 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 9d27c292d044b39f841f86906deaf386357df7c7
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062764"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>Azure Data Factory 및 Azure Synapse Analytics 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse Analytics 커넥터 문제를 해결하는 방법을 설명합니다.  

## <a name="connector-specific-problems"></a>커넥터 관련 문제

각 커넥터에 대한 문제 해결 페이지를 참조하여 해당 원인에 대한 설명 및 해결 권장 사항과 관련된 문제를 확인할 수 있습니다.

- [Azure Blob Storage](connector-troubleshoot-azure-blob-storage.md)
- [Azure Cosmos DB(SQL API 커넥터 포함)](connector-troubleshoot-azure-cosmos-db.md)
- [Azure Data Lake(Gen1 및 Gen2)](connector-troubleshoot-azure-data-lake.md)
- [PostgreSQL용 Azure 데이터베이스](connector-troubleshoot-postgresql.md)
- [Azure Files 스토리지](connector-troubleshoot-azure-files.md)
- [Azure Synapse Analytics, Azure SQL Database 및 SQL Server](connector-troubleshoot-synapse-sql.md)
- [DB2](connector-troubleshoot-db2.md)
- [구분된 텍스트 형식](connector-troubleshoot-delimited-text.md)
- [Dynamics 365, Dataverse(Common Data Service) 및 Dynamics CRM](connector-troubleshoot-dynamics-dataverse.md)
- [FTP, SFTP 및 HTTP](connector-troubleshoot-ftp-sftp-http.md)
- [Hive](connector-troubleshoot-hive.md)
- [Oracle](connector-troubleshoot-oracle.md)
- [ORC 형식](connector-troubleshoot-orc.md)
- [Parquet 형식](connector-troubleshoot-parquet.md)
- [REST (영문)](connector-troubleshoot-rest.md)
- [SharePoint Online 목록](connector-troubleshoot-sharepoint-online-list.md)
- [XML 형식](connector-troubleshoot-xml.md)

## <a name="general-copy-activity-errors"></a>일반 복사 작업 오류

아래 오류는 복사 활동에 일반적이며 커넥터에서 발생할 수 있습니다.

### <a name="error-code-jrenotfound"></a>오류 코드: JreNotFound

- **메시지**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **원인**: 자체 호스팅 IR이 Java 런타임을 찾을 수 없습니다. 특정 원본을 읽으려면 Java 런타임이 필요합니다.

- **권장 사항**: 통합 런타임 환경을 확인합니다. [자체 호스팅 통합 런타임 사용](./format-parquet.md#using-self-hosted-integration-runtime)을 참조하세요.


### <a name="error-code-wildcardpathsinknotsupported"></a>오류 코드: WildcardPathSinkNotSupported

- **메시지**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **원인**: 싱크 데이터 세트가 와일드카드 값을 지원하지 않습니다.

- **권장 사항**: 싱크 데이터 세트를 확인하고 와일드카드 값을 사용하지 않고 경로를 다시 작성합니다.


### <a name="fips-issue"></a>FIPS 문제

- **증상**: FIPS 지원 자체 호스팅 IR 머신에서 다음과 같은 오류 메시지와 함께 복사 작업이 실패했습니다. `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **원인**: 이 오류는 Azure Blob, SFTP 등과 같은 커넥터를 사용하여 데이터를 복사하는 경우에 발생할 수 있습니다. FIPS(Federal Information Processing Standards)는 사용이 허용되는 특정 암호화 알고리즘 세트를 정의합니다. 머신에서 FIPS 모드가 사용하도록 설정된 경우, 일부 시나리오에서 복사 작업이 사용하는 일부 암호화 클래스가 차단됩니다.

- **해결 방법**: [Microsoft에서 더 이상 “FIPS 모드”를 권장하지 않는 이유](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)를 알아보고 자체 호스팅 IR 머신에서 FIPS를 사용하지 않을 수 있는지 검토합니다.

    또는 FIPS를 우회하여 활동 실행이 성공적으로 진행되도록 하려는 경우에는 다음을 수행합니다.

    1. 자체 호스팅 IR이 설치된 폴더를 엽니다. 경로는 보통 *C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared* 입니다.

    2. *diawp.exe.config* 파일을 열고 `<runtime>` 섹션의 끝에 다음과 같이 `<enforceFIPSPolicy enabled="false"/>`를 추가합니다.

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="FIPS가 사용하지 않도록 설정된 diawp.exe.config 파일의 섹션을 보여 주는 스크린샷.":::

    3. 파일을 저장하고 자체 호스팅 IR 머신을 다시 시작합니다.

### <a name="error-code-jniexception"></a>오류 코드: JniException

- **메시지**: `An error occurred when invoking Java Native Interface.`

- **원인**: 오류 메시지에 "JVM을 만들 수 없습니다. JNI 반환 코드 [-6][JNI 호출 실패: 잘못된 인수]"가 포함된 경우 가능한 원인은 일부 잘못된(전역) 인수가 설정되어 있으므로 JVM을 만들 수 없다는 것입니다.

- **권장 사항**: 자체 호스팅 통합 런타임의 ‘각 노드’를 호스트하는 머신에 로그인합니다. 시스템 변수가 `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`와 같이 올바르게 설정되었는지 확인합니다. 모든 통합 런타임 노드를 다시 시작한 다음, 파이프라인을 다시 실행합니다.

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>오류 코드: GetOAuth2AccessTokenErrorResponse

- **메시지**: `Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **원인**: 클라이언트 ID 또는 클라이언트 암호가 잘못되었으며 권한 부여 서버에서 인증이 실패했습니다.

- **권장 사항**: 권한 부여 서버의 OAuth2 클라이언트 자격 증명 흐름 설정을 모두 수정합니다.

### <a name="error-code-failedtogetoauth2accesstoken"></a>오류 코드: FailedToGetOAuth2AccessToken

- **메시지**: `Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **원인**: OAuth2 클라이언트 자격 증명 흐름 설정이 잘못되었습니다.

- **권장 사항**: 권한 부여 서버의 OAuth2 클라이언트 자격 증명 흐름 설정을 모두 수정합니다.

### <a name="error-code-oauth2accesstokentypenotsupported"></a>오류 코드: OAuth2AccessTokenTypeNotSupported

- **메시지**: `The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **원인**: 권한 부여 서버가 지원되지 않습니다.

- **권장 사항**: 지원되는 토큰 형식의 토큰을 반환할 수 있는 권한 부여 서버를 사용합니다.

### <a name="error-code-oauth2clientidcolonnotallowed"></a>오류 코드: OAuth2ClientIdColonNotAllowed

- **메시지**: `The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **원인**: 클라이언트 ID에 잘못된 문자 콜론(`:`)이 포함되어 있습니다.

- **권장 사항**: 유효한 클라이언트 ID를 사용합니다.

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>오류 코드: ManagedIdentityCredentialObjectNotSupported

- **메시지**: `Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **권장 사항**: 지원되는 버전을 확인하고 통합 런타임을 더 높은 버전으로 업그레이드합니다.

### <a name="error-code-querymissingformatsettingsindataset"></a>오류 코드: QueryMissingFormatSettingsInDataset

- **메시지**: `The format settings are missing in dataset %dataSetName;.`

- **원인**: 데이터 세트 형식이 이진이며 지원되지 않습니다.

- **권장 사항**: 대신 DelimitedText, Json, Avro, Orc 또는 Parquet 데이터 세트를 사용합니다.

- **원인**: 파일 스토리지의 경우 데이터 세트에 형식 설정이 없습니다.

- **권장 사항**: 데이터 세트에서 "이진 복사"를 선택 취소하고 올바른 형식 설정으로 지정합니다.

### <a name="error-code-queryunsupportedcommandbehavior"></a>오류 코드: QueryUnsupportedCommandBehavior

- **메시지**: `The command behavior "%behavior;" is not supported.`

- **권장 사항**: 명령 동작을 미리 보기 또는 GetSchema API 요청 URL에 대한 매개 변수로 추가하지 마세요.

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>오류 코드: DataConsistencyFailedToGetSourceFileMetadata

- **메시지**: `Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **원인**: 싱크 데이터 저장소에서 일시적인 문제가 발생했거나 싱크 데이터 저장소에서 메타데이터를 검색할 수 없습니다.

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>오류 코드: DataConsistencyFailedToGetSinkFileMetadata

- **메시지**: `Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **원인**: 싱크 데이터 저장소에서 일시적인 문제가 발생했거나 싱크 데이터 저장소에서 메타데이터를 검색할 수 없습니다.

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>오류 코드: DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **메시지**: `Data consistency validation is not supported in current copy activity settings.`

- **원인**: 데이터 일관성 유효성 검사는 직접 이진 복사 시나리오에서만 지원됩니다.

- **권장 사항**: 복사 작업 페이로드에서 'validateDataConsistency' 속성을 제거합니다.

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>오류 코드: DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **메시지**: `'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **권장 사항**: 지원되는 통합 런타임 버전을 확인하고 더 높은 버전으로 업그레이드하거나 복사 활동에서 'validateDataConsistency' 속성을 제거합니다.

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>오류 코드: SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'fileMissing'을 제거합니다.

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>오류 코드: SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'dataInconsistency'를 제거합니다.

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>오류 코드: SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'fileforbidden'을 제거합니다.

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>오류 코드: SkipForbiddenFileNotSupportedForThisConnector

- **메시지**: `Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'fileforbidden'을 제거합니다.

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>오류 코드: SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'invalidFileName'을 제거합니다.

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>오류 코드: SkipInvalidFileNameNotSupportedForSource

- **메시지**: `Skip invalid file name is not supported for '%connectorName;' source.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'invalidFileName'을 제거합니다.

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>오류 코드: SkipInvalidFileNameNotSupportedForSink

- **메시지**: `Skip invalid file name is not supported for '%connectorName;' sink.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'invalidFileName'을 제거합니다.

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>오류 코드: SkipAllErrorFileNotSupportedForNonBinaryCopy

- **메시지**: `Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'allErrorFile'을 제거합니다.

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>오류 코드: DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **메시지**: `'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **권장 사항**: 'deleteFilesAfterCompletion' 설정을 제거하거나 직접 이진 복사를 사용합니다.

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>오류 코드: DeleteFilesAfterCompletionNotSupportedForThisConnector

- **메시지**: `'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **권장 사항**: 복사 활동 페이로드에서 'deleteFilesAfterCompletion' 설정을 제거합니다.

### <a name="error-code-failedtodownloadcustomplugins"></a>오류 코드: FailedToDownloadCustomPlugins

- **메시지**: `Failed to download custom plugins.`

- **원인**: 잘못된 다운로드 링크 또는 일시적인 연결 문제입니다.

- **권장 사항**: 일시적인 문제라는 메시지가 표시되면 다시 시도합니다. 문제가 지속되면 지원 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)