---
title: Azure IoT Central 관리자 가이드
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central의 관리자 역할에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b91a9c451c0160523029cd8688dfa9a91f433b37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085927"
---
# <a name="iot-central-administrator-guide"></a>IoT Central 관리자 가이드

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central 애플리케이션을 관리하는 관리자를 위한 것입니다.

IoT Central의 관리자는 다음을 수행합니다.

- 애플리케이션의 사용자 및 역할을 관리합니다.
- 조직을 만들고 관리 합니다.
- 디바이스 인증 등의 보안 상황을 관리합니다.
- 애플리케이션 설정을 구성합니다.
- 애플리케이션을 업그레이드합니다.
- 애플리케이션을 내보내고 공유합니다.
- 애플리케이션 상태를 모니터링합니다.

## <a name="users-and-roles"></a>사용자 및 역할

IoT Central은 역할 기반 액세스 제어 시스템을 사용하여 애플리케이션 내에서 사용자 권한을 관리합니다. IoT Central에는 관리자, 솔루션 빌더, 운영자에 대한 세 가지 기본 역할이 있습니다. 관리자는 특정 권한 집합을 사용하여 사용자 지정 역할을 만들 수 있습니다. 관리자는 애플리케이션 사용자를 추가하고 사용자에게 역할을 할당하는 작업을 담당합니다.

자세히 알아보려면 [IoT Central 애플리케이션의 사용자 및 역할 관리](howto-manage-users-roles.md)를 참조하세요.

## <a name="organizations"></a>조직

조직에서는 사용자가 IoT Central 응용 프로그램에서 어떤 장치를 볼 수 있는지를 관리 하는 데 사용 하는 계층을 정의할 수 있습니다. 사용자의 역할은 표시 하는 장치에 대 한 권한 및 사용자가 액세스할 수 있는 환경을 결정 합니다.

자세히 알아보려면 [IoT Central 조직 만들기](howto-create-organizations.md)를 참조 하세요.

## <a name="application-security"></a>애플리케이션 보안

IoT Central 애플리케이션에 연결하는 디바이스는 일반적으로 X.509 인증서 또는 SAS(공유 액세스 서명)를 자격 증명으로 사용합니다. 관리자는 디바이스 자격 증명이 파생된 그룹 인증서 또는 키를 관리합니다.

자세히 알아보려면 [X.509 그룹 등록](concepts-get-connected.md#x509-group-enrollment), [SAS 그룹 등록](concepts-get-connected.md#sas-group-enrollment), [X.509 디바이스 인증서를 롤하는 방법](how-to-roll-x509-certificates.md)을 참조하세요.

또한 관리자는 클라이언트 애플리케이션이 IoT Central 애플리케이션을 통해 인증하는 데 사용하는 API 토큰을 만들고 관리할 수 있습니다. 클라이언트 애플리케이션은 REST API를 사용하여 IoT Central과 상호 작용합니다.

데이터 내보내기의 경우 관리자는 [내보내기 대상](howto-export-data.md)에 대 한 연결을 보호 하도록 [관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 구성할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [관리 id 구성 (Azure Portal)](howto-manage-iot-central-from-portal.md#configure-a-managed-identity)
- [관리 id 구성 (REST API)](howto-manage-iot-central-with-rest-api.md)
- [관리 id 구성 (Azure CLI)](howto-manage-iot-central-from-cli.md#configure-a-managed-identity)

## <a name="configure-an-application"></a>애플리케이션 구성

관리자는 IoT Central 애플리케이션의 동작과 모양을 구성할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [애플리케이션 이름 및 URL 변경](howto-administer.md#change-application-name-and-url)
- [UI 사용자 지정](howto-customize-ui.md)
- [다양한 가격 책정 플랜으로 애플리케이션 이동](howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)
- [파일 업로드 구성](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>애플리케이션 내보내기

관리자는 다음을 수행할 수 있습니다.

- 애플리케이션의 복제본이 필요한 경우 애플리케이션의 복사본을 만듭니다. 예를 들어 테스트용 복제본이 필요할 수 있습니다.
- 여러 복사본을 만들 계획인 경우 기존 애플리케이션에서 애플리케이션 템플릿을 만듭니다.

자세한 내용은 [사용자 지정 애플리케이션 템플릿 만들기 및 사용](howto-create-iot-central-application.md#create-and-use-a-custom-application-template)을 참조하세요.

## <a name="migrate-to-a-new-version"></a>새 버전으로 마이그레이션

관리자는 애플리케이션을 최신 버전으로 마이그레이션할 수 있습니다. 현재 새로 만든 애플리케이션은 V3 애플리케이션입니다. 관리자가 V2 애플리케이션을 V3 애플리케이션으로 마이그레이션해야 할 수 있습니다.

자세히 알아보려면 [V2 IoT Central 애플리케이션을 V3로 마이그레이션](howto-migrate.md)을 참조하세요.

## <a name="monitor-application-health"></a>애플리케이션 상태 모니터링

관리자는 IoT Central 메트릭을 사용하여 연결된 디바이스의 상태와 실행 중인 데이터 내보내기의 상태를 평가할 수 있습니다.

메트릭을 보기 위해 관리자가 Azure Portal, REST API, PowerShell 또는 Azure CLI 쿼리의 차트를 사용할 수 있습니다.

자세한 내용은 [애플리케이션 상태 모니터링](howto-manage-iot-central-from-portal.md#monitor-application-health)을 참조하세요.

## <a name="monitor-connected-iot-edge-devices"></a>연결된 IoT Edge 디바이스 모니터링

Azure Monitor 및 기본 제공 메트릭 통합을 사용하여 IoT Edge 집합을 원격으로 모니터링하는 방법을 알아보려면 [메트릭 수집 및 전송](../../iot-edge/how-to-collect-and-transport-metrics.md)을 참조하세요.

## <a name="tools"></a>도구

관리자 권한으로 사용하는 대부분의 도구는 각 IoT Central 애플리케이션의 **관리** 섹션에서 사용할 수 있습니다. 또한 다음 도구를 사용하여 몇 가지 관리 작업을 완료할 수 있습니다.

- [Azure 명령줄](howto-manage-iot-central-from-cli.md)
- [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 애플리케이션을 관리하는 방법을 배웠으므로 다음 단계로 Azure IoT Central에서 [사용자 및 역할 관리](howto-manage-users-roles.md)에 대해 알아보는 것을 제안합니다.
