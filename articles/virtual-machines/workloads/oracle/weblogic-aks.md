---
title: Azure Kubernetes Service에서 Oracle WebLogic Server를 실행하는 솔루션에는 무엇이 있습니까?
description: Azure Kubernetes Service에서 Oracle WebLogic Server를 실행하는 방법을 알아봅니다.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.custom:
- devx-track-java
- devx-track-javaee
- devx-track-javaee-wls
- devx-track-javaee-wls-aks
ms.openlocfilehash: 6d87eed9b8f145f997e12ae21a2f2f1484b3497b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002798"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Azure Kubernetes Service에서 Oracle WebLogic Server를 실행하는 솔루션에는 무엇이 있습니까?

**적용 대상:** :heavy_check_mark: Linux VM 

이 페이지에서는 Azure Kubernetes Service (AKS)에서 Oracle WebLogic Server (WLS)를 실행하기 위한 솔루션에 대해 설명합니다. 이러한 솔루션은 Oracle과 Microsoft에서 공동으로 개발하고 지원합니다.

Azure Virtual Machines에서 WebLogic Server를 실행하는 것도 가능합니다. 이에 대한 해결 방법은 [이 Microsoft 문서](./oracle-weblogic.md)에 설명되어 있습니다.

WebLogic Server는 전 세계에서 중요 업무용 엔터프라이즈 Java 애플리케이션 중 일부를 실행하는 최고의 Java 애플리케이션 서버입니다. WebLogic Server는 Oracle 소프트웨어 제품군의 미들웨어 토대를 형성합니다. Oracle과 Microsoft는 WebLogic Server 고객이 Azure에서 워크로드를 최고의 클라우드 플랫폼으로 선택하고 유연하게 실행할 수 있도록 최선을 다하고 있습니다.

## <a name="wls-on-aks-certified-and-supported"></a>AKS의 WLS 인증 및 지원
WebLogic Server는 AKS에서 제대로 실행되도록 Oracle 및 Microsoft에 의해 인증됩니다. AKS 솔루션의 WebLogic Server는 Docker 및 Kubernetes 인프라에서 컨테이너화되고 오케스트레이션된 Java 애플리케이션을 쉽게 실행할 수 있도록 하기 위한 것입니다. 솔루션은 안정성, 확장성, 관리 효율성 및 엔터프라이즈 지원에 중점을 두었습니다.

