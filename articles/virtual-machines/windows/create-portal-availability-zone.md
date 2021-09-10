---
title: Azure Portal을 사용하여 영역 VM 만들기
description: Azure Portal을 사용하여 가용성 영역에서 VM 만들기
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 5/10/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 59250d3e2ed69da44e1d5e9187a12c1abac69d25
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688847"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Azure Portal을 사용하여 가용성 영역에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서는 Azure Portal을 사용하여 Azure 가용성 영역에서 가상 머신을 만드는 단계를 안내합니다. [가용성 영역](../../availability-zones/az-overview.md)은 Azure 지역에서 물리적으로 별도 영역입니다. 가용성 영역을 사용하여 가능성이 적은 실패 또는 전체 데이터 센터의 손실로부터 앱 및 데이터를 보호합니다.

가용성 영역을 사용하려면 [지원되는 Azure 지역](../../availability-zones/az-region.md)에 가상 머신을 만들어야 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.

1. **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 클릭합니다. 

3. 가상 머신 정보를 입력합니다. 사용자 이름과 암호는 가상 머신에 로그인하는 데 사용됩니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다. 

4. 가용성 영역을 지원하는 지역(예: 미국 동부 2)을 선택합니다. 

5. **가용성 옵션** 에서 **가용성 영역** 드롭다운을 선택합니다. 

1. **가용성 영역** 아래 드롭다운 목록에서 영역을 선택합니다.
        
4. VM의 크기를 선택합니다. 권장된 크기를 선택하거나 기능을 기반으로 필터링합니다. 사용하려는 영역에서 크기를 사용할 수 있는지 확인합니다.

6. VM에 관한 정보 입력을 완료합니다. 완료되면 **검토 + 만들기** 를 선택합니다.

7. 정보를 확인한 후 **만들기** 를 선택합니다.

1. VM을 만든 후 VM 페이지에서 **Essentials 섹션** 에 나열된 가용성 영역을 볼 수 있습니다.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>관리 디스크 및 IP 주소에 대한 영역 확인

가용성 영역에 VM을 배포하는 경우 VM에 대한 관리 디스크는 동일한 가용성 영역에서 만들어집니다. 기본적으로 공용 IP 주소도 해당 영역에서 만들어집니다.

포털에서 이러한 리소스에 대한 영역 설정을 확인할 수 있습니다.  

1. 왼쪽 메뉴에서 **디스크** 를 선택한 다음, OS 디스크를 선택합니다. 디스크 페이지에는 리소스의 위치와 가용성 영역에 관한 세부 정보가 포함됩니다.

1. VM 페이지로 돌아가서 공용 IP 주소를 선택합니다. 왼쪽 메뉴에서 **속성** 을 선택합니다. 속성 페이지에는 공용 IP 주소의 위치와 가용성 영역에 관한 세부 정보가 포함됩니다.

    
## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역에서 VM을 만드는 방법을 배웠습니다. Azure VM의 [가용성](../availability.md)에 대해 자세히 알아보세요.
