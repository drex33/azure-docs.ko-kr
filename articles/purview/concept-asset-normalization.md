---
title: 자산 정규화
description: Azure Purview가 자산 정규화를 통해 데이터 맵에서 중복 자산을 방지하는 방법 알아보기
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 68092e55cfe53ecde6e6b8146608267e39df0557
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114670056"
---
# <a name="asset-normalization"></a>자산 정규화

자산을 Azure Purview 데이터 맵으로 수집할 때 동일한 데이터 자산을 업데이트하는 다른 원본이 유사하지만 약간 다른 정규화된 이름을 보낼 수 있습니다. 이러한 정규화된 이름은 동일한 자산을 나타내지만 추가 문자 또는 다른 대문자와 같은 약간의 차이로 인해 표면적으로는 이러한 자산이 다르게 나타날 수 있습니다. 중복 항목을 저장하고 데이터 카탈로그를 사용할 때 혼동을 일으키지 않도록 Azure Purview는 수집 중에 정규화를 적용하여 동일한 엔터티 유형의 모든 정규화된 이름이 동일한 형식인지 확인합니다.

예를 들어 정규화된 이름이 `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`인 Azure Blob에서 검사합니다. 이 Blob은 자산에 계보 정보를 추가하는 Azure Data Factory 파이프라인에서도 사용됩니다. 파일을 `https://myAccount.file.core.windows.net//myshare/folderA/folderB/my-file.parquet`으로 읽도록 ADF 파이프라인을 구성할 수 있습니다. 정규화된 이름은 다르지만 이 ADF 파이프라인은 동일한 데이터 조각을 사용하고 있습니다. 정규화를 통해 Azure Blob Storage와 Azure Data Factory의 모든 메타데이터가 단일 자산 `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`에 표시됩니다.

## <a name="normalization-rules"></a>정규화 규칙

다음은 Azure Purview에서 적용되는 정규화 규칙입니다.

### <a name="encode-curly-brackets"></a>중괄호 인코딩
적용 대상: 모든 자산

이전: `https://myaccount.file.core.windows.net/myshare/{folderA}/folder{B/`

이후: `https://myaccount.file.core.windows.net/myshare/%7BfolderA%7D/folder%7BB/`

### <a name="trim-section-spaces"></a>섹션 공백 자르기
적용 대상: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

이전: `https://myaccount.file.core.windows.net/myshare/  folder A/folderB   /`

이후: `https://myaccount.file.core.windows.net/myshare/folder A/folderB/`

### <a name="remove-hostname-spaces"></a>호스트 이름 공백 제거
적용 대상: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL 풀, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

이전: `https://myaccount .file. core.win dows. net/myshare/folderA/folderB/`

이후: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="remove-square-brackets"></a>대괄호 제거 
적용 대상: Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL 풀

이전: `mssql://foo.database.windows.net/[bar]/dbo/[foo bar]`

이후: `mssql://foo.database.windows.net/bar/dbo/foo%20bar`

> [!NOTE]
> 두 대괄호 사이의 공백은 인코딩됩니다.

### <a name="lowercase-scheme"></a>소문자 구성표
적용 대상: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL 풀, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Amazon S3

이전: `HTTPS://myaccount.file.core.windows.net/myshare/folderA/folderB/`

이후: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-hostname"></a>소문자 호스트 이름
적용 대상: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL 풀, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Amazon S3

이전: `https://myAccount.file.Core.Windows.net/myshare/folderA/folderB/`

이후: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-file-extension"></a>소문자 파일 확장자
적용 대상: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Amazon S3

이전: `https://myAccount.file.core.windows.net/myshare/folderA/data.TXT`

이후: `https://myaccount.file.core.windows.net/myshare/folderA/data.txt`

### <a name="remove-duplicate-slash"></a>중복 슬래시 제거
적용 대상: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Azure SQL Database Managed Instance, Azure SQL pool, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

이전: `https://myAccount.file.core.windows.net//myshare/folderA////folderB/`

이후: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-adf-sections"></a>소문자 ADF 섹션
적용 대상: Azure Data Factory

이전: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/fooBar/providers/Microsoft.DataFactory/factories/fooFactory/pipelines/barPipeline/activities/barFoo`

이후: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/foobar/providers/microsoft.datafactory/factories/foofactory/pipelines/barpipeline/activities/barfoo`

### <a name="convert-to-adl-scheme"></a>ADL 구성표로 변환
적용 대상: Azure Data Lake Storage Gen1

이전: `https://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

이후: `adl://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

## <a name="next-steps"></a>다음 단계

[Azure Blob Storage](register-scan-azure-blob-storage-source.md) 계정에서 Azure Purview 데이터 맵으로 검사합니다. 