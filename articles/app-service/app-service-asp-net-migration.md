---
title: Azure App Service로 .NET 앱 마이그레이션
description: Azure App Service에서 사용할 수 있는 .NET 마이그레이션 리소스를 검색 합니다.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8918c32e03c6e2c27621dd5c05b9abe976ded511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102892"
---
# <a name="net-migration-cases-for-azure-app-service"></a>Azure App Service에 대 한 .NET 마이그레이션 사례

Azure App Service은 온-프레미스 .NET 웹 앱을 신속 하 게 검색 하 고, 준비 상태를 평가 하 고, 콘텐츠 & 지원 되는 구성을 모두 App Service으로 마이그레이션하는 데 사용 하기 쉬운 도구를 제공 합니다.

이러한 도구는 검색, 평가 및 마이그레이션에 초점을 맞춘 여러 종류의 시나리오를 지원 하도록 개발 되었습니다. 다음은 .NET 마이그레이션 도구 및 사용 사례의 목록입니다.

## <a name="migrate-from-multiple-servers-at-scale-preview"></a>여러 서버에서 대규모로 마이그레이션 (미리 보기)

<!-- Intent: discover how to assess and migrate at scale. -->

Azure Migrate ASP.NET 웹 앱에 대 한 대규모, 에이전트 없는 검색 및 평가를 최근에 발표 했습니다. 이제 VMware 환경의 인터넷 정보 서비스 (IIS) 서버에서 실행 되는 ASP.NET 웹 앱을 쉽게 검색 하 고 Azure App Service로 마이그레이션할 수 있도록 평가할 수 있습니다. 평가는 웹 앱 마이그레이션 준비, 마이그레이션 차단기, 수정 지침, 권장 SKU 및 호스팅 비용을 결정 하는 데 도움이 됩니다. 에 대 한 규모의 마이그레이션 리소스는 아래에 있습니다.

### <a name="at-scale-migration-resources"></a>규모에 맞게 마이그레이션 리소스

| 방법 |
|----------------|
| [웹 앱 및 SQL Server 인스턴스 검색](../migrate/how-to-discover-sql-existing-project.md)                              |
| [Azure App Service 평가 만들기](../migrate/how-to-create-azure-app-service-assessment.md)                            |
| [Azure App Service로 마이그레이션할 웹앱을 평가하는 자습서](../migrate/tutorial-assess-webapps.md)                       |
| [Azure Migrate를 사용하여 온-프레미스 서버에서 소프트웨어 인벤토리 검색](../migrate/how-to-discover-applications.md)           |
| **블로그** |
| [Azure Migrate를 사용 하 여 규모에 맞게 ASP.NET 앱 검색 및 평가](https://azure.microsoft.com/blog/discover-and-assess-aspnet-apps-atscale-with-azure-migrate/) |
| **FAQ** |
| [Azure Migrate 검색 및 평가 도구의 Azure App Service 평가](../migrate/concepts-azure-webapps-assessment-calculation.md) |
| **모범 사례** |
| [Azure Migrate 검색 및 평가 도구의 평가 모범 사례](../migrate/best-practices-assessment.md) |
| **비디오** |
| [Azure Migrate를 사용 하 여 ASP.NET 앱 마이그레이션에 대 한 확장 검색 및 평가](https://channel9.msdn.com/Shows/Inside-Azure-for-IT/At-scale-discovery-and-assessment-for-ASPNET-app-migration-with-Azure-Migrate) |

## <a name="migrate-from-an-iis-server"></a>IIS 서버에서 마이그레이션

<!-- Intent: discover how to assess and migrate from a single IIS server  -->

[PowerShell 스크립트](https://github.com/Azure/App-Service-Migration-Assistant/wiki/PowerShell-Scripts) 를 사용 하 여 확장 Azure Migrate의 검색 환경을 통해 검색 된 단일 IIS 서버에서 ASP.NET 웹 앱을 마이그레이션할 수 있습니다 [(다운로드)](https://appmigration.microsoft.com/api/download/psscriptpreview/AppServiceMigrationScripts.zip). [Azure App Service로 마이그레이션하는 방법](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service)에 대 한 비디오를 시청 하세요.

## <a name="aspnet-web-app-migration"></a>웹 앱 마이그레이션 ASP.NET
<!-- Intent: migrate a single web app -->

App Service Migration Assistant를 사용 하 여 [독립 실행형 온-프레미스 ASP.NET 웹 앱을 Azure App Service로 마이그레이션할](https://www.youtube.com/watch?v=9LBUmkUhmXU)수 있습니다. App Service Migration Assistant는 간단하고 빠른 무료 솔루션을 통해 클라우드로의 과정을 간소화하여 온-프레미스에서 클라우드로 애플리케이션을 마이그레이션하도록 설계되었습니다. Migration assistant 도구에 대 한 자세한 내용은 [FAQ](https://github.com/Azure/App-Service-Migration-Assistant/wiki)를 참조 하십시오.

## <a name="containerize-an-aspnet-web-app"></a>ASP.NET 웹 앱 컨테이너 화

일부 .NET Framework 웹 응용 프로그램에는 Azure App Service에서 사용할 수 없는 라이브러리 및 기타 기능에 대 한 종속성이 있을 수 있습니다. 이러한 앱은 전역 어셈블리 캐시의 다른 구성 요소에 의존할 수 있습니다. 이전에는 가상 컴퓨터 에서만 이러한 응용 프로그램을 실행할 수 있었습니다. 그러나 이제 Azure App Service Windows 컨테이너에서 실행할 수 있습니다.

[앱 컨테이너 화 도구](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) 는 최소한의 변경으로 응용 프로그램을 컨테이너로 다시 패키지 할 수 있습니다. 이 도구는 현재 컨테이너 화 ASP.NET 응용 프로그램 및 Apache Tomcat Java 응용 프로그램을 지원 합니다. 컨테이너 화 및 마이그레이션에 대 한 자세한 내용은 [방법](../migrate/tutorial-app-containerization-aspnet-app-service.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[온-프레미스 웹 응용 프로그램을 Azure App Service로 마이그레이션](/learn/modules/migrate-app-service-migration-assistant/)
