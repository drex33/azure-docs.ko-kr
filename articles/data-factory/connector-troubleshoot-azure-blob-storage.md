---
title: Azure Blob Storage 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 Azure Blob Storage 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e9a1c085e3d1d88d0897d46b924c5a9a21c747a2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391101"
---
# <a name="troubleshoot-the-azure-blob-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse의 Azure Blob Storage 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 Azure Blob Storage 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-azurebloboperationfailed"></a>오류 코드: AzureBlobOperationFailed

- **메시지**: “Blob 작업에 실패했습니다. ContainerName: %containerName;, 경로: %path;.”

- **원인**: Blob Storage 작업에서 문제가 발생했습니다.

- **권장 사항**: 오류 세부 정보를 확인하려면 [Blob Storage 오류 코드](/rest/api/storageservices/blob-service-error-codes)를 참조하세요. 추가 도움이 필요한 경우 Blob Storage 팀으로 문의하세요.


## <a name="invalid-property-during-copy-activity"></a>복사 작업 중에 잘못된 속성이 발견됨

- **메시지**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **원인**: 데이터 세트에 정의된 유형이 복사 작업에 정의된 원본 또는 싱크 유형과 일치하지 않습니다.

- **해결 방법**: 데이터 세트 또는 파이프라인 JSON 정의를 편집하여 유형이 일치하도록 만든 다음 배포를 다시 실행합니다.

## <a name="error-code-fipsmodeisnotsupport"></a>오류 코드: FIPSModeIsNotSupport

- **메시지**: `Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **원인**: 자체 호스팅 통합 런타임이 설치된 VM에서 FIPS 정책을 사용하도록 설정되어 있습니다.

- **권장 사항**: 자체 호스팅 통합 런타임이 설치된 VM에서 FIPS 모드를 사용하지 않도록 설정합니다. Windows에서는 FIPS 모드가 권장되지 않습니다.

## <a name="error-code-azureblobinvalidblocksize"></a>오류 코드: AzureBlobInvalidBlockSize

- **메시지**: `Block size should between %minSize; MB and 100 MB.`

- **원인**: 블록 크기가 blob 제한을 초과합니다.

## <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>오류 코드: AzureStorageOperationFailedConcurrentWrite

- **메시지**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **원인**: 동시 복사 작업이 여러 개 실행되고 있거나 애플리케이션이 동일한 파일에 쓰고 있습니다.

## <a name="error-code-azureappendblobconcurrentwriteconflict"></a>오류 코드: AzureAppendBlobConcurrentWriteConflict

- **메시지**: `Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **원인**: 동시 쓰기 요청이 여러 개 발생하고 있으므로 파일 내용에 충돌이 발생합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
