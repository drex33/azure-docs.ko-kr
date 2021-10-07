---
title: Azure VMware Solution by CloudSimple - 방화벽 테이블 및 규칙 설정
description: 서브넷 및 VLAN의 트래픽을 제한하기 위해 프라이빗 클라우드 방화벽 테이블 및 규칙을 설정하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2b1b8b9f5388dc2cf6790a94c450ab79d1862197
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612810"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>프라이빗 클라우드에 대한 방화벽 테이블 및 규칙 설정

방화벽 테이블 및 관련 규칙을 사용하여 특정 서브넷 및 VLAN에 적용할 트래픽에 대한 제한을 지정할 수 있습니다.

* 서브넷은 하나의 방화벽 테이블에 연결될 수 있습니다.
* 방화벽 테이블은 여러 서브넷에 연결될 수 있습니다.

## <a name="add-a-new-firewall-table"></a>새 방화벽 테이블 추가

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)하고 측면 메뉴에서 **네트워크** 를 선택합니다.
2. **방화벽 테이블** 을 선택합니다.
3. **방화벽 테이블 만들기** 를 선택합니다.

    ![VLAN/서브넷 페이지](media/firewall-tables-page.png)

4. 테이블의 이름을 입력합니다.
5. 테이블의 기본 규칙이 나열됩니다. **새 규칙 만들기** 를 클릭하여 추가 규칙을 만듭니다. 자세한 내용은 다음 절차를 참조하세요.
6. **완료** 를 클릭하여 방화벽 테이블을 저장합니다.

> [!IMPORTANT]
> 프라이빗 클라우드당 최대 2개의 방화벽 테이블을 만들 수 있습니다.

## <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙은 방화벽이 특정 형식의 트래픽을 처리하는 방법을 결정합니다. 선택한 방화벽 테이블의 **규칙** 탭에는 연결된 모든 규칙이 나열됩니다.

![방화벽 규칙 테이블](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>방화벽 규칙을 만들기

1. 다음 방법 중 하나로 방화벽 규칙을 만드는 설정을 표시합니다.
    * 방화벽 테이블을 만들 때 **규칙 추가** 를 클릭합니다.
    * **네트워크 > 방화벽 테이블** 페이지에서 특정 방화벽 테이블을 선택하고 **새 방화벽 규칙 만들기** 를 클릭합니다.
2. 다음과 같이 규칙을 설정합니다.
    * **이름**. 규칙 이름을 지정합니다.
    * **우선 순위**. 규칙에 우선 순위를 할당합니다. 숫자가 낮은 규칙이 먼저 실행됩니다.
    * **트래픽 형식**. 프라이빗 클라우드, 인터넷 또는 VPN 트래픽(상태 비저장) 또는 공용 IP 주소(상태 비저장)에 대한 규칙인지 선택합니다.
    * **프로토콜**. 규칙(TCP, UDP 또는 모든 프로토콜)이 적용되는 프로토콜을 선택합니다.
    * **방향**. 인바운드 또는 아웃바운드 트래픽에 대한 규칙인지 선택합니다. 인바운드 및 아웃바운드 트래픽에 대한 별도의 규칙을 정의해야 합니다.
    * **작업**. 규칙이 일치하는 경우 수행할 작업(허용 또는 거부)을 선택합니다.
    * **원본** - 규칙(CIDR 블록, 내부 또는 모든 원본)이 적용되는 원본을 지정합니다.
    * **원본 포트 범위**. 규칙이 적용되는 포트 범위를 지정합니다.
    * **방향**. 인바운드 또는 아웃바운드를 선택합니다.
    * **대상**. 규칙(CIDR 블록, 내부 또는 모든 원본)이 적용되는 대상을 지정합니다.
    * **원본 포트 범위**. 규칙이 적용되는 포트 범위를 지정합니다.

    ![방화벽 테이블 추가 규칙](media/firewall-rule-create.png)

3. **완료** 를 클릭하여 규칙을 저장하고 방화벽 테이블의 규칙 목록에 추가합니다.

> [!IMPORTANT]
> 각 방화벽 테이블에는 최대 10개의 인바운드 규칙과 20개의 아웃바운드 규칙이 있을 수 있습니다. [고객 지원팀](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에 문의하여 한도를 늘릴 수 있습니다.

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>VLAN/서브넷 연결

방화벽 테이블을 정의한 후에는 테이블의 규칙이 적용되는 서브넷을 지정할 수 있습니다.

1. **네트워크** > **방화벽 테이블** 페이지에서 방화벽 테이블을 선택합니다.
2. **연결된 VLAN/서브넷** 탭을 엽니다.
3. **VLAN/서브넷에 연결** 을 클릭합니다.
4. 프라이빗 클라우드와 VLAN을 선택합니다. 연결된 서브넷 이름과 CIDR 블록이 표시됩니다.
5. **제출** 을 클릭합니다.
