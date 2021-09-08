---
title: Azure Portal을 사용하여 VM에 포트 열기
description: Azure Portal을 사용하여 VM에 포털을 여는 방법/엔드포인트를 생성하는 방법에 대해 알아보기
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/27/2021
ms.author: cynthn
ms.openlocfilehash: 78fce318d07e8603830fe04b41df387e6a25f8d9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227241"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신에 대한 포털을 여는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Azure에 로그인
https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

1. VM에 대한 리소스 그룹을 검색하여 선택하고, **추가** 를 선택한 다음, **네트워크 보안 그룹** 을 검색하여 선택합니다.

1. **만들기** 를 선택합니다.

    **네트워크 보안 그룹 만들기** 창이 열립니다.

    :::image type="content" source="media/nsg-quickstart-portal/create-nsg.png" alt-text="네트워크 보안 그룹을 만듭니다.":::

1. 네트워크 보안 그룹의 이름을 입력합니다. 

1. 리소스 그룹을 선택하거나 만들고 위치를 선택합니다.

1. **만들기** 를 선택하여 네트워크 보안 그룹을 만듭니다.

## <a name="create-an-inbound-security-rule"></a>인바운드 보안 규칙 만들기

1. 새 네트워크 보안 그룹을 선택합니다.

1. 왼쪽 메뉴에서 **인바운드 보안 규칙** 을 선택한 다음, **추가** 를 선택합니다.

    :::image type="content" source="media/nsg-quickstart-portal/advanced.png" alt-text="인바운드 보안 규칙을 추가합니다.":::

1. 필요에 따라 **원본** 및 **원본 포트 범위** 를 제한하거나 기본값인 *모두* 를 그대로 둘 수 있습니다.
1. 필요에 따라 **대상** 을 제한하거나 기본값인 *모두* 를 그대로 둘 수 있습니다.
1. 드롭다운 메뉴에서 공통 **서비스** 를 선택합니다(예: **HTTP**). 사용할 특정 포트를 제공하려는 경우 **사용자 지정** 을 선택할 수도 있습니다. 

1. 선택적으로 **우선 순위** 또는 **이름** 을 변경합니다. 이 우선 순위는 규칙이 적용되는 순서에 영향을 줍니다. 숫자 값이 적을수록 규칙이 먼저 적용됩니다.

1. **추가** 를 선택하여 규칙을 만듭니다.

## <a name="associate-your-network-security-group-with-a-subnet"></a>네트워크 보안 그룹을 서브넷에 연결

마지막 단계는 네트워크 보안 그룹을 서브넷 또는 특정 네트워크 인터페이스에 연결하는 것입니다. 이 예제에서는 네트워크 보안 그룹을 서브넷에 연결합니다. 

1. 왼쪽 메뉴에서 **서브넷** 을 선택한 다음, **연결** 을 선택합니다.

1. 가상 네트워크를 선택하고 적절한 서브넷을 선택합니다.

    ![가상 네트워킹에 네트워크 보안 그룹 연결](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. 완료되면 **확인** 을 선택합니다.

## <a name="additional-information"></a>추가 정보

[Azure PowerShell을 사용하여 이 문서의 단계를 수행](nsg-quickstart-powershell.md)할 수도 있습니다.

이 문서에 설명된 명령을 사용하면 트래픽이 해당 VM으로 흐르도록 빠르게 설정할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../../virtual-network/tutorial-filter-network-traffic.md)을 참조하세요.

고가용성 웹 애플리케이션의 경우 VM을 Azure 부하 분산 장치 뒤에 배치하는 것이 좋습니다. 부하 분산 장치는 트래픽 필터링을 제공하는 네트워크 보안 그룹으로 트래픽을 VM에 분산합니다. 자세한 내용은 [Azure에서 Windows 가상 머신의 부하를 분산하여 고가용성 애플리케이션 만들기](tutorial-load-balancer.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 네트워크 보안 그룹을 만들고, 포트 80에서 HTTP 트래픽을 허용하는 인바운드 규칙을 만든 다음, 해당 규칙을 서브넷에 연결했습니다. 

다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.
- [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)
- [보안 그룹](../../virtual-network/network-security-groups-overview.md)
