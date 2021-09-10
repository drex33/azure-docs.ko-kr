---
title: 'VM의 D: 드라이브를 데이터 디스크로 만들기 '
description: 'Windows VM의 D: 드라이브를 데이터 드라이브로 사용할 수 있도록 드라이브 문자를 변경하는 방법을 설명합니다.'
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 83151da2cff5d4eb7fc626573b3e7719bb3a2444
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688154"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>D: 드라이브를 Windows VM의 데이터 드라이브로 사용

**적용 대상:** :heavy_check_mark: Windows VM 


애플리케이션에서 D 드라이브를 사용하여 데이터를 저장해야 하는 경우 다음 지침에 따라 임시 디스크에 다른 드라이브 문자를 사용할 수 있습니다. 보관해야 하는 데이터를 저장하는 데 임시 디스크를 사용하지 마세요.

가상 머신의 크기를 조정하거나 **중지(할당 취소)** 하는 경우 새 하이퍼바이저로 가상 머신의 배치를 트리거할 수 있습니다. 계획되거나 계획되지 않은 유지 관리 이벤트로 이 배치가 트리거될 수도 있습니다. 이 시나리오에서는 임시 디스크가 첫 번째 사용 가능한 드라이브 문자에 다시 할당됩니다. 특히 D: 드라이브가 필요한 애플리케이션의 경우 이러한 단계를 사용하여 pagefile.sys를 일시적으로 이동하고, 새 데이터 디스크를 연결한 후 문자 D를 할당한 다음, pagefile.sys를 임시 드라이브로 다시 이동해야 합니다. 완료된 후 VM이 다른 하이퍼바이저로 이동되어도 Azure는 D:를 다시 취소하지 않습니다.

Azure에서 임시 디스크를 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure Virtual Machines에서의 임시 드라이브 이해](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

## <a name="attach-the-data-disk"></a>데이터 디스크 연결
우선 가상 머신에 데이터 디스크를 연결해야 합니다. 포털을 사용하여 이를 수행하려면 [Azure Portal에서 관리되는 데이터 디스크를 연결하는 방법](attach-managed-disk-portal.md)을 참조하세요.

## <a name="temporarily-move-pagefilesys-to-c-drive"></a> pagefile.sys를 C 드라이브로 임시 이동
1. 가상 머신에 연결합니다. 
2. **시작** 메뉴를 마우스 오른쪽 단추로 클릭하고 **시스템** 을 선택합니다.
3. 왼쪽 메뉴에서 **고급 시스템 설정** 을 선택합니다.
4. **성능** 섹션에서 **설정** 을 선택합니다.
5. **고급** 탭을 선택합니다.
6. **가상 메모리** 섹션에서 **변경** 을 선택합니다.
7. **C** 드라이브를 선택한 후 **시스템이 관리하는 크기** 를 클릭하고 **설정** 을 클릭합니다.
8. **D** 드라이브를 선택하고 **페이징 파일 없음** 을 클릭하고 **설정** 을 클릭합니다.
9. 적용을 클릭합니다. 변경 내용을 적용하려면 컴퓨터를 다시 시작해야 한다는 경고가 표시됩니다.
10. 가상 컴퓨터를 다시 시작합니다.

## <a name="change-the-drive-letters"></a>드라이브 문자 변경
1. VM이 다시 시작되면 VM에 다시 로그인합니다.
2. **시작** 메뉴를 클릭하고 **diskmgmt.msc** 를 입력한 후 Enter 키를 누릅니다. 디스크 관리가 시작됩니다.
3. 임시 스토리지 드라이브인 **D** 를 마우스 오른쪽 단추로 클릭하고 **드라이브 문자 및 경로 변경** 을 선택합니다.
4. 드라이브 문자에서 **T** 와 같은 새 드라이브를 선택한 후 **확인** 을 클릭합니다. 
5. 데이터 디스크를 마우스 오른쪽 단추로 클릭하고 **드라이브 문자 및 경로 변경** 을 선택합니다.
6. 드라이브 문자에서 드라이브 **D** 를 선택한 후 **확인** 을 클릭합니다. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>pagefile.sys를 임시 스토리지 드라이브로 다시 이동합니다.
1. **시작** 메뉴를 마우스 오른쪽 단추로 클릭하고 **시스템** 을 선택합니다.
2. 왼쪽 메뉴에서 **고급 시스템 설정** 을 선택합니다.
3. **성능** 섹션에서 **설정** 을 선택합니다.
4. **고급** 탭을 선택합니다.
5. **가상 메모리** 섹션에서 **변경** 을 선택합니다.
6. OS 드라이브 **C** 를 선택하고 **페이징 파일 없음** 을 클릭하고 **설정** 을 클릭합니다.
7. 임시 스토리지 드라이브 **T** 를 선택한 후 **시스템이 관리하는 크기** 를 클릭하고 **설정** 을 클릭합니다.
8. **적용** 을 클릭합니다. 변경 내용을 적용하려면 컴퓨터를 다시 시작해야 한다는 경고가 표시됩니다.
9. 가상 컴퓨터를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계
* [추가 데이터 디스크를 연결](attach-managed-disk-portal.md)하여 가상 머신에서 사용할 수 있는 스토리지를 늘릴 수 있습니다.
