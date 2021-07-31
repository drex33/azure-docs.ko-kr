---
title: Microsoft Azure Data Box 자체 관리형 배송 | Microsoft Docs
description: Azure Data Box 디바이스용 자체 관리형 배송 워크플로에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 07/22/2021
ms.author: alkohli
ms.openlocfilehash: cafea36f45dcc063fadc1562428735ee0ac9fe20
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652823"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Azure Portal에서 Azure Data Box용 자체 관리형 배송 사용

이 문서에서는 Azure Data Box 디바이스를 주문, 픽업 및 반납하기 위한 자체 관리형 배송 태스크에 대해 설명합니다. Azure Portal에서 Data Box 디바이스를 관리할 수 있습니다.

> [!NOTE]
> Data Box 주문 및 배송에 대한 질문과 대답은 [Data Box FAQ](data-box-faq.yml)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Data Box 주문](data-box-deploy-ordered.md) 시 자체 관리형 배송을 옵션으로 사용할 수 있습니다. 자체 관리형 배송은 다음 지역에서만 사용할 수 있습니다.

* 미국 정부
* 영국
* 서부 유럽
* 일본
* 싱가포르
* 대한민국
* 인도
* 남아프리카
* 오스트레일리아
* 브라질

## <a name="use-self-managed-shipping"></a>자체 관리형 배송 사용

Data Box를 주문할 때 자체 관리형 배송 옵션을 선택할 수 있습니다.

1. Azure Data Box 주문의 **연락처 세부 정보** 에서 **+ 배송 주소 추가** 를 선택합니다.
 
   ![자체 관리형 배송, 배송 주소 추가](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 배송 유형을 선택할 때 **자체 관리형 배송** 옵션을 선택합니다. 이 옵션은 필수 구성 요소에 설명된 대로 지원되는 지역에서만 사용할 수 있습니다.

3. 배송 주소를 입력한 후에는 유효한지 검사하고 주문을 완료해야 합니다.

   ![자체 관리형 배송, 유효성 검사 및 주소 추가](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 디바이스가 준비되었다는 이메일 알림을 받으면 픽업을 예약할 수 있습니다.

   Azure Data Box 주문에서 **개요** 로 이동한 후 **픽업 예약** 을 선택합니다.

   ![Data Box 주문, 픽업 예약 옵션](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. **Azure 픽업 예약** 의 지침을 따릅니다.

   인증 코드를 받으려면 [adbops@microsoft.com](mailto:adbops@microsoft.com)으로 메일을 보내 지역 데이터 센터에서 디바이스 픽업 일정을 예약해야 합니다.

   ![Azure 픽업 예약 지침](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

   **브라질에 대한 지침:** 브라질에서 디바이스 픽업을 예약하는 경우 이메일에 다음 정보를 포함합니다. 데이터 센터는 인바운드 `Nota Fiscal`을 받은 후 픽업을 예약하며, 영업일 기준으로 최대 4일이 소요될 수 있습니다.

   ```
   Subject: Request Azure Data Box Disk pickup for order: <ordername>

   - Order name
   - Company name
   - Company legal name (if different) 
   - CNPJ (Business Tax ID, format: 00.000.000/0000-00) or CPF (Individual Tax ID, format: 000.000.000-00)
   - Address
   - Country 
   - Phone number 
   - Contact name of the person who will pick up the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.)   
   ```

6. 디바이스 픽업을 예약한 후에는 **Azure 픽업 예약** 창에서 디바이스 인증 코드를 볼 수 있습니다.

   ![디바이스 인증 코드 보기](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   **인증 코드** 를 적어 둡니다. 디바이스를 픽업하는 사람이 코드를 제공해야 합니다.

   보안 요구 사항에 따라 픽업 일정을 예약할 때 픽업하러 올 사용자의 이름과 세부 정보를 제공해야 합니다. 본인 또는 연락 담당자에게 데이터 센터에서 유효성을 검사할 정부 승인 사진 ID가 있어야 합니다.

7. 예약된 시간에 데이터 센터에서 Data Box를 픽업합니다.

   디바이스를 픽업하는 사람은 다음을 제공해야 합니다.

   * Microsoft Operations의 데이터 센터를 방문하기 위한 이메일 확인 사본.

   * 인증 코드. 픽업 또는 반납을 위한 고유한 참조 번호이며 데이터 센터에서 확인됩니다.

   * 정부 승인 사진 ID. ID는 데이터 센터에서 확인되며, 픽업 예약 시 디바이스를 픽업하는 사람의 이름과 세부 정보를 제공해야 합니다.

   > [!NOTE]
   > 예약된 약속을 놓치면 새 약속을 예약해야 합니다.

8. 데이터 센터에서 디바이스를 픽업하면 주문이 자동으로 **픽업됨** 상태로 바뀝니다.

    ![픽업됨 상태의 주문](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

9. 디바이스를 픽업한 후에 사이트의 Data Box에 데이터를 복사합니다. 데이터 복사가 완료되면 Data Box 배송 준비를 진행할 수 있습니다. 자세한 내용은 [배송 준비](data-box-deploy-picked-up.md#prepare-to-ship)를 참조하세요.

   **배송 준비** 단계는 오류 없이 완료해야 합니다. 오류가 있으면 필요한 수정 작업을 수행하고 나서 이 단계를 다시 실행해야 합니다. **배송 준비** 단계를 성공적으로 완료한 후에는 디바이스 로컬 사용자 인터페이스에서 반납을 위한 인증 코드를 볼 수 있습니다.

   > [!NOTE]
   > 메일을 통해 인증 코드를 공유하지 마세요. 인증 코드는 반납 동안 데이터 센터에서만 확인할 수 있습니다.

   **브라질에 대한 지침:** 브라질에서 디바이스 반납을 예약하는 경우 다음 정보를 포함하여 [adbops@microsoft.com](mailto:adbops@microsoft.com)으로 이메일을 보냅니다.

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. 반납 약속을 받은 경우에는 Azure Portal에서 주문이 **Azure 데이터 센터에서 수신 준비 완료** 상태여야 합니다. **반납 예약** 의 지침에 따라 디바이스를 반환합니다.

    ![디바이스 반납 지침](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

11. ID 및 인증 코드를 확인하고 데이터 센터에서 디바이스를 반납한 후에는 주문 상태가 **수신됨** 이 됩니다.

    ![수신 상태의 주문](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

12. 디바이스를 수신하면 데이터 복사가 계속됩니다. 복사가 완료되면 주문이 완료됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 시작](data-box-quickstart-portal.md)
