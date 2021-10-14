---
title: Azure Virtual Machines에서 Oracle WebLogic Server를 실행하는 솔루션에는 무엇이 있습니까
description: Microsoft Azure Virtual Machines에서 Oracle WebLogic Server를 실행하는 방법을 알아봅니다.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/23/2021
ms.author: rezar
ms.custom:
- devx-track-java
- devx-track-javaee
- devx-track-javaee-wls
- devx-track-javaee-wls-vm
ms.openlocfilehash: 93af58494e4492d7871e530e596b171a7089e521
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005808"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 Oracle WebLogic Server를 실행하는 솔루션에는 무엇이 있습니까?

**적용 대상:** :heavy_check_mark: Linux VM 

이 페이지에서는 Azure Virtual Machines에서 Oracle WebLogic 서버(WLS)를 실행하기 위한 솔루션에 대해 설명합니다. 이러한 솔루션은 Oracle과 Microsoft에서 공동으로 개발하고 지원합니다.

Azure Kubernetes Service에서 WLS를 실행할 수도 있습니다. 이에 대한 해결 방법은 [Microsoft 문서](./weblogic-aks.md)에 설명되어 있습니다.

WLS는 전 세계에서 중요 업무용 엔터프라이즈 Java 애플리케이션 중 일부를 실행하는 최고의 Java 애플리케이션 서버입니다. WLS는 Oracle 소프트웨어 제품군의 미들웨어 토대를 형성합니다. Oracle과 Microsoft는 WLS 고객이 Azure에서 워크 로드를 최고의 클라우드 플랫폼으로 선택하고 유연하게 실행할 수 있도록 최선을 다하고 있습니다.

Azure WLS 솔루션은 Java 애플리케이션을 Azure virtual machines로 가능한 쉽게 마이그레이션할 수 있도록 하기 위한 것입니다. 솔루션은 이를 가장 일반적인 클라우드 프로비전 시나리오에 대해 배포된 리소스를 생성하여 수행합니다. 이 솔루션은 가상 네트워크, 스토리지, Java, WLS 및 Linux 리소스를 자동으로 프로비전합니다. 최소한의 노력으로 WebLogic 서버가 설치됩니다. 솔루션은 네트워크 보안 그룹을 사용하여 보안을 설정하 고, Azure 앱 Gateway를 사용하여 부하를 분산하고, Azure Active Directory로 인증하고, ELK를 사용하여 중앙 집중식 로깅 및 Oracle 일관성을 사용하여 분산 캐싱한다. Azure PostgreSQL, Azure SQL 및 Oracle 클라우드 또는 Azure의 Oracle DB을 포함하여 기존 데이터베이스에 자동으로 연결할 수도 있습니다. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Azure Portal를 사용하여 Azure에서 WebLogic Server를 배포할 수 있습니다":::

여러 가지 시나리오를 충족하는 데 사용할 수 있는 네 가지 제품([관리 서버가 없는 단일 노드](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [관리 서버가 있는 단일 노드](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [클러스터](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) 및 [동적 클러스터](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster))이 있습니다. 제품은 무료로 사용 가능합니다. 이러한 제품에 대해서는 아래에 설명 및 링크가 제공되어 있습니다.

_이러한 제품은 사용자 라이선스가 필요합니다_. Oracle을 사용하여 적절한 라이선스를 이미 확보했으며 Azure에서 제품을 실행할 수 있는 적절한 라이선스를 보유하고 있다고 가정합니다.

제품은 기본 이미지 (Oracle Linux 7.6의 WebLogic 서버 14 및 JDK 11)를 통해 다양한 운영 체제, Java 및 WLS 버전을 지원합니다. 이러한 기본 이미지는 Azure에서 직접 사용할 수도 있습니다. 기본 이미지는 복잡하고 사용자 지정된 Azure 배포가 필요한 고객에게 적합합니다. 현재 기본 이미지 세트는 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)에서 사용할 수 있습니다.

_이러한 제품을 개발하는 엔지니어링 팀과 마이그레이션 시나리오를 긴밀하게 작업하는 데 관심이 있으신 경우 [마켓플레이스 제품 개요 페이지](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)에서 [연락처](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) 단추_ 를 선택합니다. 프로그램 관리자, 설계자 및 엔지니어가 곧 연락하여 긴밀한 협업을 시작할 것입니다. 제품 개발이 활발히 진행되는 동안에는 마이그레이션 시나리오에 대한 협업 기회가 무료로 제공됩니다.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server 단일 노드

[이 제품](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)은 단일 가상 머신을 프로비저닝하고 거기에 WLS을 설치합니다. 도메인을 만들거나 관리 서버를 시작하지 않습니다. 단일 노드 제품은 고도로 사용자 지정된 도메인 구성이 있는 시나리오에 유용합니다.

## <a name="oracle-weblogic-server-with-admin-server"></a>관리 서버가 있는 Oracle WebLogic Server

[이 제품](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)은 단일 가상 머신을 프로비저닝하고 거기에 WLS을 설치합니다. 도메인을 만들고 관리 서버를 시작합니다. 도메인을 관리하고 애플리케이션 배포를 바로 시작할 수 있습니다.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server 클러스터

[이 제품](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)은 항상 사용 가능한 WLS 가상 머신 클러스터를 만듭니다. 관리 서버 및 관리되는 모든 서버는 기본적으로 시작됩니다. 클러스터를 관리하고 항상 사용 가능한 애플리케이션을 바로 시작할 수 있습니다.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server 동적 클러스터

[이 제품](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)은 항상 사용 가능하고 확장성이 있는 WLS 가상 머신 동적 클러스터를 만듭니다. 관리 서버 및 관리되는 모든 서버는 기본적으로 시작됩니다.

이 솔루션을 사용하면 비교적 편리하게 프로덕션이 준비된 다양한 배포 아키텍처를 사용할 수 있습니다. 비즈니스 애플리케이션 개발에 집중하여 가능한 가장 생산적인 방법으로 대부분의 마이그레이션 사례를 충족할 수 있습니다.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Azure에서 복잡한 WebLogic 서버 배포가 사용됩니다":::

솔루션에 의해 자동으로 프로비전되는 것 외에도 고객은 완전히 유연하게 추가 배포를 사용자 지정할 수 있습니다. 뿐만 아니라 고객이 더 많은 Azure 리소스를 통합할 수 있는 배포 애플리케이션일 것 입니다. 고객은 추가적인 솔루션 향상에 대해 [개발 팀에 연락](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)하고 피드백을 제공하는 것이 장려됩니다.

## <a name="next-steps"></a>다음 단계

Azure에서 제공되는 제품을 살펴보세요.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 단일 노드](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [관리 서버가 있는 Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 클러스터](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server 동적 클러스터](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
