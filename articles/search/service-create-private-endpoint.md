---
title: 보안 연결을 위한 프라이빗 엔드포인트 만들기
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에 대한 보안 연결을 위해 가상 네트워크에서 프라이빗 엔드포인트를 설정합니다.
author: nitinme
ms.author: nitinme
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 71a618daeeb2400e32a53b555e9499a5edd69b5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061089"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Azure Cognitive Search에 보안 연결을 위한 프라이빗 엔드포인트 만들기

이 문서에서는 Azure Portal을 사용하여 인터넷을 통해 액세스할 수 없는 새 Azure Cognitive Search 서비스 인스턴스를 만듭니다. 다음으로 동일한 가상 네트워크에 Azure 가상 머신을 구성하고 이를 사용하여 프라이빗 엔드포인트를 통해 검색 서비스에 액세스합니다.

프라이빗 엔드포인트는 [Azure Private Link](../private-link/private-link-overview.md)에 의해 별도의 서비스로 제공됩니다. 비용에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조하세요.

이 문서에서 설명하는 것과 같이 Azure Portal에서 프라이빗 엔드포인트를 만들 수 있습니다. 또는 [Management REST API version 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)를 사용할 수 있습니다.

> [!NOTE]
> 서비스 엔드포인트가 프라이빗인 경우 일부 포털 기능이 사용하지 않도록 설정됩니다. 서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서 및 기술 세트 정보는 보안상의 이유로 숨겨집니다. 포털 대신 [VS Code 확장](https://aka.ms/vscode-search)을 사용하여 서비스의 다양한 구성 요소와 상호 작용할 수 있습니다.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>보안 액세스에 프라이빗 엔드포인트를 사용하는 이유는 무엇입니까?

Azure Cognitive Search를 위한 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)는 가상 네트워크의 클라이언트가 [Private Link](../private-link/private-link-overview.md)를 통해 검색 인덱스의 데이터에 안전하게 액세스할 수 있도록 합니다. 프라이빗 엔드포인트는 검색 서비스에 [가상 네트워크 주소 공간](../virtual-network/ip-services/private-ip-addresses.md)의 IP 주소를 사용합니다. 클라이언트와 검색 서비스 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통과하여 퍼블릭 인터넷에서 공개되지 않도록 합니다. Private Link를 지원하는 다른 PaaS 서비스 목록은 제품 설명서의 [가용성 섹션](../private-link/private-link-overview.md#availability)을 참조하세요.

검색 서비스에 대한 프라이빗 엔드포인트를 사용하면 다음이 가능합니다.

- 검색 서비스에 대한 퍼블릭 엔드포인트의 모든 연결을 차단합니다.
- 가상 네트워크에서의 데이터 반출을 차단하여 가상 네트워크 보안을 강화합니다.
- 개인 피어링을 통해 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes](../expressroute/expressroute-locations.md)를 사용하여 가상 네트워크에 연결하는 온-프레미스 네트워크에서 검색 서비스에 안전하게 연결합니다.

## <a name="create-the-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 검색 서비스의 프라이빗 엔드포인트에 액세스하는 데 사용되는 VM을 호스트하는 가상 네트워크 및 서브넷을 만듭니다.

1. Azure Portal 홈 탭에서 **리소스 만들기** > **네트워킹** > **가상 네트워크** 를 선택합니다.

1. **가상 네트워크 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독 선택|
    | Resource group | **새로 만들기** 를 선택하고 *myResourceGroup* 을 입력한 다음 **확인** 을 선택합니다. |
    | 이름 | *MyVirtualNetwork* 입력 |
    | 지역 | 원하는 지역 선택 |
    |||

1. 나머지 설정은 기본값을 유지합니다. **검토 + 만들기** 를 클릭한 다음 **만들기** 를 클릭합니다.

## <a name="create-a-search-service-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하여 검색 서비스 만들기

이 섹션에서는 프라이빗 엔드포인트를 사용하여 새 Azure Cognitive Search 서비스를 만듭니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **웹** > **Azure Cognitive Search** 를 선택합니다.

1. **새 검색 서비스 - 기본** 에서 이 정보를 입력 또는 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 정보** |  |
    | URL | 고유한 이름을 입력합니다. |
    | Location | 원하는 지역을 선택합니다. |
    | 가격 책정 계층 | **가격 책정 계층 변경** 을 선택하고 원하는 서비스 계층을 선택합니다. (**무료** 계층에서는 지원되지 않습니다. **기본** 이상이어야 합니다.) |
    |||
  
1. **다음: 규모** 를 선택합니다.

1. 기본값을 그대로 두고 **다음: 네트워킹** 을 선택합니다.

1. **새 검색 서비스 - 네트워킹** 에서 **엔드포인트 연결(데이터)** 에 대해 **비공개** 를 선택합니다.

1. **새 검색 서비스 - 네트워킹** 에서 **프라이빗 엔드포인트** 에 있는 **+ 추가** 를 선택합니다. 

