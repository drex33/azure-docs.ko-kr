---
title: Azure Storage Explorer를 사용하여 Azure 관리 디스크 관리
description: Azure Storage Explorer를 사용하여 지역 간에 Azure 관리 디스크를 업로드, 다운로드, 마이그레이션하는 방법과 관리 디스크 스냅샷을 만드는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 1ef24210e033c5e0af623dfa6f3cd79146732640
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692296"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage Explorer를 사용하여 Azure 관리 디스크 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Storage Explorer 1.10.0을 사용하면 사용자가 관리 디스크를 업로드, 다운로드, 복사할 수 있으며 스냅샷을 만들 수 있습니다. 이 추가 기능으로 인해 Storage Explorer를 사용하여 온-프레미스에서 Azure로 데이터를 마이그레이션하고 Azure 지역 간에 데이터를 마이그레이션할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.
- Azure 구독
- 하나 이상의 Azure 관리 디스크
- 최신 버전의 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

Storage Explorer가 Azure에 연결되지 않은 경우 리소스를 관리하는 데 사용할 수 없습니다. 이 섹션에서는 Storage Explorer를 사용하여 리소스를 관리할 수 있도록 Azure 계정에 연결하는 방법을 설명합니다.

1. Azure Storage Explorer를 시작하고 왼쪽에 있는 **플러그 인** 아이콘을 클릭합니다.

    ![플러그 인 아이콘 클릭](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. **Azure 계정 추가** 를 선택하고 **다음** 을 클릭합니다.

    ![Azure 계정 추가](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. **Azure 로그인** 대화 상자에서 Azure 자격 증명을 입력합니다.

    ![Azure 로그인 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 목록에서 구독을 선택하고 **적용** 을 클릭합니다.

    ![구독 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>온-프레미스 VHD에서 관리 디스크 업로드

1. 왼쪽 창에서 **디스크** 를 확장하고 디스크를 업로드할 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. **업로드** 를 선택합니다.

    ![업로드 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. **VHD 업로드** 에서 원본 VHD, 디스크 이름, OS 유형, 디스크를 업로드할 지역, 계정 유형을 지정합니다. 일부 지역에서는 가용성 영역이 지원되며, 해당 지역에 대해 원하는 영역을 선택할 수 있습니다.
1. **만들기** 를 선택하여 디스크 업로드를 시작합니다.

    ![VHD 업로드 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. 이제 업로드 상태가 **작업** 에 표시됩니다.

    ![업로드 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. 업로드가 완료되었으나 오른쪽 창에 디스크가 표시되지 않으면 **새로 고침** 을 선택합니다.

## <a name="download-a-managed-disk"></a>관리 디스크 다운로드

다음 단계에서는 관리 디스크를 온-프레미스 VHD로 다운로드하는 방법을 설명합니다. 디스크를 다운로드하려면 디스크가 **연결되지 않음** 상태여야 합니다. **연결됨** 상태인 디스크는 다운로드할 수 없습니다.

1. 왼쪽 창에 디스크가 아직 확장되지 않은 경우 **디스크** 를 확장하고 디스크를 다운로드할 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽 창에서 다운로드할 디스크를 선택합니다.
1. **다운로드** 를 선택하고 디스크를 저장할 위치를 선택합니다.

    ![관리 디스크 다운로드](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. **저장** 을 선택하면 디스크 다운로드가 시작됩니다. 다운로드 상태가 **작업** 에 표시됩니다.

    ![다운로드 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

Storage Explorer를 사용하면 지역 내에서 또는 지역 간에 관리 디스크를 복사할 수 있습니다. 디스크를 복사하려면 다음을 수행합니다.

1. 왼쪽의 **디스크** 드롭다운에서 복사할 디스크가 포함된 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽 창에서 복사할 디스크를 선택하고 **복사** 를 선택합니다.

    ![관리 디스크 복사](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 왼쪽 창에서 디스크를 붙여넣을 리소스 그룹을 선택합니다.

    ![리소스 그룹 2 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 오른쪽 창에서 **붙여넣기** 를 선택합니다.

    ![관리 디스크 붙여넣기](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. **디스크 붙여넣기** 대화 상자에서 값을 채웁니다. 지원되는 지역에서 가용성 영역을 지정할 수도 있습니다.

    ![디스크 붙여넣기 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. **붙여넣기** 를 선택하면 디스크가 복사되기 시작하고 상태가 **작업** 에 표시됩니다.

    ![복사하여 붙여넣기 상태](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>스냅샷 만들기

1. 왼쪽의 **디스크** 드롭다운에서 스냅샷을 만들 디스크가 포함된 리소스 그룹을 선택합니다.

    ![리소스 그룹 1 선택](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 오른쪽에서 스냅샷을 만들 디스크를 선택하고 **스냅샷 만들기** 를 선택합니다.

    ![스냅샷 만들기](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. **스냅샷 만들기** 에서 스냅샷 이름과 스냅샷을 만들 리소스 그룹을 지정합니다. 그런 다음 **생성** 를 선택합니다.

    ![스냅샷 만들기 대화 상자](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. 스냅샷이 만들어지면 **작업** 에서 **포털에서 열기** 를 선택하여 Azure Portal에서 스냅샷을 볼 수 있습니다.

    ![포털에서 스냅샷 열기](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>다음 단계


[Azure Portal을 사용하여 VHD에서 VM을 만드는 방법](windows/create-vm-specialized-portal.md)을 알아봅니다.

[Azure Portal을 사용하여 Windows VM에 관리되는 데이터 디스크를 연결하는 방법](windows/attach-managed-disk-portal.md)을 알아봅니다.