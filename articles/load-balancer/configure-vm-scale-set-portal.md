---
title: 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합 구성 - Azure Portal
description: Azure Portal을 사용하여 기존 Azure Load Balancer로 가상 머신 확장 집합을 구성하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91439519"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Azure Portal을 사용하여 기존 Azure Load Balancer로 가상 머신 확장 집합 구성

이 문서에서는 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합을 구성하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독
- 가상 머신 확장 집합이 배포될 구독의 기존 표준 SKU 부하 분산 장치입니다.
- 가상 머신 확장 집합에 대한 Azure Virtual Network입니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 부하 분산 장치를 사용하여 가상 머신 확장 집합 배포

이 섹션에서는 기존 Azure 부하 분산 장치 를 사용하여 Azure Portal에서 가상 머신 확장 집합을 만듭니다.

> [!NOTE]
> 다음 단계에서는 **myVNet** 이라는 가상 네트워크와 **myLoadBalancer** 라는 Azure Load Balancer를 이전에 배포했다고 가정합니다.

1. 화면 왼쪽 위에서 **리소스 만들기** > **계산** > **가상 머신 확장 집합** 을 클릭하거나 Marketplace 검색에서 **가상 머신 확장 집합** 을 검색합니다.

2. **만들기** 를 선택합니다.

3. **가상 머신 확장 집합 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정                        | 값                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **프로젝트 세부 정보**            |                                                                                                       |
    | Subscription                   | Azure 구독 선택                                                                        |
    | 리소스 그룹                 | 새로 만들기를 선택하고, **myResourceGroup** 을 입력한 다음, 확인을 선택하거나 기존 리소스 그룹 선택 |
    | **확장 집합 세부 정보**          |                                                                                                       |
    | 가상 머신 확장 집합 이름 | **myVMSS** 입력                                                                                      |
    | 지역                         | **미국 동부 2** 선택                                                                                    |
    | 가용성 영역              | **없음** 을 선택합니다.                                                                                       |
    | **인스턴스 세부 정보**           |                                                                                                       |
    | 이미지                          | **Ubuntu Server 18.04 LTS** 선택                                                                    |
    | Azure Spot 인스턴스            | **아니요** 를 선택합니다.                                                                                         |
    | 크기                           | 기본값 유지                                                                                      |
    | **관리자 계정**      |                                                                                                       |
    | 인증 유형            | **암호** 선택                                                                                   |
    | 사용자 이름                       | 관리자 사용자 이름 입력        |
    | 암호                       | 관리자 암호 입력    |
    | 암호 확인               | 관리자 암호 다시 입력 |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="가상 머신 확장 집합 기본 사항 만들기 탭을 보여 주는 스크린샷" border="true":::

4. **네트워킹** 탭을 선택합니다.

5. **네트워킹** 탭에서 다음 정보를 입력 하거나 선택합니다.

     설정                           | 값                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtual Network 구성** |                                                          |
    | 가상 네트워크                   | **myVNet** 또는 기존 가상 네트워크 선택      |
    | **부하 분산**.                |                                                          |
    | 부하 분산 장치 사용               | **예** 를 선택합니다.                                           |
    | **부하 분산 설정**       |                                                          |
    | 부하 분산 옵션            | **Azure 부하 분산 장치** 선택                           |
    | 부하 분산 장치 선택            | **myLoadBalancer** 또는 기존 부하 분산 장치 선택 |
    | 백 엔드 풀 선택             | **myBackendPool** 또는 기존 백 엔드 풀 선택  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="가상 머신 확장 집합 네트워킹 만들기 탭을 보여 주는 스크린샷" border="true":::

6. **관리** 탭을 선택합니다.

7. **관리** 탭에서 **부팅 진단** 을 **해제** 합니다.

8. **검토 + 만들기** 단추를 선택합니다.

9. 설정을 검토하고 **만들기** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure Load Balancer를 사용하여 가상 머신 확장 집합을 배포했습니다.  가상 머신 확장 집합 및 부하 분산 장치에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
