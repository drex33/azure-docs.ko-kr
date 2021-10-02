---
title: Azure Virtual Machine으로 공용 IP 주소 관리
titleSuffix: Azure Virtual Network
description: Azure Virtual Machines에서 공용 IP 주소를 사용하고 구성을 변경하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: a21b734ee1825a86c9276a2e603d04d723bd8d46
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373018"
---
# <a name="manage-a-public-ip-address-with-an-azure-virtual-machine"></a>Azure Virtual Machine으로 공용 IP 주소 관리

공용 IP 주소는 표준 및 기본의 두 가지 SKU로 제공됩니다. SKU를 선택하면 IP 주소의 기능이 결정됩니다. SKU에 따라 IP 주소를 연결할 수 있는 리소스가 결정됩니다.  

Azure Virtual Machines는 Azure의 주요 컴퓨팅 서비스입니다. 고객은 Linux 또는 Windows 가상 머신을 만들 수 있습니다. 가상 머신에 대한 인바운드 연결을 위해 가상 머신에 공용 IP 주소를 할당할 수 있습니다. 

가상 머신에는 해당 구성으로 공용 IP 주소가 필요하지 않습니다.

이 문서에서는 구독의 기존 공용 IP를 사용해서 Azure Virtual Machine을 만드는 방법을 알아봅니다. 가상 머신에 공용 IP 주소를 추가하는 방법을 알아봅니다. IP 주소를 변경합니다. 마지막으로 공용 IP를 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 구독에 두 개의 표준 SKU 공용 IP 주소가 있습니다. IP 주소를 리소스에 연결할 수 없습니다. 표준 SKU 공용 IP 주소를 만드는 방법에 대한 자세한 내용은 [공용 IP 주소 만들기 - Azure Portal](../../virtual-network/create-public-ip-portal.md)을 참조하세요.
    - 이 문서의 예시에 대해 새 공용 IP 주소 이름을 **myStandardPublicIP-1** 및 **myStandardPublicIP-2** 로 지정합니다.
- 구독에서 **인터넷** 의 라우팅 기본 설정이 포함된 하나의 표준 SKU 공용 IP 주소입니다. **인터넷** 라우팅 기본 설정이 포함된 공용 IP 만들기에 대한 자세한 내용은 [Azure Portal을 사용하여 공용 IP 주소에 대한 라우팅 기본 설정 구성](../../virtual-network/routing-preference-portal.md)을 참조하세요.
    - 이 문서의 예시에 대해 새 공용 IP 주소 이름을 **myStandardPublicIP-3** 으로 지정합니다.
## <a name="create-virtual-machine-existing-public-ip"></a>가상 머신 기존 공용 IP 만들기

이 섹션에서는 가상 머신을 만듭니다. 사전 요구 사항에서 만든 IP 주소를 가상 머신의 공용 IP로 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다.

3. 검색 결과에서 **가상 머신** 을 선택합니다.

4. **+ 추가** 를 선택한 후 **+ 가상 머신** 을 선택합니다.

5. **가상 머신 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **myResourceGroupVM** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. | 
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다.** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1** 을 선택합니다. |
    | Azure Spot 인스턴스  | 선택 안 함(기본값)을 그대로 둡니다. |
    | 크기 | 가상 머신의 크기 선택 |
    | **관리자 계정** |   |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 확인합니다. |
    | **인바운드 포트 규칙** |   |
    | 공용 인바운드 포트 | **선택한 포트 허용** 기본값을 그대로 둡니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 기본값을 그대로 둡니다. |

6. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.

7. **네트워킹** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워크 인터페이스** |   |
    | 가상 네트워크 | **(신규) myResourceGroupVM-vnet** 기본값을 그대로 둡니다. |
    | 서브넷 | **(신규) 기본값(10.1.0.0/24)** 을 그대로 둡니다. |
    | 공용 IP | **myStandardPublicIP-1** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 기본값을 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 기본값을 그대로 둡니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 기본값을 그대로 둡니다. |

6. **검토 + 만들기** 탭을 선택하거나 파란색 **검토 + 만들기** 단추를 선택합니다.

7. **만들기** 를 선택합니다.

> [!NOTE]
> 이것은 Azure Virtual Machine의 간단한 배포를 보여줍니다. 고급 구성 및 설정에 대해서는 [빠른 시작: Azure Portal에서 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md)를 참조하세요.
>
> Azure Virtual Machines에 대한 자세한 내용은 [Azure의 Windows Virtual Machines](../../virtual-machines/windows/overview.md)를 참조하세요.

