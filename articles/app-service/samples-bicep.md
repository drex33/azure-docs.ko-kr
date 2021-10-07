---
title: Bicep 샘플
description: 몇 가지 일반적인 App Service 시나리오에 대한 Bicep 샘플을 찾습니다. App Service 배포 또는 관리 작업을 자동화하는 방법을 알아봅니다.
author: seligj95
tags: azure-service-management
ms.topic: sample
ms.date: 8/26/2021
ms.author: jordanselig
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: de66f32afb86da526765016e9b8feca7d5ada337
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059102"
---
# <a name="bicep-files-for-app-service"></a>App Service의 Bicep 파일

다음 표에는 Azure App Service의 Bicep 샘플에 대한 링크가 포함되어 있습니다. Bicep에 대한 빠른 시작과 추가 정보는 [Bicep 문서](/azure/azure-resource-manager/bicep/)를 참조하세요.

App Services 리소스의 Bicep 구문과 속성을 알아보려면 [Microsoft.Web 리소스 종류](/azure/templates/microsoft.web/allversions)를 참조하세요.

| 앱 배포 | Description |
|-|-|
| [App Service 계획 및 기본 Linux 앱](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-linux) | Linux용으로 구성된 App Service 앱을 배포합니다. |
| [App Service 계획 및 기본 Windows 앱](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-windows) | Windows용으로 구성된 App Service 앱을 배포합니다. |
| [컨테이너를 사용하는 웹 사이트](https://github.com/Azure/bicep/tree/main/docs/examples/101/website-with-container) | Linux용으로 구성된 docker 이미지에서 웹 사이트를 배포합니다. |
| **앱 구성** | **설명** |
| [조건부 로깅을 사용하는 앱](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-conditional-log)| 조건부 로깅을 사용하여 App Service 앱을 배포합니다. |
| [로그 분석 모듈을 사용하는 앱](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-loganalytics-mod)| 로그 분석을 사용하여 App Service 앱을 배포합니다. |
| [지역 VNet 통합이 포함된 앱](https://github.com/Azure/bicep/tree/main/docs/examples/101/app-service-regional-vnet-integration)| 지역 VNet 통합이 활성화된 App Service 앱을 배포합니다. |
|**연결된 리소스를 사용하는 앱**| **설명** |
| [CosmosDB를 사용하는 앱](https://github.com/Azure/bicep/tree/main/docs/examples/101/cosmosdb-webapp)| CosmosDB를 사용하여 Linux에 App Service 앱을 배포합니다. |
| [MySQL을 사용하는 앱](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-managed-mysql)| Azure Database for MySQL로 Windows에 App Service 앱을 배포합니다. |
| [Azure SQL Database의 데이터베이스가 포함된 앱](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-sql-database)| 기본 서비스 수준에서 Azure SQL Database에 App Service 앱과 데이터베이스를 배포합니다. |
| [백 엔드 웹앱에 연결된 앱](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-privateendpoint-vnet-injection)| VNet 삽입 및 Private Endpoint와 함께 안전하게 연결된 두 개의 웹앱(프런트 엔드 및 백 엔드)을 배포합니다. |
| [데이터베이스, 관리 ID 및 모니터링을 사용하는 앱](https://github.com/Azure/bicep/tree/main/docs/examples/301/web-app-managed-identity-sql-db)| 데이터베이스, 관리 ID 및 모니터링을 사용하여 App Service 앱을 배포합니다. |
|**App Service Environment**| **설명** |
| [App Service Environment v2 만들기](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-asev2-create) | 가상 네트워크에 App Service Environment v2를 만듭니다. |
| | |