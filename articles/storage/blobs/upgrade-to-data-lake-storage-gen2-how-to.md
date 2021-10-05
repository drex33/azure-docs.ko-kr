---
title: Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드 | Microsoft Docs
description: Resource Manager 템플릿을 사용하여 Azure Blob Storage에서 Data Lake Storage 업그레이드하는 방법을 보여줍니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: b1f612ee189d47529033fdd9e6c7e7df0f523df9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129459185"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 기능을 통해 Azure Blob Storage 업그레이드

이 문서는 파일 및 디렉터리 수준 보안 및 더 빠른 작업과 같은 기능의 잠금을 해제하는 데 도움이 됩니다. 이러한 기능은 빅 데이터 분석 워크로드에서 널리 사용되며 전체적으로 Azure Data Lake Storage Gen2라고 합니다. 

이러한 기능에 대해 자세히 알아보고 이 업그레이드가 워크로드, 애플리케이션, 비용, 서비스 통합, 도구, 기능 및 설명서에 미치는 영향을 평가하려면 [Azure Data Lake Storage Gen2 기능을 사용하여 Azure Blob Storage 업그레이드를 참조하세요.](upgrade-to-data-lake-storage-gen2.md)

> [!IMPORTANT]
> 업그레이드는 단방향입니다. 업그레이드를 수행한 후에는 계정을 되돌릴 방법이 없습니다. 비프로덕션 환경에서 업그레이드의 유효성을 검사하는 것이 좋습니다.

## <a name="perform-the-upgrade"></a>업그레이드 수행

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **Data Lake Gen2 마이그레이션을** 선택합니다.

   **Azure Data Lake Gen2 기능** 구성을 사용하여 Storage 계정으로 업그레이드 페이지가 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![구성 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. **1단계: 업그레이드하기 전에 계정 변경 내용 검토 섹션을** 확장하고 **검토 및 변경 내용에 동의를** 클릭합니다.

5. 계정 **변경 내용 검토** 페이지에서 확인란을 선택한 다음 **변경 내용에 동의를** 클릭합니다.

6. **업그레이드하기 전에 2단계: 계정 유효성 검사 섹션을** 확장한 다음 **유효성 검사 시작을** 클릭합니다.

   유효성 검사에 실패하면 **오류 보기** 링크를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![오류 보기 링크](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   그런 다음 **error.json** 파일의 상황에 맞는 메뉴에서 **다운로드를** 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![오류 json 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   다운로드한 파일을 열어 계정이 유효성 검사 단계를 통과하지 못한 이유를 확인합니다. 

   다음 JSON은 계정에서 호환되지 않는 기능을 사용하도록 설정되었음을 나타냅니다. 이 경우 기능을 사용하지 않도록 설정하고 유효성 검사 프로세스를 다시 시작합니다.

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

7. 계정의 유효성을 성공적으로 검사한 후 **3단계: 계정 업그레이드** 섹션을 확장한 다음 **업그레이드 시작을** 클릭합니다.

   > [!IMPORTANT]
   > 계정이 업그레이드되는 동안에는 쓰기 작업을 사용할 수 없습니다. 읽기 작업은 사용하지 않도록 설정되지 않지만 업그레이드 프로세스가 불안정해질 수 있으므로 읽기 작업을 일시 중단하는 것이 좋습니다.

   마이그레이션이 성공적으로 완료되면 다음과 유사한 메시지가 나타납니다. 

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 완료 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

## <a name="migrate-data-workloads-and-applications"></a>데이터, 워크로드 및 애플리케이션 마이그레이션 

1. **Blob service** 엔드포인트 또는 **Data Lake Storage** 엔드포인트를 가리키도록 [워크로드에서 서비스를](data-lake-storage-integrate-with-azure-services.md) 구성합니다.

   > [!div class="mx-imgBorder"]
   > ![계정 엔드포인트](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Windows Azure Storage Blob 드라이버 또는 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 드라이버를 사용하는 Hadoop 워크로드의 경우 [ABFS(Azure Blob File System)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 드라이버를 사용하도록 수정해야 합니다. **Blob service** 엔드포인트에 요청을 하는 WASB 드라이버와 달리 ABFS 드라이버는 계정의 **Data Lake Storage** 엔드포인트를 요청합니다.

2. 사용자 지정 애플리케이션을 테스트하여 업그레이드된 계정에서 예상대로 작동하는지 확인합니다. 

   [Data Lake Storage 대한 다중 프로토콜 액세스를](data-lake-storage-multi-protocol-access.md) 통해 대부분의 애플리케이션은 수정 없이 Blob API를 계속 사용할 수 있습니다. 문제가 발생하거나 API를 사용하여 디렉터리 작업 및 ACL을 사용하려는 경우 일부 코드를 이동하여 Data Lake Storage Gen2 API를 사용하는 것이 좋습니다. [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python,](data-lake-storage-directory-file-acl-python.md) [Node.js](data-lake-storage-acl-javascript.md)및 [REST에](/rest/api/storageservices/data-lake-storage-gen2)대한 가이드를 참조하세요. 

3. 사용자 지정 스크립트를 테스트하여 업그레이드된 계정에서 예상대로 작동하는지 확인합니다. 

   Blob API의 경우와 마찬가지로 대부분의 스크립트는 수정할 필요 없이 작동합니다. 그러나 필요한 경우 Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)및 Azure CLI 명령을 사용하도록 스크립트 파일을 업그레이드할 수 [있습니다.](data-lake-storage-directory-file-acl-cli.md)
 

## <a name="see-also"></a>추가 정보

[Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)