---
title: Azure Spring Cloud 마이크로 서비스 앱용 Java 및 기본 OS
description: Azure Spring Cloud 마이크로 서비스 앱의 Java 및 기본 운영 체제를 정상으로 유지 관리하기 위한 원칙
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 1f8ef8ded5dc86af6ab8dc8b3344fae86dc8cdd7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258034"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>스프링 마이크로서비스 앱용 Java 및 기본 OS

**이 문서는 다음에 적용됩니다.** ✔️ Java

다음은 Spring 마이크로 서비스 앱의 Java 및 기본 운영 체제를 정상으로 유지 관리하기 위한 원칙입니다.

## <a name="principles-for-healthy-java-and-base-os"></a>정상적인 Java 및 기본 OS의 원칙

* 기본 | 표준 | 프리미엄 계층 간 기본 운영 체제가 동일해야 합니다.

    * 현재 Azure Spring Cloud의 앱은 Debian 10과 Ubuntu 18.04를 혼합하여 사용합니다.
    * VMware 빌드 서비스는 Ubuntu 18.04를 사용합니다.

* 원본 | JAR과 같은 배포 시작 지점과 관계없이 동일한 기본 운영 체제여야 합니다.

    * 현재 Azure Spring Cloud의 앱은 Debian 10과 Ubuntu 18.04를 혼합하여 사용합니다.

* 기본 운영 체제에는 보안 취약성이 없어야 합니다.

    * Debian 10 기본 운영 체제에는 147개의 오픈 CVE가 있습니다.
    * Ubuntu 18.04 기본 운영 체제에는 132개의 오픈 CVE가 있습니다.

* JRE-헤드리스를 사용해야 합니다.

    * 현재 Azure Spring Cloud의 앱은 JDK를 사용합니다. JRE-헤드리스는 더 작은 이미지입니다.

* Java의 최신 빌드를 사용해야 합니다.

    * 현재 Azure Spring Cloud의 앱은 Java 8 빌드 242를 사용합니다. 이는 오래된 빌드입니다.

Azul Systems는 기본 운영 체제에 대한 변경 내용을 지속적으로 검색하고 마지막으로 빌드된 이미지를 최신 상태로 유지합니다. Azure Spring Cloud는 이미지에 대한 변경 내용을 검색하여 배포 간에 지속적으로 업데이트합니다.

## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud에 대한 FAQ

* 어떤 Java 버전이 지원되나요? 주 버전 및 빌드 번호입니다.

    * LTS 버전 - Java 8 및 11을 지원합니다.
    * 최신 빌드를 사용합니다. 예를 들어 현재는 Java 8 빌드 252와 Java 11 빌드 7이 해당합니다.

* 이러한 Java 런타임은 누가 빌드했나요?

    * Azul Systems입니다.

* 이미지에 대한 기본 운영 체제는 무엇인가요?

    * Ubuntu 20.04 LTS(Focal Fossa)입니다. 앱은 Ubuntu의 최신 LTS 버전을 계속 유지합니다.
    * [Ubuntu 20.04 LTS(Focal Fossa)](http://releases.ubuntu.com/focal/)를 참조하세요.

* 로컬 개발을 위해 지원되는 Java 런타임을 다운로드하려면 어떻게 해야 하나요?

    * [Azure 및 Azure Stack용 JDK 설치](/azure/developer/java/fundamentals/java-jdk-install)

* Java 런타임 수준에서 문제에 대한 지원을 받으려면 어떻게 해야 하나요?

    * Azure 지원을 통해 지원 티켓을 엽니다.

## <a name="default-deployment-on-azure-spring-cloud"></a>Azure Spring Cloud의 기본 배포

> ![기본 배포](media/spring-cloud-principles/spring-cloud-default-deployment.png)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure 스프링 클라우드에서 첫 번째 스프링 부팅 앱 배포](./quickstart.md)
* [Azure 및 Azure Stack에 대한 Java 장기 지원](/azure/developer/java/fundamentals/java-support-on-azure)
