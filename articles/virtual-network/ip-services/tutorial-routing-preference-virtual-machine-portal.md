---
title: '자습서: VM에 대한 라우팅 기본 설정 구성 - Azure Portal'
description: 이 자습서에서는 Azure Portal을 통해 라우팅 기본 설정을 사용하여 공용 IP 주소가 있는 VM을 만드는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a06ec95b6970e4c3f8d4bde4cc180bf832f3a6a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369508"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 VM에 대한 라우팅 기본 설정 구성 

이 자습서에서는 가상 머신의 라우팅 기본 설정을 구성하는 방법을 보여줍니다. 라우팅 기본 설정 옵션으로 **인터넷** 을 선택하면 VM의 인터넷 바인딩 트래픽이 ISP 네트워크를 통해 라우팅됩니다. 기본 라우팅은 Microsoft 글로벌 네트워크를 통해 전달됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * **인터넷** 라우팅 기본 설정에 대해 구성된 공용 IP 주소로 가상 머신을 만듭니다.
> * 공용 IP 주소가 **인터넷** 라우팅 기본 설정으로 설정되어 있는지 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>가상 머신 만들기

이 섹션에서는 가상 머신과 공용 IP 주소를 만듭니다. 공용 IP 주소 구성 중에 라우팅 기본 설정으로 **인터넷** 을 선택합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

3. **가상 머신** 에서 **+ 만들기** 를 선택한 다음, **+ 가상 머신** 을 선택합니다.

4. **가상 머신 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **새로 만들기** 를 선택합니다. </br> **TutorVMRoutePref-rg** 를 입력합니다. **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **미국 서부 2** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다.** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen2** 를 선택합니다. |
    | Azure Spot 인스턴스 | 선택 안 함(기본값)을 그대로 둡니다. |
    | 크기 | 크기를 선택합니다. |
    | **관리자 계정** |   |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **RDP(3389)** 기본값을 그대로 둡니다. </br> _**인터넷에서 포트 3389를 여는 것은 프로덕션 워크로드에 권장되지 않습니다.**_ |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-virtual-machine.png" alt-text="가상 머신 만들기 스크린샷":::

5. **다음: 디스크** 를 선택한 다음, **다음: 네트워킹** 을 선택하거나 **네트워킹** 탭을 선택합니다.

6. 네트워킹 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워크 인터페이스** |   |
    | 가상 네트워크 | **(신규) TutorVMRoutePref-rg-vnet** 의 기본값을 그대로 둡니다. |
    | 서브넷 | **(신규) 기본값(10.1.0.0/24)** 을 그대로 둡니다. |
    | 공용 IP | **새로 생성** 를 선택합니다. </br> **이름** 에 **myPublicIP** 를 입력합니다. </br> **SKU** 에서 **표준** 을 선택합니다. </br> **라우팅 기본 설정** 에서 **인터넷** 을 선택합니다. </br> **확인** 을 선택합니다. |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-public-ip.png" alt-text="공용 IP 주소 만들기의 스크린샷.":::

7. **검토 + 만들기** 를 선택합니다.

8. **만들기** 를 선택합니다.

## <a name="verify-internet-routing-preference"></a>인터넷 라우팅 기본 설정 확인

이 섹션에서는 이전에 만든 공용 IP 주소를 검색하고 인터넷 라우팅 기본 설정을 확인합니다.

1. 포털 검색 상자에 **공용 IP 주소** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

2. **공용 IP 주소** 에서 **myPublicIP** 를 선택합니다.

3. **설정** 에서 **속성** 을 선택합니다.

4. **라우팅 기본 설정** 에 **인터넷** 이 표시되는지 확인합니다. 

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/verify-routing-preference.png" alt-text="인터넷 라우팅 기본 설정 확인의 스크린샷.":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 공용 IP 주소를 삭제합니다.

1. 포털 맨 위에 있는 검색 상자에 **리소스 그룹** 을 입력합니다.

2. 검색 결과에서 **리소스 그룹** 을 선택합니다.

3. **TutorVMRoutePref-rg** 를 선택합니다.

4. **리소스 그룹 삭제** 를 선택합니다.

5. **리소스 그룹 이름 입력** 에 대해 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하여 라우팅 기본 설정이 혼합된 가상 머신을 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [가상 머신의 라우팅 기본 설정 옵션 두 가지를 모두 구성하기](routing-preference-mixed-network-adapter-portal.md)

