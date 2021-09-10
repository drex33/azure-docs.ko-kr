---
title: Microsoft Azure의 Oracle 솔루션 | Microsoft Docs
description: 완전히 Azure 인프라에서 실행하거나 OCI(Oracle Cloud Infrastructure)와의 클라우드 간 연결을 사용하는 방법을 포함하여 Microsoft Azure에 Oracle 애플리케이션 및 솔루션을 배포하는 옵션에 대해 알아봅니다.
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 0f41a089dc6757c36ba8fc262553a92b03147263
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823762"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure의 Oracle 애플리케이션 및 솔루션 개요

**적용 대상:** :heavy_check_mark: Linux VM 

이 문서에서는 Azure 인프라를 사용하여 Oracle 솔루션을 실행하는 기능을 소개합니다. 사용 가능한 [WebLogic Server Azure 애플리케이션](oracle-weblogic.md), Azure Marketplace의 [Oracle VM 이미지](oracle-vm-solutions.md) 및 [OCI(Oracle Cloud Infrastructure)와 Azure를 상호 연결](oracle-oci-overview.md)하는 기능에 대한 자세한 소개도 참조하세요.

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure 인프라 기반 Oracle 데이터베이스

Azure Marketplace에 제공된 Oracle Linux 기반 Oracle Database 이미지를 사용하여 Azure 인프라에서 Oracle 데이터베이스를 실행합니다.

* Oracle Database 12.2 및 18.3 Enterprise Edition 

* Oracle Database 12.2 및 18.3 Standard Edition

* Oracle Database 19.3

또한 Azure에 사용 가능한 비 Oracle Linux 기반 Oracle Database를 설치하거나, 처음부터 Azure에서 만든 사용자 지정 이미지를 기반으로 솔루션을 제작하거나 온-프레미스 환경에서 사용자 지정 이미지를 업로드할 수도 있습니다.

필요에 따라 연결된 여러 디스크를 사용하여 구성하고 Oracle ASM(Automated Storage Management)을 설치하여 데이터베이스 성능을 개선하세요.

## <a name="weblogic-server-with-azure-service-integrations"></a>Azure 서비스가 통합된 WebLogic Server

다양한 WebLogic Server Azure 애플리케이션 중에 원하는 애플리케이션을 선택하여 클라우드 여정을 가속화하세요.  데이터베이스, Azure App Gateway 및 Azure Active Directory를 비롯한 몇 가지 미리 구성된 Azure 서비스 통합을 사용할 수 있습니다.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux 및 WebLogic Server의 애플리케이션

지원되는 Oracle Linux 이미지에서 Azure의 엔터프라이즈 애플리케이션을 실행합니다. Azure Marketplace에 제공되는 가상 머신 이미지는 다음과 같습니다.

* Oracle WebLogic Server 12.1.2

* UEK(Unbreakable Enterprise Kernel) 6.8, 6.9, 6.10, 7.3~7.7, 8.0, 8.1을 포함하는 Oracle Linux 

## <a name="high-availability-and-disaster-recovery-options"></a>고가용성 및 재해 복구 옵션

* 지역 내 가용성을 위해 [가용성 영역](../../../availability-zones/az-overview.md)과 함께 [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard with FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) 또는 [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html)를 Azure 인프라에 구성합니다. 또한 추가 가용성 및 재해 복구를 위해 여러 Azure 지역에 이러한 구성을 설정할 수 있습니다.

* [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)를 사용하여 Azure의 Oracle Linux VM과 물리 서버의 재해 복구를 오케스트레이션 및 관리합니다. 

## <a name="backup-oracle-workloads"></a>Oracle 워크로드 백업

* [Azure Backup](../../../backup/backup-overview.md)을 사용하여 Oracle VM 백업

* Oracle RMAN을 사용하여 Oracle Database를 백업하고, 선택적으로 [Azure Blob Fuse](../../../storage/blobs/storage-how-to-mount-container-linux.md)를 사용하여 [중복성이 높은 Azure Blob Storage 계정](../../../storage/common/storage-redundancy.md)을 탑재하고, 추가적인 복원력을 위해 RMAN 백업을 작성합니다.

## <a name="integration-of-azure-with-oci"></a>OCI와 Azure 통합

OCI(Oracle Cloud Infrastructure)의 백 엔드 데이터베이스에 연결된 Azure 인프라에서 Oracle 애플리케이션을 실행합니다. 이 솔루션에서는 다음과 같은 기능을 사용합니다. 

* **클라우드 간 네트워킹** - Azure ExpressRoute와 Oracle FastConnect 간에 사용 가능한 직접 상호 연결을 사용하여 애플리케이션과 데이터베이스 계층 간에 고대역폭, 비공개, 대기 시간이 짧은 연결을 설정합니다.
* **통합 ID** - Azure AD와 Oracle IDCS 간에 페더레이션 ID를 설정하여 솔루션의 단일 ID 원본을 만듭니다. Single Sign-On를 사용하도록 설정하여 OCI 및 Azure의 리소스를 관리합니다.

### <a name="deploy-oracle-applications-on-azure"></a>Azure에 Oracle 애플리케이션 배포

Terraform 템플릿을 사용하여 Azure 인프라를 설정하고 Oracle 애플리케이션을 설치합니다. 

Oracle은 Azure/Oracle 클라우드 상호 연결 솔루션을 통하여 Oracle 데이터베이스에 연결 중일 때 Azure에서 실행하도록 이러한 애플리케이션을 인증하였습니다.

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail 애플리케이션
* Oracle Hyperion Financial Management

또한 Azure에서 OCI 및 기타 Azure 서비스와 연결되는 사용자 지정 애플리케이션을 배포합니다.

### <a name="set-up-oracle-databases-in-oci"></a>OCI에 Oracle 데이터베이스 설정

Oracle Database 클라우드 서비스(자율운영 데이터베이스, RAC, Exadata, DBaaS, 단일 노드)를 Azure에서 실행되는 Oracle 소프트웨어와 함께 사용합니다. [OCI 데이터베이스 옵션](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)에 대해 자세히 알아보세요. 
 

## <a name="licensing"></a>라이선스

Azure에 Oracle 애플리케이션을 배포하는 작업은 "사용자 라이선스 필요" 모델을 기반으로 합니다. Oracle 소프트웨어를 사용하는 데 필요한 라이선스가 있으며 현재 Oracle과의 지원 계약이 체결되어 있는 것으로 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. Oracle-Azure [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [WebLogic Server Azure 애플리케이션](oracle-weblogic.md) 및 지원되는 Azure 서비스 통합에 대해 자세히 알아보세요.

* Azure 인프라에서 [Oracle VM 이미지](oracle-vm-solutions.md)를 배포하는 방법에 대해 자세히 알아보세요.

* [OCI와 Azure를 상호 연결](oracle-oci-overview.md)하는 방법에 대해 자세히 알아보세요.

* Ignite 2019의 [Azure의 Oracle 개요 세션](https://www.pluralsight.com/courses/microsoft-ignite-session-57)을 확인하세요. 
