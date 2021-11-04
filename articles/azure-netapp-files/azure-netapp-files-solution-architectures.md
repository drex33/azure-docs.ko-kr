---
title: Azure NetApp Files를 사용하는 솔루션 아키텍처 | Microsoft Docs
description: Azure NetApp Files를 사용하는 솔루션 아키텍처에 대한 모범 사례 참조를 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: b-juche
ms.openlocfilehash: 8b37458635f73b463bf4e01630e406753e26f353
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576323"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Azure NetApp Files를 사용하는 솔루션 아키텍처
이 문서에서는 Azure NetApp Files 사용을 위한 솔루션 아키텍처를 이해하는 데 도움이 되는 모범 사례 참조를 제공합니다.  

다음 다이어그램은 Azure NetApp Files에서 제공하는 솔루션 아키텍처의 범주를 요약합니다.

![솔루션 아키텍처 범주](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS 앱 및 데이터베이스 솔루션

이 섹션에서는 Linux OSS 애플리케이션 및 데이터베이스용 솔루션에 대한 참조를 제공합니다. 

### <a name="oracle"></a>Oracle

* [Azure NetApp Files로 Oracle Database-Azure 예제 시나리오](/azure/architecture/example-scenario/file-storage/oracle-azure-netapp-files)
* [Azure NetApp Files를 사용하는 Microsoft Azure의 Oracle Database](https://www.netapp.com/media/17105-tr4780.pdf)
* [Microsoft Azure의 Oracle VM 이미지 및 배포: 공유 스토리지 구성 옵션](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Azure NetApp Files 단일 볼륨의 Oracle 데이터베이스 성능](performance-oracle-single-volumes.md)
* [Oracle Database에서 Azure NetApp Files를 사용할 경우의 이점](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)
*   [Azure의 분산 교육: 레인 검색 - 솔루션 설계](https://www.netapp.com/media/32427-tr-4896-design.pdf)
*   [Azure의 분산 교육: Click-Through 요금 예측 – 솔루션 디자인](https://docs.netapp.com/us-en/netapp-solutions/ai/aks-anf_introduction.html)

### <a name="education"></a>Education
* [Azure NetApp Files NFS 스토리지의 Moodle](https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-netapp-files-for-nfs-storage-with-moodle/ba-p/2300630)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows 앱 및 SQL Server 솔루션

이 섹션에서는 Windows 애플리케이션 및 SQL Server 솔루션에 대한 참조를 제공합니다.

### <a name="file-sharing-and-global-file-caching"></a>파일 공유 및 전역 파일 캐싱

* [Azure NetApp Files 및 DFS 네임 스페이스를 사용 하 여 Enterprise 파일 공유에 대 한 재해 복구](https://techcommunity.microsoft.com/t5/azure-architecture-blog/disaster-recovery-for-enterprise-file-shares/ba-p/2808757)
* [고유한 Azure NFS 빌드? 클라우드에 Linux 파일 공유 구현](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Azure NetApp Files 및 NetApp 전역 파일 캐시를 사용하여 전역적으로 분산된 엔터프라이즈 파일 공유](https://f.hubspotusercontent20.net/hubfs/525875/NA-580-0521-Architecture-Doc-R3.pdf)
* [Azure NetApp Files에 대한 클라우드 규정 준수](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Azure NetApp Files를 사용 하 여 azure Virtual Machines에서 SQL Server-azure 예제 시나리오](/azure/architecture/example-scenario/file-storage/sql-server-azure-netapp-files)
* [Azure NetApp Files를 사용한 Azure의 SQL Server 배포 가이드](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [SQL Server 배포에 Azure NetApp Files를 사용할 경우의 이점](solutions-benefits-azure-netapp-files-sql-server.md)
* [Azure NetApp Files를 사용하여 SMB를 통한 SQL Server 배포](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Azure NetApp Files를 사용하여 SMB를 통해 SQL Server Always-On 장애 조치(Failover) 클러스터 배포](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Azure NetApp Files를 사용하여 Always-On 가용성 그룹 배포](https://www.youtube.com/watch?v=y3VQmzzeyvc) 

## <a name="sap-on-azure-solutions"></a>Azure의 SAP 솔루션

이 섹션에서는 Azure의 SAP 솔루션에 대한 참조를 제공합니다. 

### <a name="generic-sap-and-sap-netweaver"></a>일반 SAP 및 SAP Netweaver 

* [Azure NetApp Files를 사용하는 Microsoft Azure의 SAP 애플리케이션](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP 애플리케이션용 Azure NetApp Files를 사용하여 SUSE Linux Enterprise Server에서 Azure VM의 SAP NetWeaver 고가용성 실현](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [SAP 애플리케이션용 Azure NetApp Files를 사용하여 Red Hat Enterprise Linux에서 Azure VM의 SAP NetWeaver 고가용성 실현](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [SAP 애플리케이션용 Azure NetApp Files(SMB)를 사용하는 Windows의 Azure VM에서 SAP NetWeaver에 대한 고가용성](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [SAP 애플리케이션용 Red Hat Enterprise Linux의 Azure VM 기반 SAP NetWeaver에 대한 다중 SID 고가용성 가이드](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 가상 머신 스토리지 구성](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Red Hat Enterprise Linux에서 Azure NetApp Files를 사용한 SAP HANA 확장의 고가용성](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Red Hat Enterprise Linux에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [RHEL에서 HSR 및 Pacemaker를 사용하여 SAP HANA 스케일 아웃 - Azure Virtual Machines](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Azure Application Consistent Snapshot Tool(AzAcSnap)](azacsnap-introduction.md)
* [Azure NetApp Files를 사용 하 여 재해 복구 SAP HANA](https://docs.netapp.com/us-en/netapp-solutions-sap/pdfs/sidebar/SAP_HANA_Disaster_Recovery_with_Azure_NetApp_Files.pdf)

### <a name="sap-anydb"></a>SAP AnyDB

* [SAP 워크로드용 Oracle Azure Virtual Machines DBMS 배포 - Azure Virtual Machines](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Azure NetApp Files를 사용하여 SAP AnyDB(Oracle 19c) 배포](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)
* [Azure NetApp Files를 사용하여 SAP 워크로드용 IBM Db2 Azure Virtual Machines DBMS 배포](../virtual-machines/workloads/sap/dbms_guide_ibm.md#using-azure-netapp-files)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 SAP IQ-NLS 고가용성 솔루션 배포](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [고가용성 시나리오에서 SAP IQ 라이선스를 관리하는 방법](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>SAP 기술 커뮤니티 및 블로그 게시물 

* [Azure NetApp Files – 몇 초 만에 SAP HANA 백업](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – 스냅샷 백업에서 HANA 데이터베이스 복원](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – 클라우드 동기화를 사용하여 SAP HANA 백업 오프로드](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Azure NetApp Files를 사용하여 SAP HANA 시스템 복사본 속도 향상](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud Volumes ONTAP 및 Azure NetApp Files: 손쉬운 SAP HANA 시스템 마이그레이션](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [HANA N+M 스케일 아웃 아키텍처의 ANF 투자를 극대화하기 위한 아키텍처 결정 - 1부](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [HANA N+M 스케일 아웃 아키텍처의 ANF 투자를 극대화하기 위한 아키텍처 결정 - 2부](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [HANA N+M 스케일 아웃 아키텍처의 ANF 투자를 극대화하기 위한 아키텍처 결정 - 3부](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [Azure NetApp Files를 사용한 SAP Landscape 크기 조정 및 볼륨 통합](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Azure VMware Solutions

* [Azure VMware Solution을 사용하는 Azure NetApp Files - 게스트 OS 탑재](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>가상 데스크톱 인프라 솔루션

이 섹션에서는 가상 데스크톱 인프라 솔루션에 대한 참조를 제공합니다.

### <a name="azure-virtual-desktop"></a><a name="windows-virtual-desktop"></a>Azure Virtual Desktop

* [Azure Virtual Desktop에서 Azure NetApp Files를 사용할 경우의 이점](solutions-windows-virtual-desktop.md)
* [Azure Virtual Desktop의 FSLogix 프로필 컨테이너에 대한 스토리지 옵션](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Azure NetApp Files를 사용하여 호스트 풀에 대한 FSLogix 프로필 컨테이너 만들기](../virtual-desktop/create-fslogix-profile-container.md)
* [엔터프라이즈 규모의 Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [엔터프라이즈용 Microsoft FSLogix - Azure NetApp Files 모범 사례](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [MSIX 앱 연결에 대한 Azure NetApp Files 설정](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

### <a name="citrix"></a>Citrix   

* [Azure NetApp Files 모범 사례 가이드를 사용한 Citrix 프로필 관리](https://www.netapp.com/pdf.html?item=/media/55973-tr-4901.pdf)


## <a name="hpc-solutions"></a>HPC 솔루션

이 섹션에서는 HPC(고성능 컴퓨팅) 솔루션에 대한 참조를 제공합니다. 

### <a name="generic-hpc"></a>일반 HPC

* [Azure NetApp Files: 클라우드 스토리지를 최대한 활용](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Azure Batch 및 Azure NetApp Files를 사용하여 MPI 워크로드 실행](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: Azure NetApp Files의 CycleCloud HPC 환경](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>석유 및 가스

* [HPC(고성능 컴퓨팅): Azure의 석유 및 가스](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Azure에서 저수지 시뮬레이션 소프트웨어 실행](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>전자 디자인 자동화(EDA)

* [전자 디자인 자동화에 Azure NetApp Files 사용의 이점](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: Azure NetApp Files을 사용 하는 EDA HPC 랩](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [반도체 업계를 위한 Azure](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>분석

* [Azure NetApp Files: Microsoft Azure의 SAS 그리드에 사용하는 공유 파일 시스템](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp Files: MS Azure의 SAS 그리드에 사용하는 공유 파일 시스템 - RHEL8.3/nconnect 업데이트](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [SAS®에서의 Microsoft Azure 사용에 대한 모범 사례](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)
* [Azure의 SAS 아키텍처 가이드 - Azure 아키텍처 센터 | Azure NetApp Files](/azure/architecture/guide/sas/sas-overview#azure-netapp-files-nfs)

### <a name="healthcare"></a>의료

* [Azure NetApp Files를 사용 하는 Microsoft Azure의 대규모 EHR](https://www.netapp.com/pdf.html?item=/media/62375-tr-4909.pdf)

## <a name="azure-platform-services-solutions"></a>Azure 플랫폼 서비스 솔루션

이 섹션에서는 Azure 플랫폼 서비스에 대한 솔루션을 제공합니다. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services 및 Kubernetes

* [Astra: Azure NetApp Files에서 AKS 워크로드 보호, 복구 및 관리](https://cloud.netapp.com/hubfs/Astra%20Azure%20Documentation.pdf) 
* [Azure NetApp Files와 Azure Kubernetes Service의 통합](../aks/azure-netapp-files.md)
* [Azure NetApp Files를 사용하여 Azure에서 뛰어난 Kubernetes 성능](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = Kubernetes용 동적 및 영구 스토리지](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident - 컨테이너용 스토리지 오케스트레이터](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Azure Kubernetes Service(AKS)의 Magento 전자 상거래 플랫폼](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-red-hat-openshift"></a>Azure Red Hat Openshift   

*   [Trident를 사용 하 여 OpenShift에서 Azure NetApp Files 자동화](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/using-trident-to-automate-azure-netapp-files-from-openshift/ba-p/2367351)


### <a name="azure-batch"></a>Azure Batch

* [Azure Batch 및 Azure NetApp Files를 사용하여 MPI 워크로드 실행](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
