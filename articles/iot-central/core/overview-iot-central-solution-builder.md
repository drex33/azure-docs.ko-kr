---
title: Azure IoT Central에 대한 솔루션 빌드 | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서는 IoT Central을 사용하여 통합 솔루션을 빌드하는 데 관한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 371c4b5cbe5c8ea037f76f628b063eaa601808ed
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133541949"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central 솔루션 빌더 가이드

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central을 사용하여 통합 솔루션을 빌드하는 솔루션 빌더용입니다. IoT Central 애플리케이션을 사용하여 디바이스를 관리하고, 디바이스 원격 분석을 분석하고, 다른 백 엔드 서비스와 통합할 수 있습니다.

솔루션 빌더:

- IoT Central 웹 UI에서 대시보드 및 보기를 구성합니다.
- 기본 제공 규칙 및 분석 도구를 사용하여 연결된 디바이스에서 비즈니스 인사이트를 가져옵니다.
- 데이터 내보내기 및 규칙 기능을 사용하여 IoT Central을 다른 백 엔드 서비스와 통합합니다.

## <a name="configure-dashboards-and-views"></a>대시보드 및 보기 구성

IoT Central 애플리케이션에는 운영자가 애플리케이션을 보고 상호 작용하는 데 사용하는 하나 이상의 대시보드가 있을 수 있습니다. 솔루션 빌더는 기본 대시보드를 사용자 지정하고 특수한 대시보드를 만들 수 있습니다.

- 사용자 지정 대시보드의 몇 가지 예를 보려면 [업계 지향적 템플릿](concepts-app-templates.md#industry-focused-templates)을 참조하세요.
- 대시보드에 대한 자세한 정보를 알아보려면 [여러 대시보드 만들기 및 관리](howto-manage-dashboards.md)와 [애플리케이션 대시보드 구성](howto-manage-dashboards.md)을 참조하세요.

디바이스가 IoT Central에 연결되면 해당 디바이스는 디바이스 유형에 대한 디바이스 템플릿과 연결됩니다. 디바이스 템플릿에는 운영자가 개별 디바이스를 관리하는 데 사용하는 사용자 지정 가능한 보기가 있습니다. 솔루션 개발자는 디바이스 유형에 대해 사용 가능한 보기를 만들고 사용자 지정할 수 있습니다. 자세한 정보를 알아보려면 [보기 추가](howto-set-up-template.md#views)를 참조하세요.

## <a name="use-built-in-rules-and-analytics"></a>기본 제공 규칙 및 분석 사용

솔루션 개발자는 사용자 지정 가능한 작업을 실행하는 IoT Central 애플리케이션에 규칙을 추가할 수 있습니다. 규칙은 디바이스에서 들어오는 데이터를 기반으로 조건을 평가하여 작업 실행 시기를 결정합니다. 규칙에 대한 자세한 정보는 다음을 참조하세요.

- [자습서: Azure IoT Central 애플리케이션에서 규칙 생성 및 알림 설정](tutorial-create-telemetry-rules.md)
- [규칙 구성](howto-configure-rules.md)

IoT Central에는 운영자가 연결된 디바이스에서 데이터 흐름을 분석하는 데 사용할 수 있는 기본 제공 분석 기능이 있습니다. 자세한 정보를 알아보려면 [분석을 사용하여 디바이스 데이터를 분석하는 방법](howto-create-analytics.md)을 참조하세요.

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

솔루션 빌더는 IoT Central의 데이터 내보내기 및 규칙 기능을 사용하여 다른 서비스와 통합할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [데이터 내보내기를 사용하여 클라우드 대상으로 IoT 데이터 내보내기](howto-export-data.md)
- [IoT Central에 대한 데이터 변환](howto-transform-data.md)
- [워크플로를 사용하여 Azure IoT Central 애플리케이션을 다른 클라우드 서비스와 통합](howto-configure-rules-advanced.md)
- [Stream Analytics, Azure Functions 및 SendGrid를 사용하여 사용자 지정 규칙으로 Azure IoT Central 확장](howto-create-custom-rules.md)
- [Azure Databricks를 사용하여 사용자 지정 분석으로 Azure IoT Central 확장](howto-create-custom-analytics.md)

IoT Central 애플리케이션에 연결된 IoT Edge 디바이스를 사용하여 [Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/overview.md)와 통합할 수 있습니다. 자세한 내용은 GitHub [Azure Video Analyzer용 Azure IoT Central 게이트웨이 모듈을](https://github.com/iot-for-all/iotc-ava-gateway/blob/main/README.md) 참조하세요.

## <a name="apis"></a>API

IoT Central API를 사용하면 IoT 솔루션의 다른 서비스와 긴밀하게 통합할 수 있습니다. 사용 가능한 API는 *데이터 평면* 또는 *컨트롤 플레인* API로 분류됩니다.

데이터 평면 API를 사용하여 IoT Central 애플리케이션의 엔터티 및 기능에 액세스합니다. 예를 들어 디바이스, 디바이스 템플릿, 사용자 및 역할을 관리합니다. IoT Central REST API 작업은 *데이터 평면* 작업입니다. 자세한 내용은 [IoT Central REST API를 사용하여 사용자 및 역할을 관리하는 방법](howto-manage-users-roles-with-rest-api.md)을 참조하세요.

*컨트롤 플레인* 을 사용하여 Azure 구독에서 IoT Central 관련 리소스를 관리합니다. 컨트롤 플레인 작업에 Azure CLI 및 Resource Manager 템플릿을 사용할 수 있습니다. 예를 들어 Azure CLI를 사용하여 IoT Central 애플리케이션을 만들 수 있습니다. 자세한 내용은 [Azure CLI 에서 IoT Central 관리](howto-manage-iot-central-from-cli.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

IoT Central 사용에 대해 자세히 알아보려면 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)부터 시작하여 빠른 시작을 완료하세요.
