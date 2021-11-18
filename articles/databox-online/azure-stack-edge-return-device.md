---
title: Azure Stack Edge 디바이스 반환
description: 데이터를 초기화하고 Azure Stack Edge 디바이스를 반환한 다음 디바이스와 연결된 리소스를 삭제하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/28/2021
ms.author: alkohli
ms.openlocfilehash: 120b227bb2732194d8db09c26b7b2721453e94a3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132762939"
---
# <a name="return-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스에서 반환

[!INCLUDE [applies-to-pro-fpga](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

이 문서에서는 데이터를 지우고 Azure Stack Edge 디바이스를 반환하는 방법을 설명합니다. 디바이스를 반환한 후에는 디바이스와 연결된 리소스를 삭제할 수도 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 디바이스의 데이터 디스크에서 데이터 지우기
> * Azure Portal에서 디바이스 반환 시작
> * 디바이스 포장 및 픽업 예약
> * Microsoft Azure Portal에서 리소스 삭제

## <a name="erase-data-from-the-device"></a>디바이스에서 데이터 지우기

디바이스의 데이터 디스크에서 데이터를 지우려면 디바이스를 초기화해야 합니다.

필요한 경우 초기화하기 전에 디바이스의 로컬 데이터를 복사합니다. 디바이스에서 Azure Storage 컨테이너로 데이터를 복사할 수 있습니다. 

디바이스를 다시 설정하기 전에도 디바이스 반환을 시작할 수 있습니다.

로컬 웹 UI 또는 PowerShell에서 디바이스를 초기화할 수 있습니다. PowerShell 지침은 [디바이스 초기화](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)를 참조하세요.

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - 디바이스를 교환하거나 새 디바이스로 업그레이드하는 경우 새 디바이스를 받은 후에만 디바이스를 초기화하는 것이 좋습니다.
> - 디바이스를 재설정하면 디바이스에 있는 로컬 데이터만 모두 삭제됩니다. 클라우드에 있는 데이터는 삭제되지 않으며 [요금](https://azure.microsoft.com/pricing/details/storage/)이 징수됩니다. 이 데이터는 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 클라우드 스토리지 관리 도구를 사용하여 별도로 삭제해야 합니다.

## <a name="initiate-device-return"></a>디바이스 반환 시작

반환 프로세스를 시작하려면 다음 단계를 수행하세요.

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center(미리 보기)](#tab/azure-edge-hardware-center) 

Azure Edge 하드웨어 센터를 사용하여 디바이스를 주문한 경우 다음 단계에 따라 디바이스를 반환합니다.

1. Azure Portal Azure Edge 하드웨어 센터 주문 항목 리소스로 이동합니다. **개요** 에서 오른쪽 창의 위쪽 명령 모음으로 이동하여 **반환을** 선택합니다. 반환 옵션은 디바이스를 받은 후에만 사용할 수 있습니다.

    ![디바이스 반환 1](media/azure-stack-edge-return-device/hardware-center-return-device-1.png)  

1. 하드웨어 **반환** 블레이드에서 다음 정보를 제공합니다.

    ![디바이스 반환 2](media/azure-stack-edge-return-device/hardware-center-return-device-2.png) 

    1. 드롭다운 목록에서 를 **반환하는 이유를** 선택합니다.

    1. 디바이스의 일련 번호를 제공합니다. 디바이스의 일련 번호를 확인하려면 디바이스의 로컬 웹 UI로 이동한 다음 **개요** 로 이동하세요.  
    
       ![디바이스 일련 번호 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    1. (선택 사항) 서비스 **태그** 번호를 입력합니다. 서비스 태그 번호는 디바이스의 고유한 다섯 개 이상의 문자를 포함하는 식별자입니다. 서비스 태그는 디바이스의 오른쪽 아래 모서리에 있습니다(디바이스를 향하는 경우). 정보 태그를 끌어옵니다(슬라이드 아웃 레이블 패널). 이 패널에는 서비스 태그, NIC, MAC 주소 등의 시스템 정보가 포함되어 있습니다. 
    
       ![서비스 태그 번호 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    1. 반송 상자를 요청하려면 하드웨어 **단위를 반환하는 데 필요한 배송 상자를** 선택합니다. 요청할 수 있습니다. **반환용 빈 상자 필요** 질문에 **예** 로 답합니다.
    
    1. **개인 정보 사용 약관** 을 검토하고, 검토한 메모 옆의 확인란을 선택하고, 개인 정보 사용 약관에 동의합니다.

    1. 픽업 세부 정보 를 **확인합니다.** 기본적으로 이러한 주소는 배송 주소로 설정됩니다. 새 주소를 추가하거나 반송 픽업을 위해 저장된 주소에서 다른 주소를 선택할 수 있습니다.

        ![디바이스 반환 3](media/azure-stack-edge-return-device/hardware-center-return-device-3.png) 

    1. **반환 시작** 을 선택합니다.

1. 반환 요청이 제출되면 주문 항목 리소스가 반품 배송 상태를 반영하기 시작합니다. 상태는 **Return initiated에서 Picked** **up으로** 진행되어 **완료된 반환까지 진행됩니다.** 언제든지 포털을 사용하여 리소스의 반환 상태를 확인합니다.

    ![디바이스 반환 5](media/azure-stack-edge-return-device/hardware-center-return-device-4.png) 

1. 요청이 시작되면 Azure Stack Edge 운영 팀이 디바이스 픽업을 예약하기 위해 연락합니다.

다음 단계는 디바이스를 패키지하는 것입니다.


### <a name="portal-classic"></a>[포털(클래식)](#tab/azure-portal)

클래식 포털을 사용하여 디바이스를 주문한 경우 다음 단계에 따라 디바이스를 반환합니다.

1. Azure Portal Azure Stack Edge 리소스로 이동합니다. **개요** 에서 오른쪽 창의 명령 모음으로 이동하고 **디바이스 반환** 을 선택합니다. 

    ![디바이스 반환 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. **디바이스 반환** 블레이드의 **기본 세부 정보** 아래:

    1. 디바이스의 일련 번호를 제공합니다. 디바이스의 일련 번호를 확인하려면 디바이스의 로컬 웹 UI로 이동한 다음 **개요** 로 이동하세요.  
    
       ![디바이스 일련 번호 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. 서비스 태그 번호를 입력합니다. 서비스 태그 번호는 디바이스의 고유한 다섯 개 이상의 문자를 포함하는 식별자입니다. 서비스 태그는 디바이스의 오른쪽 아래 모서리에 있습니다(디바이스를 향하는 경우). 정보 태그를 끌어옵니다(슬라이드 아웃 레이블 패널). 이 패널에는 서비스 태그, NIC, MAC 주소 등의 시스템 정보가 포함되어 있습니다. 
    
       ![서비스 태그 번호 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. 드롭다운 목록에서 반환 이유를 선택합니다.

       ![디바이스 반환 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. **배송 세부 정보** 에서:

    1. 이름, 회사 이름 및 전체 회사 주소를 제공합니다. 지역 번호 및 알림에 대한 이메일 ID를 포함하는 직장 전화를 입력합니다.
    2. 반송 상자가 필요한 경우 요청할 수 있습니다. **반환용 빈 상자 필요** 질문에 **예** 로 답합니다.

    ![디바이스 반환 3](media/azure-stack-edge-return-device/return-device-3.png)

4. **개인 정보 사용 약관** 을 검토하고, 검토한 메모 옆의 확인란을 선택하고, 개인 정보 사용 약관에 동의합니다.

5. **반환 시작** 을 선택합니다.

    ![디바이스 반환 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. 디바이스 반환 세부 정보가 캡처되면 이메일을 통해 Azure Stack Edge 운영 팀에 알릴 수 있습니다. 이메일 애플리케이션이 설치 및 구성되어 있다고 가정하고 이메일 애플리케이션을 사용할 수 있습니다. 데이터를 복사하여 이메일을 만들고 보낼 수도 있습니다.

    ![디바이스 반환 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge 운영 팀이 이메일을 받으면 역방향 배송 레이블을 보냅니다. 이 레이블을 받으면 운송업체에서 디바이스 픽업을 예약할 수 있습니다. 

---

## <a name="pack-the-device"></a>디바이스 팩

디바이스를 포장하려면 다음 단계를 수행합니다.

1. 디바이스를 종료합니다. 로컬 웹 UI에서 **유지 관리 > 전원 설정** 으로 이동합니다.
2. **종료** 를 선택합니다. 확인 메시지가 표시되면 **예** 를 클릭하여 계속합니다. 자세한 내용은 [전원 관리](../databox-online/azure-stack-edge-manage-access-power-connectivity-mode.md#manage-power)를 참조하세요.
3. 전원 케이블을 분리하고 디바이스에서 모든 네트워크 케이블을 제거합니다.
4. 다음 다이어그램과 같이 다음 지침에 따라 배송 패키지를 신중하게 준비합니다.

    ![디바이스 패키징](media/azure-stack-edge-return-device/device-packaging-1.svg) 

    1. Azure에서 요청한 배송 상자 또는 포장 포장이 있는 원래 배송 상자를 사용합니다. 
    1. 상자의 아래쪽 밑면을 놓습니다.
    1. 깔개기 위에 디바이스를 놓아서 깔개에 깔고 있는 것을 주의를 기울입니다.
    1. 패키지에 맨 위 씩씩한 조각을 놓습니다.
    1. 접근자 트레이에 전원 코드를 놓고 레일을 맨 위 돋매기 위에 놓습니다.
    1. 상자를 봉인하고 패키지의 Azure에서 받은 배송 레이블을 부착합니다.
   
    > [!IMPORTANT]
    > 반송 배송을 준비하기 위한 적절한 지침이 관찰되지 않으면 디바이스가 손상되고 손상된 디바이스 요금이 적용될 수 있습니다. 제품 [서비스 약관](https://www.microsoft.com/licensing/product-licensing/products) 및 [분실 또는 손상된 디바이스에 대한 FAQ를](https://azure.microsoft.com/pricing/details/databox/edge/)검토합니다.
 


## <a name="schedule-a-pickup"></a>픽업 예약

픽업을 예약하려면 다음 단계를 수행합니다.

1. 해당 지역 운송업체에서 픽업하도록 예약합니다. 미국 내에서 디바이스를 반환하는 경우 UPS 또는 FedEx를 이용할 수 있습니다. UPS로 픽업을 예약하려면 다음을 수행합니다.

    1. 로컬 UPS(국가/지역별 무료 전화 번호)에 전화합니다.
    2. 통화 시 인쇄된 레이블에 표시된 역방향 배송 추적 번호를 알려줍니다.
    3. 추적 번호가 인용되지 않은 경우 UPS를 통해 픽업 시 추가 요금을 지불해야 합니다.

    픽업을 예약하는 대신 가장 가까운 반납 위치에 Azure Stack Edge를 반납할 수도 있습니다.

## <a name="complete-return"></a>반환 완료

이 섹션에서는 반환이 완료된 시기를 확인한 다음 주문을 삭제하도록 선택할 수 있습니다. 

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center(미리 보기)](#tab/azure-edge-hardware-center)

반환을 시작하면 청구가 일시 중지됩니다. Azure 데이터 센터에서 디바이스를 받은 후 디바이스에 손상 또는 변조 징후가 있는지 검사합니다.

- 디바이스가 그대로 도착하여 모양이 양호하면 Azure Stack Edge 운영 팀에서 디바이스가 반환되었는지 확인하기 위해 연락합니다. Azure Portal 디바이스와 연결된 리소스를 삭제하도록 선택할 수 있습니다.
- 디바이스가 크게 손상된 경우 요금이 적용될 수 있습니다. 자세한 내용은 [분실 또는 손상된 디바이스에 대한 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/)와 [제품 서비스 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요. 

### <a name="portal-classic"></a>[포털(클래식)](#tab/azure-portal) 

반환을 시작하면 청구가 일시 중지됩니다. Azure 데이터 센터에서 디바이스를 받은 후 디바이스에 손상 또는 변조 징후가 있는지 검사합니다.

- 디바이스가 그대로 도착하여 모양이 양호하면 Azure Stack Edge 운영 팀에서 디바이스가 반환되었는지 확인하기 위해 연락합니다. Azure Portal 디바이스와 연결된 리소스를 삭제하도록 선택할 수 있습니다.
- 디바이스가 크게 손상된 경우 요금이 적용될 수 있습니다. 자세한 내용은 [분실 또는 손상된 디바이스에 대한 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/)와 [제품 서비스 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요.  


Microsoft Azure Portal에서 디바이스를 삭제할 수 있습니다.

- 주문을 한 후에 Microsoft에서 디바이스를 준비합니다.
- 디바이스를 Microsoft로 반환한 후 Azure Stack Edge 운영 팀에서 디바이스가 반환되었는지 확인하기 위해 전화를 걸었습니다. 운영 팀은 반환된 디바이스가 Azure 데이터 센터에서 물리적 검사를 통과할 때까지 전화를 걸지 않습니다.

다른 구독 또는 위치에 대해 디바이스를 활성화한 경우 Microsoft는 1일(업무일 기준) 이내에 주문을 새 구독 또는 위치로 이동합니다. 주문이 이동된 후 이 리소스를 삭제할 수 있습니다.


Microsoft Azure Portal에서 디바이스와 리소스를 삭제하려면 다음 단계를 수행하세요.

1. Microsoft Azure Portal에서 리소스로 이동한 다음, **개요** 로 이동합니다. 명령 모음에서 **삭제** 를 선택합니다.

    ![삭제 선택](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **디바이스 삭제** 블레이드에서 삭제하려는 디바이스의 이름을 입력하고 **삭제** 를 선택합니다.

    ![삭제 확인](media/azure-stack-edge-return-device/delete-resource-2.png)

디바이스와 연결된 리소스가 성공적으로 삭제되면 알림을 받습니다.

---

## <a name="next-steps"></a>다음 단계

- [교체 Azure Stack Edge 디바이스 가져오기](azure-stack-edge-replace-device.md) 방법을 알아봅니다.
