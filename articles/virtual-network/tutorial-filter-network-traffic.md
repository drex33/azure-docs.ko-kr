---
title: 네트워크 트래픽 필터링 - 자습서 - Azure Portal
titlesuffix: Azure Virtual Network
description: 이 자습서에서는 Azure Portal을 사용하여 네트워크 보안 그룹을 통해 서브넷에 네트워크 트래픽을 필터링하는 방법에 대해 알아봅니다.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 4af40f7f1caab7e4517c0dd621e400f58e3a29ad
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091372"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링

네트워크 보안 그룹을 통해 가상 네트워크 서브넷의 인바운드 및 아웃바운드 네트워크 트래픽을 필터링할 수 있습니다.

네트워크 보안 그룹에는 IP 주소, 포트, 프로토콜에 따라 네트워크 트래픽을 필터링하는 보안 규칙이 포함됩니다. 보안 규칙은 서브넷에 배포된 리소스에 적용됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 네트워크 보안 그룹 및 보안 규칙 만들기
> * 가상 네트워크 만들기 및 서브넷에 네트워크 보안 그룹 연결
> * 서브넷에 VM(가상 머신) 배포
> * 트래픽 필터 테스트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

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

5. **검토 + 만들기** 탭을 선택하거나, 페이지 아래쪽에서 파란색 **검토 + 만들기** 단추를 선택합니다.

6. **만들기** 를 선택합니다.

## <a name="create-application-security-groups"></a>애플리케이션 보안 그룹 만들기

애플리케이션 보안 그룹을 사용하면 웹 서버와 같은 유사한 기능을 갖는 서버를 함께 그룹화할 수 있습니다.

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에서 **애플리케이션 보안 그룹** 을 입력합니다. 검색 결과에서 **애플리케이션 보안 그룹** 을 선택합니다.

3. **애플리케이션 보안 그룹** 페이지에서 **만들기** 를 선택합니다.

4. **애플리케이션 보안 그룹 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**프로젝트 세부 정보** |  |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 속성 | **myAsgWebServers** 를 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. | 

5. **검토 + 만들기** 탭을 선택하거나, 페이지 아래쪽에서 파란색 **검토 + 만들기** 단추를 선택합니다.

6. **만들기** 를 선택합니다.

7. 다음 값을 지정하여 4단계를 다시 반복합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**프로젝트 세부 정보** |  |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 속성 | **myAsgMgmtServers** 를 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |

8. **검토 + 만들기** 탭을 선택하거나, 페이지 아래쪽에서 파란색 **검토 + 만들기** 단추를 선택합니다.

9. **만들기** 를 선택합니다.

## <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

네트워크 보안 그룹은 가상 네트워크의 네트워크 트래픽을 보호합니다.

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에서 **네트워크 보안 그룹** 을 입력합니다. 검색 결과에서 **네트워크 보안 그룹** 을 선택합니다.

3. **네트워크 보안 그룹** 페이지에서 **만들기** 를 선택합니다.

4. **네트워크 보안 그룹 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 속성 | **myNSG** 를 입력합니다. |
    | 위치 | **(미국) 미국 동부** 를 선택합니다. | 

5. **검토 + 만들기** 탭을 선택하거나, 페이지 아래쪽에서 파란색 **검토 + 만들기** 단추를 선택합니다.

6. **만들기** 를 선택합니다.

## <a name="associate-network-security-group-to-subnet"></a>서브넷에 네트워크 보안 그룹 연결

이 섹션에서는 네트워크 보안 그룹을 이전에 만든 가상 네트워크의 서브넷에 연결합니다.

1. 포털 맨 위에 있는 **리소스, 서비스 및 문서 검색** 상자에 **myNsg** 를 입력하기 시작합니다. 검색 결과에서 표시되는 **myNsg** 를 선택합니다.

2. **myNSG** 개요 페이지의 **설정** 에서 **서브넷** 을 선택합니다.

3. **설정** 페이지에서 **연결** 을 선택합니다.

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="서브넷에 NSG 연결" border="true":::

3. **서브넷 연결** 아래에서 **가상 네트워크** 를 선택한 다음, **myVNet** 을 선택합니다. 

4. **서브넷**, **기본값**, **확인** 을 차례로 선택합니다.

## <a name="create-security-rules"></a>보안 규칙 만들기

1. **myNSG** 의 **설정** 에서 **인바운드 보안 규칙** 을 선택합니다.

2. **인바운드 보안 규칙** 에서 **+ 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="인바운드 보안 규칙 추가" border="true":::

