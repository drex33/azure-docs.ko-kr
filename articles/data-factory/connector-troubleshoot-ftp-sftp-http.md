---
title: FTP, SFTP 및 HTTP 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics FTP, SFTP 및 HTTP 커넥터와 관련된 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 53c70456f754dd451d3bb3cfa3bed0c6ddac3461
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391117"
---
# <a name="troubleshoot-the-ftp-sftp-and-http-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse FTP, SFTP 및 HTTP 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse FTP, SFTP 및 HTTP 커넥터의 일반적인 문제를 해결하기 위한 제안을 제공합니다.

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>오류 코드: FtpFailedToConnectToFtpServer

- **메시지**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **원인**: FTP 서버에 대해 연결된 서비스 유형이 잘못 사용되었을 수 있습니다(예: SFTP(보안 FTP) 연결된 서비스를 사용하여 FTP 서버를 연결한 경우).

- **권장 사항**: 대상 서버의 포트를 확인합니다. FTP는 포트 21을 사용합니다.

### <a name="error-code-ftpfailedtoreadftpdata"></a>오류 코드: FtpFailedToReadFtpData

- **메시지**: `Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **원인**: 데이터 팩터리 또는 Synapse 파이프라인에서 지원하는 수동 모드에서 데이터 전송에 대해 1024에서 65535 사이의 포트 범위가 열려 있지 않습니다.

- **권장 사항**: 대상 서버의 방화벽 설정을 확인합니다. 포트 1024-65535 또는 FTP 서버에 지정된 포트 범위를 SHIR/Azure IR IP 주소로 엽니다.

## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>오류 코드: SftpOperationFail

- **메시지**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **원인**: SFTP 작업에 문제가 있습니다.

- **권장 사항**: SFTP에서 오류 세부 정보를 확인합니다.


### <a name="error-code-sftprenameoperationfail"></a>오류 코드: SftpRenameOperationFail

- **메시지**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **원인**: SFTP 서버에서 temp 파일의 이름 바꾸기를 지원하지 않습니다.

- **권장 사항**: 복사 싱크에서 “useTempFileRename”을 false로 설정하여 temp 파일로의 업로드를 비활성화합니다.


### <a name="error-code-sftpinvalidsftpcredential"></a>오류 코드: SftpInvalidSftpCredential

- **메시지**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **원인**: 프라이빗 키 콘텐츠를 Azure Key Vault 또는 SDK에서 가져왔으나 올바르게 인코딩되지 않았습니다.

- **권장 사항**:  

    프라이빗 키 콘텐츠가 사용자의 키 자격 증명 모음에서 가져온 것인 경우, SFTP 연결된 서비스에 직접 업로드한다면 원본 키 파일이 작동할 수 있습니다.

    자세한 내용은 [데이터 팩터리 또는 Synapse 파이프라인을 사용하여 SFTP 서버 간에 데이터 복사](./connector-sftp.md#use-ssh-public-key-authentication)를 참조하세요. 프라이빗 키 콘텐츠는 base64로 인코딩된 SSH 프라이빗 키 콘텐츠입니다.

    원본 프라이빗 키 파일 ‘전체’를 base64 인코딩으로 변환하고 인코딩된 문자열을 사용자의 키 자격 증명 모음에 저장하세요. 원본 프라이빗 키 파일은 파일에서 **업로드** 를 선택하면 SFTP 연결된 서비스에서 작동할 수 있는 키 파일입니다.

    다음은 문자열을 생성하는 데 사용할 수 있는 몇 가지 샘플입니다.

    - C# 코드 사용:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python 코드 사용:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - 타사 base64 변환 도구를 사용합니다. Microsoft는 [Encode to Base64 format](https://www.base64encode.org/) 도구를 추천합니다.

- **원인**: 잘못된 키 콘텐츠 형식이 선택되었습니다.

- **권장 사항**:  

    PKCS#8 형식 SSH 프라이빗 키(“-----BEGIN ENCRYPTED PRIVATE KEY-----”로 시작)는 현재 SFTP 서버에 액세스하는 용도로 지원되지 않습니다. 

    키를 “-----BEGIN RSA PRIVATE KEY-----”로 시작하는 기존 SSH 키 형식으로 변환하려면 다음 명령을 실행합니다.

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **원인**: 자격 증명 또는 프라이빗 키 콘텐츠가 잘못되었습니다.

- **권장 사항**: 키 파일 또는 암호가 올바른지 확인하려면 WinSCP와 같은 도구를 사용하여 재차 확인합니다.

### <a name="sftp-copy-activity-failed"></a>SFTP 복사 작업 실패

- **증상**: 
  * 오류 코드: UserErrorInvalidColumnMappingColumnNotFound 
  * 오류 메시지: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **원인**: 원본에 “AccMngr” 열이 없습니다.

- **해결 방법**: “AccMngr” 열이 있는지 확인하려면 대상 데이터 세트 열을 매핑하여 데이터 세트 구성을 재차 확인합니다.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>오류 코드: SftpFailedToConnectToSftpServer

- **메시지**: `Failed to connect to SFTP server '%server;'.`

- **원인**: 오류 메시지에 “Socket read operation has timed out after 30,000 milliseconds”라는 문자열이 포함된 경우 SFTP 서버에 대해 올바르지 않은 연결된 서비스 유형이 사용된 것이 한 가지 가능한 원인일 수 있습니다. 예를 들어, SFTP 서버에 연결하는 데 FTP 연결된 서비스를 사용하고 있을 수 있습니다.

- **권장 사항**: 대상 서버의 포트를 확인합니다. 기본적으로 SFTP는 포트 22를 사용합니다.

- **원인**: 오류 메시지에 “Server response does not contain SSH protocol identification”이라는 문자열이 포함된 경우 SFTP 서버가 연결을 제한하고 있는 것이 한 가지 가능한 원인일 수 있습니다. SFTP 서버에서 병렬로 다운로드하기 위해 여러 연결이 생성되며, 때로는 이로 인해 SFTP 서버 제한이 발생할 수 있습니다. 보통은 서버마다 제한이 발생한 경우 표시되는 오류가 서로 다릅니다.

- **권장 사항**:  

    SFTP 데이터 세트의 최대 동시 연결 수를 1로 설정하고 복사 작업을 다시 실행합니다. 작업이 성공하면 제한이 문제였음을 확인할 수 있습니다.

    낮은 처리량을 올리려면 SFTP 관리자에게 동시 연결 개수 제한을 늘려 달라고 요청하거나 다음 중 하나를 수행합니다.

    * 자체 호스팅 IR을 사용하는 경우 허용 목록에 자체 호스팅 IR 컴퓨터의 IP를 추가합니다.
    * Azure IR을 사용하는 경우 [Azure Integration Runtime IP 주소](./azure-integration-runtime-ip-addresses.md)를 추가합니다. SFTP 서버 허용 목록에 IP 범위를 추가하지 않으려면 자체 호스팅 IR을 대신 사용합니다.


### <a name="error-code-sftppermissiondenied"></a>오류 코드: SftpPermissionDenied

- **메시지**: `Permission denied to access '%path;'`

- **원인**: 작업을 수행할 때 지정된 사용자에게 폴더 또는 파일에 대한 읽기 또는 쓰기 권한이 없습니다.

- **권장 사항**: 사용자에게 SFTP 서버의 폴더 또는 파일을 읽거나 쓸 수 있는 권한을 부여합니다.
 
### <a name="error-code-sftpauthenticationfailure"></a>오류 코드: SftpAuthenticationFailure

- **메시지**: `Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **원인**: 지정된 자격 증명(암호 또는 프라이빗 키)이 잘못되었습니다.

