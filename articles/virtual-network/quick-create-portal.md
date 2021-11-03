---
title: '빠른 시작: 가상 네트워크 만들기 - Azure Portal'
titleSuffix: Azure Virtual Network
description: 이 빠른 시작에서 Azure Portal을 사용하여 가상 네트워크를 만드는 방법에 대해 알아봅니다.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags: azure-resource-manager
ms.custom: mode-portal
ms.openlocfilehash: 1ba52ed17f5eddafe4489ddf9d4277ac301c3e15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074254"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>빠른 시작: Azure 포털을 사용하여 가상 네트워크 만들기

이 빠른 시작에서 Azure Portal을 사용하여 가상 네트워크를 만드는 방법에 대해 알아봅니다. 2대의 VM(가상 머신)을 배포합니다. 다음으로 VM 간에 안전하게 통신하고 인터넷에서 VM에 연결합니다. 가상 네트워크는 Azure에서 프라이빗 네트워크의 기본 구성 요소입니다. 이 네트워크를 사용하면 VM과 같은 Azure 리소스가 서로 인터넷을 통해 안전하게 통신할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에서 **Virtual Network** 를 입력합니다. 검색 결과에서 **Virtual Network** 를 선택합니다.

3. **Virtual Network** 페이지에서 **만들기** 를 선택합니다.

4. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다.  </br> **myResourceGroup** 을 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 속성 | **myVNet** 을 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="가상 네트워크 Azure Portal 만들기" border="true":::

5. **IP 주소** 탭을 선택하거나 페이지 하단에 있는 **다음: IP 주소** 단추를 선택합니다.

6. **IPv4 주소 공간** 에서 기존 주소 공간을 선택하고 **10.1.0.0/16** 으로 변경합니다.

7. **+서브넷 추가** 를 선택한 다음, **서브넷 이름** 에 **MySubnet** 을 입력하고 **서브넷 주소 범위** 에 **10.1.0.0/24** 를 입력합니다.

8. **추가** 를 선택합니다.

9. **보안** 탭을 선택하거나 페이지 하단에 있는 **다음: 보안** 단추를 선택합니다.

10. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.1.1.0/24** 입력 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |

11. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

12. **만들기** 를 선택합니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 차례로 선택합니다. 
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM1** 을 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |    |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
    |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **mySubnet** 을 선택합니다. |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다.|
    | 퍼블릭 인바운드 포트 네트워크 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 차례로 선택합니다. 
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM2** 입력 |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter** 를 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |    |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
    |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **mySubnet** 을 선택합니다. |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본** 을 선택합니다.|
    | 퍼블릭 인바운드 포트 네트워크 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>myVM1에 연결

1. 개인 VM을 관리하기 위해 [Azure Portal](https://portal.azure.com)로 이동합니다. **가상 머신** 을 검색하여 선택합니다.

2. 프라이빗 가상 머신 **myVM1** 의 이름을 선택합니다.

3. VM 메뉴 모음에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Azure Bastion을 사용하여 myVM1에 연결" border="true":::

4. **연결** 페이지에서 파란색 **Bastion 사용** 단추를 선택합니다.

5. **Bastion** 페이지에서 이전에 가상 머신에 대해 만든 사용자 이름과 암호를 입력합니다.

6. **연결** 을 선택합니다.

## <a name="communicate-between-vms"></a>VM 간 통신

1. **myVM1** 의 베스천 연결에서 PowerShell을 엽니다.

2. `ping myvm2`를 입력합니다.

    다음 출력과 유사한 메시지가 표시됩니다.

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. **myVM1** 에 대한 베스천 연결을 닫습니다.

4. [myVM1에 연결](#connect-to-myvm1)의 단계를 완료하고 **myVM2** 에 연결합니다.

5. **myVM2** 에서 PowerShell을 열고 `ping myvm1`을 입력합니다.

    다음과 같은 메시지를 받게 됩니다.

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. **myVM2** 에 대한 베스천 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서는 기본 가상 네트워크와 두 개의 VM을 만들었습니다. 

인터넷에서 하나의 VM에 연결하고 두 VM 간에 안전하게 통신했습니다.

가상 네트워크 및 VM을 다 사용했으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

1. **myResourceGroup** 을 검색하고 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. **리소스 그룹 이름 입력** 에 대해 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크 설정에 대한 자세한 내용은 [가상 네트워크 만들기, 변경 또는 삭제](manage-virtual-network.md)를 참조하세요.

VM 네트워크 통신의 유형에 대한 자세한 내용은 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md)을 참조하세요.