3. **myAsgWebServers** 애플리케이션 보안 그룹에 포트 80 및 443을 허용하는 보안 규칙을 만듭니다. **인바운드 보안 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 원본 | **모두**(기본값)를 그대로 둡니다. |
    | 원본 포트 범위 | **(*)** (기본값)를 그대로 둡니다. |
    | 대상 | **애플리케이션 보안 그룹** 을 선택합니다. |
    | 대상 애플리케이션 보안 그룹 | **myAsgWebServers** 를 선택합니다. |
    | 서비스 | **사용자 지정**(기본값)을 그대로 둡니다. |
    | 대상 포트 범위 | **80,443** 을 입력합니다. |
    | 프로토콜 | **TCP** 를 선택합니다. |
    | 작업 | **허용**(기본값)을 그대로 둡니다. |
    | 우선 순위 | **100**(기본값)을 그대로 둡니다. |
    | Name | **Allow-Web-All** 을 입력합니다. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="인바운드 보안 규칙" border="true":::

3. 다음 값을 사용하여 2단계를 다시 완료합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 원본 | **모두**(기본값)를 그대로 둡니다. |
    | 원본 포트 범위 | **(*)** (기본값)를 그대로 둡니다. |
    | 대상 | **애플리케이션 보안 그룹** 을 선택합니다. |
    | 대상 애플리케이션 보안 그룹 | **myAsgMgmtServers** 를 선택합니다. |
    | 서비스 | **사용자 지정**(기본값)을 그대로 둡니다. |
    | 대상 포트 범위 | **3389** 를 입력합니다. |
    | 프로토콜 | **임의** 를 선택합니다. |
    | 작업 | **허용**(기본값)을 그대로 둡니다. |
    | 우선 순위 | **110**(기본값)을 그대로 둡니다. |
    | Name | **Allow-RDP-All** 을 입력합니다. |

    > [!CAUTION]
    > 이 문서에서 RDP(3389 포트)는 **myAsgMgmtServers** 애플리케이션 보안 그룹에 할당된 VM에 대한 인터넷에 공개됩니다. 
    >
    > 프로덕션 환경에서는 3389 포트를 인터넷에 공개하는 대신 VPN, 개인 네트워크 연결 또는 Azure Bastion을 통해 관리하려는 Azure 리소스에 연결하는 것이 좋습니다.
    >
    > Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](../bastion/bastion-overview.md)을 참조하세요.

1-3단계를 완료한 후 사용자가 만든 규칙을 검토합니다. 목록은 다음 예의 목록과 같습니다.

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="보안 규칙" border="true":::

## <a name="create-virtual-machines"></a>가상 머신 만들기

가상 네트워크에 두 개의 VM을 만듭니다.

### <a name="create-the-first-vm"></a>첫 번째 VM 만들기

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

2. **컴퓨팅** 을 선택한 다음, **가상 머신** 을 선택합니다.

3. **가상 머신 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |  |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 가상 머신 이름 | **myVMWeb** 을 입력합니다. |
    | 지역 | **(미국) 미국 동부** 를 선택합니다. |
    | 가용성 옵션 | 중복이 필요하지 않음(기본값)을 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1** 을 선택합니다. |
    | Azure Spot 인스턴스 | 선택 안 함(기본값)을 그대로 둡니다. |
    | 크기 | **Standard_D2s_V3** 을 선택합니다. |
    | **관리자 계정** |   |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |   |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |

4. **네트워킹** 탭을 선택합니다.

5. **네트워킹** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워크 인터페이스** |   |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **기본값(10.0.0.0/24)** 을 선택합니다. |
    | 공용 IP | 새 공용 IP(기본값)를 그대로 둡니다. |
    | NIC 네트워크 보안 그룹 추가 | **없음** 을 선택합니다. | 

6. **검토 + 만들기** 탭을 선택하거나 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.

7. **만들기** 를 선택합니다.

### <a name="create-the-second-vm"></a>두 번째 VM 만들기

1~7단계를 다시 완료하지만, 3단계에서 VM 이름을 **myVMMgmt** 로 지정합니다. VM을 배포하는 데 몇 분이 걸립니다. 

VM이 배포될 때까지 다음 단계를 계속하지 마세요.

## <a name="associate-network-interfaces-to-an-asg"></a>네트워크 인터페이스를 ASG에 연결

포털에서 VM을 만들 때, 각 VM에 대한 네트워크 인터페이스가 생성되고 네트워크 인터페이스는 VM에 연결됩니다. 

이전에 생성한 애플리케이션 보안 그룹 중 하나에 각 VM의 네트워크 인터페이스를 추가합니다.

1. 포털 위쪽의 **리소스, 서비스 및 문서 검색** 상자에서 **myVMWeb** 을 입력하기 시작합니다. 검색 결과에 **myVMWeb** 가상 머신이 표시되면 이를 선택합니다.

2. **설정** 에서 **네트워킹** 을 선택합니다.  

3. **애플리케이션 보안 그룹** 탭을 선택한 다음, **애플리케이션 보안 그룹 구성** 을 선택합니다.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="애플리케이션 보안 그룹 구성" border="true":::

