---
title: 서비스 커넥터란?
description: 서비스 커넥터를 사용하는 일반적인 사용 사례 시나리오를 더 잘 이해하고 서비스 커넥터의 주요 이점에 대해 알아봅니다.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ca822d14f225372f40d14165557677dff66f6ff8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053267"
---
# <a name="what-is-service-connector"></a>서비스 커넥터란?

서비스 커넥터 서비스를 사용하면 Azure 계산 서비스를 다른 지원 서비스에 쉽게 연결할 수 있습니다. 이 서비스는 관리 평면에서 계산 서비스와 대상 지원 서비스 간의 네트워크 설정 및 연결 정보(예: 환경 변수 생성)를 구성합니다. 개발자는 연결 정보를 사용하는 기본 SDK 또는 라이브러리를 사용하여 대상 지원 서비스에 대한 데이터 평면 작업을 수행합니다. 

이 문서에서는 서비스 커넥터 서비스의 개요를 제공합니다.

## <a name="what-is-service-connector-used-for"></a>서비스 커넥터는 왜 사용하나요?

Azure 계산 서비스에서 실행되고 지원 서비스를 필요로 하는 모든 애플리케이션은 서비스 커넥터를 사용할 수 있습니다. 서비스 커넥터를 사용하여 서비스 간 연결 환경을 간소화할 수 있는 몇 가지 예를 나열합니다.

* **WebApp + DB:** 서비스 커넥터를 사용하여 PostgreSQL, MySQL SQL DB 또는 Cosmos DB를 App Service에 연결합니다.  
* **WebApp + 스토리지:** 서비스 커넥터를 사용하여 Azure Storage 계정에 연결하고 App Service에서 기본 스토리지 제품을 쉽게 사용합니다.
* **Spring Cloud + 데이터베이스:** 서비스 커넥터를 사용하여 PostgreSQL, MySQL, SQL DB 또는 Cosmos DB를 Spring Cloud 애플리케이션에 연결합니다.
* **Spring Cloud + Apache Kafka:** 서비스 커넥터를 사용하면 Spring Cloud 애플리케이션을 Confluent Cloud의 Apache Kafka에 연결할 수 있습니다.

지원되는 서비스 및 애플리케이션 패턴을 자세히 보려면 [서비스 커넥터에서 지원되는 서비스](#what-services-are-supported-in-service-connector)를 참조하세요.

## <a name="what-are-the-benefits-using-service-connector"></a>서비스 커넥터를 사용하는 이점은 무엇인가요?

**단일 명령이나 몇 번의 클릭 만으로 대상 지원 서비스에 연결합니다.**

서비스 커넥터는 사용 편의성을 위해 설계되었습니다. 대상 서비스 인스턴스, 계산 서비스와 대상 서비스 간의 인증 형식, 애플리케이션 클라이언트 형식 등의 세 가지 필수 매개 변수를 요청하여 연결을 만듭니다. 개발자는 Azure Connection CLI 또는 단계별 Azure Portal 환경을 사용하여 연결을 쉽게 만들 수 있습니다.

**연결 상태를 사용하여 연결 문제를 모니터링하거나 식별합니다.**

일단 서비스 연결이 생성됩니다. 개발자는 연결 상태의 유효성을 검사하고 확인할 수 있습니다. 서비스 커넥터는 끊어진 연결을 수정하는 작업을 제안할 수 있습니다.

## <a name="what-services-are-supported-in-service-connector"></a>서비스 커넥터에서 지원 되는 서비스는 무엇인가요?

> [!NOTE]
> 서비스 커넥터는 공개 미리 보기로 제공됩니다. 제품 팀이 목록에서 지원되는 서비스 유형을 더 추가하 고 있습니다.

**컴퓨팅 서비스:**

* Azure App Service
* Azure Spring Cloud

**대상 서비스:**

* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure Storage(Blob, 큐, 파일 및 테이블 스토리지)
* Azure Key Vault
* Azure SignalR Service
* Confluent Cloud의 Apache Kafka

## <a name="how-to-use-service-connector"></a>서비스 커넥터를 사용하는 방법

Azure 애플리케이션에 대해 서비스 커넥터를 사용하는 두 가지 주요 방법이 있습니다.

* **Azure Connection CLI:** Azure CLI에서 연결 명령 그룹을 사용하여 서비스 간 연결을 만들고, 나열하고, 유효성을 검사하고, 삭제합니다.
* **Azure Portal에서의 서비스 커넥터 환경:** 단계별 포털 환경을 사용하여 서비스 간 연결을 만들고 계층 구조 목록을 사용하여 연결을 관리합니다.

## <a name="next-steps"></a>다음 단계

아래 나열된 자습서를 따라 서비스 커넥터로 사용자만의 애플리케이션을 빌드할 수 있습니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure CLI를 사용하는 App Service의 서비스 커넥터](./quickstart-cli-app-service-connection.md)

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하는 App Service의 서비스 커넥터](./quickstart-portal-app-service-connection.md)

> [!div class="nextstepaction"]
> [빠른 시작: Azure CLI를 사용하는 Spring Cloud 서비스의 서비스 커넥터](./quickstart-cli-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하는 Spring Cloud의 서비스 커넥터](./quickstart-portal-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대한 자세한 정보](./concept-service-connector-internals.md)
