---
title: 포털을 사용하여 VM 이미지 캡처
description: Azure Portal을 사용하여 VM 이미지를 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: b9218f6eebedd8f94c06a664518fc718f0eb7d19
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992120"
---
# <a name="create-an-image-of-a-vm-in-the-portal"></a>포털에서 VM 이미지 만들기

VM에서 이미지를 만든 다음 여러 VM을 만드는 데 사용할 수 있습니다.

공유 이미지 갤러리에 저장된 이미지의 경우 이미 계정이 만들어진(특수화된) VM을 사용하거나 이미지를 만들기 전에 VM을 일반화하여 머신 계정 및 기타 머신 특정 정보를 제거할 수 있습니다. VM을 일반화하려면 [Windows VM 일반화](generalize.md)를 참조하세요. 자세한 내용은 [일반화 및 특수화 이미지](shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.


## <a name="capture-a-vm-in-the-portal"></a>포털에서 VM 캡처 

1. [Azure Portal](https://portal.azure.com)로 이동한 다음 **가상 머신** 을 검색하여 선택합니다.

2. 목록에서 VM을 선택합니다.

3. VM의 **가상 머신** 페이지 위쪽 메뉴에서 **캡처** 를 선택합니다.

   **이미지 만들기** 페이지가 나타납니다.

5. **리소스 그룹** 에서 **새로 만들기** 를 선택하고 이름을 입력하거나, 드롭다운 목록에서 사용할 리소스 그룹을 선택합니다. 기존 갤러리를 사용하려면 사용하려는 갤러리의 리소스 그룹을 선택합니다.

1. 갤러리에서 이미지를 만들려면 **예, 갤러리에 이미지 버전으로 공유합니다.** 를 선택합니다.
    
   관리되는 이미지만 만들려면 **아니요, 관리되는 이미지만 캡처합니다.** 를 선택합니다. 관리되는 이미지를 만들려면 VM을 일반화해야 합니다. 다른 유일한 필수 정보는 이미지의 이름입니다.

6. 이미지가 만들어진 후 원본 VM을 삭제하려면 **이미지를 만든 후 이 가상 머신을 자동으로 삭제** 를 선택합니다. 이 방법은 권장되지 않습니다.

1. **갤러리 세부 정보** 에서 갤러리를 선택하거나 **새로 만들기** 를 선택하여 새 갤러리를 만듭니다.

1. **운영 체제 상태** 에서 일반화 또는 전문화를 선택합니다. 자세한 내용은 [일반화 및 특수화 이미지](shared-image-galleries.md#generalized-and-specialized-images)를 참조하세요.
 
1. 이미지 정의를 선택하거나 **새로 만들기** 를 선택하고 새 [이미지 정의](shared-image-galleries.md#image-definitions)에 대한 이름과 정보를 제공합니다.

1. [이미지 버전](shared-image-galleries.md#image-versions) 번호를 입력합니다. 이 이미지의 첫 번째 버전인 경우 *1.0.0* 을 입력합니다.

1. 이미지 버전에 대해 *최신* 을 지정할 때 이 버전을 포함하려면 **최신 버전에서 제외** 를 선택하지 않은 상태로 둡니다.

1. **수명 종료** 날짜를 선택합니다. 이 날짜는 오래된 이미지를 사용 중지해야 하는 시기를 추적하는 데 사용할 수 있습니다.

1. [복제](shared-image-galleries.md#replication)에서 기본 복제본 수를 선택한 다음 이미지를 복제할 추가 지역을 선택합니다.

8. 완료되면 **검토 + 만들기** 를 선택합니다.

1. 유효성 검사가 통과되면 **만들기** 를 선택하여 이미지를 만듭니다.



## <a name="next-steps"></a>다음 단계

- [공유 이미지 갤러리 개요](shared-image-galleries.md)  
