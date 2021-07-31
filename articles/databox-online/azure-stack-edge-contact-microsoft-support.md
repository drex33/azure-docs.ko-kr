---
title: Azure Stack Edge, Azure Data Box Gateway의 지원 티켓 기록 | Microsoft Docs
description: Azure Stack Edge 또는 Data Box Gateway 주문과 관련된 문제에 관한 지원 요청을 기록하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.openlocfilehash: 1ad5475078c515d36a57b7608ab9d363c6f678aa
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983956"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Azure Stack Edge 및 Azure Data Box Gateway의 지원 티켓 열기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-fpga-databox-gateway-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-databox-gateway-sku.md)]

이 문서는 Azure Stack Edge/Azure Data Box Gateway 서비스에서 관리하는 Azure Stack Edge 및 Azure Data Box Gateway에 둘 다 적용됩니다. 서비스와 관련된 문제가 발생하면 기술 지원을 위한 서비스 요청을 만들 수 있습니다. 이 문서에서는 다음을 안내합니다.

* 지원 요청을 만드는 방법
* 포털 내에서 지원 요청 주기를 관리하는 방법

## <a name="create-a-support-request"></a>지원 요청 만들기

지원 요청을 만들려면 다음 단계를 수행합니다.

1. Azure Stack Edge 또는 Data Box Gateway 주문으로 이동합니다. **지원 + 문제 해결** 섹션으로 이동한 다음, **새 지원 요청** 을 선택합니다.

2. **새 지원 요청** 의 **기본 사항** 탭에서 다음 단계를 수행합니다.

    1. **문제점 유형** 드롭다운 목록에서 **기술** 을 선택합니다.
    2. **구독** 을 선택합니다.
    3. **서비스** 에서 **내 서비스** 를 확인합니다. 드롭다운 목록에서 **Azure Stack Edge 및 Data Box Gateway** 를 선택합니다.
    4. **리소스** 를 선택합니다. 이는 주문의 이름에 해당합니다.
    5. 발생한 문제에 대한 간략한 **요약** 을 제공합니다. 
    6. **문제 유형** 을 선택합니다.
    7. 선택한 문제 유형에 따라 해당 **문제 하위 유형** 을 선택합니다.
    8. 완료되면 **다음: 솔루션 >>** 을 선택합니다.

        ![기본 사항](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. **세부 정보** 탭에서 다음 단계를 수행합니다.

    1. 문제의 시작 날짜 및 시간을 제공합니다.
    2. 문제에 관한 **설명** 을 제공합니다.
    3. **파일 업로드** 에서 폴더 아이콘을 선택하여 업로드할 다른 파일을 찾습니다.
    4. **진단 정보 공유** 를 선택합니다.
    5. 구독에 따라 **지원 플랜** 이 자동으로 채워집니다.
    6. 드롭다운 목록에서 **심각도** 를 선택합니다.
    7. **기본 연락 방법** 을 지정합니다.
    8. **응답 시간** 은 구독 플랜에 따라 자동으로 선택됩니다.
    9. 원하는 지원 언어를 제공합니다.
    10. **연락처 정보** 에서 이름, 메일, 전화, 선택적 연락처, 국가/지역을 제공합니다. Microsoft 지원에서 이 정보를 사용하여 사용자에게 자세한 정보, 진단 및 해결 방법을 알려줍니다. 
    11. 완료되면 **다음: 검토 + 만들기 >>** 를 선택합니다.

        ![문제](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. **검토 + 만들기** 탭에서 지원 티켓과 관련된 정보를 검토합니다. **만들기** 를 선택합니다. 

    ![문제 2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    지원 티켓을 만든 후에 지원 엔지니어가 요청을 계속 진행하기 위해 최대한 빨리 연락할 것입니다.

## <a name="get-hardware-support"></a>하드웨어 지원 받기

이 정보는 Azure Stack 디바이스에만 적용됩니다. 하드웨어 문제를 보고하는 프로세스는 다음과 같습니다.

1. Azure Portal에서 하드웨어 문제의 지원 티켓을 엽니다. **문제 유형** 에서 **Azure Stack 하드웨어** 를 선택합니다. **문제 하위 유형** 을 **하드웨어 오류** 로 선택합니다.

    ![하드웨어 문제](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    지원 티켓을 만든 후에 지원 엔지니어가 요청을 계속 진행하기 위해 최대한 빨리 연락할 것입니다.

2. Microsoft 지원에서 하드웨어 문제라고 판단하면 다음 작업 중 하나가 수행됩니다.

    * 오류가 발생한 하드웨어 파트의 FRU(Field Replacement Unit)가 전송됩니다. 현재 지원되는 FRU는 전원 공급 장치와 반도체 드라이브뿐입니다.
    * FRU만 다음 영업일 내에 교체되며, 다른 파트의 경우 모두 FSR(Full System Replacement)이 제공되어야 합니다.

3. 현지 시간으로 월요일~금요일 오후 1시까지 FRU 교체가 필요하다고 확인되면 다음 영업일에 현장 기술자가 해당 위치로 파견되어 FRU 교체를 수행합니다. 부품이 공장에서 배송되며 운송 및 세관 지연이 발생할 수 있기 때문에 전체 시스템 교체는 일반적으로 훨씬 더 오래 걸립니다.

## <a name="manage-a-support-request"></a>지원 요청 관리

지원 티켓을 만든 후에는 포털 내에서 티켓의 수명 주기를 관리할 수 있습니다.

### <a name="to-manage-your-support-requests"></a>지원 요청을 관리하려면

1. 도움말 및 지원 페이지로 이동하려면 **찾아보기 > 도움말 + 지원** 으로 이동합니다.

    ![지원 요청 관리](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. **최근 지원 요청** 의 테이블 형식 목록이 **도움말 + 지원** 에 표시됩니다.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. 지원 요청을 클릭하여 선택합니다. 이 요청의 상태 및 세부 정보를 볼 수 있습니다. 이 요청에 대해 후속 작업을 수행하려는 경우 **+ 새 메시지** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge FPGA와 관련된 문제를 해결합니다](azure-stack-edge-troubleshoot.md).
- [Azure Stack Edge Pro GPU에 대한 디바이스 문제를 해결합니다](azure-stack-edge-gpu-troubleshoot.md).
- [Data Box Gateway와 관련된 문제를 해결합니다](../databox-gateway/data-box-gateway-troubleshoot.md).