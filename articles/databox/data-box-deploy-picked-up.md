---
title: Azure Data Box 반송 자습서 | Microsoft Docs
description: 이 자습서에서는 배송 준비, Data Box 배송, 데이터 업로드 확인 및 Data Box에서 데이터 지우기를 포함하여 Azure Data Box를 반환하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/17/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 8b51cbf8af938a71faea05b2cadbea904b508d26
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138653"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>자습서: Azure Data Box 반송 및 Azure에 대한 데이터 업로드 확인

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box 반환 및 Azure에 대한 데이터 업로드 확인

::: zone-end

::: zone target="docs"

이 자습서에서는 Azure Data Box를 반송하고 Azure에 업로드된 데이터를 확인하는 방법을 설명합니다.

이 자습서에서는 다음과 같은 주제에 대해 학습합니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 배송 준비
> * Microsoft에 Data Box 배송
> * Azure에 대한 데이터 업로드 확인
> * Data Box에서 데이터 지우기

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

* [자습서: Azure Data Box에 데이터 복사 및 확인](data-box-deploy-copy-data.md)을 완료했는지 확인합니다.
* 복사 작업이 완료되었으며 **연결 및 복사** 페이지에 오류가 없습니다. 복사 작업이 진행 중이거나 **연결 및 복사** 페이지에 오류가 있는 경우에는 **배송 준비** 를 실행할 수 없습니다.

## <a name="prepare-to-ship"></a>배송 준비

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

데이터 복사가 완료되면 디바이스를 준비하고 배송합니다. 디바이스가 Azure 데이터 센터에 도달하면 데이터가 자동으로 Azure에 업로드됩니다.

## <a name="prepare-to-ship"></a>배송 준비

배송을 준비하기 전에 복사 작업이 완료되었는지 확인합니다.

1. 로컬 웹 UI에서 **배송 준비** 페이지로 이동하여 배송 준비를 시작합니다.
2. 로컬 웹 UI에서 디바이스를 끕니다. 디바이스에서 케이블을 뽑습니다.

다음 단계는 디바이스를 반송하는 위치에 따라 결정됩니다.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box 반송

디바이스에 대한 데이터 복사가 완료되고 **배송 준비** 실행이 성공했는지 확인합니다. 디바이스를 배송하는 지역에 따라 절차가 다릅니다.

### <a name="microsoft-managed-shipping"></a>Microsoft 관리형 배송

Microsoft 관리형 배송을 사용하는 경우 배송할 지역에 대한 지침을 따르세요.

::: zone-end

## <a name="us--canada"></a>[미국 및 캐나다](#tab/in-us-canada)

미국 또는 캐나다에서 디바이스를 반송하는 경우 다음 단계를 수행합니다.

1. 디바이스의 전원이 꺼져 있고 케이블이 분리되었는지 확인합니다.
2. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
3. 배송 레이블이 E-ink 디스플레이에 표시되는지 확인하고 운송업체에 픽업을 예약합니다. 레이블이 손상 또는 손실되었거나 E-ink 디스플레이에 표시되지 않으면 Microsoft 지원에 문의하세요. 고객 지원팀에서 제안하는 경우 Azure Portal에서 **개요 > 배송 레이블 다운로드** 로 차례로 이동합니다. 배송 레이블을 다운로드하여 디바이스에 부착합니다. 
4. 디바이스를 반송하는 경우 UPS로 픽업을 예약합니다. 픽업을 예약하려면 다음을 수행합니다.

    * 로컬 UPS(국가/지역별 무료 전화 번호)에 전화합니다.
    * 통화에서 E-ink 디스플레이 또는 인쇄된 레이블에서 표시한 대로 역방향 배송 추적 번호를 알려줍니다. 추적 번호를 알려주지 않으면 픽업 중에 UPS에 추가 요금이 부과됩니다.
    * 픽업을 예약하는 동안 문제가 발생하거나 추가 요금을 지불하라는 메시지가 표시되면 Azure Data Box Operations에 문의하세요. [adbops@microsoft.com](mailto:adbops@microsoft.com)에 이메일을 보냅니다.

    픽업을 예약하는 대신 가장 가까운 반납 위치에 Data Box를 반납할 수도 있습니다.
4. 운송업체에서 Data Box를 픽업하고 나면 포털의 주문 상태가 **픽업됨** 으로 업데이트됩니다. 추적 ID도 표시됩니다.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기

Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

