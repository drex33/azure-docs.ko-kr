---
title: Azure Data Box Disk에서 스토리지 계정으로 데이터 업로드를 확인하는 자습서
description: 이 자습서를 사용하여 Azure Data Box Disk에서 Azure 스토리지 계정으로 업로드된 데이터를 확인하는 방법을 알아봅니다.
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: 53e2db3728d92a862fce64ba1fc379a2ae2205ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591637"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>자습서: Azure Data Box Disk에서 데이터 업로드 확인

이 자습서는 Azure Data Box Disk 배포 시리즈의 마지막 자습서입니다. 이 자습서에서는 다음 사항에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에 대한 데이터 업로드 확인
> * Data Box Disk에서 데이터 지우기

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [자습서: Azure Data Box Disk 반환](data-box-disk-deploy-picked-up.md)을 완료했는지 확인합니다.


## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

운송업체에서 디스크를 픽업하면 포털에서 주문 상태는 **픽업됨** 으로 업데이트됩니다. 추적 ID도 표시됩니다.

![디스크 픽업됨](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Microsoft에서 디스크를 수신하고 검사할 때 작업 상태는 **수신됨** 으로 업데이트됩니다. 

![디스크 수신됨](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

디스크가 Azure 데이터 센터에서 서버에 연결되면 데이터는 자동으로 복사됩니다. 데이터 크기에 따라 복사 작업을 완료하는 데 몇 시간에서 몇 일이 걸릴 수 있습니다. 포털에서 복사 작업 진행 상황을 모니터링할 수 있습니다.

복사가 완료되면 주문 상태는 **완료됨** 으로 업데이트됩니다.

![데이터 복사 완료됨](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

오류와 함께 복사가 완료되면 [업로드 오류 문제 해결](data-box-disk-troubleshoot-upload.md)을 참조하세요.

원본에서 데이터를 삭제하기 전에 데이터 스토리지 계정에 있는지 확인합니다. 데이터는 다음 위치에 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사할 때, 데이터 형식에 따라 Azure Storage 계정에서 다음 경로 중 하나에 업로드됩니다.

  - 블록 Blob 및 페이지 Blob의 경우: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files의 경우: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    또는 Azure Portal에서 Azure 스토리지 계정으로 이동하여 찾아볼 수도 있습니다.

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문을 만들 때 지정된 리소스 그룹에 연결됩니다.

  - Azure에서 관리 디스크로 복사가 완료되면 Azure Portal에서 **주문 세부 정보** 로 이동하여 관리 디스크에 지정된 리소스 그룹을 메모할 수 있습니다.

      ![주문 세부 정보 보기](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    기록한 리소스 그룹으로 이동하여 관리 디스크를 찾습니다.

      ![관리 디스크의 리소스 그룹](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > 데이터 복사 중에 페이지 Blob이 관리 디스크로 성공적으로 변환되지 않은 경우 스토리지 계정에 유지되고 스토리지에 대한 요금이 청구됩니다.

  -  VHDX 또는 동적/차이점 보관용 VHD를 복사한 경우 VHD/VHD가 준비 스토리지 계정에 블록 Blob으로 업로드됩니다. 준비 **Storage 계정 > Blob** 으로 차례로 이동한 다음, 적절한 컨테이너(StandardSSD, StandardHDD 또는 PremiumSSD)를 선택합니다. VHDX/VHD는 준비 스토리지 계정에서 블록 Blob으로 표시되어야 합니다.
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

데이터가 Azure에 업로드되면 데이터가 스토리지 계정에 있는지 확인한 후 원본에서 데이터를 삭제합니다. 데이터는 다음 위치에 있을 수 있습니다.

- Azure Storage 계정. Data Box에 데이터를 복사할 때, 데이터 형식에 따라 Azure Storage 계정에서 다음 경로 중 하나에 업로드됩니다.

    - **블록 Blob 및 페이지 Blob의 경우**: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`

    - **Azure Files의 경우**: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

- 관리 디스크 리소스 그룹. 관리 디스크를 만들 때 VHD는 페이지 Blob으로 업로드된 다음, 관리 디스크로 변환됩니다. 관리 디스크는 주문을 만들 때 지정된 리소스 그룹에 연결됩니다.

::: zone-end

데이터가 Azure로 업로드되었는지 확인하려면 다음 단계를 수행합니다.

1. 디스크 주문과 연결된 스토리지 계정으로 이동합니다.
2. **Blob service > Blob 찾아보기** 로 이동합니다. 컨테이너 목록이 표시됩니다. *BlockBlob* 및 *PageBlob* 폴더 아래에서 만든 하위 폴더에 해당하는 동일한 이름의 컨테이너가 스토리지 계정에 만들어집니다.
    폴더 이름이 Azure 명명 규칙을 따르지 않는 경우 Azure에 대한 데이터 업로드가 실패합니다.

3. 전체 데이터 세트가 로드되었는지 확인하려면 Microsoft Azure Storage Explorer를 사용합니다. Data Box Disk 순서에 해당하는 스토리지 계정을 연결한 다음, Blob 컨테이너의 목록을 살펴봅니다. 컨테이너를 선택하고, **...자세히** 를 클릭한 다음, **폴더 통계** 를 클릭합니다. **작업** 창에서 Blob의 수 및 총 Blob 크기를 포함하는 해당 폴더에 대한 통계가 표시됩니다. 총 Blob 크기(바이트)는 데이터 세트의 크기와 일치해야 합니다.

    ![Storage Explorer에서 폴더 통계](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Data Box Disk에서 데이터 지우기

Azure에 대한 업로드가 완료되면 Data Box Disk는 [NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) 표준에 따라 데이터를 지웁니다.

::: zone target="docs"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Disk 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Azure에 대한 데이터 업로드 확인
> * Data Box Disk에서 데이터 지우기


Azure Portal을 통해 Data Box Disk를 관리하는 방법을 알아보려면 다음으로 진행합니다.

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 Azure Data Box Disk 관리](./data-box-portal-ui-admin.md)

::: zone-end




