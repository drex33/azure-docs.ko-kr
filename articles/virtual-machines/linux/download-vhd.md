---
title: Azure에서 Linux VHD 다운로드
description: Azure CLI 및 Azure Portal을 사용하여 Linux VHD를 다운로드합니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 393706ca463d8a3cc6edaede2689feae3e867bfa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074501"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure에서 Linux VHD 다운로드

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 Azure Portal을 사용하여 Azure에서 Linux VHD(가상 하드 디스크) 파일을 다운로드하는 방법을 알아봅니다. 

## <a name="stop-the-vm"></a>VM을 중지합니다.

실행 중인 VM에 연결된 경우 Azure에서 VHD는 다운로드할 수 없습니다. VM을 계속 실행하려면 [스냅샷을 만든 다음 스냅샷을 다운로드](#alternative-snapshot-the-vm-disk)할 수 있습니다.

VM을 중지하려면:

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  왼쪽 메뉴에서 **Virtual Machines** 를 선택합니다.
3.  목록에서 VM을 선택합니다.
4.  VM 페이지에서 **중지** 를 선택합니다.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="VM을 중지하는 메뉴 단추를 표시합니다.":::

### <a name="alternative-snapshot-the-vm-disk"></a>대안: VM 디스크 스냅샷 만들기

다운로드할 디스크의 스냅샷을 만듭니다.

1. [포털](https://portal.azure.com)에서 VM을 선택합니다.
2. 왼쪽 메뉴에서 **디스크** 를 선택한 다음 스냅샷을 만들 디스크를 선택합니다. 디스크의 세부 정보가 표시됩니다.  
3. 페이지 위에 있는 메뉴에서 **스냅샷 만들기** 를 선택합니다. **스냅샷 만들기** 페이지가 열립니다.
4. **이름** 에서 스냅샷의 이름을 입력합니다. 
5. **스냅샷 유형** 에 대해 **전체** 또는 **증분** 을 선택합니다.
6. 완료되면 **검토 + 만들기** 를 선택합니다.

스냅숏이 잠시 생성 되 고 다른 VM을 다운로드 하거나 만드는 데 사용할 수 있습니다.

> [!NOTE]
> VM을 먼저 중지하지 않으면 스냅샷이 정리되지 않습니다. 스냅샷은 VM의 전원을 껐다가 켜거나 VM이 스냅샷이 만들어진 시점에서 중단된 것과 동일한 상태가 됩니다. 일반적으로 안전 하 게 작동 하는 동안 실행 중인 응용 프로그램이 작동 중단 되지 않는 경우 문제가 발생할 수 있습니다.
>  
> 이 방법은 단일 OS 디스크가 있는 VM에만 권장됩니다. 하나 이상의 데이터 디스크가 있는 VM은 다운로드하기 전에 또는 OS 디스크 및 각 데이터 디스크에 대한 스냅샷을 만들기 전에 중지해야 합니다.

## <a name="generate-sas-url"></a>SAS URL 생성

VHD 파일을 다운로드하려면 [SAS(공유 액세스 서명)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL을 생성해야 합니다. URL이 생성될 때 만료 시간이 URL에 할당됩니다.

1. VM 페이지 메뉴에서 **디스크** 를 선택합니다.
2. VM에 대한 운영 체제 디스크를 선택한 다음 **디스크 내보내기** 를 선택합니다.
1. 필요한 경우 **URL 만료(초)** 값을 업데이트하여 다운로드를 완료하는 데 충분한 시간을 제공합니다. 기본값은 3600초(1시간)입니다.
3. **URL 생성** 을 선택합니다.
 
      
## <a name="download-vhd"></a>VHD 다운로드

1.  생성된 URL에서 **VHD 파일 다운로드** 를 선택합니다.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="VHD 다운로드 단추를 표시합니다.":::

2.  다운로드를 시작하려면 브라우저에서 **저장** 을 선택해야 합니다. VHD 파일에 대한 기본 이름은 *abcd* 입니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 업로드 및 만들기](upload-vhd.md) 방법을 알아봅니다. 
- [Azure CLI를 사용하여 Azure 디스크를 관리합니다](tutorial-manage-disks.md).