4. **애플리케이션 보안 그룹 구성** 에서 **myAsgWebServers** 를 선택합니다. **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="애플리케이션 보안 그룹 선택" border="true":::

5. **myVMMgmt** 가상 머신을 검색하고 **myAsgMgmtServers** ASG를 선택하여 1 및 2단계를 다시 완료합니다.

## <a name="test-traffic-filters"></a>트래픽 필터 테스트

1. **myVMMgmt** VM에 연결합니다. 포털 위쪽의 검색 상자에서 **myVMMgmt** 를 입력합니다. 검색 결과에 **myVMMgmt** 가 표시되면 이를 선택합니다. **연결** 단추를 선택합니다.

2. **RDP 파일 다운로드** 를 선택합니다.

3. 다운로드한 rdp 파일을 열고 **연결** 을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

4. **확인** 을 선택합니다.

5. 연결 프로세스 중에 인증서 경고를 받을 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속** 을 선택하여 연결을 계속합니다.

    인터넷에서 **myAsgMgmtServers** 애플리케이션 보안 그룹으로의 인바운드 트래픽을 3389 포트에서 허용하므로 연결이 성공합니다. 
    
    **myVMMgmt** 에 대한 네트워크 인터페이스가 **myAsgMgmtServers** 애플리케이션 보안 그룹과 연결되어 연결을 허용합니다.

6. **myVMMgmt** 에서 PowerShell 세션을 엽니다. 다음 예제를 사용하여 **myVMWeb** 에 연결합니다. 

    ```powershell
    mstsc /v:myVmWeb
    ```

    기본적으로 동일한 네트워크의 가상 머신에서 모든 포트를 통해 각 가상 머신과 통신할 수 있으므로 **myVMMgmt** 에서 **myVMWeb** 으로의 RDP 연결이 성공합니다.
    
    인터넷에서 **myVMWeb** 가상 머신으로의 RDP 연결을 만들 수 없습니다. **myAsgWebServers** 에 대한 보안 규칙은 인터넷에서 3389 포트로의 인바운드 연결을 차단합니다. 인터넷에서 모든 리소스로의 인바운드 트래픽은 기본적으로 거부됩니다.

7. Microsoft IIS를 **myVMWeb** 가상 머신에 설치하려면 **myVMWeb** 가상 머신의 PowerShell 세션에서 다음 명령을 입력합니다.

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. IIS 설치가 완료되면 **myVMWeb** 가상 머신의 연결을 끊습니다. 그러면 **myVMMgmt** 가상 머신 원격 데스크톱 연결이 종료됩니다.

9. **myVMMgmt** VM의 연결을 끊습니다.

10. Azure Portal 위쪽의 **리소스, 서비스 및 문서 검색** 상자에서 컴퓨터의 **myVMWeb** 을 입력하기 시작합니다. 검색 결과에 **myVMWeb** 이 표시되면 이를 선택합니다. VM의 **공용 IP 주소** 를 적어둡니다. 다음 예에 표시된 주소는 23.96.39.113이지만, 주소가 다릅니다.

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="공용 IP 주소." border="true":::
    
11. 인터넷에서 **myVMWeb** 웹 서버에 액세스할 수 있는지 확인하기 위해 컴퓨터에서 인터넷 브라우저를 열고 `http://<public-ip-address-from-previous-step>`으로 이동합니다. 

인터넷에서 **myAsgWebServers** 애플리케이션 보안 그룹으로의 인바운드 트래픽을 80 포트에서 허용하므로 IIS 시작 화면이 표시됩니다. 

**myVMWeb** 에 연결된 네트워크 인터페이스가 **myAsgWebServers** 애플리케이션 보안 그룹과 연결되어 연결을 허용합니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 **myResourceGroup** 을 입력합니다. 검색 결과에 **myResourceGroup** 이 보이면 선택합니다.
2. **리소스 그룹 삭제** 를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 작업을 수행했습니다.

* 네트워크 보안 그룹을 만들어 가상 네트워크 서브넷에 연결했습니다. 
* 웹 및 관리를 위한 애플리케이션 보안 그룹을 만들었습니다.
* 두 개의 가상 머신을 만들었습니다.
* 애플리케이션 보안 그룹 네트워크 필터링을 테스트했습니다.

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](./network-security-groups-overview.md) 및 [네트워크 보안 그룹 관리](manage-network-security-group.md)를 참조하세요.

기본적으로 Azure는 서브넷 간에 트래픽을 라우팅합니다. 그 대신 방화벽 역할을 하는 VM 등을 통해 서브넷 간에 트래픽을 라우팅하도록 선택할 수 있습니다. 

경로 테이블을 만드는 방법을 알아보려면 다음 자습서를 진행하세요.
> [!div class="nextstepaction"]
> [경로 테이블 만들기](./tutorial-create-route-table-portal.md)
