---
title: Azure에서 SAP BusinessObjects BI 플랫폼 배포 | Microsoft Docs
description: Azure에서 SAP BusinessObjects BI 플랫폼 계획, 배포 및 구성
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 946fe1e1700cfa90af6f329dc6786c5801e526a6
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073946"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure의 SAP BusinessObjects BI 플랫폼 계획 및 구현 가이드

## <a name="overview"></a>개요

이 가이드의 목적은 Azure에서 SAP BOBI 플랫폼이라고도 하는, SAP BusinessObjects BI 플랫폼을 계획, 배포 및 구성하는 지침을 제공하는 것입니다. 이 가이드에서는 SAP BOBI 플랫폼과 관련된 일반적인 Azure 서비스 및 기능을 설명합니다. 이 가이드는 가능한 모든 구성 옵션의 전체 목록을 포함하지 않습니다. 일반적인 배포 시나리오에 공통적으로 적용되는 솔루션을 포함합니다.

이 가이드는 표준 SAP BOBI 플랫폼 설치 및 관리 가이드, 운영 체제 또는 데이터베이스 설명서를 대체하는 문서가 아닙니다.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Azure에서 SAP BusinessObjects BI 플랫폼 계획 및 구현

Microsoft Azure는 기업이 긴 조달 주기 없이 애플리케이션을 구축할 수 있도록 컴퓨팅, 스토리지, 네트워킹 등을 비롯한 다양한 서비스를 제공합니다. Azure VM(가상 머신)은 기업의 비즈니스 요구 사항에 따라 SAP NetWeaver 기반 애플리케이션, SAP Hybris, SAP BusinessObjects BI 플랫폼과 같은 여러 SAP 애플리케이션에 대해 확장 가능한 주문형 컴퓨팅 리소스를 배포할 수 있도록 지원합니다. Azure는 또한 프레미스 간 연결을 지원합니다. 이를 통해 기업은 Azure 가상 머신을 온-프레미스 도메인, 프라이빗 클라우드 및 SAP 시스템 환경에 통합할 수 있습니다.

이 문서는 Azure의 SAP BusinessObjects BI 플랫폼 계획 및 구현 고려 사항에 대한 지침을 제공하며, SAP BOBI 설치 및 배포를 위한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP Notes를 보완합니다.

### <a name="architecture-overview"></a>아키텍처 개요

SAP BusinessObjects BI 플랫폼은 단일 Azure 가상 머신에 존재하거나 다양한 구성 요소를 실행하는 여러 Azure 가상 머신의 클러스터로 확장될 수 있는 자체 포함 시스템입니다. SAP BOBI 플랫폼은 6가지 개념적 계층인 클라이언트 계층, 웹 계층, 관리 계층, 스토리지 계층, 처리 계층, 데이터 계층으로 구성됩니다. (각 계층에 대한 자세한 내용은 [SAP BusinessObjects Business Intelligence 플랫폼](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) 도움말 포털의 관리자 가이드를 참조하십시오.) 다음은 각 계층에 대한 대략적인 정보입니다.

- **클라이언트 계층:** 여기에는 BI 플랫폼과 상호 작용하여 다양한 종류의 보고, 분석 및 관리 기능을 제공하는 모든 데스크톱 클라이언트 애플리케이션이 포함됩니다.
- **웹 계층:** JAVA 웹 애플리케이션 서버에 배포된 웹 애플리케이션이 포함됩니다. 웹 애플리케이션은 웹 브라우저를 통해 최종 사용자에게 BI 플랫폼 기능을 제공합니다.
- **관리 계층:** BI 플랫폼을 구성하는 모든 구성 요소를 조정하고 제어합니다. 여기에는 CMS(중앙 관리 서버), 이벤트 서버 및 관련 서비스가 포함됩니다.
- **스토리지 계층:** 문서 및 보고서와 같은 파일 처리를 담당합니다. 사용자가 보고서에 액세스할 때 시스템 리소스를 절약하기 위해 보고서 캐싱도 처리합니다.
- **처리 계층:** 데이터를 분석하고 보고서 및 기타 출력 유형을 생성합니다. 보고서 데이터가 포함된 데이터베이스에 액세스하는 유일한 계층입니다.
- **데이터 계층:** CMS 시스템 데이터베이스와 감사 데이터 저장소를 호스팅하는 데이터베이스 서버로 구성됩니다.

