---
title: 포털을 사용하여 Azure Shared Image Gallery 만들기
description: Azure Portal을 사용하여 가상 머신 이미지를 만들고 공유하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/21/2021
ms.author: cynthn
ms.openlocfilehash: 4ac9577b91fde3af20374d9d06a24f6c7dd7dce2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692435"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>포털을 사용하여 Azure Shared Image Gallery 만들기

**적용 대상:** :heavy_check_mark: Windows VM 

[공유 이미지 갤러리](../shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 배포 작업에 사용자 지정 이미지를 사용할 수 있습니다. 

공유 이미지 갤러리를 사용하면 조직, 지역, AAD 테넌트의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 여러 갤러리를 만들어서 공유 이미지 논리적으로 그룹화할 수 있습니다. 

갤러리는 전체 Azure RBAC(Azure 역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 이미지 버전을 관리할 수 있으며, 각 이미지 버전을 여러 Azure 지역에 복제하도록 선택할 수 있습니다. 갤러리는 관리되는 이미지에서만 작동합니다.

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다. 이 문서에서는 이러한 리소스를 사용하거나 작성하게 됩니다.


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>VM 만들기

이제 새 VM을 한 개 이상 만들 수 있습니다. 이 예제에서는 ‘미국 동부’ 데이터 센터에서 *myResourceGroup* 에 *myVM* 이라는 VM을 만듭니다.

1. 이미지 정의로 이동합니다. 리소스 필터를 사용하여 사용 가능한 모든 이미지 정의를 표시할 수 있습니다.
1. 이미지 정의 페이지의 맨 위에 있는 메뉴에서 **VM 만들기** 를 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고 *myResourceGroup* 을 이름으로 입력합니다.
1. **가상 머신 이름** 에 *myVM* 을 입력합니다.
1. **지역** 에 대해 *미국 동부* 를 선택합니다.
1. **가용성 옵션** 의 경우에는 ‘인프라 중복이 필요하지 않습니다’ 기본값을 그대로 둡니다.
1. 이미지 정의에 대한 페이지에서 시작한 경우 **이미지** 값이 `latest` 이미지 버전으로 자동으로 채워집니다.
1. **크기** 의 경우 사용 가능한 크기 목록에서 VM 크기를 고른 다음 **선택** 을 선택합니다.
1. **Administrator 계정** 에서 이미지가 일반화된 경우 *azureuser* 와 같은 사용자 이름과 암호를 제공해야 합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다. 이미지가 특수화된 경우 원본 VM의 사용자 이름과 암호가 사용되므로 사용자 이름 및 암호 필드가 회색으로 표시됩니다.
1. VM에 대한 원격 액세스를 허용하려면 **퍼블릭 인바운드 포트** 에서 **선택한 포트 허용** 을 선택한 다음 드롭다운에서 **RDP(3389)** 를 선택합니다. VM에 대한 원격 액세스를 허용하지 않으려면 **퍼블릭 인바운드 포트** 에 대해 **안 함** 을 선택한 상태로 둡니다.
1. 작업을 마치면 페이지 하단의 **검토 + 만들기** 단추를 선택합니다.
1. VM이 유효성 검사를 통과하면 페이지 하단에서 **만들기** 를 선택하여 배포를 시작합니다.


## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고 **삭제** 를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

개별 리소스를 삭제하려면 해당 리소스를 역순으로 삭제해야 합니다. 예를 들어 이미지 정의를 삭제하려면 해당 이미지에서 만든 모든 이미지 버전을 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](../shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](../troubleshooting-shared-images.md)을 참조하세요.
