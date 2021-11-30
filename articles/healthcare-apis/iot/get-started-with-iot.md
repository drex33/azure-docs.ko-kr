---
title: IoT 커넥터 시작 - Azure Healthcare API
description: 이 문서에서는 Azure Healthcare API에서 IoT 커넥터를 시작하는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: 65080a0d884d6388f713ea80f4447f4c8d9fba7b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273180"
---
# <a name="get-started-with-the-iot-connector"></a>IOT 커넥터를 시작

이 문서에서는 Azure Healthcare API에서 IoT 커넥터를 시작하기 위한 기본 단계를 [간략하게 설명합니다.](../healthcare-apis-overview.md) 

필수 조건으로 Azure 구독이 필요하며 Azure 리소스 그룹을 만들고 Azure 리소스를 배포할 수 있는 적절한 권한이 부여됩니다. 모든 단계를 수행하거나 기존 환경이 있는 경우 일부 단계를 건너뛸 수 있습니다. 또한 모든 단계를 결합하고 PowerShell, Azure CLI 및 REST API 스크립트에서 완료할 수 있습니다.

[![DICOM으로 시작](media/get-started-with-iot.png)](media/get-started-with-iot.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Azure 구독에서 작업 영역 만들기

Azure Portal 또는 PowerShell, [Azure CLI](../healthcare-apis-quickstart.md) 및 Rest API]를 사용하여 작업 영역을 만들 수 있습니다. [의료 API 샘플](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)에서 스크립트를 찾을 수 있습니다.

> [!NOTE]
> 각 Azure 구독에서 만들 수 있는 작업 영역 수와 IoT 커넥터 인스턴스 수에는 제한이 있습니다.

## <a name="create-the-fhir-service-and-an-event-hub"></a>FHIR 서비스 및 이벤트 허브 만들기

IoT 커넥터는 Azure Event Hub 및 FHIR 서비스에서 작동합니다. 새 [FHIR 서비스를](../fhir/get-started-with-fhir.md) 만들거나 동일하거나 다른 작업 영역에서 기존 서비스를 사용할 수 있습니다. 마찬가지로 새 이벤트 허브를 만들거나 기존 [이벤트 허브를](../../event-hubs/event-hubs-create.md) 사용할 수 있습니다.


## <a name="create-a-iot-connector-in-the-workspace"></a>작업 영역에서 IoT 커넥터 만들기

Azure Portal 또는 PowerShell, [Azure CLI](deploy-iot-connector-in-azure.md) 또는 REST API 사용하여 IoT 커넥터를 만들 수 있습니다. [의료 API 샘플](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)에서 스크립트를 찾을 수 있습니다.

필요에 따라 작업 영역에서 [FHIR 서비스](../fhir/fhir-portal-quickstart.md) 및 [DICOM 서비스를](../dicom/deploy-dicom-services-in-azure.md) 만들 수 있습니다.

## <a name="assign-roles-to-allow-iot-to-access-event-hub"></a>IoT가 이벤트 허브에 액세스할 수 있도록 역할 할당

의도적으로 IoT 커넥터는 시스템 관리 ID를 사용하여 지정된 이벤트 허브에서 데이터를 검색합니다. [Event Hub에서](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#granting-iot-connector-access)IoT 커넥터에 역할을 할당하는 방법에 대한 자세한 내용은 을 참조하십시오.

## <a name="assign-roles-to-allow-iot-to-access-fhir-service"></a>IoT가 FHIR 서비스에 액세스할 수 있도록 역할 할당

IoT 커넥터는 시스템 관리 ID를 사용하여 데이터를 FHIR 저장소에 유지합니다. [FHIR 서비스에서](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#accessing-the-iot-connector-from-the-fhir-service)IoT 커넥터에 역할을 할당하는 방법에 대한 세부 정보를 참조하세요.

## <a name="sending-data-to-the-iot-connector"></a>IoT 커넥터로 데이터 보내기

IoT 커넥터와 연결된 Event Hub로 데이터를 보낼 수 있습니다. FHIR 저장소에 데이터가 표시되지 않으면 IoT 커넥터에 대한 매핑 및 역할 할당을 확인합니다.

## <a name="iot-connector-mappings-data-flow-ml-power-bi-and-teams-notifications"></a>IoT 커넥터 매핑, 데이터 흐름, ML, Power BI 및 Teams 알림

IoT 커넥터 매핑, 데이터 흐름, 기계 학습 서비스, Power BI 및 Teams 알림에 대한 자세한 내용은 [IoT 커넥터](iot-connector-overview.md) 설명서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 커넥터 사용을 시작하는 기본 단계를 설명했습니다. 작업 영역에서 IoT 커넥터를 배포하는 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[Azure Portal IoT 커넥터 배포](deploy-iot-connector-in-azure.md)