SAP BI 플랫폼은 하나 이상의 호스트에서 실행되는 서버 컬렉션으로 구성됩니다. 규모, 비즈니스 요구 및 환경의 유형에 따라 올바른 배포 전략을 선택하는 것이 중요합니다. 개발 또는 테스트와 같은 소규모 설치의 경우 웹 애플리케이션 서버, 데이터베이스 서버 및 모든 BI 플랫폼 서버에 단일 Azure Virtual Machines를 사용할 수 있습니다. Azure의 DBaaS(Database-as-a-Service) 제품을 사용하는 경우 데이터베이스 서버는 다른 구성 요소와 별도로 실행됩니다. 중대형 설치의 경우 여러 Azure 가상 머신에서 서버를 실행할 수 있습니다.

아래 그림에는 Azure 가상 머신에 SAP BOBI 플랫폼을 대규모로 배포하는 아키텍처가 나와 있습니다. 여기서 각 구성 요소는 분산되고 서비스 중단이 발생할 경우 장애 조치(failover)를 유지할 수 있는 가용성 집합에 배치됩니다.

![Azure의 SAP BusinessObjects BI 플랫폼 아키텍처](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>아키텍처 세부 정보

- 부하 분산 장치

  SAP BOBI 다중 인스턴스 배포에서는 웹 애플리케이션 서버(또는 웹 계층)가 둘 이상의 호스트에서 실행됩니다. 사용자 부하를 웹 서버 간에 균등하게 분산하려면 최종 사용자와 웹 서버 간에 부하 분산 장치를 사용하면 됩니다. Azure에서 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 또는 [Azure Application Gateway](../../../application-gateway/overview.md)를 사용하여 웹 서버에 대한 트래픽을 관리할 수 있습니다.

- 웹 애플리케이션 서버

  웹 서버는 SAP BOBI 플랫폼의 웹 애플리케이션(예: CMC 및 BI Launch Pad)을 호스팅합니다. 웹 서버의 고가용성을 달성하려면 중복성과 부하 분산을 관리하기 위해 둘 이상의 웹 애플리케이션 서버를 배포해야 합니다. Azure에서 이러한 웹 애플리케이션 서버는 가용성을 높이기 위해 가용성 집합 또는 가용성 영역에 배치할 수 있습니다.

  Tomcat은 SAP BI 플랫폼용 기본 웹 애플리케이션입니다. Tomcat에 대한 고가용성을 달성하려면 Azure에서 [고정적인 멤버 자격 인터셉터](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes)를 사용하여 세션 복제가 가능하도록 설정합니다. 그러면 Tomcat 서비스가 중단된 경우에도 사용자가 SAP BI 웹 애플리케이션에 액세스할 수 있습니다.

  > [!Important]
  > 기본적으로 Tomcat은 클러스터링에 멀티캐스트 IP 및 포트를 사용하지만 Azure에서는 지원되지 않습니다(SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- BI 플랫폼 서버

  BI 플랫폼 서버는 SAP BOBI 애플리케이션에 속하는 모든 서비스(관리 계층, 처리 계층, 스토리지 계층)를 포함합니다. 웹 서버가 요청을 받으면 각 BI 플랫폼 서버(특히 클러스터의 모든 CMS 서버)를 감지하고 해당 요청에 대한 부하를 자동으로 분산합니다. BI 플랫폼 호스트 중 하나에 장애가 발생하면 웹 서버는 자동으로 다른 호스트에 요청을 보냅니다.

  BI 플랫폼에 대한 고 가용성 또는 중복성을 달성하려면 적어도 둘 이상의 Azure 가상 머신에 애플리케이션을 배포해야 합니다. 규모에 따라 더 많은 Azure 가상 머신에서 실행되도록 BI 플랫폼을 확장할 수 있습니다.

- FRS(파일 리포지토리 서버)

  파일 리포지토리 서버에는 생성된 모든 보고서 및 기타 BI 문서가 포함됩니다. 다중 인스턴스 배포에서 BI Platform 서버는 여러 가상 머신에서 실행되고 각 VM은 이러한 보고서 및 기타 BI 문서에 액세스할 수 있어야 합니다. 따라서 파일 시스템은 모든 BI 플랫폼 서버에서 공유해야 합니다.

  Azure에서는 파일 리포지토리 서버에 [Azure Premium Files](../../../storage/files/storage-files-introduction.md) 또는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 사용할 수 있습니다. 이 두 Azure 서비스에는 기본 제공 중복성이 있습니다.

  > [!Important]
  > Azure Files용 SMB 프로토콜은 일반 공급되지만 Azure Files용 NFS 프로토콜 지원은 현재 미리 보기로 제공됩니다. 자세한 내용은 [현재 미리 보기로 제공되는 Azure Files에 대한 NFS 4.1 지원](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)을 참조하세요.

- CMS 및 감사 데이터베이스
  
  SAP BOBI 플랫폼에는 시스템 데이터를 저장할 데이터베이스가 필요하며, 이것을 CMS 데이터베이스라고 합니다. 사용자, 서버, 폴더, 문서, 구성 및 인증 세부 정보와 같은 BI 플랫폼 정보를 저장하는 데 사용됩니다.

  Azure는 CMS 데이터베이스 및 감사 데이터베이스에 사용할 수 있는 [MySQL 데이터베이스](https://azure.microsoft.com/services/mysql/) 및 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/) DBaaS(Database-As-A-Service) 제품을 제공합니다. PaaS 제품이기 때문에 고객이 데이터베이스의 운영, 가용성 및 유지 관리에 대해 걱정할 필요가 없습니다. 고객은 비즈니스 요구에 따라 CMS 및 감사 리포지토리용 자체 데이터베이스를 선택할 수도 있습니다.

## <a name="support-matrix"></a>지원 매트릭스

이 섹션에서는 SAP BusinessObjects BI 플랫폼 버전, 운영 체제, Azure의 데이터베이스와 같은 다양한 SAP BOBI 구성 요소의 지원 가능성에 대해 설명합니다.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 플랫폼

Azure IaaS(Infrastructure as a Service)를 사용하면 Azure Compute에서 SAP BusinessObjects BI 플랫폼을 배포하고 구성할 수 있습니다. 다음 버전의 SAP BOBI 플랫폼을 지원합니다.

- SAP BusinessObjects BI 플랫폼 4.3
- SAP BusinessObjects BI 플랫폼 4.2 SP04+
- SAP BusinessObjects BI 플랫폼 4.1 SP05+

SAP BI 플랫폼은 다양한 운영 체제와 데이터베이스에서 실행됩니다. 운영 체제와 데이터베이스 버전 간 SAP BOBI 플랫폼 지원 가능성은 SAP BOBI용 [제품 가용성 매트릭스](https://apps.support.sap.com/sap/support/pam)에서 찾을 수 있습니다.

### <a name="operating-system"></a>운영 체제

Azure는 SAP BusinessObjects BI 플랫폼 배포를 위해 다음과 같은 운영 체제를 지원합니다.

- Microsoft Windows Server
- SLES(SUSE Linux Enterprise Server)
- RHEL(Red Hat Enterprise Linux)
- OL(Oracle Linux)

[SAP BusinessObjects BI 플랫폼용 PAM(제품 가용성 매트릭스)](https://support.sap.com/pam)에 나열된 운영 체제 버전은 호환되는 경우 Azure 인프라에서 실행되도록 지원됩니다.

### <a name="databases"></a>데이터베이스

BI 플랫폼에는 CMS 및 감사 데이터 저장소용 데이터베이스가 필요하며, [SAP 제품 가용성 매트릭스](https://support.sap.com/pam)에 나열된 다음을 비롯한 지원되는 모든 데이터베이스에 설치할 수 있습니다.

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) (Windows의 SAP BOBI 플랫폼에서만 지원되는 데이터베이스)

  안정적인 최신 SQL Server Enterprise Edition을 기반으로 하는 완전 관리형 SQL Server 데이터베이스 엔진입니다. Azure SQL 데이터베이스는 사용자 개입 없이 업그레이드, 패치 적용, 모니터링과 같은 대부분의 데이터베이스 관리 기능을 처리합니다. Azure SQL Database를 사용하면 Azure의 애플리케이션 및 솔루션을 위한 고가용성 고성능 데이터 스토리지 레이어를 만들 수 있습니다. 자세한 내용은 [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) 설명서를 참조하세요.

- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/) (SAP PAM의 MySQL AB에 대해 설명한 것과 동일한 호환성 지침 준수)

  MySQL 커뮤니티 에디션으로 구동되는 관계형 데이터베이스 서비스입니다. 완전 관리형 DBaaS(Database-as-a-Service) 제품이며, 예측 가능한 성능과 동적인 확장성을 통해 중요 업무용 워크로드를 처리할 수 있습니다. 고가용성, 자동 백업, 소프트웨어 패치 적용, 자동 오류 검색, 최대 35일에 대한 특정 시점 복원 기능이 기본 제공되기 때문에 운영 작업이 크게 줄어듭니다. 자세한 내용은 [Azure Database for MySQL](../../../mysql/overview.md) 설명서를 참조하세요.

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle(버전 및 제한 사항은 SAP Note [2039619](https://launchpad.support.sap.com/#/notes/2039619) 참조)

- MaxDB

이 문서에서는 **Azure SQL Database를 사용하여 Windows에 SAP BOBI 플랫폼** 을 배포하고 **Azure Database for MySQL을 사용하여 Linux에 SAP BOBI 플랫폼** 을 배포하는 지침을 설명합니다. 또한 Azure에서 SAP BusinessObjects BI 플랫폼을 실행하는 데 권장되는 접근 방식이기도 합니다.

## <a name="sizing"></a>크기 조정

크기 조정은 애플리케이션을 효율적으로 실행하기 위한 하드웨어 요구 사항을 결정하는 프로세스입니다. SAP BOBI 플랫폼의 경우 [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)라는 SAP 크기 조정 도구를 사용하여 크기 조정을 수행해야 합니다. 이 도구는 입력을 기반으로 SAPS를 제공하기 때문에 SAP의 인증된 Azure 가상 머신 유형에 매핑되어야 합니다. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533)에는 지원되는 SAP 제품 및 Azure VM 유형 목록이 SAPS와 함께 제공됩니다. 크기 조정에 대한 자세한 내용은 [SAP BI 크기 조정 가이드](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)를 참조하세요.

SAP BOBI 플랫폼에 필요한 스토리지를 위해, Azure는 다양한 유형의 [Managed Disks](../../managed-disks-overview.md)를 제공합니다. SAP BOBI 설치 디렉터리의 경우 프리미엄 관리 디스크를 사용하고, 가상 머신에서 실행되는 데이터베이스의 경우 [SAP 워크로드용 DBMS 배포](dbms_guide_general.md)에 제공된 지침을 따르는 것이 좋습니다.

Azure는 SAP BOBI 플랫폼 데이터 계층에 대해 두 가지 DBaaS 제품 즉, [Azure SQL Database](https://azure.microsoft.com/services/sql-database)(Windows에서 실행되는 BI 애플리케이션) 및 [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)(Linux 및 Windows에서 실행되는 BI 애플리케이션)을 지원합니다. 따라서 크기 조정 결과에 따라 필요에 가장 적합한 구매 모델을 선택할 수 있습니다.

> [!Tip]
> 빠른 크기 조정 참조를 위해, SAP BOBI 플랫폼 데이터베이스 계층의 SAPS 결과를 Azure Database-as-a-Service(Azure SQL Database 또는 Azure Database for MySQL)에 매핑하는 경우 800 SAPS = 1 vCPU를 고려합니다.

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 모델 크기 조정

Azure SQL Database는 다음 3가지 구매 모델을 제공합니다.

- vCore 기반

  vCore 수, 메모리 크기, 스토리지의 크기와 속도를 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다. 이 모델은 유연성, 제어 및 투명성을 중요시하는 고객에게 적합합니다.

  vCore 모델에는 세 가지 [서비스 계층 옵션](../../../azure-sql/database/service-tiers-vcore.md#service-tiers)인 범용, 중요 비즈니스용, 하이퍼스케일이 제공됩니다. 서비스 계층은 가용성 및 재해 복구와 관련된 스토리지 아키텍처, 공간, I/O 제한 및 비즈니스 연속성 옵션을 정의합니다. 각 서비스 계층 옵션에 대한 대략적인 정보는 다음과 같습니다.

  1. **범용** 서비스 계층은 비즈니스 워크로드에 가장 적합합니다. 예산 중심의 균형 잡히고 확장 가능한 컴퓨팅 및 스토리지 옵션을 제공합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)을 참조하세요.
  2. **중요 비즈니스용** 서비스 계층은 여러 개의 격리된 복제본을 사용하여 비즈니스 애플리케이션에 장애에 대한 최고의 복원력을 제공하고, 데이터베이스 복제본당 최고의 I/O 성능을 제공합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)을 참조하세요.
  3. **하이퍼스케일** 서비스 계층은 확장성이 뛰어난 스토리지 및 읽기 확장 요구 사항이 있는 비즈니스 워크로드에 가장 적합합니다. 둘 이상의 격리된 데이터베이스 복제본 구성이 가능하여 오류에 대한 더 높은 복원력을 제공합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)을 참조하세요.

- DTU 기반

  DTU 기반 구매 모델은 경량 내지 중량의 데이터베이스 워크로드를 지원하기 위해 세 가지 서비스 계층에서 컴퓨팅, 메모리 및 I/O 리소스를 함께 제공합니다. 각 계층 내의 컴퓨팅 크기는 이러한 리소스의 다양한 조합을 제공하여 추가 스토리지 리소스를 추가할 수 있습니다. 간단하고 미리 구성된 리소스 옵션을 원하는 고객에게 가장 적합합니다.

  DTU 기반 구매 모델의 [서비스 계층](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)은 포함된 스토리지의 고정된 양, 고정된 백업 보존 기간 및 고정 가격을 갖춘 다양한 컴퓨팅 크기로 구분됩니다.

- 서버를 사용하지 않음

  서버리스 모델은 워크로드 요구 사항에 따라 자동으로 컴퓨팅 크기를 조정하고 초당 사용된 컴퓨팅 양에 대한 요금을 청구합니다. 서버리스 컴퓨팅 계층은 스토리지 비용만 청구되는 비활성 기간 동안은 데이터베이스를 자동으로 일시 중지하고 활동이 돌아오면 데이터베이스를 자동으로 다시 시작합니다. 자세한 내용은 [리소스 옵션 및 제한](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.
  
  시간에 따른 평균 컴퓨팅 사용률이 낮은, 일시적이고 예측할 수 없는 사용량에 더 적합합니다. 따라서 이 모델은 비프로덕션 SAP BOBI 배포에 사용할 수 있습니다.

> [!Note]
> SAP BOBI의 경우 vCore 기반 모델을 사용하고 비즈니스 요구 사항에 따라 범용 또는 중요 비즈니스용 서비스 계층을 선택하는 것이 편리합니다.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Azure Database for MySQL의 모델 크기 조정

Azure Database for MySQL에는 세 가지 가격 책정 계층이 제공됩니다. vCore의 컴퓨팅 양, vCore당 메모리, 날짜 저장에 사용되는 스토리지 기술로 구분됩니다. 다음은 옵션에 대한 개략적인 정보이며 다양한 특성에 대한 자세한 내용은 Azure Database for MySQL의 [가격 책정 계층](../../../mysql/concepts-pricing-tiers.md)을 참조하세요.

- Basic

  가벼운 컴퓨팅 및 I/O 성능이 필요한 대상 워크로드에 사용됩니다.

- 범용

  확장 가능한 I/O 처리량을 갖춘 부하 분산된 컴퓨팅 및 메모리가 필요한 대부분의 비즈니스 워크로드에 적합합니다.

- 메모리 최적화

  빠른 트랜잭션 처리와 높은 동시성을 위해 메모리 내 성능이 필요한 고성능 데이터베이스 워크로드에 적합합니다.

> [!Note]
> SAP BOBI의 경우 비즈니스 워크로드를 기반으로 범용 또는 메모리 최적화 가격 책정 계층을 사용하는 것이 편리합니다.

## <a name="azure-resources"></a>Azure 리소스

### <a name="choosing-regions"></a>지역 선택

Azure 지역은 여러 Azure 서비스를 실행하고 호스팅하는 인프라가 포함된 하나의 데이터 센터 또는 여러 데이터 센터의 컬렉션입니다. 이 인프라에는 컴퓨팅 노드 또는 스토리지 노드로 작동하거나 네트워크 기능을 실행하는 다수의 노드가 포함됩니다. 모든 지역이 동일한 서비스를 제공하는 것은 아닙니다.

SAP BI 플랫폼에는 특정 지역에서 사용할 수 없는 특정 VM 유형, 데이터 계층에 대한 Azure Files 또는 Azure NetApp Files 또는 DBaaS (Database as a Service)와 같은 스토리지가 필요할 수 있는 다양한 구성 요소가 포함되어 있습니다. VM 유형, Azure Storage 유형 또는 기타 Azure 서비스에 대한 정확한 정보는 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 사이트에서 찾을 수 있습니다. Azure에서 SAP 시스템을 이미 실행 중인 경우에는 지역이 확인되었을 수 있습니다. 이런 경우, 먼저 필요한 서비스를 해당 지역에서 사용할 수 있는지 조사하여 SAP BI 플랫폼의 아키텍처를 결정해야 합니다.

### <a name="availability-zones"></a>가용성 영역

가용성 영역은 Azure 지역 내에서 물리적으로 떨어진 위치입니다. 각 가용성 영역은 독립적인 전력, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다.

SAP BI 플랫폼의 각 계층에서 고가용성을 달성하기 위해 고가용성 프레임워크를 구현하여 가용 영역 전체에 VM을 분포하면 Azure에서 최고의 SLA를 제공할 수 있습니다. Azure의 가상 머신 SLA는 최신 버전의 [가상 머신 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 확인하세요.

데이터 계층의 경우 Azure DBaaS(Database as a Service) 서비스는 기본적으로 고가용성 프레임워크를 제공합니다. 지역 및 서비스 고유의 고가용성, 중복성 및 복원 기능을 선택하기만 하면, 계획된 중단과 계획되지 않은 중단으로 인한 데이터베이스 가동 중지 시간을 완화할 수 있으며, 사용자가 구성 요소를 추가로 구성할 필요가 없습니다. Azure에서 지원되는 DBaaS 제품의 SLA에 대한 자세한 내용은 [Azure Database for MySQL의 고가용성](../../../mysql/concepts-high-availability.md) 및 [Azure SQL Database의 고가용성](../../../azure-sql/database/high-availability-sla.md)을 참조하세요.

### <a name="availability-sets"></a>가용성 집합

가용성 집합은 배포 시 VM(Virtual Machine) 리소스를 서로 격리하기 위한 논리적 그룹화 기능입니다. Azure의 가용성 집합 내에 배치한 VM은 다수의 물리적 서버, 컴퓨팅 랙, 스토리지 단위 및 네트워크 스위치에서 실행됩니다. 하드웨어 또는 소프트웨어 한 곳에 장애가 발생해도 VM의 하위 집합만 영향을 받고 전체 솔루션은 계속 작동합니다. 따라서 가상 머신을 가용성 집합에 배치하면, Azure Fabric Controller는 VM을 여러 [Fault](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) 및 [Upgrade](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) 도메인에 배포하여, 하나의 장애 도메인 내에서 발생하는 인프라 유지 관리 또는 장애로 인해 모든 VM에 액세스할 수 없는 상황을 방지합니다.

SAP BI 플랫폼에는 다양한 구성 요소가 포함되어 있으며, 아키텍처를 설계하는 동안 각각의 구성 요소가 중단에 대해 복원력이 있는지 확인해야 합니다. 각 구성 요소의 Azure 가상 머신을 가용성 집합 내에 배치하면 달성할 수 있습니다. 하나의 가용성 집합 내에서 서로 다른 VM 제품군의 VM을 혼합하는 경우, 가용성 집합에 특정 VM 유형을 포함할 수 없는 문제가 발생할 수 있으니 유의해야 합니다. 따라서 아키텍처 개요에서 언급했듯이, 웹 애플리케이션 및 SAP BI 플랫폼용 BI 애플리케이션에 대해 별도의 가용성 집합을 사용하는 것이 좋습니다.

또한, Azure 배율 단위 내에서 Azure 가용성 집합이 사용할 수 있는 업데이트 도메인 및 장애 도메인 수는 유한합니다. 따라서 단일 가용성 집합에 VM을 계속 추가하면, 둘 이상의 VM이 결국 동일한 장애 도메인 또는 업데이트 도메인에 추가됩니다. 자세한 내용은 SAP용 Azure Virtual Machines 계획 및 구현 문서의 [Azure 가용성 집합](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) 섹션을 참조하세요.

Azure 가용성 집합의 개념 및 가용성 집합이 장애 도메인 및 업그레이드 도메인과 관련되는 방식을 이해하려면 [가용성 관리](../../availability.md) 문서를 참조하세요.

> [!Important]
> Azure 가용성 영역과 Azure 가용성 집합의 개념은 함께 사용할 수 없습니다. 즉, 한 쌍 또는 여러 VM을 특정 가용성 영역 또는 Azure 가용성 집합에 배포할 수 있지만 두 개에 모두 배포할 수는 없다는 의미입니다.

### <a name="virtual-machines"></a>가상 머신

Azure 가상 머신은 사용자 지정 이미지를 Azure IaaS(Infrastructure as a Service) 인스턴스에 배포할 수 있는 서비스 제품입니다. 이 플랫폼은 웹 애플리케이션 및 연결된 애플리케이션을 호스트, 확장 및 관리하기 위한 주문형 컴퓨팅 및 스토리지를 제공하여 애플리케이션 유지 관리 및 운영을 간소화합니다.

Azure는 모든 애플리케이션 요구 사항에 맞는 다양한 가상 머신을 제공합니다. 단, SAP 워크로드의 경우, Azure는 특히 SAP 워크로드 및 SAP HANA 워크로드에 적합한 다양한 VM 제품군으로 선택 범위를 좁혔습니다. 자세한 내용은 [Azure 배포에 지원되는 SAP 소프트웨어](sap-supported-product-on-azure.md)를 확인하세요.

SAP BI 플랫폼 규모에 따라 요구 사항을 SAP용 Azure 제품에서 지원되는 Azure Virtual Machine에 매핑해야 합니다. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533)은 Windows 및 Linux의 SAP 제품에 대해 지원되는 Azure VM 유형을 확인하기 좋은 출발점입니다. 또한 지원되는 VM 유형을 선택하는 것 외에도 특정 지역에서 해당 VM 유형을 사용할 수 있는지 여부도 확인해야 합니다. VM 유형의 가용성은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 페이지에서 확인할 수 있습니다. 가격 책정 모델을 선택하려면 [SAP 워크로드를 위한 Azure 가상 머신](planning-guide.md#azure-virtual-machines-for-sap-workload)을 참조하세요.

### <a name="storage"></a>스토리지

Azure Storage는 가용성, 보안, 내구성, 확장성 및 중복성이 높은 스토리지를 제공하는 Azure 관리형 클라우드 서비스입니다. 일부 스토리지 유형은 SAP 시나리오에서 제한적으로 사용됩니다. 하지만, 몇 가지 Azure Storage 유형은 특정 SAP 워크로드 시나리오에 적합하거나 최적화되어 있습니다. 자세한 내용은 SAP에 적합한 다양한 스토리지 옵션이 설명되어 있는 [SAP 워크로드에 대한 Azure Storage 유형](planning-guide-storage.md) 가이드를 참조하세요.

Azure Storage에는 고객이 사용할 수 있는 다양한 Storage 유형이 있으며 [Azure에서 사용할 수 있는 디스크 유형](../../disks-types.md) 문서에서 동일한 세부 정보 읽을 수 있습니다. SAP BOBI 플랫폼은 다음 Azure Storage를 사용하여 애플리케이션을 빌드합니다.

- Azure 관리 디스크

  Azure가 관리하는 블록 수준 스토리지 볼륨입니다. 이 디스크는 Azure 가상 머신에 설치된 경우 SAP BOBI 플랫폼 애플리케이션 서버 및 데이터베이스에 사용할 수 있습니다. 다양한 유형의 [Azure Managed Disks](../../managed-disks-overview.md)를 사용할 수 있지만 SAP BOBI 플랫폼 애플리케이션 및 데이터베이스에는 [프리미엄 SSD](../../disks-types.md#premium-ssds)를 사용하는 것이 좋습니다.

  아래 예에서는 프리미엄 SSD가 BOBI 플랫폼 설치 디렉터리에 사용됩니다. 가상 머신에 설치된 데이터베이스의 경우 지침에 따라 데이터 및 로그 볼륨에 관리 디스크를 사용할 수 있습니다. CMS 및 감사 데이터베이스는 일반적으로 작으며 다른 SAP OLTP/OLAP 데이터베이스와 동일한 스토리지 성능 요구 사항이 없습니다.

- Azure Premium Files 또는 Azure NetApp Files

  SAP BOBI 플랫폼에서 FRS(파일 리포지토리 서버)는 시스템의 모든 애플리케이션 서버에서 사용하는 보고서, Universe 및 연결과 같은 콘텐츠가 저장되는 디스크 디렉터리를 나타냅니다. [Azure Premium Files](../../../storage/files/storage-files-introduction.md) 또는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 스토리지는 SAP BOBI 애플리케이션 FRS의 공유 파일 시스템으로 사용할 수 있습니다. 이 스토리지 제품은 일부 지역에서는 사용할 수 없으므로 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/) 사이트에서 최신 정보를 참조하세요.

  사용 지역에서 이 서비스를 사용할 수 없는 경우 파일 시스템을 SAP BOBI 애플리케이션과 공유할 수 있는 NFS 서버를 만들면 됩니다. 하지만 고가용성도 고려해야 합니다.

![Azure의 SAP BusinessObjects BI 플랫폼 스토리지 레이아웃](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>네트워킹

SAP BOBI는 비즈니스 데이터를 보유하지 않는 보고 및 분석 BI 플랫폼입니다. 따라서 이 시스템은 다른 데이터베이스 서버에 연결하여 모든 데이터를 가져오고 사용자에게 인사이트를 제공합니다. Azure는 SAP BI 플랫폼으로 실현할 수 있는 모든 시나리오(예: 온-프레미스 시스템, 다른 가상 네트워크의 시스템에 연결 등)를 매핑할 수 있는 네트워크 인프라를 제공합니다. 자세한 내용은 [SAP 워크로드를 위한 Microsoft Azure 네트워킹](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)을 참조하세요.

Database-as-a-Service 제품의 경우, 새로 생성된 모든 데이터베이스(Azure SQL Database 또는 Azure Database for MySQL)에는 모든 외부 연결을 차단하는 방화벽이 있습니다. BI 플랫폼 가상 머신에서 DBaaS 서비스에 액세스할 수 있도록 하려면 하나 이상의 서버 수준 방화벽 규칙을 지정하여 DBaaS 서버에 대한 액세스가 가능하도록 설정해야 합니다. 자세한 내용은 Azure Database for MySQL의 [방화벽 규칙](../../../mysql/concepts-firewall-rules.md) 및 Azure SQL 데이터베이스의 [네트워크 액세스 제어](../../../azure-sql/database/network-access-controls-overview.md) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Linux에 SAP BusinessObjects BI 플랫폼 배포](businessobjects-deployment-guide-linux.md)
- [SAP용 Azure Virtual Machines 계획 및 구현](planning-guide.md)
- [SAP용 Azure Virtual Machines 배포](deployment-guide.md)
- [SAP용 Azure Virtual Machines DBMS 배포](./dbms_guide_general.md)