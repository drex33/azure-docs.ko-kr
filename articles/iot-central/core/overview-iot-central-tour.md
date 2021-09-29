---
title: Azure IoT Central UI 둘러보기 | Microsoft Docs
description: IoT 솔루션을 만들고, 관리하고, 활용하는 데 사용되는 Azure IoT Central UI의 주요 영역을 익힐 수 있습니다.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 187db7eefb019584835bd7f67fdb8d6abd91f45c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083907"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Azure IoT Central UI 둘러보기

이 문서에서는 Azure IoT Central UI를 소개합니다. 이 UI를 사용하여 IoT Central 애플리케이션과 연결된 디바이스를 생성, 관리 및 사용할 수 있습니다.

## <a name="iot-central-homepage"></a>IoT Central 홈페이지

[IoT Central 홈페이지](https://apps.azureiotcentral.com/) 페이지에서는 IoT Central에서 사용할 수 있는 최신 뉴스 및 기능에 대해 자세히 알아보고, 새 애플리케이션을 만들고, 기존 애플리케이션을 살펴보고 시작할 수 있습니다.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central 홈페이지":::

### <a name="create-an-application"></a>애플리케이션 만들기

**빌드** 섹션에서는 업계 관련 IoT Central 템플릿 목록을 찾아보거나, 사용자 지정 앱 템플릿을 사용하여 처음부터 시작할 수 있습니다.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central 빌드 페이지":::

자세한 내용은 [Azure IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 참조하세요.

### <a name="launch-your-application"></a>애플리케이션 시작

앱을 만드는 동안 선택한 URL로 이동하여 IoT Central 애플리케이션을 시작합니다. [IoT Central 앱 관리자](https://apps.azureiotcentral.com/myapps)에서 액세스 권한이 있는 모든 애플리케이션 목록을 볼 수도 있습니다.

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central 앱 관리자":::

## <a name="navigate-your-application"></a>애플리케이션 탐색

IoT 애플리케이션 내부로 이동한 후에는 왼쪽 창을 사용하여 다양한 기능에 액세스합니다. 창 위에 있는 세 개의 선 아이콘을 선택하여 왼쪽 창을 확장하거나 축소할 수 있습니다.

> [!NOTE]
> 왼쪽 창에 표시되는 항목은 사용자 역할에 따라 다릅니다. [사용자 및 역할 관리](howto-manage-users-roles.md)에 대해 자세히 알아보세요. 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="왼쪽 창":::

  :::column-end:::
  :::column span="2":::
     **대시보드** 는 모든 애플리케이션 및 개인 대시보드를 표시합니다. 
     
     **디바이스** 에서는 모든 디바이스를 관리할 수 있습니다.

     **디바이스 그룹** 에서는 쿼리에 지정된 디바이스 컬렉션을 살펴보고 만들 수 있습니다. 디바이스 그룹은 애플리케이션을 통해 대량 작업을 수행하는 데 사용됩니다.

     **규칙** 에서는 디바이스를 모니터링하는 규칙을 만들고 편집할 수 있습니다. 규칙은 디바이스 데이터를 기준으로 평가되며, 사용자 지정 가능한 작업을 트리거합니다.

     **분석** 은 기록 추세를 분석하고 디바이스에서 다양한 원격 분석의 상관 관계를 지정하는 풍부한 기능을 제공합니다.

     **작업** 에서는 대량 작업을 실행하여 디바이스를 대규모로 관리할 수 있습니다.

     **디바이스 템플릿** 에서는 애플리케이션에 연결하는 디바이스의 특성을 만들고 관리합니다.

     **데이터 내보내기** 에서는 스토리지, 큐 등의 외부 서비스로 연속 내보내기를 구성할 수 있습니다.

     **관리** 에서는 애플리케이션의 설정, 사용자 지정, 청구, 사용자 및 역할을 관리할 수 있습니다.

     **내 앱** 에서는 IoT Central 앱 관리자로 다시 이동할 수 있습니다.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>검색, 도움말, 테마 및 지원

모든 페이지에 상단 메뉴가 나타납니다.

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central 도구 모음":::

* 디바이스를 검색하려면 **검색** 값을 입력합니다.
* UI 언어 또는 테마를 변경하려면 **설정** 아이콘을 선택합니다. [애플리케이션 기본 설정 관리](howto-manage-preferences.md)에 대한 자세한 정보
* 도움말을 보거나 지원을 받으려면 리소스 목록에 대한 **도움말** 드롭다운을 선택합니다. **앱 정보** 링크에서 [애플리케이션에 대한 정보를 가져올](howto-faq.yml#how-do-i-get-information-about-my-application-) 수 있습니다.
* 애플리케이션에서 로그아웃하려면 **계정** 아이콘을 선택합니다.

UI의 밝은 테마 또는 어두운 테마 중에서 선택할 수 있습니다.

> [!NOTE]
> 관리자가 애플리케이션에 대해 사용자 지정 테마를 구성하면 밝은 테마와 어두운 테마 중에서 선택할 수 있는 옵션을 사용할 수 없습니다.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="IoT Central 테마 선택의 스크린샷.":::

### <a name="dashboard"></a>대시보드

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="IoT Central 대시보드의 스크린샷.":::

* **대시보드** 는 IoT Central 애플리케이션에 로그인할 때 표시되는 첫 번째 페이지입니다. 여러 애플리케이션 대시보드를 만들고 사용자 지정할 수 있습니다. [대시보드에 타일 추가](howto-manage-dashboards.md)에 대한 자세한 정보

* 개인 대시보드를 만들어 관심 있는 항목을 모니터링할 수도 있습니다. 자세한 내용은 [Azure IoT Central 개인 대시보드 만들기](howto-manage-dashboards.md) 방법 문서를 참조하세요.

### <a name="devices"></a>디바이스

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="디바이스 페이지의 스크린샷.":::

이 페이지는 IoT Central 애플리케이션의 디바이스를 _디바이스 템플릿_ 별로 보여 줍니다.

* 디바이스 템플릿은 애플리케이션에 연결할 수 있는 디바이스의 유형을 정의합니다.
* 디바이스는 애플리케이션의 실제 또는 시뮬레이션된 디바이스를 나타냅니다.

### <a name="device-groups"></a>디바이스 그룹

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="디바이스 그룹 페이지":::

이 페이지에서는 IoT Central 애플리케이션의 디바이스 그룹을 만들고 볼 수 있습니다. 디바이스 그룹을 사용하여 애플리케이션에서 대량 작업을 수행하거나 데이터를 분석할 수 있습니다. 자세한 내용은 [Azure IoT Central 애플리케이션에서 디바이스 그룹 사용](tutorial-use-device-groups.md) 문서를 참조하세요.

### <a name="rules"></a>규칙
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="규칙 페이지의 스크린샷.":::

이 페이지에서는 디바이스 데이터를 기반으로 규칙을 보고 만들 수 있습니다. 규칙이 실행되면 이메일 보내기 또는 웹후크 호출과 같은 하나 이상의 작업을 트리거할 수 있습니다. 자세한 내용은 [규칙 구성](tutorial-create-telemetry-rules.md) 자습서를 참조하세요.

### <a name="analytics"></a>분석

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="분석 페이지의 스크린샷.":::

분석은 기록 추세를 분석하고 디바이스에서 다양한 원격 분석의 상관 관계를 지정하는 풍부한 기능을 제공합니다. 자세한 내용은 [Azure IoT Central 애플리케이션을 위한 분석 만들기](howto-create-analytics.md) 문서를 참조하세요.

### <a name="jobs"></a>작업

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="작업 페이지":::

이 페이지에서는 디바이스의 대량 디바이스 관리 작업에 사용할 수 있는 작업을 보고 만들 수 있습니다. 디바이스 속성 및 설정을 업데이트하고 디바이스 그룹에 대해 명령을 실행할 수 있습니다. 자세한 내용은 [작업 실행](howto-manage-devices-in-bulk.md) 문서를 참조하세요.

### <a name="device-templates"></a>디바이스 템플릿

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="디바이스 템플릿의 스크린샷.":::

디바이스 템플릿 페이지에서는 애플리케이션의 디바이스 템플릿을 보고 만들 수 있습니다. 자세한 내용은 [Azure IoT Central 애플리케이션에서 새 디바이스 유형 정의](howto-set-up-template.md) 자습서를 참조하세요.

### <a name="data-export"></a>데이터 내보내기

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="데이터 내보내기":::

데이터 내보내기를 사용하면 외부 시스템으로 나가는 데이터 스트림을 설정할 수 있습니다. 자세한 내용은 [Azure IoT Central에서 데이터 내보내기](./howto-export-data.md) 문서를 참조하세요.

### <a name="administration"></a>관리

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="IoT 관리의 스크린샷.":::

관리 페이지에서는 IoT Central 애플리케이션을 구성하고 사용자 지정할 수 있습니다. 여기서 애플리케이션 이름, URL, 테마를 변경하고, 사용자 및 역할을 관리하고, API 토큰을 만들고, 애플리케이션을 내보낼 수 있습니다. 자세한 내용은 [Azure IoT Central 애플리케이션 관리](howto-administer.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central의 개요를 알고 UI 레이아웃에 익숙해졌으므로, 권장되는 다음 단계는 [Azure IoT Central 애플리케이션 만들기](quick-deploy-iot-central.md) 빠른 시작을 완료하는 것입니다.
