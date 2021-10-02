---
title: Azure Data Lake Storage 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 Azure Data Lake Storage Gen1 및 Gen2 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 1c36fa5295acafb96e57484cf34429091dd634e9
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391106"
---
# <a name="troubleshoot-the-azure-data-lake-storage-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse의 Azure Data Lake Storage 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 Azure Data Lake Storage Gen1 및 Gen2 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안을 제공 합니다.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>오류 메시지: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대한 신뢰 관계를 설정할 수 없습니다.

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다. 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **원인**: TLS 핸드셰이크 중에 인증서 유효성 검사가 실패했습니다.

- **해결 방법**: 임시 방편으로 스테이징된 복사본을 사용하여 Azure Data Lake Storage Gen1에 대해 TLS(전송 계층 보안) 유효성 검사를 건너뜁니다. 이 문제를 재현하여 네트워크 모니터(netmon) 추적을 수집한 다음 네트워크 팀의 지원을 받아 로컬 네트워크 구성을 확인해야 합니다.

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="문제 해결을 위한 Azure Data Lake Storage Gen1 연결 다이어그램.":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>오류 메시지: 원격 서버에서 다음과 같은 오류를 반환했습니다. (403) 사용할 수 없음

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다. 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **원인**: 한 가지 가능한 원인은 사용자가 사용하는 서비스 주체 또는 관리 ID에 특정 폴더 또는 파일에 대한 액세스 권한이 없는 것입니다.

- **해결 방법**: 복사하려는 모든 폴더와 하위 폴더에 적절한 권한을 부여합니다. 자세한 내용은 [Azure Data Lake Storage Gen1 간에 데이터 복사](connector-azure-data-lake-store.md#linked-service-properties)를 참조하세요.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>오류 메시지: 서비스 주체를 사용하여 토큰에 액세스하지 못했습니다. ADAL 오류: service_unavailable

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다.

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **원인**: Azure Active Directory에서 소유하고 있는 STS(서비스 토큰 서버)를 사용할 수 없는 경우(즉, 다른 작업 때문에 요청을 처리할 수 없는 경우) HTTP 오류 503을 반환합니다. 

- **해결 방법**: 몇 분 후에 복사 작업을 다시 실행합니다.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>오류 코드: ADLSGen2OperationFailed

- **메시지**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

  | 원인 분석                                               | 권장                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Azure Data Lake Storage Gen2가 작업이 실패했다는 오류를 throw하는 경우.| Azure Data Lake Storage Gen2에서 throw한 상세한 오류 메시지를 확인합니다. 일시적인 오류인 경우 작업을 다시 시도합니다. 추가 도움이 필요한 경우 Azure Storage 지원으로 문의하세요. 이때 오류 메시지에 표시된 요청 ID를 알려 주세요. |
  | 오류 메시지에 “Forbidden”이라는 문자열이 포함된 경우, 사용 중인 서비스 주체 또는 관리 ID에 Azure Data Lake Storage Gen2에 대한 액세스 권한이 없을 수 있습니다. | 오류를 해결하려면 [Azure Data Lake Storage Gen2에서 데이터 복사 및 변환](./connector-azure-data-lake-storage.md#service-principal-authentication)을 참조하세요. |
  | 오류 메시지에 “InternalServerError”라는 문자열이 포함된 경우, 이 오류는 Azure Data Lake Storage Gen2에서 반환한 것입니다. | 일시적인 오류로 인해 오류가 발생한 것일 수 있습니다. 그렇다면 작업을 다시 시도합니다. 문제가 지속되면 Azure Storage 지원에 문의하고 오류 메시지에 표시된 요청 ID를 알려 주세요. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Azure Data Lake Storage Gen2 계정에 대한 요청에서 시간 제한 오류가 발생함

- **Message**: 
  * 오류 코드 = `UserErrorFailedBlobFSOperation`
  * 오류 메시지 = `BlobFS operation failed for: A task was canceled.`

- **원인**: 이 문제의 원인은 Azure Data Lake Storage Gen2 싱크 시간 제한 오류입니다. 이 오류는 주로 자체 호스팅 IR(통합 런타임) 머신에서 발생합니다.

- **권장 사항**: 

    - 가능한 경우 자체 호스팅 IR 머신과 대상 Azure Data Lake Storage Gen2 계정을 동일한 지역에 배치합니다. 이렇게 하면 임의의 시간 제한 오류를 방지하고 더 나은 성능을 얻을 수 있습니다.

    - ExpressRoute와 같은 특수한 네트워크 설정이 있는지 살펴보고, 네트워크의 대역폭이 충분한지 확인합니다. 전체적인 대역폭이 낮다면 자체 호스팅 IR 동시 작업 설정을 줄이는 것이 좋습니다. 이렇게 하면 여러 동시 작업 간의 네트워크 리소스 경합을 방지할 수 있습니다.

    - 파일 크기가 보통이거나 작은 경우에는 비이진 복사본에 더 작은 블록 크기를 사용하여 시간 제한 오류를 방지할 수 있습니다. 자세한 내용은 [Blob Storage Put Block](/rest/api/storageservices/put-block)을 참조하세요.

       사용자 지정 블록 크기를 지정하려면 다음과 같이 JSON 파일 편집기에서 속성을 편지합니다.
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
