---
title: Azure VMware Solution by CloudSimple - 공용 IP 주소 할당
description: 프라이빗 클라우드 환경에서 가상 머신에 대한 공용 IP 주소를 할당하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 39cbef9621297f25de4cfadd6c94ca2ccea14e4a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322040"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>프라이빗 클라우드 환경에 공용 IP 주소 할당

네트워크 페이지에서 공용 IP 탭을 열어 프라이빗 클라우드 환경의 가상 머신에 공용 IP 주소를 할당합니다.

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)하고 측면 메뉴에서 **네트워크** 를 선택합니다.
2. **공용 IP** 를 선택합니다.
3. **새 공용 IP** 를 클릭합니다.

    ![공용 IP 페이지](media/public-ips-page.png)

4. IP 주소 항목을 식별하는 이름을 입력합니다.
5. 기본 위치를 유지합니다.
6. 필요한 경우 슬라이더를 사용하여 유휴 시간 제한을 변경합니다.
7. 공용 IP 주소를 할당할 로컬 IP 주소를 입력합니다.
8. 연결된 DNS 이름을 입력합니다.
9. **제출** 을 클릭합니다.

![공용 IP 할당](media/network-public-ip-allocate.png)

공용 IP 주소를 할당하는 작업이 시작됩니다. **활동 > 작업** 페이지에서 작업 상태를 확인할 수 있습니다. 할당이 완료되면 새 항목이 공용 IP 페이지에 표시됩니다.
