---
title: Azure Portal에서 VM에 대한 FQDN 만들기
description: Azure Portal에서 가상 머신에 대한 FQDN(정규화된 도메인 이름)을 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/07/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad48a8d4c2f10bab26e04bcb105747e7a7c474f9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696515"
---
# <a name="create-a-fully-qualified-domain-name-for-a-vm-in-the-azure-portal"></a>Azure Portal에서 정규화된 도메인 이름 만들기

**적용 대상:** **적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs

[Azure Portal](https://portal.azure.com)에서 VM(가상 머신)을 만들 때, 가상 머신의 공용 IP 리소스가 자동으로 만들어집니다. 이 공용 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 포털에서 [정규화된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 추가할 수 있습니다. 이 문서에서는 DNS 이름 또는 FQDN을 만드는 단계를 보여 줍니다. 공용 IP 주소 없이 VM을 만드는 경우 FQDN을 만들 수 없습니다.

## <a name="create-a-fqdn"></a>FQDN 만들기
이 문서에서는 사용자가 VM을 이미 만들었다고 가정합니다. 필요한 경우 포털에서 [Linux](./linux/quick-create-portal.md) 또는 [Windows](./windows/quick-create-portal.md) VM을 만들 수 있습니다. VM이 실행되면 다음 단계를 따릅니다.


1. 포털에서 VM을 선택합니다. 
1. 왼쪽 메뉴에서 **속성** 을 선택합니다.
1. **공용 IP 주소/DNS 이름 레이블** 에서 IP 주소를 선택합니다.
2. **DNS 이름 레이블** 에서 사용할 접두사를 입력합니다.
3. 페이지 위쪽에서 **저장** 을 선택합니다.
4. 왼쪽 메뉴에서 **개요** 를 선택하여 VM 개요 블레이드로 돌아갑니다.
5. ‘DNS 이름’이 올바르게 표시되는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

[Azure DNS 영역](../dns/dns-getstarted-portal.md)을 사용하여 DNS를 관리할 수도 있습니다.