WebLogic Server 클러스터는 WebLogic Kubernetes Operator(이하 단순히'Operator' 라고 칭함)를 통해 Kubernetes에서 실행되도록 완전히 활성화됩니다. 이 Operator는 표준 Kubernetes Operator 패턴을 따릅니다. 달리 수동 작업을 자동화하고 추가 운영 안정성 기능을 추가 하여 WebLogic 도메인의 관리 및 운영 그리고 Kubernetes에서의 배포를 간소화합니다. 이 Operator는 Oracle WebLogic Server 12c, Oracle Fusion 미들웨어 인프라 12c 이상을 지원합니다. 이 Operator로 WebLogic Server 12.2.1.3 및 12.2.1.4에 대한 공식 Docker 이미지를 테스트 했습니다. Operator에 대한 자세한 내용은 [Oracle의 공식 문서](https://oracle.github.io/weblogic-kubernetes-operator/)를 참조하세요.

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>AKS의 WLS에 대한 참고 자료, 스크립트 및 샘플
AKS의 WebLogic Server 인증 외에도 Oracle과 Microsoft는 AKS의 WebLogic Server를 실행하기 위한 자세한 지침, 스크립트 및 샘플을 공동으로 제공합니다. 이 참고 자료는 [Operator 문서](https://oracle.github.io/weblogic-kubernetes-operator/)의 Azure Kubernetes Service 샘플 섹션에 통합되어 있습니다. 이 참고 자료는 최대한 쉽게 AKS 배포에 대한 프로덕션 WebLogic Server를 만들기 위한 것입니다. 이 참고 자료에서는 Oracle에서 제공하는 공식 WebLogic Server Docker 이미지를 사용합니다. 장애 조치(failover)는 Kubernetes Persistent Volume Claims을 통해 액세스하는 Azure Files을 통해 이루어집니다. Azure Load Balancer는 'LoadBalancer' 유형의 Kubernetes 서비스를 사용하여 프로비저닝할 때 지원됩니다. ACR (Azure Container Registry)은 사용자 지정 Docker 이미지 내에 WLS 도메인을 배포하는 데 지원됩니다. 이 참고 자료를 통해 높은 수준의 구성과 사용자 지정을 수행할 수 있습니다.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="샘플 스크립트를 사용하여 AKS에 WebLogic Server를 배포할 수 있습니다.":::

솔루션에는 WLS 도메인을 AKS에 배포하는 두 가지 방법이 포함되어 있습니다. 도메인은 Kubernetes Persistent Volumes에 직접 배포될 수 있습니다. 이 배포 옵션은 AKS로 마이그레이션하려는 데 여전히 Admin Console이나 WebLogic Scripting Tool (WLST)를 사용하여 WLS를 관리하려는 경우에 유용합니다. 이 옵션을 사용하여 Docker 개발을 채택하지 않고 AKS로 이동할 수도 있습니다. WLS 도메인을 AKS에 배포하는 Kubernetes 기본 방법은 Oracle Container Registry의 공식 WLS 이미지를 기반으로 사용자 지정 Docker 이미지를 빌드하고, 사용자 지정 이미지를 ACR에 게시하며, Operator를 사용하여 AKS에 도메인을 배포하는 것입니다. 또한 솔루션의 이 옵션을 사용하면 배포가 완료된 후 Kubernetes ConfigMaps를 통해 도메인을 업데이트할 수 있습니다.

추가 사용 편의성 및 Azure 서비스 통합은 나중에 Azure Virtual Machines 솔루션의 Oracle WebLogic Server를 미러링하는 Marketplace를 통해 가능합니다.

_이러한 제품은 사용자 라이선스가 필요합니다_. Oracle을 사용하여 적절한 라이선스를 이미 확보했으며 Azure에서 제품을 실행할 수 있는 적절한 라이선스를 보유하고 있다고 가정합니다.

_엔지니어링 팀이 이러한 솔루션을 개발하는 마이그레이션 시나리오에 대해 자세히 알아보려면, [이 짧은 설문 조사](https://aka.ms/wls-on-azure-survey)를 작성하고 연락처 정보를 기재합니다_. 프로그램 관리자, 설계자 및 엔지니어가 곧 연락하여 긴밀한 협업을 시작할 것입니다. 솔루션 개발이 활발히 진행되는 동안에는 마이그레이션 시나리오에 대한 협업 기회가 무료로 제공됩니다.

## <a name="deployment-architectures"></a>배포 아키텍처

Azure Kubernetes Service에서 Oracle WebLogic Server를 실행하기 위한 솔루션은 비교적 쉽게 다양한 프로덕션 준비 배포 아키텍처를 사용하도록 설정합니다.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="AKS에서 복잡한 WebLogic Server 배포가 사용됩니다":::

솔루션이 제공하는 것 외에도 고객은 완전한 유연성을 가지고 추가 배포를 사용자 지정할 수 있습니다. 배포 애플리케이션뿐만 아니라 고객은 더 많은 Azure 리소스를 자신의 배포에 통합할 가능성이 높습니다. 고객은 지속적 솔루션 개선을 위해 설문 조사에 피드백을 제공하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Azure Kubernetes Service에서 Oracle WebLogic Server 실행을 탐색합니다.

> [!div class="nextstepaction"]
> [AKS에서 WLS 실행에 대한 참고 자료, 스크립트 및 샘플](https://oracle.github.io/weblogic-kubernetes-operator/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes Operator](https://oracle.github.io/weblogic-kubernetes-operator/)
