---
title: 원본 릴리스와의 차이점
titleSuffix: Azure Digital Twins
description: 새 버전의 Azure Digital Twins에서 변경된 내용 이해
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 09e15469b221ac8c70147ab9e65a9e90af510a37
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528338"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>새로운 Azure Digital Twins란? 원래 버전(2018)과 어떻게 다른가요?

Azure Digital Twins의 첫 번째 공개 미리 보기는 2018년 10월에 릴리스됐습니다. 원래 버전의 핵심 개념은 현재 서비스에 그대로 구현되었지만 서비스 유연성 및 액세스 가능성을 개선하기 위해 많은 인터페이스 및 구현 세부 정보가 변경되었습니다. 이러한 변경 내용은 고객의 피드백에 따라 반영되었습니다.

> [!IMPORTANT]
> 새 서비스의 확장된 기능을 고려하여 원래 Azure Digital Twins 서비스는 사용이 중지되었습니다. 2021년 1월부터 해당 API 및 연결된 데이터를 더 이상 사용할 수 없습니다.

첫 번째 공개 미리 보기 중에 Azure Digital Twins의 첫 번째 버전을 사용한 경우 이 문서의 정보 및 모범 사례를 사용하여 현재 서비스로 작업하고 해당 기능을 활용하는 방법을 알아보세요.

## <a name="differences-by-topic"></a>항목의 차이점

아래 차트에서는 원래 버전의 서비스와 현재 서비스 간에 변경된 개념을 나란히 표시합니다.

| 항목 | 원래 버전 | 현재 버전 |
| --- | --- | --- | --- |
| **모델링**<br>*보다 유연함* | 원래 릴리스는 스마트 공간을 중심으로 설계되었으므로 건물에 대한 기본 제공 어휘와 함께 제공됩니다. | 현재 Azure Digital Twins는 도메인 독립적입니다. 솔루션에 대한 사용자 지정 어휘 및 사용자 지정 모델을 정의하여 보다 유연한 방식으로 더 많은 종류의 환경을 나타낼 수 있습니다.<br><br>[사용자 지정 모델](concepts-models.md)에서 자세히 알아보세요. |
| **토폴로지**<br>*보다 유연함*| 원래 릴리스에서는 스마트 공간에 맞게 조정된 트리 데이터 구조를 지원했습니다. Digital twins는 계층 관계와 연결되었습니다. | 현재 릴리스부터 Digital Twins는 원하는 대로 구성된 임의의 그래프 토폴로지에 연결할 수 있습니다. 이렇게 하면 실제 세계의 복잡한 관계를 보다 유연하게 표현할 수 있습니다.<br><br>[Digital Twins 및 트윈 그래프](concepts-twins-graph.md)에서 자세히 알아보세요. |
| **컴퓨팅**<br>*보다 풍부하고 유연함* | 원래 릴리스에서는 이벤트 및 원격 분석을 처리하는 논리가 JavaScript UDF(사용자 정의 함수)에서 정의되었습니다. UDF를 사용한 디버깅이 제한적입니다. | 현재 릴리스에는 개방형 컴퓨팅 모델이 있습니다. [Azure Functions](../azure-functions/functions-overview.md)와 같은 외부 컴퓨팅 리소스를 연결하여 사용자 지정 논리를 제공합니다. 이렇게 하면 사용자가 선택한 프로그래밍 언어를 사용하고, 제한 없이 사용자 지정 코드 라이브러리에 액세스하고, 외부 서비스에 포함될 수 있는 개발 및 디버깅 리소스를 활용할 수 있습니다.<br><br>Azure 함수를 통한 데이터 흐름에 기반한 엔드투엔드 시나리오를 보려면 [엔드투엔드 솔루션 연결](tutorial-end-to-end.md)을 참조하세요. |
| **IoT Hub를 사용하는 디바이스 관리**<br>*액세스 가능* | 원래 릴리스에서는 Azure Digital Twins 서비스 내부에 있는 [IoT Hub](../iot-hub/about-iot-hub.md)의 인스턴스를 사용하는 디바이스를 관리했습니다. 이 통합 허브는 개발자에게 완전히 액세스할 수 없습니다. | 현재 릴리스에서는 독립적으로 만들어진 IoT Hub 인스턴스(이미 관리하는 모든 디바이스를 포함)를 연결하여 ‘사용자 고유의 IoT Hub를 가져올’ 수 있습니다. 이를 통해 IoT Hub 기능에 대한 모든 권한을 제공하고 장치 관리를 제어할 수 있습니다.<br><br>[IoT Hub에서 원격 분석 수집](how-to-ingest-iot-hub-data.md)에서 자세히 알아보세요. |
| **보안**<br>*추가 표준* | 원래 릴리스에는 인스턴스에 대한 액세스를 관리하는 데 사용할 수 있는 미리 정의된 역할이 있습니다. | 현재 릴리스는 다른 Azure 서비스에서 사용하는 것과 동일한 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md) 백 엔드 서비스와 통합됩니다. 이를 통해 IoT Hub, Azure Functions, Event Grid 등과 같은 솔루션의 다른 Azure 서비스 간에 더 쉽게 인증할 수 있습니다.<br>RBAC를 사용하면 미리 정의된 역할을 계속 사용하거나 사용자 지정 역할을 빌드 및 구성할 수 있습니다.<br><br>[Azure Digital Twins 솔루션에 대한 보안](concepts-security.md)에서 자세히 알아보세요. |
| **확장성**<br>*더 큼* | 원래 릴리스에는 디바이스, 메시지, 그래프 및 배율 단위에 대한 크기 제한이 있었습니다. 구독당 Azure Digital Twins의 인스턴스는 하나만 지원됩니다.  | 현재 릴리스는 확장성이 향상된 새 아키텍처를 사용하며 컴퓨팅 능력이 더 큽니다. 또한 구독당 지역당 10개의 인스턴스를 지원합니다.<br><br>현재 릴리스의 한도에 대한 자세한 내용은 Azure Digital Twins 서비스 제한을 참조하세요. |

## <a name="service-limits"></a>서비스 제한

Azure Digital Twins 제한 목록은 [Azure Digital Twins 서비스 제한](reference-service-limits.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Digital Twins Explorer 시작](quickstart-azure-digital-twins-explorer.md)에서 현재 릴리스 작업에 대해 자세히 알아보세요.

* 또는 [사용자 지정 모델](concepts-models.md)에서 핵심 개념에 대해 알아보세요.