- **권장 사항**: 자격 증명을 확인합니다.

- **원인**: 지정된 인증 유형이 허용되지 않거나 SFTP 서버에서 인증을 완료하는 데 충분하지 않습니다.

- **권장 사항**: 올바른 인증 유형을 사용하려면 다음 옵션을 적용합니다.
    - 서버에 암호가 필요한 경우 "기본"을 사용합니다.
    - 서버에 프라이빗 키가 필요한 경우 "SSH 퍼블릭 키 인증"을 사용합니다.
    - 서버에 "암호"와 "프라이빗 키"가 모두 필요한 경우 "다단계 인증"을 사용합니다.

- **원인**: SFTP 서버는 인증을 위해 "키보드 대화형"이 필요하지만 "암호"를 제공했습니다.

- **권장 사항**: 

    "키보드 대화형"은 "암호"와 다른 특수 인증 방법입니다. 즉, 서버에 로그인할 때 암호를 수동으로 입력해야 하며 이전에 저장된 암호를 사용할 수 없습니다. 그러나 ADF(Azure Data Factory)는 예약된 데이터 전송 서비스이며 런타임에 암호를 제공할 수 있는 팝업 입력 상자가 없습니다. <br/> 
    
    절충안으로 실제 수동 입력 대신 백그라운드에서 입력을 시뮬레이션하는 옵션이 제공됩니다. 이 방식은 "키보드 대화형"을 "암호"로 변경할 때와 같은 결과를 나타냅니다. 이 보안 문제를 수락할 수 있는 경우 아래 단계에 따라 사용하도록 설정합니다.<br/> 
    1. ADF 포털에서 SFTP 연결된 서비스를 마우스로 가리키고 코드 단추를 선택하여 해당 페이로드를 엽니다.
    1. typeProperties 섹션에서 `"allowKeyboardInteractiveAuth": true`를 추가합니다.

## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>오류 코드: HttpFileFailedToRead

- **메시지**: `Failed to read data from http server. Check the error from http server：%message;`

- **원인**: 이 오류는 데이터 팩터리 또는 Synapse 파이프라인이 HTTP 서버와 통신하는데 HTTP 요청 작업이 실패한 경우 발생합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드를 확인하고 원격 서버 문제를 해결합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
