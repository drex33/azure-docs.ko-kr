---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/10/2021
ms.author: alkohli
ms.openlocfilehash: d02eb129dd66234cf7c8522ca8204fb0026da092
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354187"
---
- 컨테이너, 공유 및 폴더:
  - 사전 생성된 공유에 파일을 직접 복사하지 마세요. 공유 아래에 폴더를 만든 다음, 해당 폴더에 파일을 복사해야 합니다.
  - *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 의 폴더는 컨테이너입니다. 예를 들어 컨테이너는 *StorageAccount_BlockBlob/container* 및 *StorageAccount_PageBlob/container* 로 만들어집니다.
  - *StorageAccount_AzFile* 에서 직접 만들어진 각각의 폴더는 Azure 파일 공유로 변환됩니다.
  - Azure Blob Storage는 디렉터리를 지원하지 않습니다. *StorageAccount_BlockBlob* 폴더에 폴더를 만든 경우, 가상 폴더가 Blob 이름으로 생성됩니다. Azure Files을 위한 실제 디렉터리 구조가 유지됩니다.
- 폴더 내용 병합:
  - *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 공유에 기록된 모든 파일은 각각 블록 Blob 및 페이지 Blob으로 업로드됩니다.
  - 폴더의 이름이 기존 컨테이너와 같으면 폴더의 내용이 컨테이너의 내용과 병합됩니다. 클라우드에 아직 없는 파일 또는 Blob이 컨테이너에 추가됩니다. 파일 또는 Blob의 이름이 컨테이너에 이미 있는 파일 또는 Blob과 동일한 경우 기존 파일 또는 Blob을 덮어씁니다.
  - *StorageAccount_BlockBlob* 및 *StorageAccount_PageBlob* 폴더 아래에 만들어진 빈 디렉터리 계층 구조(어떤 파일도 없음)는 업로드되지 않습니다.
- 데이터 복사본에 대해 SMB 및 NFS 프로토콜을 모두 사용 하는 경우 다음을 수행 하는 것이 좋습니다.
  - SMB 및 NFS에 대해 다른 저장소 계정을 사용 합니다.
  - 동일한 데이터를 Azure에서 SMB와 NFS를 모두 사용 하는 동일한 최종 대상에 복사 하지 마세요. 이 경우 최종 결과를 확인할 수 없습니다.
  - 동시에 SMB와 NFS를 모두 사용 하 여 복사할 수 있지만 사용자 오류가 발생 하기 쉬우므로 권장 하지 않습니다. NFS 데이터 복사를 시작 하기 전에 SMB 데이터 복사가 완료 될 때까지 기다립니다.
- 업로드 관리: 
  - 데이터 업로드 중 성능을 향상시키려면 [스토리지 계정에서 대용량 파일 공유를 사용하도록 설정하고 공유 용량을 100TiB로 늘리](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)는 것이 좋습니다. 대용량 파일 공유는 LRS(로컬 중복 스토리지)가 있는 스토리지 계정에 대해서만 지원됩니다.
  - 데이터를 Azure에 업로드할 때 오류가 발생하는 경우 오류 로그는 대상 스토리지 계정에서 만들어집니다. 업로드가 완료되면 이 오류 로그 경로를 사용할 수 있으며, 로그를 검토하여 수정 작업을 수행할 수 있습니다. 업로드된 데이터를 확인하지 않고 원본에서 데이터를 삭제하지 마세요.
  - [Azure Data Box를 통해 파일 ACL, 특성 및 타임스탬프 유지](../articles/databox/data-box-file-acls-preservation.md)의 지침을 사용하여 Azure Files에 데이터를 업로드하면 파일 메타데이터 및 NTFS 권한을 보존할 수 있습니다.
  - 파일의 계층은 blob 및 Azure Files 모두에 대해 클라우드에 업로드 하는 동안 유지 관리 됩니다. 예를 들어 `<container folder>\A\B\C.txt` 경로에 파일을 복사했습니다. 이 파일은 클라우드의 동일한 경로에 업로드 됩니다.
  
