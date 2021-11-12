---
title: Azure Portal Defender for IoT를 통해 센서 관리
description: Azure Portal Defender for IoT를 사용하여 센서를 온보딩, 보기 및 관리하는 방법을 알아봅니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: c01f5826238a5e4091fbaef58b42205c0bb2d9d2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339900"
---
# <a name="manage-sensors-with-defender-for-iot-in-the-azure-portal"></a>Azure Portal Defender for IoT를 통해 센서 관리

이 문서에서는 [Azure Portal Defender for IoT를](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)사용하여 센서를 온보딩, 보기 및 관리하는 방법을 설명합니다.

## <a name="onboard-sensors"></a>온보드 센서

Microsoft Defender for IoT에 등록하고 센서 활성화 파일을 다운로드하여 센서를 등록합니다.

### <a name="register-the-sensor"></a>센서 등록

**등록하려면 다음을 수행합니다.**

1. Azure Portal [Defender for IoT: 시작 페이지로](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 이동합니다.

1. **온보드 센서** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/onboard-a-sensor.png" alt-text="센서 온보딩을 선택하여 센서에 대한 온보딩 프로세스를 시작합니다.":::

1. 센서 이름을 만듭니다. 

    이름의 일부로 설치한 센서의 IP 주소를 포함하거나 쉽게 식별할 수 있는 이름을 사용하는 것이 좋습니다. 이렇게 하면 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 등록 이름과 센서 콘솔에 표시된 배포된 센서의 IP 간에 보다 쉽게 추적 및 일관된 이름을 지정할 수 있습니다.

1. 센서를 Azure 구독과 연결합니다.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/name-subscription.png" alt-text="의미 있는 이름을 입력하고 센서를 구독에 연결합니다.":::

1. **클라우드 연결** 토글을 사용하여 센서 연결 모드를 선택합니다. 토글이 켜져 있으면 센서가 클라우드에 연결된 것입니다. 토글이 꺼져 있으면 센서는 로컬 관리형입니다.

   - **클라우드 연결 센서**: 센서가 감지하는 정보가 센서 콘솔에 표시됩니다. 경고 정보는 IoT Hub를 통해 전달되며 Microsoft Sentinel과 같은 다른 Azure 서비스와 공유할 수 있습니다. 또한 위협 인텔리전스 패키지를 Defender for IoT에서 센서로 푸시할 수 있습니다. 반대로 센서가 클라우드에 연결되어 있지 않으면 위협 인텔리전스 패키지를 다운로드한 다음 엔터프라이즈 센서에 업로드해야 합니다. Defender for IoT가 패키지를 센서에 푸시하도록 허용하려면 **자동 위협 인텔리전스 업데이트** 토글을 사용하도록 설정합니다. 자세한 내용은 [위협 인텔리전스 연구 및 패키지](how-to-work-with-threat-intelligence-packages.md)를 참조하세요.
   
   클라우드 연결 센서의 경우 온보딩 중에 정의된 이름은 센서 콘솔에 표시되는 이름입니다. 콘솔에서 직접 이 이름을 변경할 수 없습니다. 로컬 관리형 센서의 경우 온보딩 중에 적용된 이름이 Azure에 저장되지만, 센서 콘솔에서 업데이트할 수 있습니다.

   - **로컬 관리형 센서**: 센서에서 탐지한 정보가 센서 콘솔에 표시됩니다. 에어-갭 네트워크에서 작업 중이고 여러 로컬 관리형 센서가 탐지한 모든 정보를 통합 보기에서 확인하려는 경우에는 온-프레미스 관리 콘솔을 사용합니다.

1. IoT Hub 내에서 센서를 연결할 사이트를 선택합니다. IoT Hub 이 센서와 Microsoft Defender for IoT 간의 게이트웨이 역할을 담당합니다. 표시 이름 및 영역을 정의합니다. 설명 태그를 추가할 수도 있습니다. 표시 이름, 영역 및 태그는 [사이트 및 센서 페이지](#view-onboarded-sensors)의 설명 항목입니다.

1. **등록** 을 선택합니다. 

### <a name="download-the-sensor-activation-file"></a>센서 활성화 파일 다운로드

센서를 등록하면 활성화 파일을 다운로드할 수 있습니다. 센서 활성화 파일에는 센서의 관리 모드에 대한 지침이 포함되어 있습니다. 배포하는 각 센서에 대해 고유한 활성화 파일을 다운로드합니다. 센서 콘솔에 처음으로 로그인한 사용자는 활성화 파일을 센서에 업로드합니다.

**활성화 파일을 다운로드하려면:**

1. **센서 온보딩** 페이지에서 **등록** 을 선택합니다.

1. **활성화 파일 다운로드** 를 선택합니다.

1. 처음으로 센서 콘솔에 로그인한 사용자가 파일에 액세스할 수 있도록 설정합니다.

## <a name="view-onboarded-sensors"></a>온보드 센서 보기

온보딩된 센서에 대한 중요한 운영 정보를 보려면 다음을 수행합니다.

1. Azure Portal [Defender for IoT: 시작하기로](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)이동합니다.

1. **사이트 및 센서** 를 선택합니다. 이 페이지에는 온보딩된 센서의 수, 클라우드에 연결되고 로컬로 관리되는 센서의 수 및 다음이 표시됩니다.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/sites-and-sensors.png" alt-text="사이트 및 센서 페이지를 선택하여 연결된 모든 센서를 봅니다.":::

    - 온보딩 중에 할당된 센서 이름
    - 연결 유형(클라우드 연결 또는 로컬 관리)
    - 센서와 관련된 영역
    - 설치된 센서 버전
    - 클라우드에 대한 센서 연결 상태
    - 센서가 클라우드에 연결되어 있는 것을 마지막으로 감지한 시간

## <a name="manage-onboarded-sensors"></a>온보딩 센서 관리

센서와 관련된 관리 작업에는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 사용합니다.

온보딩된 센서는 **사이트 및 센서** 페이지에서 볼 수 있습니다. 이 페이지에서 센서 정보를 편집할 수도 있습니다.

### <a name="export-sensor-details"></a>센서 세부 정보 내보내기

온보딩된 센서 정보를 내보내려면 **사이트 및 센서** 페이지 맨 위에 있는 **내보내기** 아이콘을 선택합니다.

:::image type="content" source="media/how-to-manage-sensors-on-the-cloud/export-sensors.png" alt-text="내보내기 단추를 선택하여 센서 정보를 내보냅니다.":::

### <a name="edit-sensor-zone-details"></a>센서 영역 세부 정보 편집

**사이트 및 센서** 편집 옵션을 사용하여 센서 이름 및 영역을 편집할 수 있습니다.

**편집하려면:**

1. 편집하려는 센서의 **줄임표**( **...** )를 선택합니다.

1. **편집** 을 선택합니다.

1. 센서 영역을 업데이트하거나 새 영역을 만듭니다.

### <a name="delete-a-sensor"></a>센서 삭제

클라우드 연결 센서를 삭제하면 정보가 IoT 허브로 전송되지 않습니다. 더 이상 작업하지 않는 경우 로컬에서 연결된 센서를 삭제합니다.

**센서를 삭제하려면:**

1. 삭제하려는 센서의 줄임표( **...** )를 선택합니다.

1. **센서 삭제** 를 선택합니다.

### <a name="reactivate-a-sensor"></a>센서 다시 활성화 

다음을 수행하려는 경우 센서를 다시 활성화해야 할 수 있습니다.

- **로컬 관리 모드 대신 클라우드 연결 모드에서 작업**: 다시 활성화 후 센서 감지가 센서에 표시되고 새로 감지된 경고 정보가 IoT 허브를 통해 전달됩니다. 이 정보는 Microsoft Sentinel과 같은 다른 Azure 서비스와 공유할 수 있습니다.

- **클라우드 연결 모드 대신 로컬 관리 모드에서 작업**: 다시 활성화 후 센서 감지 정보가 센서에만 표시됩니다.

- **센서를 새 IoT 허브에 연결**: 이렇게 하려면 새 허브에 센서를 다시 등록한 다음 새 활성화 파일을 다운로드합니다.

**센서를 다시 활성화하려면:**

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) **사이트 및 센서** 페이지로 이동합니다.

1. 새 활성화 파일을 업로드하려는 센서를 선택합니다.

1. **줄임표**( **...** )를 선택한 다음, **센서 삭제** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/delete-a-sensor.png" alt-text="줄임표를 선택한 다음, 센서를 삭제합니다.":::

1. 시작 페이지에서 **센서 온보딩** 을 선택하여 새 모드 또는 새 IoT 허브로 [센서를 다시 온보딩](#onboard-sensors)합니다.

1. 활성화 파일을 다운로드합니다.

1. Defender for IoT 센서 콘솔에 로그인합니다.

1. 센서 콘솔에서 **시스템 설정** 을 선택한 다음 **다시 활성화** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="활성화 파일을 업로드하여 센서를 다시 활성화합니다.":::

1. **업로드** 를 선택하고 온보드 센서 페이지에서 저장한 파일을 선택합니다.

1. **활성화** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

[센서 활성화 및 설정](how-to-activate-and-set-up-your-sensor.md)
