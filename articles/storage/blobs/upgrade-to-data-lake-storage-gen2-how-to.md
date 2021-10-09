---
title: Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드 | Microsoft Docs
description: 리소스 관리자 템플릿을 사용 하 여 Azure Blob storage에서 Data Lake Storage로 업그레이드 하는 방법을 보여 줍니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: 025aa395fa6d2fd3a8fe98f4781a6b554e2b506d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710542"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드

이 문서는 파일 및 디렉터리 수준 보안과 같은 기능의 잠금을 해제 하는 데 도움이 됩니다. 이러한 기능은 빅 데이터 분석 워크 로드에서 널리 사용 되며 Azure Data Lake Storage Gen2로 통칭 됩니다. 

이러한 기능에 대해 자세히 알아보고 작업, 응용 프로그램, 비용, 서비스 통합, 도구, 기능 및 설명서에 대 한이 업그레이드의 영향을 평가 하려면 [Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2.md)를 참조 하세요.

> [!IMPORTANT]
> 업그레이드는 단방향입니다. 업그레이드를 수행한 후에는 계정을 되돌릴 방법이 없습니다. 비프로덕션 환경에서 업그레이드의 유효성을 검사 하는 것이 좋습니다.

## <a name="perform-the-upgrade"></a>업그레이드 수행

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **Data Lake Gen2 마이그레이션** 을 선택 합니다.

   **Azure Data Lake Gen2 기능 구성을 사용 하 여 Storage 계정으로 업그레이드** 페이지가 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![구성 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. **1 단계: 업그레이드 전에 계정 변경 내용 검토** 섹션을 확장 하 고 **검토 및 변경 내용에 동의를** 클릭 합니다.

5. **계정 변경 내용 검토** 페이지에서 확인란을 선택 하 고 **변경 내용에 동의를** 클릭 합니다.

6. **Step 2: 업그레이드 하기 전에 계정 유효성 검사** 섹션을 확장 하 고 **유효성 검사 시작** 을 클릭 합니다.

   유효성 검사에 실패 하면 페이지에 오류가 표시 됩니다. **오류 보기** 링크가 표시 되는 경우도 있습니다. 해당 링크가 표시 되 면 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![오류 보기 링크](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   그런 다음, **오류 json** 파일의 상황에 맞는 메뉴에서 **다운로드** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![오류 json 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   다운로드 한 파일을 열어 계정이 유효성 검사 단계를 통과 하지 못한 이유를 확인 합니다. 

   다음 JSON은 계정에서 호환 되지 않는 기능을 사용할 수 있음을 나타냅니다. 이 경우 기능을 사용 하지 않도록 설정한 다음 유효성 검사 프로세스를 다시 시작 합니다.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. 계정이 성공적으로 확인 되 면 **3 단계: 업그레이드 계정** 섹션을 확장 하 고 **업그레이드 시작** 을 클릭 합니다.

   > [!IMPORTANT]
   > 계정을 업그레이드 하는 동안에는 쓰기 작업을 사용할 수 없습니다. 읽기 작업은 사용 하지 않도록 설정 되어 있지만 업그레이드 프로세스를 불안정 하 게 할 수 있으므로 읽기 작업을 일시 중단 하는 것이 좋습니다.

   마이그레이션이 성공적으로 완료 되 면 다음과 유사한 메시지가 나타납니다. 

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 완료 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

## <a name="migrate-data-workloads-and-applications"></a>데이터, 워크 로드 및 응용 프로그램 마이그레이션 

1. **Blob service** 끝점 또는 **Data Lake 저장소** 끝점을 가리키도록 [작업에서 서비스](data-lake-storage-integrate-with-azure-services.md) 를 구성 합니다.

   > [!div class="mx-imgBorder"]
   > ![계정 끝점](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Windows Azure Storage blob 드라이버 또는 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 드라이버를 사용 하는 Hadoop 워크 로드의 경우 [abfs (Azure Blob 파일 시스템)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 드라이버를 사용 하도록 수정 해야 합니다. **Blob service** 끝점에 요청을 수행 하는 WASB 드라이버와는 달리, abfs 드라이버는 계정의 **Data Lake Storage** 끝점에 요청을 만듭니다.

2. 사용자 지정 응용 프로그램을 테스트 하 여 업그레이드 된 계정에서 예상 대로 작동 하는지 확인 합니다. 

   [Data Lake Storage에 대 한 다중 프로토콜 액세스](data-lake-storage-multi-protocol-access.md) 를 통해 대부분의 응용 프로그램은 수정 없이 Blob api를 계속 사용할 수 있습니다. 문제가 발생 하거나 api를 사용 하 여 디렉터리 작업 및 acl 작업을 수행 하려는 경우 Data Lake Storage Gen2 api를 사용 하도록 일부 코드를 이동 하는 것이 좋습니다. [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md)및 [REST](/rest/api/storageservices/data-lake-storage-gen2)에 대 한 가이드를 참조 하세요. 

3. 사용자 지정 스크립트를 테스트 하 여 업그레이드 된 계정에서 예상 대로 작동 하는지 확인 합니다. 

   Blob Api를 사용 하는 경우에도 대부분의 스크립트는 수정 하지 않아도 작동 합니다. 그러나 필요한 경우 Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)및 [Azure CLI 명령을](data-lake-storage-directory-file-acl-cli.md)사용 하도록 스크립트 파일을 업그레이드할 수 있습니다.
 

## <a name="see-also"></a>참고 항목

[Azure Data Lake storage Gen2 소개](data-lake-storage-introduction.md)