1. **프라이빗 엔드포인트 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | Location | **미국 서부** 를 선택합니다.|
    | Name | *myPrivateEndpoint* 를 입력합니다.  |
    | 대상 하위 리소스 | 기본 **searchService** 를 그대로 둡니다. |
    | **네트워킹** |  |
    | 가상 네트워크  | 리소스 그룹 *myResourceGroup* 에서 *MyVirtualNetwork* 를 선택합니다. |
    | 서브넷 | *mySubnet* 을 선택합니다. |
    | **프라이빗 DNS 통합** |  |
    | 프라이빗 DNS 영역과 통합  | **예**(기본값)를 그대로 둡니다. |
    | 프라이빗 DNS 영역  | 기본값 **(New) privatelink.search.windows.net**을 그대로 둡니다. |
    |||

1. **확인** 을 선택합니다. 

1. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 

1. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다. 

1. 새 서비스의 프로비저닝이 완료되면 방금 만든 리소스를 찾습니다.

1. 왼쪽 콘텐츠 메뉴에서 **키** 를 선택합니다.

1. 이후 서비스에 연결할 때 사용하도록 **기본 관리자 키** 를 복사합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 선택합니다.

1. **가상 머신 만들기 - 기본 사항** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *myVm* 을 입력합니다. |
    | 지역 | **미국 서부** 또는 사용 중인 지역을 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다** 를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | 크기 | 기본값인 **표준 DS1 v2** 를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본값인 **선택한 포트 허용** 을 그대로 둡니다. |
    | 인바운드 포트 선택 | 기본값인 **RDP(3389)** 를 그대로 둡니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요** 를 그대로 둡니다. |
    |||

1. 완료되면 **다음: 디스크** 를 선택합니다.

1. **가상 머신 만들기 - 디스크** 에서 기본값을 그대로 두고 **다음: 네트워킹** 을 선택합니다.

1. **가상 머신 만들기 - 네트워킹** 에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본값인 **MyVirtualNetwork** 를 그대로 둡니다.  |
    | 주소 공간 | 기본값인 **10.1.0.0/24** 를 그대로 둡니다.|
    | 서브넷 | 기본값인 **mySubnet(10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip** 를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP** 를 선택합니다.|
    ||

   > [!NOTE]
   > IPv4 주소는 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 형식으로 표현할 수 있습니다. [RFC 1918](https://tools.ietf.org/html/rfc1918)에서 설명하는 것과 같이 프라이빗 네트워킹에 예약된 IP 범위를 피해야 합니다.
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다. 

## <a name="connect-to-the-vm"></a>VM에 연결

다음과 같이 다운로드하고 *myVm* VM에 연결합니다.

1. 포털의 검색 창에 *myVm* 을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결** 이 열립니다.

1. **RDP 파일 다운로드** 를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드 한 .rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결** 을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용** 을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인** 을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속** 을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="test-connections"></a>연결 테스트

이 섹션에서는 검색 서비스에 대한 프라이빗 네트워크 액세스를 확인하고 프라이빗 엔드포인트를 사용하여 비공개로 연결합니다.

검색 서비스 엔드포인트가 프라이빗인 경우 일부 포털 기능이 사용하지 않도록 설정됩니다. 서비스 수준 설정을 보고 관리할 수 있지만 인덱스, 인덱서, 기술 세트 정의와 같은 서비스의 다양한 구성 요소 및 인덱스에 대한 포털의 액세스는 보안상의 이유로 제한됩니다.

1. *myVM* 의 원격 데스크톱에서 PowerShell을 엽니다.

1. 'nslookup [search service name].search.windows.net'을 입력합니다.

    다음과 유사한 메시지가 표시됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. VM에서 검색 서비스에 연결하여 인덱스를 만듭니다. 이 [빠른 시작](search-get-started-rest.md)을 따라 REST API를 사용하여 서비스에서 새 검색 인덱스를 만들 수 있습니다. 웹 API 테스트 도구에서 요청을 설정하려면 검색 서비스 엔드포인트(https://[search service name].search.windows.net)와 이전 단계에서 복사한 관리자 API 키가 필요합니다.

1. VM에서 빠른 시작을 완료하면 서비스가 완전히 작동하는지 확인하는 것입니다.

1. *myVM* 에 대한 원격 데스크톱 연결을 닫습니다. 

1. 퍼블릭 엔드포인트에서 서비스에 액세스할 수 없는지 확인하려면 로컬 워크스테이션에서 Postman을 열고 빠른 시작에서 처음 몇 가지 작업을 시도합니다. 원격 서버가 존재하지 않는다는 오류가 표시되면 검색 서비스에 대한 프라이빗 엔드포인트를 성공적으로 구성한 것입니다.

## <a name="clean-up-resources"></a>리소스 정리 
프라이빗 엔드포인트, 검색 서비스 및 VM 사용을 완료하면 포함된 모든 리소스에서 리소스 그룹을 삭제합니다.
1. 포털 맨 위에 있는 **검색** 상자에  *myResourceGroup* 을 입력하고 검색 결과에서  *myResourceGroup* 을 선택합니다. 
1. **리소스 그룹 삭제** 를 선택합니다. 
1.  **리소스 그룹 이름 입력** 에 대해  *myResourceGroup* 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 가상 네트워크에서 VM을 만들고 프라이빗 엔드포인트를 사용하여 검색 서비스를 만들었습니다. 인터넷에서 VM에 연결하고 Private Link를 사용하여 검색 서비스에 안전하게 전달했습니다. 프라이빗 엔드포인트에 대한 자세한 정보는  [Azure 개인 엔드포인트란 무엇인가요?](../private-link/private-endpoint-overview.md)를 참조하세요.