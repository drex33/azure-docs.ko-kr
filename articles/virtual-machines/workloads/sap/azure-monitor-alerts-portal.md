---
title: Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor에서 경고 구성
description: SAP 솔루션을 위한 Azure Monitor에서 경고를 구성하기 위해 브라우저 방법을 사용하는 방법을 알아봅니다.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 08/30/2021
ms.openlocfilehash: 642eb96d07a351537f1320f823e698968e38d23e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309570"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor에서 경고 구성

이 문서에서는 SAP 솔루션을 위한 Azure Monitor에서 경고를 구성하는 단계를 안내합니다. [Azure Portal](https://azure.microsoft.com/features/azure-portal)에서 브라우저 기반 인터페이스를 사용하여 경고 및 알림을 구성합니다.

## <a name="prerequisites"></a>필수 조건

하나 이상의 공급자를 사용하여 SAP 솔루션을 위한 Azure Monitor 리소스를 배포합니다. 공급자 구성에서 다음을 구성할 수 있습니다. 
- SAP 애플리케이션(NetWeaver)
- SAP HANA
- Microsoft SQL Server:
- 고가용성(Pacemaker) 클러스터

## <a name="sign-in-to-the-portal"></a>포털에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-alert-rule"></a>경고 규칙 만들기

1.  Azure Portal에서 SAP 솔루션을 위한 Azure Monitor 리소스를 찾아 선택합니다. 이 리소스에 대해 구성된 공급자가 하나 이상 있는지 확인합니다. 
2.  원하는 통합 문서로 이동합니다. 예를 들어 SAP HANA로 이동하고 HANA 인스턴스를 선택합니다.

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="경고 단추의 위치를 보여 주는 스크린샷." lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  사용 가능한 **경고 템플릿** 을 보려면 **경고** 단추를 선택합니다.

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="경고 템플릿 목록을 보여 주는 스크린샷." lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  **규칙 만들기** 를 선택하여 원하는 경고를 구성합니다.
5.  **경고 임계값** 을 입력하고 **공급자 인스턴스** 를 선택한 다음,**작업 그룹** 을 선택하거나 만들어 알림 설정을 구성합니다. 요구 사항에 따라 빈도 및 심각도 정보를 편집할 수 있습니다.

    >[!Tip]
    > [작업 그룹](../../../azure-monitor/alerts/action-groups.md)에 대해 자세히 알아보세요. 
    
7.  **경고 규칙 사용** 을 선택합니다.

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="경고 구성 페이지를 보여 주는 스크린샷." lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  **경고 규칙 배포** 를 선택하여 경고 규칙 구성을 완료합니다. **템플릿 보기** 를 클릭하여 경고 템플릿을 볼 수 있습니다.

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="경고 구성의 마지막 단계를 보여 주는 스크린샷." lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  새로 만든 경고 규칙을 보려면 **경고 규칙** 으로 이동하세요. 경고가 실행된 경우 **실행된 경고** 에서 확인할 수 있습니다.

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="경고 구성 결과를 보여 주는 스크린샷." lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>다음 단계

SAP 솔루션을 위한 Azure Monitor에 관해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure의 SAP 모니터링](monitor-sap-on-azure.md)
