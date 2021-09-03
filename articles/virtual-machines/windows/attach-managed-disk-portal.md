---
title: Windows VM에 관리되는 데이터 디스크 연결 - Azure
description: Azure Portal을 사용하여 Windows VM에 관리되는 데이터 디스크를 연결하는 방법입니다.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ba6691e343276445bd881d2cbe2f8fcb5d1d314f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666530"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Windows VM에 관리되는 데이터 디스크 연결

이 문서에서는 Azure Portal을 사용하여 새롭게 관리되는 데이터 디스크를 Windows VM(가상 머신)에 연결하는 방법을 보여 줍니다. VM 크기에 따라 연결할 수 있는 데이터 디스크 개수가 결정됩니다. 자세한 내용은 [가상 머신의 크기](../sizes.md)를 참조하세요.


## <a name="add-a-data-disk"></a>데이터 디스크 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **가상 머신** 을 검색하여 선택합니다.
1. 목록에서 가상 머신을 선택합니다.
1. **가상 머신** 창에서 **디스크** 를 선택합니다.
1. **디스크** 창에서 **새 디스크 만들기 및 연결** 을 선택합니다.
1. 새 디스크의 드롭다운에서 원하는 항목을 선택하고 디스크 이름을 지정합니다.
1. **저장** 을 선택하여 새 데이터 디스크를 만들고 VM에 연결합니다.

## <a name="initialize-a-new-data-disk"></a>새 데이터 디스크 초기화

1. VM에 연결합니다.
1. 실행 중인 VM 내에서 Windows **시작** 메뉴를 선택하고 검색 상자에 **diskmgmt.msc** 를 입력합니다. **디스크 관리** 콘솔이 열립니다.
1. 디스크 관리에서 초기화되지 않은 새 디스크가 있다고 인식하고 **디스크 초기화** 창이 나타납니다.
1. 새 디스크가 선택되었는지 확인한 후 **확인** 을 선택하여 초기화합니다.
1. 새 디스크가 **할당되지 않음** 으로 나타납니다. 디스크의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 **새 단순 볼륨** 을 선택합니다. **새 단순 볼륨 마법사** 창이 열립니다.
1. 모든 기본값을 유지하여 마법사를 계속 진행하고 완료하면 **마침** 을 선택합니다.
1. **디스크 관리** 를 닫습니다.
1. 사용하기 전에 새 디스크를 포맷해야 함을 알리는 팝업 창이 나타납니다. **디스크 포맷** 을 선택합니다.
1. **새 디스크 포맷** 창에서 설정을 확인한 후 **시작** 을 선택합니다.
1. 디스크를 포맷하면 모든 데이터가 지워짐을 알리는 경고가 나타납니다. **확인** 을 선택합니다.
1. 포맷이 완료되면 **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- 또한 [PowerShell을 사용하여 데이터 디스크를 연결](attach-disk-ps.md)할 수 있습니다.
- 애플리케이션이 데이터를 저장하는 데 *D:* 드라이브를 사용해야 하면 [Windows 임시 디스크의 드라이브 문자를 변경](change-drive-letter.md)할 수 있습니다.