---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: bd845eef3e2f41bc9f14303430e3e44ab68c1b68
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247297"
---
유럽에서 디바이스를 반송하는 경우 다음 단계를 수행합니다.

1. 디바이스의 전원이 꺼져 있고 케이블이 분리되었는지 확인합니다.
2. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
3. 배송 레이블이 E-ink 디스플레이에 표시되는지 확인하고 운송업체에 픽업을 예약합니다. 레이블이 손상 또는 손실되었거나 E-ink 디스플레이에 표시되지 않으면 Microsoft 지원에 문의하세요. 고객 지원팀에서 제안하는 경우 Azure Portal에서 **개요 > 배송 레이블 다운로드** 로 차례로 이동합니다. 배송 레이블을 다운로드하여 디바이스에 부착합니다.
1. **독일 또는 스위스에서 배송하는 경우** Azure 데이터 센터에는 모든 디바이스 반환에 대한 사전 알림이 필요합니다.
    1. 다음 템플릿을 사용하여 Azure Data Box 작업에 이메일을 보내 인바운드 ID를 받습니다. [adbops@microsoft.com](mailto:adbops@microsoft.com)에 이메일을 보냅니다.

       ```
       To: adbops@microsoft.com
       Subject: Request for Azure Data Box Inbound ID: <orderName> 
       Body: 
        
       I am ready to return an Azure Data Box and would like to request an Inbound ID for the following order:
       
       Order Name: <orderName>
       Return Tracking Number: <returnTracking#>
       ```

    1. Azure Data Box 작업에서 제공하는 인바운드 ID 번호를 적어 두고 반환 레이블 근처에 명확하게 표시되는 단위에 붙여넣습니다.
1. 디바이스를 반송하는 경우 UPS로 픽업을 예약합니다. 픽업을 예약하려면 다음을 수행합니다.

    * 로컬 UPS(국가/지역별 무료 전화 번호)에 전화합니다.
    * 통화에서 E-ink 디스플레이 또는 인쇄된 레이블에서 표시한 대로 역방향 배송 추적 번호를 알려줍니다. 추적 번호를 알려주지 않으면 픽업 중에 UPS에 추가 요금이 부과됩니다.
    * 픽업을 예약하는 동안 문제가 발생하거나 추가 요금을 지불하라는 메시지가 표시되면 Azure Data Box Operations에 문의하세요. [adbops@microsoft.com](mailto:adbops@microsoft.com)에 이메일을 보냅니다.

    픽업을 예약하는 대신 가장 가까운 반납 위치에 Data Box를 반납할 수도 있습니다.

    <!--FOLLOW-UP: Move this to after the Include in data-box-deploy-picked-up. This has been done already in data-box-export-deploy-picked-up. - **If you're shipping from Germany or Switzerland,** you can also [use self-managed shipping](data-box-deploy-picked-up.md#self-managed-shipping).-->

4. 운송업체에서 Data Box를 픽업하고 나면 포털의 주문 상태가 **픽업됨** 으로 업데이트됩니다. 추적 ID도 표시됩니다.