## <a name="europe"></a>[유럽](#tab/in-europe)

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

    **독일 또는 스위스에서 배송하는 경우**, [자체 관리형 배송을 사용](data-box-deploy-picked-up.md#self-managed-shipping)할 수도 있습니다.

4. 운송업체에서 Data Box를 픽업하고 나면 포털의 주문 상태가 **픽업됨** 으로 업데이트됩니다. 추적 ID도 표시됩니다.


::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기

Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

## <a name="australia"></a>[오스트레일리아](#tab/in-australia)

오스트레일리아의 Azure 데이터 센터에는 추가 보안 알림이 있습니다. 모든 인바운드 배송에는 고급 알림이 있어야 합니다. 오스트레일리아에서 배송하려면 다음 단계를 수행합니다.

1. 반품 배송을 위해 디바이스를 배송하는 데 사용된 원래 상자를 보관하세요.
2. 디바이스로 데이터 복사가 완료되고 **배송 준비** 실행이 성공했는지 확인합니다.
3. 디바이스의 전원을 끄고 케이블을 분리합니다.
4. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
5. [DHL 링크](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)에서 온라인으로 픽업을 예약합니다.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기

Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

## <a name="japan"></a>[일본](#tab/in-japan)

1. 반품 배송을 위해 디바이스를 배송하는 데 사용된 원래 상자를 보관하세요.
2. 디바이스의 전원을 끄고 케이블을 분리합니다.
3. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
4. 탁송 운송장에 회사 이름과 주소 정보를 보낸 사람 정보로 작성합니다.
5. 다음 이메일 템플릿을 사용하여 Quantium Solutions에 이메일을 보냅니다.

    * Japan Post Chakubarai 탁송 운송장이 포함되지 않거나 누락된 경우 이를 이메일에서 언급해야 합니다. Quantium Solutions Japan은 Japan Post에 픽업 시 탁송 운송장을 가져올 것을 요청합니다.
    * 주문이 여러 개인 경우에는 개별 픽업을 보장하도록 이메일을 보냅니다.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body:
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. 픽업을 예약한 후 Quantium Solutions에서 이메일 확인을 수신합니다. 이메일 확인에는 Chakubarai 탁송 운송장에 대한 정보도 포함되어 있습니다.

필요한 경우 다음 정보로 Quantium Solutions 지원(일본어)에 문의할 수 있습니다.

* 이메일: Customerservice.JP@quantiumsolutions.com 
* 전화: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

## <a name="singapore"></a>[싱가포르](#tab/in-singapore)

1. 반품 배송을 위해 디바이스를 배송하는 데 사용된 원래 상자를 보관하세요.
2. Data Box 로컬 웹 UI의 **배송 준비** 페이지에 참조 번호로 표시된 추적 번호를 적어둡니다. 추적 번호는 **배송 준비** 단계가 성공적으로 완료된 후에 사용할 수 있습니다. 이 페이지에서 배송 레이블을 다운로드하고 압축 상자에 붙여넣습니다.
3. 디바이스의 전원을 끄고 케이블을 분리합니다.
4. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다. 
5. 추적 번호가 포함된 다음 이메일 템플릿을 사용하여 SingPost Customer Service에 이메일을 보냅니다.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > 영업일에 수신된 예약 요청:
   > * 오후 3시 이전에는 오전 9시에서 오후 1시 사이에 다음 영업일에 픽업합니다.
   > * 오후 3시 이후에는 오후 2시에서 오후 6시 사이에 다음 영업일에 픽업합니다.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기

Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

## <a name="south-africa"></a>[남아프리카 공화국](#tab/in-sa)

1. 반품 배송을 위해 디바이스를 원래 상자에 포장합니다.
2. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
3. Data Box 로컬 웹 UI의 **배송 준비** 페이지에 참조 번호로 표시된 추적 번호를 적어둡니다. 추적 번호는 "배송 준비" 단계가 성공적으로 완료된 후에 사용할 수 있습니다. 이 페이지에서 배송 레이블을 다운로드하고 포장 상자에 붙여넣습니다.
4. Azure Data Box 작업에서 반환 코드를 요청합니다. 패키지를 데이터 센터로 다시 전달하려면 반환 코드가 필요합니다. [adbops@microsoft.com](mailto:adbops@microsoft.com)에 이메일을 보냅니다. 반환 주소 옆에 있는 배송 레이블에 이 코드를 명확하게 표시합니다.
5. 다음 방법 중 하나를 사용하여 DHL로 픽업을 예약합니다.
 
   * [DHL Express 남아프리카, **픽업예약**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference)으로 이동하여 온라인으로 픽업을 예약합니다.
   * 다음 템플릿을 사용하여 [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com)에 이메일을 보냅니다.

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * 또는 가장 가까운 DHL 서비스 지점에서 패키지를 삭제합니다.

6. 문제가 발생하는 경우 발생한 문제에 대한 세부 정보가 포함된 [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) 이메일을 보내고 화물 운송장 번호를 제목: 줄에 입력합니다. +27(0)119213902로 전화할 수도 있습니다.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기

Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

## <a name="hong-kong"></a>[홍콩](#tab/in-hk)

1. 반품 배송을 위해 디바이스를 원래 상자에 포장합니다.
2. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
3. 근무 시간(오전 9시~오후 6시, 월요일~금요일) 동안 **Quantium Solutions** 핫라인 **(852) 2318 1213** 으로 전화하세요.  
4. Microsoft Azure 픽업과 반송 레이블의 참조 번호 및 추적 번호(바코드 위)를 인용하여 컬렉션을 준비합니다.
5. 픽업 일정에 대한 구두 확인 메시지를 받게 됩니다. 택배 회사에서 수거를 위해 도착하지 않으면 Quantium Solutions 핫라인으로 전화를 걸어 대체 예약을 요청합니다.
6. Quantium Solutions에서 픽업을 예약하는 경우 다음 템플릿을 사용하여 [Microsoft Data Box Operations 아시아](mailto:adbo@microsoft.com)와 확인을 공유합니다.

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

문제가 발생하는 경우 발생한 문제에 대한 세부 정보가 포함된 Data Box Operations 아시아 [adbo@microsoft.com](mailto:adbo@microsoft.com) 이메일을 보내고 작업 이름을 제목: 줄에 입력합니다.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 

::: zone-end

## <a name="uae"></a>[아랍에미리트](#tab/in-uae)

1. 반품 배송을 위해 디바이스를 배송하는 데 사용된 원래 상자를 보관하세요.
2. 디바이스로의 데이터 복사가 완료되고 **배송 준비** 단계가 성공적으로 완료되었는지 확인합니다.
3. 디바이스 로컬 웹 UI의 **배송 준비** 페이지에서 참조 번호를 확인합니다.
4. 디바이스의 전원을 끄고 케이블을 분리합니다. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
6. 반품 배송을 위해 디바이스를 원래 상자에 포장합니다.
7. [Azure Data Box 운영 담당자](mailto:adbops@microsoft.com)에게 이메일을 보내 데이터 센터에 다시 도착할 때 패키지를 식별하는 데 사용할 ID를 얻습니다.
8. 명확하게 표시되도록 이 ID를 반송 주소 옆에 인쇄되는 배송 레이블에 기록합니다.  
9. [DHL Express UAE](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab) > **픽업 예약** 으로 차례로 이동하여 온라인으로 픽업을 예약합니다.
   - 디바이스 로컬 웹 UI의 **배송 준비** 페이지에 있는 참조 번호를 화물 운송장 번호 필드에 입력합니다.
   - 예약은 주 6일(금요일 및 공휴일 제외) 오전 9시부터 오후 2시까지 허용됩니다.
   - 픽업은 고객 종료 시간 최소 90분 전에 요청해야 합니다.
10. DHL 예약 도구와 관련하여 문제가 발생하는 경우 다음 방법 중 하나를 사용하여 DHL에 문의할 수 있습니다.
    - 전화(04-2924545)를 겁니다.
    - 문제에 대한 세부 정보가 포함된 이메일을 [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com)에 보내고, 화물 운송장 번호를 '제목:' 줄에 입력합니다.
    - DHL 고객 지원에 전화(600 567567)를 겁니다.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload.md)]

