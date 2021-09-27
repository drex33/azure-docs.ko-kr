---
title: Azure Storage Explorer를 사용하여 Azure 관리 디스크 관리
description: Azure Storage Explorer를 사용하여 지역 간에 Azure 관리 디스크를 업로드, 다운로드, 마이그레이션하는 방법과 관리 디스크 스냅샷을 만드는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 84b2ea53ebb0b6102edf5bc501e0e1b9b6f21726
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781730"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage Explorer를 사용하여 Azure 관리 디스크 관리

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure Storage Explorer 다음을 수행할 수 있는 다양한 기능 집합이 포함되어 있습니다.

- 관리 디스크를 업로드, 다운로드 및 복사합니다.
- 운영 체제 또는 데이터 디스크 가상 하드 디스크에서 스냅샷을 만듭니다.
- 온-프레미스에서 Azure로 데이터를 마이그레이션합니다.
- Azure 지역 간에 데이터를 마이그레이션합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독
- 하나 이상의 Azure 관리 디스크.
- 최신 버전의 [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

Storage Explorer Azure에 연결되지 않은 경우 리소스를 관리하는 데 사용할 수 없습니다. 이 섹션의 단계에 따라 Storage Explorer Azure 계정에 연결합니다. 나중에 디스크를 관리하는 데 사용할 수 있습니다.

1. Azure Storage Explorer 열고 도구 모음에서 **커넥트** 아이콘을 선택합니다.

    [![커넥트 아이콘의 위치를 보여 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-lrg.png#lightbox)

1. Azure Storage **커넥트** 대화 상자에서 **구독을** 선택합니다.

    [![구독 옵션의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-lrg.png#lightbox)

1. 적절한 환경을 선택하고 **다음을** 선택합니다. 사용자 지정 **환경 관리를** 선택하여 사용자 지정 환경을 구성하고 추가할 수도 있습니다.

    [![Azure 환경 옵션의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-lrg.png#lightbox)

1. 로그인 대화 **상자에서** Azure 자격 증명을 입력합니다.

    ![로그인 대화 상자의 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 목록에서 구독을 선택한 다음 **탐색기 열기를** 선택합니다.

    [![탐색기 열기 단추의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-lrg.png#lightbox)

## <a name="upload-an-on-premises-vhd"></a>온-프레미스 VHD 업로드

온-프레미스 VHD(가상 하드 디스크) 파일을 Azure에 업로드하고 이를 사용하여 이미지를 만들 수 있습니다. 이 섹션의 단계에 따라 원본 파일을 업로드합니다.

1. **탐색기** 창에서 디스크를 확장하고 **디스크를** 업로드할 리소스 그룹을 선택합니다.

    [![디스크를 업로드하기 위한 디스크 노드의 위치를 강조 표시하는 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. 리소스 그룹 세부 정보 창에서 **업로드** 선택합니다.

    [![업로드 단추의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-lrg.png#lightbox)

1. 업로드 **VHD** 대화 상자에서 VHD 원본 파일, 디스크 이름, 운영 체제 유형, 디스크를 업로드할 지역 및 계정 유형을 지정합니다. 지역에서 가용성 영역을 지원하는 경우 원하는 영역을 선택할 수 있습니다. **만들기** 를 선택하여 디스크 업로드를 시작합니다.

    [![Azure Storage Explorer 업로드 VHD 대화 상자의 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-lrg.png#lightbox)

1. 이제 업로드 상태가 **작업** 에 표시됩니다.

    [![업로드 상태 메시지가 포함된 활동 창의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-lrg.png#lightbox)

업로드가 완료되고 **작업** 창에 디스크가 표시되지 않으면 **새로 고침을** 선택합니다.

## <a name="download-a-managed-disk"></a>관리 디스크 다운로드

이 섹션의 단계에 따라 관리 디스크를 온-프레미스 VHD에 다운로드합니다. 디스크를 다운로드하려면 먼저 디스크의 상태가 **연결되지 않은** 상태여야 합니다.

1. **탐색기** 창에서 디스크를 확장하고 **디스크를** 다운로드할 리소스 그룹을 선택합니다.

    [![디스크를 다운로드하기 위한 디스크 노드의 위치를 강조 표시하는 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. 리소스 그룹 세부 정보 창에서 다운로드할 디스크를 선택합니다.
1. **다운로드** 를 선택하고 디스크를 저장할 위치를 선택합니다.

    [![다운로드 단추의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-lrg.png#lightbox)

1. **저장을** 선택하여 다운로드를 시작합니다. 다운로드 상태는 활동 **에** 표시됩니다.

    [![다운로드 상태 메시지와 함께 활동 창의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-lrg.png#lightbox)

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

Storage Explorer를 사용하면 지역 내에서 또는 지역 간에 관리 디스크를 복사할 수 있습니다. 디스크를 복사하려면 다음을 수행합니다.

1. **탐색기** 창에서 디스크 **드롭다운을** 확장하고 복사할 디스크가 포함된 리소스 그룹을 선택합니다.

    [![디스크를 복사하기 위한 디스크 노드의 위치를 강조 표시하는 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. 리소스 그룹 세부 정보 창에서 복사하려는 디스크를 선택하고 **복사를** 선택합니다.

    [![복사 단추의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-lrg.png#lightbox)

1. **탐색기** 창에서 디스크를 확장하고 **디스크를** 붙여넣을 리소스 그룹을 선택합니다.

    [![디스크를 붙여넣을 디스크 노드의 위치를 강조 표시하는 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-lrg.png#lightbox)

1. 리소스 그룹 세부 정보 창에서 **붙여넣기를** 선택합니다.

    [![붙여넣기 단추의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-lrg.png#lightbox)

1. 디스크 **붙여넣기** 대화 상자에서 값을 입력합니다. 지원되는 지역에서 가용성 영역을 지정할 수도 있습니다.

    [![Azure Storage Explorer 디스크 붙여넣기 양식의 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-lrg.png#lightbox)

1. **붙여넣기를** 선택하여 디스크 복사를 시작합니다. 상태는 활동 **에** 표시됩니다.

    [![상태 메시지 복사 및 붙여넣기와 함께 활동 창의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-lrg.png#lightbox)

## <a name="create-a-snapshot"></a>스냅샷 만들기

1. **탐색기** 창에서 **디스크를** 확장하고 스냅샷할 디스크가 포함된 리소스 그룹을 선택합니다.

    [![디스크 스냅샷을 만들기 위한 디스크 노드의 위치를 강조 표시하는 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. 리소스 그룹 세부 정보 창에서 스냅샷을 만들려는 디스크를 선택하고 **스냅샷 만들기를** 선택합니다.

    [![스냅샷 만들기 단추의 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-lrg.png#lightbox)

1. **스냅샷 만들기에서 스냅샷의** 이름과 스냅샷을 만들 리소스 그룹을 지정합니다. **만들기** 를 선택합니다.

    [![Azure Storage Explorer 스냅샷 만들기 대화 상자의 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-lrg.png#lightbox)

1. 스냅샷을 만든 후 **활동에서** **포털에서 열기를** 선택하여 Azure Portal 스냅샷을 볼 수 있습니다.

    [![스냅샷 상태 메시지와 함께 활동 창의 링크 위치를 강조 표시한 Azure Storage Explorer 스크린샷.](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-lrg.png#lightbox)

## <a name="next-steps"></a>다음 단계

- [Azure Portal 사용하여 VHD에서 가상 머신 만들기](/azure/virtual-machines/windows/create-vm-specialized-portal)
- [Azure Portal 사용하여 관리되는 데이터 디스크를 Windows 가상 머신에 연결합니다.](/azure/virtual-machines/windows/attach-managed-disk-portal)
