---
title: ISO 27001 ASE/SQL 워크로드 청사진 샘플 개요
description: ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플에 대한 개요 및 아키텍처입니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 19a81d873873bec1d4bfa38d4bf39b407a4e7f76
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675006"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플 개요

ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플은 [ISO 27001 Shared Services](../iso27001-shared/index.md) 청사진 샘플에 추가 인프라를 제공합니다.
이 청사진은 고객이 승인 또는 규정 준수 요구 사항이 있는 시나리오에 대한 솔루션을 제공하는 클라우드 기반 아키텍처를 구축하는 데 도움이 됩니다.

ISO 27001 청사진 샘플은 두 가지로, 이 샘플과 [ISO 27001 Shared Services](../iso27001-shared/index.md) 청사진 샘플입니다.

> [!IMPORTANT]
> 이 샘플은 [ISO 27001 Shared Services](../iso27001-shared/index.md) 청사진 샘플에 의해 배포되는 인프라에 종속됩니다. 이것이 먼저 배포되어야 합니다.

## <a name="architecture"></a>Architecture

ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플은 PaaS(Platform as a Service) 기반 웹 환경을 배포합니다. 이 환경은 ISO 27001 표준을 따르는 다수의 웹 애플리케이션, 웹 API 및 SQL Database 인스턴스를 호스팅하는 데 사용할 수 있습니다. 이 청사진 샘플은 [ISO 27001 Shared Services](../iso27001-shared/index.md) 청사진 샘플에 종속됩니다.

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="ISO 27001 ASE/SQL 워크로드 청사진 샘플 디자인" border="false":::

이 환경은 ISO 27001 표준을 기반으로 하는 완전히 모니터링되는 안전한 엔터프라이즈급 워크로드 인프라를 제공하는 데 사용되는 여러 가지 Azure 서비스로 구성됩니다. 이 환경은 다음과 같은 요소로 구성됩니다.

- [Azure 역할](../../../../role-based-access-control/overview.md): 이름이 DevOps이며, 청사진 샘플에 의해 배포된 [Azure App Service Environment](../../../../app-service/environment/intro.md)의 리소스를 배포 및 관리할 수 있는 권한이 있습니다.
- [Azure Policy](../../../policy/overview.md) 정의: 환경에 배포할 수 있는 서비스를 잠그고 모든 PIP(공용 IP 주소) 리소스 생성을 거부합니다.
- 가상 네트워크: 단일 서브넷을 포함하며 기존 [공유 서비스](../iso27001-shared/index.md) 환경으로 다시 피어링되고 모든 트래픽이 [공유 서비스](../iso27001-shared/index.md) 방화벽을 통과하도록 적용합니다. 가상 네트워크는 다음 리소스를 호스팅합니다.
  - 하나 이상의 웹 애플리케이션, 웹 API 또는 함수를 호스팅하는 데 사용할 수 있는 [Azure App Service Environments](../../../../app-service/environment/intro.md)
  - 워크로드 환경에서 실행되는 애플리케이션에 사용되는 비밀을 저장하기 위해, VNet 서비스 엔드포인트를 사용하는 [Azure Key Vault](../../../../key-vault/general/overview.md) 인스턴스
  - 워크로드 환경의 애플리케이션에 사용되는 데이터베이스를 호스팅하기 위해, VNet 서비스 엔드포인트를 사용하는 [Azure SQL Database](../../../../azure-sql/database/sql-database-paas-overview.md) 인스턴스

## <a name="next-steps"></a>다음 단계

ISO 27001 App Service Environment/SQL Database 워크로드 청사진 샘플에 대한 개요 및 아키텍처를 검토했습니다. 이제 다음 문서를 참조하여 컨트롤 매핑 및 이 샘플을 배포하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database 워크로드 청사진 - 컨트롤 매핑](./control-mapping.md)
> [ISO 27001 App Service Environment/SQL Database 워크로드 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