::: zone-end
-->

---

### <a name="self-managed-shipping"></a>자체 관리형 배송

Microsoft 관리형 배송을 사용하는 경우 배송할 지역에 대한 지침을 따르세요.

미국 정부, 일본, 싱가포르, 대한민국, 인도, 남아프리카, 영국, 독일, 스위스, 서유럽, 오스트레일리아 또는 브라질에서 Data Box를 사용 중이고 주문을 생성할 때 자체 관리형 배송을 선택한 경우 다음 지침을 따르세요.

1. 이 단계가 성공적으로 완료된 후 Data Box에 대한 로컬 웹 UI의 **배송 준비** 페이지에 표시된 인증 코드를 적어둡니다.
2. 디바이스의 전원을 끄고 케이블을 분리합니다. 디바이스와 함께 제공된 전원 코드를 디바이스 뒷면에 스풀링하고 안전하게 고정합니다.
3. 디바이스를 반환할 준비가 되면 아래 템플릿을 사용하여 Azure Data Box Operations 팀에 이메일을 보냅니다.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a government-approved ID during the drop off.
    ```

   > [!NOTE]
   > - 반환에 필요한 정보는 지역에 따라 다를 수 있습니다. 
   > - 브라질에 Data Box을 반환하는 경우 자세한 지침은 [Azure Data Box에 대한 자체 관리형 배송 사용](data-box-portal-customer-managed-shipping.md)을 참조하세요. 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box에서 데이터 지우기
 
Azure에 대한 업로드가 완료되면 Data Box 디스크의 데이터가 [NIST SP800-88 개정 1 지침](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)에 따라 지워집니다.

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Azure에 대한 데이터 업로드 확인

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
