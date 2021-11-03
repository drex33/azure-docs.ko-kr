---
title: Java 앱을 Azure App Service로 마이그레이션
description: Azure App Service 하는 데 사용할 수 있는 Java 마이그레이션 리소스를 검색 합니다.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f876445673d0237af33f10e3e5b599032ba28bd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102932"
---
# <a name="java-migration-resources-for-azure-app-service"></a>Azure App Service에 대 한 Java 마이그레이션 리소스

Azure App Service은 온-프레미스 웹 서버에 배포 된 웹 앱을 검색 하는 도구를 제공 합니다. 이러한 앱을 준비 하기 위해 평가한 다음 App Service로 마이그레이션할 수 있습니다. 웹 앱 콘텐츠와 지원 되는 구성은 모두 App Service 마이그레이션할 수 있습니다. 이러한 도구는 검색, 평가 및 마이그레이션에 초점을 맞춘 다양 한 시나리오를 지원 하기 위해 개발 되었습니다.

## <a name="java-tomcat-migration-linux"></a>Java Tomcat 마이그레이션 (Linux)

[길잡이를 다운로드](https://azure.microsoft.com/services/app-service/migration-assistant/) 하 여 Apache Tomcat 웹 서버에서 실행 되는 Java 앱을 마이그레이션합니다. Azure Container Registry를 사용 하 여 온-프레미스 Linux Docker 컨테이너를 App Service로 마이그레이션할 수도 있습니다.

| 리소스 |
|-----------|
| **방법** |
| [Java 앱 서비스-마이그레이션-길잡이 Wiki](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information) |
| [Azure/App Service-마이그레이션-길잡이 Wiki](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Linux-Notes) |
| **비디오** |
|[마이그레이션 시스템을 사용 하 여 Java 앱 가리키기 및 Azure App Service로 마이그레이션](https://www.youtube.com/watch?v=Mpxa0KE0X9k) |

## <a name="standalone-tomcat-web-app-migration-windows-os"></a>독립 실행형 Tomcat 웹 앱 마이그레이션 (Windows OS)

이 [미리 보기 도구](https://azure.microsoft.com/services/app-service/migration-assistant/) 를 다운로드 하 여 Apache Tomcat의 Java 웹 앱을 Windows App Service으로 마이그레이션합니다. 자세한 내용은 [비디오](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service) 및 [방법](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information)을 참조 하세요.

## <a name="containerize-standalone-tomcat-web-app"></a>컨테이너 화 독립 실행형 Tomcat 웹 앱

앱 컨테이너 화은 코드를 최소한으로 변경 하 여 응용 프로그램을 컨테이너로 다시 패키지 하는 간단한 방법을 제공 합니다. 이 도구는 현재 컨테이너 화 ASP.NET 및 Apache Tomcat Java 웹 응용 프로그램을 지원 합니다. 자세한 내용은 [블로그](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) 및 [방법](../migrate/tutorial-app-containerization-java-app-service.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Tomcat 애플리케이션을 Azure App Service의 Tomcat으로 마이그레이션](/azure/developer/java/migration/migrate-tomcat-to-tomcat-app-service)
