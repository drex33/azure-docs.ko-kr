---
title: Azure Data Box Heavy 주문 자습서 | Microsoft Docs
description: 이 자습서에서는 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션인 Azure Data Box Heavy와 Data Box Heavy를 정렬하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: alkohli
ms.localizationpriority: high
ms.custom: contperf-fy22q1
ms.openlocfilehash: d5334314626d29dc9e3047bc382d41fcfa318a1d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469521"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>자습서: Azure Data Box Heavy 주문


Azure Data Box Heavy는 빠르고 쉽게 신뢰할 수 있는 방식으로 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션입니다. Microsoft에서 제공한 770TB(대략적인 사용 가능한 용량) 스토리지 디바이스에 데이터를 전송한 다음, 디바이스를 다시 제공합니다. 그런 다음, 이 데이터는 Azure에 업로드됩니다.

이 자습서에서는 Azure Data Box Heavy를 주문할 수 있는 방법에 대해 설명합니다. 이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Data Box Heavy 필수 구성 요소
> * Data Box Heavy 주문
> * 주문 추적
> * 주문 취소

## <a name="prerequisites"></a>필수 구성 요소

디바이스를 배포하기 전에 Data Box 서비스 및 디바이스에 대해 다음 필수 구성 요소를 완료합니다.

### <a name="for-installation-site"></a>설치 장소의 경우

시작하기 전에 다음 사항을 확인합니다.

- 디바이스가 표준 출입구 및 통로를 통과하기에 맞는 크기인지 여부 단, 디바이스가 모든 통로를 통과하기에 맞는 크기인지 확인합니다. 디바이스 크기: 너비: 26" 길이: 48" 높이: 28".
- 지층 이외의 층에 설치된 경우 엘리베이터 또는 경사로를 통해 디바이스에 접근해야 합니다. 디바이스는 최대 500lb(약 227kg)의 무게이며,
- 사용 가능한 네트워크 연결에 근접한 데이터 센터에 이 공간을 차지하는 디바이스를 수용할 수 있는 평평한 부분이 있어야 합니다.

### <a name="for-service"></a>서비스의 경우

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.
- 디바이스의 포장을 풀었는지 여부
- 호스트 컴퓨터를 데이터 센터 네트워크에 연결해야 합니다. Data Box Heavy는 이 컴퓨터에서 데이터를 복사합니다. 호스트 컴퓨터는 [Azure Data Box Heavy 시스템 요구 사항](data-box-system-requirements.md)에 설명된 대로 지원되는 운영 체제를 실행해야 합니다.
- 로컬 UI에 연결하고 디바이스를 구성하기 위해 RJ-45 케이블이 있는 랩톱이 있어야 합니다. 랩톱을 사용하여 디바이스의 각 노드를 한 번 구성합니다.
- 데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다.
- 디바이스 노드당 40Gbps 또는 10Gbps 케이블이 하나 필요합니다. [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 네트워크 인터페이스와 호환되는 케이블을 선택합니다.

    - 40Gbps 케이블의 경우 케이블의 디바이스 끝이 QSFP+이어야 합니다.
    - 10Gbps 케이블의 경우, 한 쪽에는 10G 스위치에 꽂을 SFP+ 케이블이 필요하고, 디바이스에 꽂는 쪽에는 QSFP+ ~ SFP+ 어댑터(또는 QSA 어댑터)가 필요합니다.
    - 전원 케이블은 디바이스에 포함되어 있습니다.

## <a name="order-data-box-heavy"></a>Data Box Heavy 주문

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

## <a name="track-the-order"></a>주문 추적

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box Heavy 주문, **개요** 로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

디바이스를 사용할 수 없는 경우 알림을 받습니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 디바이스를 준비하는 동안 수행되는 작업은 다음과 같습니다.

- 디바이스와 연결된 각 스토리지 계정에 대해 SMB 공유가 생성됩니다.
- 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
- 디바이스의 잠금을 해제하는 데 도움이 되는 디바이스 암호도 생성됩니다.
- Data Box Heavy는 언제든지 디바이스에 대한 권한이 없는 액세스를 방지하기 위해 잠겨 있습니다.

디바이스 준비가 완료되면 포털에서는 해당 주문을 **처리됨** 상태로 표시합니다.

![Data Box Heavy 주문 처리됨](media/data-box-overview/data-box-order-status-processed.png)

그런 다음, Microsoft에서는 디바이스를 준비하고 지역 배송업체를 통해 발송합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![Data Box Heavy 주문 발송됨](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>주문 취소

이 주문을 취소하려면 Azure Portal에서 **개요** 로 이동하고, 명령 모음에서 **취소** 를 클릭합니다.

주문이 완료되면 주문 상태가 처리됨으로 표시되기 전까지는 취소할 수 있습니다.
 
취소된 주문을 삭제하려면 **개요** 로 이동하고, 명령 모음에서 **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Heavy 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * 사전 요구 사항
> * Data Box Heavy 주문
> * 주문 추적
> * 주문 취소

Data Box Heavy를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box Heavy 설정](./data-box-heavy-deploy-set-up.md)
