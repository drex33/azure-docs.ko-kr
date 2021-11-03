---
title: Azure에서 Windows VHD 다운로드
description: Azure Portal을 사용하여 Windows VHD를 다운로드합니다.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5d43ce73efa602cded23c533fefd29009998b809
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003355"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure에서 Windows VHD 다운로드

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 Azure Portal을 사용하여 Azure에서 Windows VHD(가상 하드 디스크) 파일을 다운로드하는 방법을 알아봅니다.

## <a name="optional-generalize-the-vm"></a>선택 사항: VM 일반화

VHD를 [이미지](tutorial-custom-images.md)로 사용하여 다른 VM을 만들려면 [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)를 사용하여 운영 체제를 일반화해야 합니다. 그러지 않으면 만들려는 각 VM에 대해 디스크를 복사해야 합니다.

VHD를 이미지로 사용하여 다른 VM을 만들려면 VM을 일반화합니다.

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. [VM에 연결합니다](connect-logon.md). 
3. VM에서 관리자로 명령 프롬프트 창을 엽니다.
4. 디렉터리를 *%windir%\system32\sysprep* 로 변경한 후 sysprep.exe를 실행합니다.
5. 시스템 준비 도구 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력** 을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
6. 종료 옵션에서 **종료** 를 선택한 다음 **확인** 을 클릭합니다. 

현재 VM을 일반화하지 않으려는 경우에는 먼저 [OS 디스크의 스냅샷을 만들고](#alternative-snapshot-the-vm-disk), 스냅샷에서 새 VM을 만든 다음, 복사본을 일반화하여 일반화된 이미지를 만들 수 있습니다.

## <a name="stop-the-vm"></a>VM을 중지합니다.

실행 중인 VM에 연결된 경우 Azure에서 VHD는 다운로드할 수 없습니다. VM을 계속 실행하려는 경우에는 [스냅샷을 만든 다음, 스냅샷을 다운로드](#alternative-snapshot-the-vm-disk)할 수 있습니다.

1. Azure Portal에 있는 허브 메뉴에서 **Virtual Machines** 를 클릭합니다.
1. 목록에서 VM을 선택합니다.
1. VM에 대한 블레이드에서 **중지** 를 클릭합니다.

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
> VM을 먼저 중지하지 않으면 스냅샷이 정리되지 않습니다. 스냅샷은 VM의 전원을 껐다가 켜거나 VM이 스냅샷이 만들어진 시점에서 중단된 것과 동일한 상태가 됩니다.  일반적으로 안전 하 게 작동 하는 동안 실행 중인 응용 프로그램이 작동 중단 되지 않는 경우 문제가 발생할 수 있습니다.
>  
> 이 방법은 단일 OS 디스크가 있는 VM에만 권장됩니다. 하나 이상의 데이터 디스크가 있는 VM은 다운로드하기 전에 또는 OS 디스크 및 각 데이터 디스크에 대한 스냅샷을 만들기 전에 중지해야 합니다.

## <a name="generate-download-url"></a>다운로드 URL 생성

VHD 파일을 다운로드하려면 [SAS(공유 액세스 서명)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL을 생성해야 합니다. URL이 생성될 때 만료 시간이 URL에 할당됩니다.

1. VM 관련 페이지의 왼쪽 메뉴에서 **디스크** 를 클릭합니다.
1. VM의 운영 체제 디스크를 선택합니다.
1. 디스크 관련 페이지의 왼쪽 메뉴에서 **디스크 내보내기** 를 선택합니다.
1. URL의 기본 만료 시간은 *3600* 초(1시간)입니다. Windows OS 디스크 또는 대규모 데이터 디스크의 경우 이 시간을 늘려야 할 수 있습니다. 일반적으로 **36000** 초(10시간)로 충분합니다.
1. **URL 생성** 을 클릭합니다.

> [!NOTE]
> Windows Server 운영 체제에 대한 큰 VHD 파일을 다운로드하는 데 충분한 시간을 제공하기 위해 만료 시간은 기본 시간에서 증가되었습니다. 대규모 VHD는 연결 및 VM 크기에 따라 다운로드하는 데 최대 몇 시간이 걸릴 수 있습니다. 
> 
> 

## <a name="download-vhd"></a>VHD 다운로드

1. 생성된 URL에서 VHD 파일 다운로드를 클릭합니다.
1. 다운로드를 시작하려면 브라우저에서 **저장** 을 클릭해야 할 수 있습니다. VHD 파일에 대한 기본 이름은 *abcd* 입니다.

## <a name="next-steps"></a>다음 단계

- [Azure로 VHD 파일 업로드](upload-generalized-managed.md) 방법을 알아봅니다. 
- [스토리지 계정의 비관리 디스크에서 관리 디스크를 만듭니다](attach-disk-ps.md).
- [PowerShell을 사용하여 Azure 디스크를 관리합니다](tutorial-manage-data-disk.md).
