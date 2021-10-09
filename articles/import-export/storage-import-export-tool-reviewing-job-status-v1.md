---
title: Azure Import/Export의 가져오기 및 내보내기에서 로그 복사 검토 | Microsoft Docs
description: 데이터 복사, 업로드 문제에 대 한 가져오기 및 내보내기에서 오류/복사 로그를 검토 하는 방법에 대해 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03f4cf19922f808decfd84fe0538930dee807b5d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709253"
---
# <a name="review-copy-logs-from-imports-and-exports-via-azure-importexport"></a>Azure Import/Export를 통해 가져오기 및 내보내기에서 복사 로그 검토
Microsoft Azure Import/Export 서비스는 가져오기 또는 내보내기 작업과 연결된 드라이브를 처리할 때 blob을 가져오거나 내보내는 데 사용한 저장소 계정에 복사본 로그 파일을 작성합니다. 

로그 파일에는 가져오거나 내보낸 각 파일에 대한 자세한 상태가 포함되어 있습니다. 

서비스는 완료된 작업의 상태를 쿼리할 때 각 복사본 로그 파일에 대한 URL을 반환합니다. 자세한 내용은 [작업 가져오기](/rest/api/storageimportexport/Jobs/Get)를 참조하세요.  

## <a name="example-urls"></a>예제 URL

다음 예제는 두 드라이브를 사용하는 가져오기 작업에 대한 복사 로그 파일 URL입니다.  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 <!--See [Import/Export service Log File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-log) for the format of copy logs and the full list of status codes. ARCHIVED-->  

## <a name="next-steps"></a>다음 단계

<!--* [Setting Up the Azure Import/Export Tool](storage-import-export-tool-setup-v1.md) ARCHIVED-->
 * [가져오기 작업을 위한 하드 드라이브 준비](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
<!--* [Repairing an import job](./storage-import-export-tool-repairing-an-import-job-v1.md)-->  
<!--* [Repairing an export job](./storage-import-export-tool-repairing-an-export-job-v1.md)-->