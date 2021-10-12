---
title: '자습서: 가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기 - Azure portal'
titlesuffix: Azure Virtual Network
description: 이 자습서에서는 Azure Portal 사용하여 가상 머신에 대한 라우팅 기본 설정 옵션을 구성하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: d11e77725dfb99060c96f62233833330867f1c0d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369208"
---
# <a name="tutorial-configure-both-routing-preference-options-for-a-virtual-machine-using-the-azure-portal"></a>자습서: Azure portal을 사용하여 가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기

본 문서에서는 VM의 [라우팅 기본 설정](routing-preference-overview.md) 옵션 두 가지(인터넷과 Microsoft 전역 네트워크)를 모두 구성하는 방법을 보여 줍니다. 이 구성은 두 개의 가상 네트워크 인터페이스를 사용하여 수행됩니다. 하나의 네트워크 인터페이스는 Microsoft 글로벌 네트워크를 통해 라우팅되는 공용 IP로 구성됩니다. 다른 네트워크 인터페이스는 ISP 네트워크를 통해 라우팅된 공용 IP로 구성됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * **Microsoft 네트워크** 라우팅 기본 설정을 사용하여 공용 IP 주소가 있는 가상 머신을 만듭니다.
> * **인터넷** 라우팅 기본 설정을 사용하여 공용 IP 주소를 만듭니다.
> * 가상 머신에 대한 새 네트워크 인터페이스를 만듭니다.
> * **인터넷** 라우팅 설정 공용 IP를 가상 머신 보조 네트워크 인터페이스에 연결합니다.
> * 가상 머신에 이차 네트워크 인터페이스를 연결합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-machine"></a>가상 머신 만들기

이 섹션에서는 가상 머신 및 공용 IP 주소를 만듭니다. 공용 IP 주소 구성 중에 라우팅 기본 설정에 대해 **Microsoft 네트워크** 를 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

3. **가상 머신** 에서 **+ 만들기** 를 선택한 다음, **+ 가상 머신** 을 선택합니다.

4. **가상 머신 만들기** 의 **기본** 탭에서 입력하거나 다음 정보를 선택할 수 있습니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **TutorVMMixRoutePref-rg** 를 입력합니다. **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다.** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen2** 을 선택합니다. |
    | Azure Spot 인스턴스 | 선택 안 함(기본값)을 그대로 둡니다. |
    | 크기 | 크기를 선택합니다. |
    | **관리자 계정** |   |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 기본값을 그대로 둡니다. </br> _**인터넷에서 포트 3389를 여는 것은 프로덕션 워크로드에 권장되지 않습니다.**_ |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-virtual-machine.png" alt-text="가상 머신 만들기 스크린샷":::

5. **다음: 디스크** 탭을 선택하거나 **다음: 네트워킹**, 그리고 **네트워킹** 탭을 선택합니다.

6. 네트워킹 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워크 인터페이스** |   |
    | 가상 네트워크 | **(신규) myResourceGroupVM-vnet** 기본값을 그대로 둡니다. |
    | 서브넷 | **(신규) 기본값(10.1.0.0/24)** 을 그대로 둡니다. |
    | 공용 IP | **새로 생성** 를 선택합니다. </br> **이름** 에 **myPublicIP** 를 입력합니다. </br> **SKU** 에서 **표준** 을 선택합니다. </br> **라우팅 기본 설정** 에서 **Microsoft network** 을 선택합니다. </br> **확인** 을 선택합니다. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-ms-rp.png" alt-text="Microsoft 라우팅 설정을 사용하여 공용 IP 주소 만들기의 스크린샷":::

7. **검토 + 만들기** 를 선택합니다.

8. **만들기** 를 선택합니다.

## <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기

이 섹션에서 **인터넷** 라우팅 설정을 사용하여 공용 IP 주소를 만들 것입니다.

1. 포털 검색 상자에서 **공용 IP 주소** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

2. **+ 만들기** 를 선택합니다.