## <a name="change-public-ip-address"></a>공용 IP 주소 변경

이 섹션에서는 가상 머신의 기본 공용 IP 구성과 연결된 공용 IP 주소를 변경합니다.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다.

2. 검색 결과에서 **가상 머신** 을 선택합니다.

3. **가상 머신** 에서 **myVM** 을 선택합니다.

4. **myVM** 의 **설정** 에서 **네트워킹** 을 선택합니다.

5. **네트워킹** 에서 VM의 **네트워크 인터페이스** 를 선택합니다. NIC 이름은 앞에 VM 이름이 추가되고 임의 숫자로 끝납니다.  이 예제에서는 **myvm793** 입니다.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="네트워크 인터페이스를 선택합니다." border="true":::

6. 네트워크 인터페이스의 **설정** 에서 **IP 구성** 을 선택합니다.

7. **IP 구성** 에서 **ipconfig1** 을 선택합니다.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="ipconfig를 선택하여 IP 주소를 변경합니다." border="true":::

1. **ipconfig1** 의 **공용 IP 주소** 에서 **myStandardPublicIP-2** 를 선택합니다.

7. **저장** 을 선택합니다.

## <a name="add-public-ip-configuration"></a>공용 IP 구성 추가

이 섹션에서는 가상 머신에 공용 IP 구성을 추가합니다. 

여러 IP 주소 추가에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 머신에 여러 IP 주소 할당](../../virtual-network/virtual-network-multiple-ip-addresses-portal.md)을 참조하세요. 

라우팅 기본 설정 유형을 모두 사용하는 방법은 [가상 머신에 대한 라우팅 기본 설정 옵션 모두 구성](../../virtual-network/routing-preference-mixed-network-adapter-portal.md)을 참조하세요.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다.

2. 검색 결과에서 **가상 머신** 을 선택합니다.

3. **가상 머신** 에서 **myVM** 을 선택합니다.

4. **myVM** 의 **설정** 에서 **네트워킹** 을 선택합니다.

5. **네트워킹** 에서 VM의 **네트워크 인터페이스** 를 선택합니다. NIC 이름은 앞에 VM 이름이 추가되고 임의 숫자로 끝납니다.  이 예제에서는 **myvm793** 입니다.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="네트워크 인터페이스를 선택합니다." border="true":::

6. 네트워크 인터페이스의 **설정** 에서 **IP 구성** 을 선택합니다.

7. **IP 구성** 에서 **+ 추가** 를 선택합니다.

8. **이름** 에 **ipconfig2** 를 입력합니다.

9. **공용 IP 주소** 에서 **연결** 을 선택합니다.

10. **공용 IP 주소** 에서 **myStandardPublicIP-3** 를 선택합니다.

11. **확인** 을 선택합니다.

## <a name="remove-public-ip-address-association"></a>공용 IP 주소 연결 제거

이 섹션에서는 네트워크 인터페이스에서 공용 IP 주소를 제거합니다. 이 프로세스 이후의 가상 머신은 외부 연결에 제공되지 않습니다.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다.

2. 검색 결과에서 **가상 머신** 을 선택합니다.

3. **가상 머신** 에서 **myVM** 을 선택합니다.

4. **myVM** 의 **설정** 에서 **네트워킹** 을 선택합니다.

5. **네트워킹** 에서 VM의 **네트워크 인터페이스** 를 선택합니다. NIC 이름은 앞에 VM 이름이 추가되고 임의 숫자로 끝납니다.  이 예제에서는 **myvm793** 입니다.

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="네트워크 인터페이스를 선택합니다." border="true":::

6. 네트워크 인터페이스의 **설정** 에서 **IP 구성** 을 선택합니다.

7. **IP 구성** 에서 **ipconfig1** 을 선택합니다.  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="ipconfig를 선택하여 IP 주소를 변경합니다." border="true":::

8. **공용 IP 주소 설정** 에서 **연결 해제** 를 선택합니다.

9. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 머신을 만들고 기존 공용 IP를 사용하는 방법을 알아봤습니다. 기본 IP 구성의 공용 IP를 변경했습니다. 마지막으로 인터넷 라우팅 기본 설정을 사용하여 방화벽에 공용 IP 구성을 추가했습니다.

- Auzre의 공용 IP 주소에 대한 자세한 내용은 [공용 IP 주소](../../virtual-network/public-ip-addresses.md)를 참조하세요.
