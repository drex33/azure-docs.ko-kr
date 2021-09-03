---
title: Azure 독일에서 글로벌 Azure로 Azure IoT 리소스 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure IoT 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 6e480ef9c12bdf425a41f567f1941d3f1a654b08
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122566179"
---
# <a name="migrate-iot-resources-to-global-azure"></a>IoT 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 독일에서 글로벌 Azure로 Azure IoT 리소스를 마이그레이션하는 데 도움이 되는 정보가 있습니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB 데이터 마이그레이션 도구를 사용하여 Azure Cosmos DB로 데이터를 마이그레이션할 수 있습니다. Azure Cosmos DB 데이터 마이그레이션 도구는 다양한 원본에서 Azure Cosmos DB로 데이터를 가져오는 오픈 소스 솔루션입니다.

Azure Cosmos DB 데이터 마이그레이션 도구는 그래픽 인터페이스 도구 또는 명령줄 도구로 사용할 수 있습니다. 소스 코드는 [Azure Cosmos DB 데이터 마이그레이션 도구](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub 리포지토리에서 사용할 수 있습니다. [컴파일된 버전의 도구](https://www.microsoft.com/download/details.aspx?id=46436)는 Microsoft 다운로드 센터에서 사용할 수 있습니다.

Azure Cosmos DB 리소스를 마이그레이션하려면 다음 단계를 완료하는 것이 좋습니다.

1. 애플리케이션 가동 시간 요구 사항 및 계정 구성을 검토하여 최상의 실행 계획을 결정합니다.
1. 데이터 마이그레이션 도구를 실행하여 Azure 독일에서 새 지역으로 계정 구성을 복제합니다.
1. 유지 관리 기간을 사용할 수 있는 경우 데이터 마이그레이션 도구를 실행하여 원본에서 대상으로 데이터를 복사합니다.
1. 유지 관리 기간을 사용할 수 없는 경우 도구를 실행하여 원본에서 대상으로 데이터를 복사한 후 다음 단계를 완료하세요.
   1. 구성 기반 접근 방식을 사용하여 애플리케이션에서 읽기/쓰기를 변경합니다.
   1. 최초 동기화를 완료합니다.
   1. 증분 동기화를 설정하고 변경 피드를 파악합니다.
   1. 새 계정에 대한 읽기를 가리키고 애플리케이션의 유효성을 검사합니다.
   1. 이전 계정에 대한 쓰기를 중지하고 변경 피드가 발생되었는지 확인한 다음 새 계정에 대한 쓰기를 가리킵니다.
   1. 도구를 중지하고 이전 계정을 삭제합니다.
1. 도구를 실행하여 데이터가 이전 계정과 새 계정 간에 일관성이 있는지 확인합니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure Cosmos DB 소개](../cosmos-db/introduction.md)를 읽습니다.
- [Azure Cosmos DB로 데이터 가져오기](../cosmos-db/import-data.md) 방법을 알아봅니다.

## <a name="functions"></a>Functions

Azure 독일에서 글로벌 Azure로의 Azure Functions 리소스 마이그레이션은 현재 지원되지 않습니다. Resource Manager 템플릿을 내보내고 위치를 변경한 다음 대상 지역에 다시 배포하는 것이 좋습니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Azure Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

추가 정보는 다음 항목을 참조하세요.

- [함수 자습서](../azure-functions/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md) 방법을 알아보거나 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요를 참조하세요.
- [Azure Functions 개요](../azure-functions/functions-overview.md)를 검토합니다.
- [Azure 위치 개요](https://azure.microsoft.com/global-infrastructure/locations/)를 참조하세요.
- [템플릿 재배포](../azure-resource-manager/templates/deploy-powershell.md) 방법에 대해 알아봅니다.

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs의 한 인스턴스에서 다른 인스턴스로 설정을 마이그레이션하려면 모든 등록 토큰 및 태그를 내보낸 다음 가져옵니다.

1. Azure Blob Storage 컨테이너로 [기존 알림 허브 등록을 내보냅니다](/previous-versions/azure/azure-services/dn790624(v=azure.100)).
1. 대상 환경에서 새 알림 허브를 만듭니다.
1. Blob Storage에서 새 알림 허브로 [등록 토큰을 가져옵니다](/previous-versions/azure/azure-services/dn790624(v=azure.100)).

추가 정보는 다음 항목을 참조하세요.

- [Notification Hubs 자습서](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)를 완료하여 지식을 새로 고칩니다.
- [Notification Hubs 개요](../notification-hubs/notification-hubs-push-notification-overview.md)를 검토합니다.

## <a name="iot-hub"></a>IoT Hub

Azure 독일에서 글로벌 Azure로 Azure IoT Hub 인스턴스를 마이그레이션할 수 있지만 마이그레이션이 원활하지 않습니다.

> [!NOTE]
> 이 마이그레이션으로 인해 Azure IoT 애플리케이션에서 가동 중지 시간 및 데이터 손실이 발생할 수 있습니다. 모든 원격 분석 메시지, C2D 명령 및 작업 관련 정보(일정 및 기록)는 마이그레이션되지 않습니다. 새 연결 문자열을 사용하려면 디바이스와 백 엔드 애플리케이션을 다시 구성해야 합니다.

### <a name="step-1-re-create-the-iot-hub"></a>1단계: IoT Hub 다시 만들기

IoT Hub는 기본적으로 복제를 지원하지 않습니다. 그러나 Azure Resource Manager 기능을 사용하여 [리소스 그룹을 템플릿으로 내보내고](../azure-resource-manager/templates/export-template-portal.md) IoT Hub 메타데이터를 내보낼 수 있습니다. 구성된 경로 및 기타 IoT Hub 설정은 내보낸 메타데이터에 포함됩니다. 그런 다음 글로벌 Azure에 템플릿을 다시 배포합니다. 내보낸 JSON의 세부 정보를 보면 Azure Portal에서 IoT Hub를 다시 만드는 것이 더 쉽다는 것을 알 수 있습니다.

### <a name="step-2-migrate-device-identities"></a>2단계: 디바이스 ID 마이그레이션

디바이스 ID를 마이그레이션하려면 다음을 수행합니다.

1. Azure 독일의 원본 테넌트에서 [ExportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) Resource Manager API를 사용하여 모든 디바이스 ID, 디바이스 쌍 및 모듈 쌍(키 포함)을 스토리지 컨테이너로 내보냅니다. Azure 독일 또는 글로벌 Azure에서 스토리지 컨테이너를 사용할 수 있습니다. 생성된 공유 액세스 서명 URI에 충분한 권한이 있는지 확인합니다. 
1. [ImportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) Resource Manager API를 실행하여 스토리지 컨테이너의 모든 디바이스 ID를 글로벌 Azure의 복제된 IoT Hub로 가져옵니다.
1. 새 연결 문자열을 사용하기 시작하려면 디바이스와 백 엔드 서비스를 재구성합니다. 호스트 이름이 **\*.azure-devices.de** 에서 **\*.azure-devices.com** 으로 변경됩니다.  

> [!NOTE]
> 루트 인증 기관은 Azure 독일과 글로벌 Azure에서 다릅니다. IoT Hub 인스턴스와 상호 작용하는 디바이스 및 백 엔드 애플리케이션을 재구성할 때 이를 고려합니다.

추가 정보는 다음 항목을 참조하세요.

- [IoT Hub 대량 ID 내보내기](../iot-hub/iot-hub-bulk-identity-mgmt.md#export-devices) 방법을 알아봅니다.
- [IoT Hub 대량 ID 가져오기](../iot-hub/iot-hub-bulk-identity-mgmt.md#import-devices) 방법을 알아봅니다.
- [Azure IoT Hub 개요](../iot-hub/about-iot-hub.md)를 검토합니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)