3. **공용 IP 주소 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | IP 버전 | **IPv4**(기본값)를 그대로 둡니다. |
    | SKU | 기본값인 **표준** 을 그대로 둡니다. |
    | 계층 | **지역**(기본값)을 그대로 둡니다. |
    | **IPv4 IP 주소 구성** |   |
    | Name | **myPublicIP-lb** |
    | 라우팅 기본 설정 | **인터넷** 을 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **TutorVMMixRoutePref-rg** 를 선택합니다. |
    | 위치 | **미국 서부 2** 를 선택합니다. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-internet-rp.png" alt-text="인터넷 라우팅 설정을 사용하여 공용 IP 주소를 만드는 스크린샷.":::

4. **생성** 를 선택합니다.

## <a name="create-the-secondary-nic"></a>두 번째 NIC를 만듭니다.

이 섹션에서는 이전에 만든 가상 머신에 대한 보조 네트워크 인터페이스를 만듭니다.

1. 포털 검색 상자에 **네트워크 인터페이스** 를 입력합니다. 검색 결과에서 **네트워크 인터페이스** 를 선택합니다.

2. **+ 만들기** 를 선택합니다.

3. **네트워크 인터페이스 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **TutorVMMixRoutePref-rg** 를 선택합니다. |
    | Name | **myVMNic2** 을 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 가상 네트워크 | **TutorVMMixRoutePref-rg-vnet** 을 선택합니다. |
    | 서브넷 | **TutorVMMixRoutePref-rg-vnet/default(10.1.0.0/24)를** 선택합니다. |
    | 네트워크 보안 그룹 | **my-VM** 을 선택합니다. |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-network-interface.png" alt-text="보조 네트워크 인터페이스 만들기의 스크린샷.":::

4. **검토 + 만들기** 탭을 선택하거나, 페이지 하단에 있는 **검토 + 만들기** 단추를 선택합니다.

5. **만들기** 를 선택합니다.

## <a name="associate-the-public-ip-address-with-secondary-nic"></a>보조 NIC와 공용 IP 주소 연결

이 섹션에서는 이전에 만든 **인터넷** 라우팅 기본 설정 공용 IP 주소를 이전 섹션에서 만든 네트워크 인터페이스와 연결합니다.

1. 포털 검색 상자에서 **공용 IP 주소** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

2. **myPublicIP-IR** 을 선택합니다.

3. **myPublic-IR** 의 **개요** 페이지에서 **연결** 을 선택합니다.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/associate-public-ip.png" alt-text="연결 단추가 있는 myPublicIP-IR 개요 페이지의 스크린샷.":::

4. **공용 IP 주소 연결** **리소스 종류** 풀다운 상자에서 **네트워크 인터페이스** 를 선택합니다.

5. **네트워크 인터페이스** 풀다운 상자에서 **myVMNic2를** 선택합니다.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/select-ip-association-resource.png" alt-text="공용 IP 주소에 연결할 리소스를 선택하는 스크린샷.":::

6. **확인** 을 선택합니다.

## <a name="attach-secondary-network-interface-to-virtual-machine"></a>가상 머신에 네트워크 인터페이스 연결

이 섹션에서는 이전에 만든 보조 네트워크 인터페이스를 가상 머신에 연결합니다.

1. 포털 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

2. **myVM** 을 선택합니다.

3. 실행 중인 경우 **myVM** 을 중지하고, 그렇지 않으면 다음 단계를 계속합니다.

4. **myVM** 의 **설정** 에서 **네트워킹** 을 선택합니다.

5. **myVM** 의 **네트워킹에서** **네트워크 인터페이스 연결** 을 선택합니다.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-01.png" alt-text="myVM 네트워킹 개요 페이지의 스크린샷.":::

6. **네트워크 인터페이스 연결을 위해** 풀다운 상자에서 **myVMNic2** 를 선택합니다.

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-02.png" alt-text="네트워크 인터페이스 연결의 스크린샷":::

7. **확인** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 더 이상 사용하지 않으려면 다음 단계에 따라 공용 IP 주소 및 가상 머신을 삭제합니다.

1. 포털 맨 위에 있는 검색 상자에 **리소스 그룹** 을 입력합니다.

2. 검색 결과에서 **리소스 그룹** 을 선택합니다.

3. **TutorVMMixRoutePref-rg** 를 선택합니다.

4. **리소스 그룹 삭제** 를 선택합니다.

5. **리소스 그룹 이름 입력** 에 대해 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 공용 IP 접두사를 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Kubernetes 클러스터에 대한 라우팅 기본 설정 구성](routing-preference-azure-kubernetes-service-cli.md)