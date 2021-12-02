---
title: Azure VM의 SAP 시작 | Microsoft Docs
description: Microsoft Azure의 VM(가상 머신)에서 실행되는 SAP 솔루션에 대한 자세한 정보
services: virtual-machines-sap
ms.service: virtual-machines-sap
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/01/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5af2df81cc6afcd26adf9d6348013879f1cc4bd
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484413"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Azure를 사용하여 SAP 워크로드 시나리오 호스트 및 실행

Microsoft Azure를 사용하면 확장성 있고 규정을 준수하는 엔터프라이즈 입증 플랫폼에서 중요 업무용 SAP 워크로드 및 시나리오를 안정적으로 실행할 수 있습니다. Azure의 확장성, 유연성 및 비용 절감을 누릴 수 있습니다. Microsoft와 SAP 사이의 확장된 파트너 관계 덕분에 Azure에서 개발과 테스트 및 프로덕션 시나리오 전체에서 SAP 애플리케이션을 실행할 수 있으며 완전한 지원을 받을 수 있습니다. SAP NetWeaver부터 SAP S/4HANA까지, Linux의 SAP BI부터 Windows까지, SAP HANA에서 SQL까지 모두 지원됩니다.

Azure에 다양한 DBMS가 있는 SAP NetWeaver 시나리오 호스트 외에 Azure의 SAP BI와 같은 다양한 SAP 워크로드 시나리오를 호스트할 수 있습니다. 

SAP HANA용 Azure의 고유한 특성은 Azure를 차별화하는 고유한 요소입니다. SAP HANA와 관련된 더 많은 메모리 및 CPU 리소스 수요가 많은 SAP 시나리오 호스트를 지원하기 위해 Azure는 고객 전용 운영 체제 미설치 하드웨어를 제공합니다. 이 솔루션을 사용하면 S/4HANA 또는 기타 SAP HANA 워크로드에 대해 최대 24TB(120TB 스케일 아웃)의 메모리가 있어야 하는 SAP HANA 배포를 실행할 수 있습니다. 

Azure에서 SAP 워크로드 시나리오를 호스트하면 ID 통합 및 Single Sign-On 요구 사항을 만들 수 있습니다. 이 상황은 Azure AD(Azure Active Directory)를 사용하여 다양한 SAP 구성 요소와 SAP SaaS(Software as a Service) 또는 PaaS(Platform as a Service) 제품을 연결하는 경우에 발생할 수 있습니다. Azure AD와 SAP 엔터티를 사용한 해당 통합 및 Single Sign-On 시나리오 목록은 “Azure AD SAP ID 통합 및 Single Sign-On” 섹션에 설명 및 문서화되어 있습니다.

## <a name="changes-to-the-sap-workload-section"></a>SAP 워크로드 섹션에 대한 변경 내용
Azure의 SAP 워크로드 섹션의 문서에 대한 변경 내용은 이 문서의 끝에 나열되어 있습니다. 변경 로그의 항목은 약 180일 동안 유지됩니다.

## <a name="you-want-to-know"></a>자주 하는 질문과 대답
특정 질문이 있을 경우 시작 페이지의 이 섹션에서 특정 문서 또는 흐름으로 안내합니다. 자주 하는 질문과 대답은 다음과 같습니다.

- SAP 소프트웨어 릴리스 및 운영 체제 버전별로 지원되는 Azure VM 및 HANA(대규모 인스턴스) 단위는 무엇인가요? 답변 및 정보를 찾는 과정은 [Azure 배포에 대해 지원되는 SAP 소프트웨어](./sap-supported-product-on-azure.md) 문서를 참조하세요.
- Azure VM 및 HANA(대규모 인스턴스)에서 지원되는 SAP 배포 시나리오는 무엇인가요? 지원되는 시나리오에 대한 정보는 다음 문서에서 찾을 수 있습니다.
    - [Azure 가상 머신의 SAP 워크로드 지원 시나리오](./sap-planning-supported-configurations.md)
    - [HANA(대규모 인스턴스)의 지원되는 시나리오](./hana-supported-scenario.md)
- 여러 Azure 지역에서 사용할 수 있는 Azure 서비스, Azure VM 유형 및 Azure 스토리지 서비스에 대한 정보는 [지역별 제공 제품](https://azure.microsoft.com/global-infrastructure/services/) 사이트에서 확인할 수 있습니다. 
- Windows 및 Pacemaker 지원 외에 타사 HA 프레임도 작동하나요? [SAP 지원 노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533)의 아래쪽 부분을 확인해 보세요.
- 제 시나리오에는 어떤 Azure 스토리지가 가장 적합한가요? [SAP 워크로드에 대한 Azure Storage 형식](./planning-guide-storage.md)을 참조하세요.
- SAP에서 지원하는 Oracle Enterprise Linux의 Red Hat 커널이 있나요? [SAP 지원 노트 #1565179](https://launchpad.support.sap.com/#/notes/1565179)를 참조하세요.
- Azure [Da(s)v4](../../dav4-dasv4-series.md)/[Ea(s)](../../eav4-easv4-series.md) VM 제품군이 SAP HANA에 인증되지 않는 이유는 무엇인가요? Azure Das/Eas VM 제품군은 AMD 프로세서로 운영되는 하드웨어를 기반으로 합니다. SAP HANA는 가상화 시나리오에서도 AMD 프로세서를 지원하지 않습니다.
- 가장 최근 Linux 커널을 실행하고 있음에도 불구하고 SAP HANA에서 ‘RDTSCP 명령의 CPU 플래그나 constant_tsc, 혹은 nonstop_tsc 명령의 CPU 플래그가 설정되지 않았거나 current_clocksource 및 available_clocksource가 바르게 구성되지 않았습니다’라는 메시지가 계속 나오는 이유는 무엇인가요? 자세한 답변 내용은 [SAP 지원 노트 #2791572](https://launchpad.support.sap.com/#/notes/2791572)를 확인하세요.
- Azure에 SAP Fiori를 배포하는 아키텍처는 어디서 찾을 수 있나요? [Azure의 SAP: 인터넷 연결 SAP Fiori 앱용 Application Gateway WAF(Web Application Firewall) v2 설정](https://blogs.sap.com/2020/12/03/sap-on-azure-application-gateway-web-application-firewall-waf-v2-setup-for-internet-facing-sap-fiori-apps/) 블로그를 확인하세요. 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)에서 SAP HANA 사용

일련의 설명서를 통해 Azure의 SAP HANA(대규모 인스턴스) 또는 약식 HANA(대규모 인스턴스)에 대해 알아봅니다. HANA(대규모 인스턴스)에 대한 자세한 내용은 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](./hana-overview-architecture.md) 문서부터 시작하여 HANA(대규모 인스턴스) 섹션에서 관련 설명서를 참조하세요.



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 가상 머신의 SAP HANA
설명서의이 섹션에서는 SAP HANA의 다양한 측면을 다룹니다. 전제 조건으로 Azure IaaS 기본 서비스를 제공하는 Azure의 주요 서비스를 잘 알고 있어야 합니다. 따라서 Azure 컴퓨팅, 스토리지 및 네트워킹에 대한 지식이 필요합니다. 이러한 주제 중 대부분은 SAP NetWeaver 관련 [Azure 계획 가이드](./planning-guide.md)에서 다룹니다. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 가상 머신에서 배포된 SAP NetWeaver
이 섹션에서는 Azure의 SAP NetWeaver, SAP LaMa 및 Business One에 대한 계획 및 배포 설명서가 나열되어 있습니다. 이 설명서에서는 Azure의 SAP 워크로드에서 HANA가 아닌 데이터베이스의 기본 사항 및 사용에 중점을 둡니다. 고가용성에 대한 문서와 아티클은 Azure의 SAP HANA 고가용성의 기반이 되기도 합니다.

## <a name="sap-netweaver-and-s4hana-high-availability"></a>SAP NetWeaver 및 S/4HANA 고가용성
SAP 애플리케이션 계층 및 DBMS의 고가용성은 [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](./sap-high-availability-guide-start.md) 문서에서부터 자세히 설명합니다.



## <a name="integrate-azure-ad-with-sap-services"></a>SAP 서비스와 Azure AD 통합
이 섹션에서는 대부분의 SAP SaaS 및 PaaS 서비스, NetWeaver, Fiori를 사용하여 SSO를 구성하는 방법에 대한 정보를 찾을 수 있습니다. 



## <a name="documentation-on-integration-of-azure-services-into-sap-components"></a>SAP 구성 요소에 Azure 서비스를 통합하는 방법에 대한 설명서
이 섹션에서는 Microsoft Power BI를 SAP 데이터 원본에 통합하는 방법과 Azure Data Factory를 SAP BW에 통합하는 방법을 설명하는 문서를 찾을 수 있습니다.

## <a name="change-log"></a>로그 변경

- 2021년 12월 2일: Azure 공유 디스크 SBD 디바이스를 사용하여 [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정에서](high-availability-guide-suse-pacemaker.md) 새로운 STONITH 펜싱 방법을 소개합니다.
- 2021년 12월 1일: [WSFC 및 파일 공유를 사용하여 SAP ASCS/SCS 인스턴스를 변경하고,](./sap-high-availability-guide-wsfc-file-share.md) [SMB(Azure NetApp Files)를 Windows Azure VM의](./high-availability-guide-windows-netapp-files-smb.md) [SAP NetWeaver에 대한 HA를 Azure Files(SMB)로 Windows SAP NetWeaver의 SAP NetWeaver를](./high-availability-guide-windows-azure-files-smb.md) 사용하여 SAP 커널 버전을 업데이트하고, 파일 공유를 사용하여 Windows SAP 클러스터링을 지원하는 데 필요합니다.  
- 2021년 11월 30일: [WINDOWS DFS-N을 사용하여 SMB 기반 파일 공유에 대한 유연한 SAPMNT 공유 만들기 지원](./high-availability-guide-windows-dfs.md) 추가
- 2021년 11월 22일: J2EE SAP 시스템에 대한 지침을 명확히 하고 스토리지 계정당 통합을 공유하기 위해 [Azure Files NFS를 사용하여 SLES의](./high-availability-guide-suse-nfs-azure-files.md) SAP NW에 대한 HA를 변경하고 [Azure Files NFS를 사용하여 RHEL의 SAP NW에](./high-availability-guide-rhel-nfs-azure-files.md) 대한 HA를 변경합니다.
- 2021년 11월 16일: Azure 파일 HA의 NFS를 통해 SAP ASCS/ERS에 대한 고가용성 가이드 릴리스- [SLES의 SAP NW에는 NFS on Azure Files,](./high-availability-guide-suse-nfs-azure-files.md) AZURE FILES [NFS가 있는 RHEL의 SAP NW는 HA입니다.](./high-availability-guide-rhel-nfs-azure-files.md) 
- 2021년 11월 15일: SAP 애플리케이션을 사용하여 [네트워크 대기 시간을 최적화하기](./sap-proximity-placement-scenarios.md) 위해 Azure 근접 배치 그룹의 영역 배포를 위한 새로운 근접 배치 아키텍처 소개
- 2021년 11월 2일: Azure NetApp Files NFS에 대한 SAP ASE 지원을 선언하도록 [SAP 워크로드용 SAP ASE Azure Virtual Machines DBMS 배포에](./dbms_guide_sapase.md) 대한 Azure Storage [유형이](./planning-guide-storage.md) 변경되었습니다.
- 2021년 11월 2일: 활성/활성 구성을 위해 싱가포르 남부를 지역으로 이동하기 위해 [Azure 가용성 영역 SAP 워크로드](./sap-ha-availability-zones.md) 구성 변경
- 2021년 11월 [2일: Red Hat Enterprise Linux의 Azure VM에서 SAP HANA 고가용성을](./sap-hana-high-availability-rhel.md) 변경하여 HANA 강화 활성/활성(읽기 사용) 구성에 대한 지침을 업데이트합니다.
- 2021년 10월 26일: [RHEL의 Azure VM에서 Pacemaker를 사용하여 SAP HANA 스케일 아웃 HSR을](./sap-hana-high-availability-scale-out-hsr-rhel.md) 변경하여 HANA 스케일 아웃 활성/활성(읽기 사용) 구성에서 리소스 이름을 업데이트합니다.
- 2021년 10월 19일: [RHEL의 Azure VM에서 Pacemaker를 사용하여 SAP HANA 스케일 아웃 HSR을](./sap-hana-high-availability-scale-out-hsr-rhel.md) 변경하여 HANA 스케일 아웃 활성/활성(읽기 사용) 구성에 대한 지침을 추가합니다.
- 2021년 10월 11일: [Azure에서 클러스터 공유 디스크를 사용하여 Windows 장애 조치(failover) 클러스터의 SAP ASCS/SCS 인스턴스](./sap-high-availability-guide-wsfc-shared-disk.md)클러스터 변경 , [SAP ASCS/SCS 및 SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md) 인스턴스 다중 SID 고가용성을 위한 Windows 장애 조치(failover) 클러스터 및 공유 디스크를 사용하여 SAP HA용 Azure 인프라 [준비 Windows 서버 장애 조치(failover) 클러스터링 및 Azure 공유 디스크의 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)  Azure 공유 디스크 지원을 위한 ZRS(영역 중복 스토리지)에 대한 지침을 추가하려면
- 2021년 10월 8일: [SLES의 Azure VM에서 Pacemaker를 사용하여 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-suse.md)변경, [RHEL의 ANF를 사용하여 SAP HANA 강화를 위한 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 및 [RHEL의 Azure VM에서 Pacemaker를 사용하여 규모 확장 HSR을 SAP HANA](./sap-hana-high-availability-scale-out-hsr-rhel.md) sudoers 파일에 기본값을 추가하고 HANA 스케일 아웃(HANA srHook용)에 대한 업데이트를 업데이트합니다.
- 2021년 10월 1일: SAP HANA 백업 문서를 위한 새로운 Azure Backup 아키텍처에 대한 링크가 목차에 추가되었습니다. SAP 워크로드용 Azure Virtual Machines Oracle DBMS [배포에 Oracle DBMS용 Azure Backup](./dbms_guide_oracle.md) 서비스 링크 추가
- 2021년 9월 24일: [SLES의 Azure VM에서 Pacemaker를 사용하여 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-suse.md)변경, [RHEL에서 ANF를 사용하여 SAP HANA 강화를 위한 HA, RHEL의](./sap-hana-high-availability-netapp-files-red-hat.md) [Azure VM에서 Pacemaker를 사용하여 규모 확장 HSR을 SAP HANA](./sap-hana-high-availability-scale-out-hsr-rhel.md) sudoers 파일(HANA srHook용)을 만들기 위한 지침을 조정합니다.
- 2021년 9월 16일: [SMB(Azure Files)를 Windows Azure VM의 SAP NetWeaver에 대한 HA](./high-availability-guide-windows-azure-files-smb.md) 릴리스
- 2021년 9월 15일: [SAP 워크로드용 SAP ASE Azure Virtual Machines DBMS 배포의 SAP ASE에 대한](./dbms_guide_sapase.md) 새로운 HADR 구성 소개
- 2021년 9월 [8일: Azure NetApp Files NFS v4.1 볼륨에](./hana-vm-operations-netapp.md) 수동 QoS 용량 풀 추가SAP HANA
- 2021년 8월 26일: Azure Fence 에이전트의 역할 정의 JSON을 수정하기 위해 [Azure에서 RHEL의 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 및 [Azure에서 SLES의 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 변경
- 2021년 8월 17일: ANF에서 호스트되는 NFS 볼륨을 사용하는 IBM Db2를 지원하기 위해 [SAP 워크로드에 대한 IBM Db2 Azure Virtual Machines DBMS 배포](./dbms_guide_ibm.md), [SAP 워크로드에 대한 Azure Storage 형식 변경](./planning-guide-storage.md) 및 [Azure 가상 머신 지원 시나리오에서의 SAP 워크로드](./sap-planning-supported-configurations.md) 변경
- 2021년 8월 2일: 서버 실패 큐에 넣기를 시뮬레이션하는 테스트 시나리오에 대한 작동(ENSA1/ENSA2)을 명시하기 위해 [SAP 애플리케이션용 SLES의 Azure VM에서 SAP NW의 HA](./high-availability-guide-suse.md), [ANF를 사용한 SLES의 Azure VM에서 SAP NW의 HA](./high-availability-guide-suse-netapp-files.md), [SAP 애플리케이션용 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel.md) 및 [ANF를 사용한 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-netapp-files.md) 문서 변경     
- 2021년 8월 11일: 클러스터 리소스 고정, 마이그레이션 임계값, 순서 제약을 조정하기 위해 [SAP 애플리케이션용 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel.md), [ANF를 사용한 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-netapp-files.md) 및 [RHEL 다중 SID 가이드의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-multi-sid.md) 변경  
- 2021년 8월 11일: [Azure의 SAP IQ를 사용한 SAP BW-NLS(니어라인 스토리지) 구현 가이드](./sap-iq-deployment-guide.md) 릴리스 
- 2021년 7월 29일: [SAP 애플리케이션용 Azure NetApp Files(SMB)가 있는 Windows의 Azure VM에서 SAP NetWeaver의 고가용성](./high-availability-guide-windows-netapp-files-smb.md) 및 [Azure에서 클러스터 공유 디스크를 사용하여 Windows 장애 조치(failover) 클러스터에 SAP ASCS/SCS 인스턴스 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)에서 ASCS/SCS 및 DBMS에 대한 결합 2노드 Windows 클러스터 도입 
- 2021년 7월 26일: Azure Fence Agent에 대한 설정을 설명하는 섹션에서 RBAC 설명서에 대한 링크로 역할 할당 지침을 바꾸기 위해 [Azure의 RHEL에서 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 및 [Azure의 SLES에서 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 변경
- 2021년 7월 22일: ASCS 클러스터 리소스에 대한 `failure-timeout`을 제거하기 위해(ENSA2만 해당) [SAP 애플리케이션용 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel.md), [ANF를 사용한 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-netapp-files.md) 및 [RHEL 다중 SID 가이드의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-multi-sid.md) 변경
- 2021년 7월 16일: 더 체계적인 탐색을 위해 Azure 설명서 TOC(목차)의 SAP 구조 변경
- 2021년 7월 2일: azacsnap 도구와, HANA Large Instances의 백업 및 복원에 대한 중복 콘텐츠를 제거하기 위해 [HANA Large Instances에서의 SAP HANA 백업 및 복원](./hana-backup-restore.md) 변경
- 2021년 7월 2일: 두 노드 Pacemaker 클러스터와 KB 링크 간의 펜스 경합을 방지하는 방법에 대한 정보를 추가하여`fence_kdump`에 선택적 STONITH 구성을 사용할 때 장애 조치(failover) 지연을 줄이는 방법을 설명하기 위해 [Azure의 RHEL에서 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 변경
- 2021년 7월 1일: [HLI에 사용 가능한 SKU](./hana-available-skus.md)에 새로 인증된 HANA Large Instances SKU 추가 
- 2021년 6월 30일: 권장 SAP 프로필 매개 변수에 대한 섹션을 추가하도록 [WSFC 및 SMB(Azure NetApp Files)를 사용하는 SAP ASCS/SCS에 대한 HA 가이드](./high-availability-guide-windows-netapp-files-smb.md) 변경 
- 2021년 6월 29일: fence_kdump가 포함된 선택적 stonith 구성을 추가하기 위해 [Azure에서 RHEL의 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 문서 변경
- 2021년 6월 28일: SAP 호스트 이름 길이 제한이 발생하지 않도록 SMB 서버(컴퓨터 계정) .접두어가 8자를 초과하지 않아야 한다는 내용을 추가하기 위해 [WSFC 및 SMB(Azure NetApp Files)를 사용하는 SAP ASCS/SCS에 대한 HA 가이드](./high-availability-guide-windows-netapp-files-smb.md) 변경
- 2020년 6월 17일: HANA 리소스 만들기 명령(RHEL 8.x)에서 메타 키워드를 제거하기 위해 [RHEL의 Azure VM에서 SAP HANA 고가용성](./sap-hana-high-availability-rhel.md) 변경
- 2021년 6월 9일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)에서 M192---_v2 대한 VM SKU 이름 수정
- 2021년 5월 26일: [SLES의 Azure VM에서 Pacemaker를 사용한 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-suse.md), [RHEL에서 ANF를 사용한 SAP HANA 스케일 업의 HA](./sap-hana-high-availability-netapp-files-red-hat.md), [RHEL의 Azure VM에서 Pacemaker를 사용한 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) 변경으로 ANF에서 HANA를 실행하기 위한 OS를 준비하는 구성 추가  
- 2021년 5월 13일: [Azure의 SLES에서 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 변경으로 리소스 에이전트 azure-events의 작동 방식을 명확히 함 
- 2021년 4월 30일: [Azure의 SLES에서 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 변경으로 python3-azure-mgmt-compute 패키지 버전(SLES 15)에 Azure Fence 에이전트와 호환되지 않는 변경에 대한 경고 포함  
- 2021년 4월 27일: [WSFC 및 파일 공유를 사용한 SAP ASCS/SCS 인스턴스](./sap-high-availability-guide-wsfc-file-share.md) 변경으로 사전 요구 사항 섹션에 중요한 SAP 메모에 대한 링크 추가
- 2021년 4월 27일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)의 HANA 스토리지 구성에 새 Msv2, Mdsv2 VM 추가
- 2021년 4월 27일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)에서 HSR 구성의 모든 VM에 동일한 스토리지 유형을 HANA 시스템 복제에서 사용하기 위한 요구 사항 추가
- 2021년 4월 27일: [SAP 워크로드에 대한 Azure Storage 형식](./planning-guide-storage.md)에서 DBMS 고가용성 복제 구성의 모든 VM에 동일한 스토리지 유형을 DBMS 복제 시나리오에서 사용하기 위한 요구 사항 추가
- 2021년 4월 23일: [Azure의 Linux용 SAP BusinessObjects BI 플랫폼 배포 지침](businessobjects-deployment-guide-linux.md)에 Azure Database for MySQL에 대한 프라이빗 링크를 구성하는 섹션 및 일부 사소한 변경 내용 추가
- 2021년 4월 22일: Azure의 Windows용 SAP BusinessObjects BI 플랫폼 설명서인 [Windows용 SAP BusinessObjects BI 플랫폼 배포 지침](businessobjects-deployment-guide-windows.md) 릴리스
- 2021년 4월 21일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에서 프리미엄 스토리지 디스크에 대한 읽기 캐시를 사용하도록 설정할 때 M32ts 및 M32ls에 대한 HCMT/HWCCT 스토리지 테스트가 HANA KPI에 부족한 이유에 대한 설명 추가
- 2021년 4월 20일: [SAP 워크로드용 IBM Db2 Azure Virtual Machines DBMS 배포](./dbms_guide_ibm.md) 문서에서 Azure 블록 스토리지가 다른 IBM Db2의 스토리지 블록 크기를 명확히 함
- 2021년 4월 12일: [SLES의 Azure VM에서 SAP HANA에 대한 HA](./sap-hana-high-availability.md), [RHEL의 Azure VM에서 SAP HANA에 대한 HA](./sap-hana-high-availability-rhel.md), [RHEL에서 ANF를 사용한 SAP HANA 스케일 업에 대한 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 변경으로 SAP HANA 시스템 복제 Python 후크에 대한 구성 지침 추가  
- 2021년 4월 12일: SAP HANA용 백업 문서를 [Azure Backup 서비스를 사용한 SAP HANA 백업/복원](../../../backup/sap-hana-db-about.md) 문서로 대체 
- 2021년 4월 12일: [SLES의 Azure VM에서 Pacemaker를 사용한 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-suse.md) 구성 지침 릴리스
- 2021년 4월 7일: [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](./dbms_guide_sqlserver.md)에서 SQL Server 다중 인스턴스 및 다중 데이터베이스 지원을 명확히 함
- 2021년 4월 7일: [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현](./planning-guide.md)에 보조 IP 주소와 관련된 정보 추가
- 2021년 4월 7일: [SAP 워크로드에 대한 Azure Storage 형식](./planning-guide-storage.md)에 ANF에서의 Oracle DBMS 지원에 대한 지원 추가
- 2021년 3월 17일: [SLES의 Azure VM에서 SAP HANA에 대한 HA](./sap-hana-high-availability.md), [RHEL의 Azure VM에서 SAP HANA에 대한 HA](./sap-hana-high-availability-rhel.md) 및 [RHEL에서 ANF를 사용한 SAP HANA 스케일 업에 대한 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 변경으로 Pacemaker 클러스터의 HANA 활성/읽기 가능 시스템 복제에 대한 지침 추가
- 2021년 3월 15일: [WSFC 및 파일 공유를 사용한 SAP ASCS/SCS 인스턴스](./sap-high-availability-guide-wsfc-file-share.md), [WSFC 및 파일 공유를 사용한 SAP ASCS/SCS 인스턴스 설치](./sap-high-availability-installation-wsfc-file-share.md) 및 [WSFC 및 파일 공유를 사용한 SAP ASCS/SCS 다중 SID](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 변경으로 SAP ASCS/SCS 인스턴스와 SOFS 공유는 별개의 클러스터에서 배포되어야 한다는 점을 명확히 함
- 2021년 3월 3일: [WSFC 및 Azure NetApp Files(SMB)를 사용한 SAP ASCS/SCS에 대한 HA 지침](./high-availability-guide-windows-netapp-files-smb.md) 변경으로 SAP 시스템 설치 도중 SWPM을 실행하는 사용자에게는 상승된 권한이 필요하다는 경고문 추가
- 2021년 2월 11일: [Red Hat Enterprise Linux 서버의 Azure VM에서 IBM Db2 LUW의 고가용성](./high-availability-guide-rhel-ibm-db2-luw.md) 변경으로 RHEL 8.x의 Pacemaker 클러스터 명령을 수정
- 2021년 2월 3일: [Azure의 RHEL에서 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 변경으로 STONITH 만들기 명령의 pcmk_host_map 업데이트
- 2021년 2월 3일: [Azure의 SLES에서 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 변경으로 STONITH 만들기 명령의 pcmk_host_map 추가 
- 2021년 2월 3일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에 SUSE에 대한 I/O 스케줄러 설정 관련 상세 내용 추가
- 2021년 2월 1일: [RHEL에서 ANF를 사용한 SAP HANA 스케일 업의 HA](./sap-hana-high-availability-netapp-files-red-hat.md), [RHEL의 Azure VM에서 Pacemaker를 사용한 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md), [SLES에서 ANF를 사용한 Azure VM의 대기 노드를 통한 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md) 및 [RHEL에서 ANF를 사용한 Azure VM의 대기 노드를 통한 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-rhel.md)의 변경으로 [SAP HANA에 대한 Azure NetApp Files의 NFS v4.1 볼륨](./hana-vm-operations-netapp.md) 링크 추가
- 2021년 1월 23일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 및 [SAP HANA에 대한 Azure NetApp Files의 NFS v4.1 볼륨](./hana-vm-operations-netapp.md) 문서에서 HANA 데이터 볼륨 분할의 기능을 디스크 볼륨 관리자를 쓰지 않고도 다른 Azure 디스크나 NFS 공유에서 HANA 데이터 파일에 대해 I/O 작업 스트라이프가 가능한 기능으로 소개
- 2021년 1월 18일: [SAP 워크로드용 Azure Virtual Machines Oracle DBMS 배포](./dbms_guide_oracle.md) 문서에 Oracle용 Azure net Apps Files 기반 NFS 지원을 추가하고 [SAP HANA에 대한 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md) 문서의 표에 적힌 소수 자릿수를 수정
- 2021년 1월 11일: [SAP 애플리케이션용 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel.md), [ANF를 사용한 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-netapp-files.md) 및 [RHEL의 Azure VM에서 SAP NW의 HA 다중 SID 지침](./high-availability-guide-rhel-multi-sid.md) 문서의 사소한 내용을 변경해 RHEL8 및 RHEL7, ENSA1 및 ENSA2에서 작동하는 명령을 조정
- 2021년 1월 5일: [SLES의 ANF를 사용한 Azure VM의 대기 노드를 이용한 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md) 및 [RHEL에서 ANF를 사용한 Azure VM의 대기 노드를 이용한 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-rhel.md)의 내용을 변경해 SAP 호스트 에이전트가 로컬 포트 ​​범위를 관리할 수 ​​있도록 권장 구성 수정  
- 2021년 1월 4일: [Azure의 SAP HANA(대규모 인스턴스) 무엇인가요](./hana-overview-architecture.md) 문서에 HLI가 지원하는 새로운 Azure 지역 추가
- 2020년 12월 29일: [Azure 가용성 영역을 사용하여 SAP 워크로드 구성](./sap-ha-availability-zones.md) 문서에 특정 Azure 지역에 대한 아키텍처 권장 사항을 추가
-  2020년 12월 21일: [HLI용으로 사용 가능한 SKU](./hana-available-skus.md) 문서에 HANA 대규모 인스턴스의 SKU에 대한 새 인증 추가
- 2020년 12월 12일: [Azure 배포를 지원하는 SAP 소프트웨어](./sap-supported-product-on-azure.md#oracle-dbms-support) 문서에 SAP의 Oracle Enterprise Linux 지원을 상세히 설명하는 SAP 노트로의 포인터를 추가
- 2020년 11월 26일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 및 [SAP 워크로드에 대한 Azure Storage 형식](./planning-guide-storage.md) 문서에 변경된 단일 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)를 적용
- 2020년 11월 5일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에서 HANA를 지원하는 파일 시스템 형식에 대한 새 SAP 노트의 링크를 변경 
- 2020년 10월 26일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에서 프로비저닝된 처리량과 버스트된 처리량을 명확히 하기 위한 Azure 프리미엄 스토리지 구성용 표를 일부 변경
- 2020년 10월 22일: [SAP 애플리케이션용 SLES의 Azure VM에서 SAP NW의 HA](./high-availability-guide-suse.md), [ANF를 사용한 SLES의 Azure VM에서 SAP NW의 HA](./high-availability-guide-suse-netapp-files.md), [SAP 애플리케이션용 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel.md) 및 [ANF를 사용한 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-netapp-files.md) 문서의 변경으로 net.ipv4.tcp_keepalive_time에 대한 권장 사항 조정  
- 2020년 10월 16일: [Pacemaker를 사용한 SLES의 Azure VM에서 IBM Db2 LUW의 HA](./dbms-guide-ha-ibm.md), [SAP 애플리케이션용 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel.md), [RHEL의 Azure VM에서 IBM Db2 LUW의 HA](./high-availability-guide-rhel-ibm-db2-luw.md), [RHEL의 Azure VM에서 SAP NW의 HA 다중 SID 지침](./high-availability-guide-rhel-multi-sid.md), [ANF를 사용한 RHEL의 Azure VM에서 SAP NW의 HA](./high-availability-guide-rhel-netapp-files.md), [SAP 애플리케이션용 SLES의 Azure VM에서 SAP NW의 HA](./high-availability-guide-suse.md), [SLES의 Azure VM에서 SAP NW의 HA 다중 SID 지침](./high-availability-guide-suse-multi-sid.md), [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에서 SAP NW의 HA](./high-availability-guide-suse-netapp-files.md), [SLES의 Azure VM에서 NFS의 HA](./high-availability-guide-suse-nfs.md), [SLES의 Azure VM에서 SAP HANA의 HA](./sap-hana-high-availability.md), [RHEL에서 ANF를 사용한 SAP HANA 스케일 업의 HA](./sap-hana-high-availability-netapp-files-red-hat.md), [RHEL의 Azure VM에서 SAP HANA의 HA](./sap-hana-high-availability-rhel.md), [RHEL의 Azure VM에서 Pacemaker를 사용한 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md), [WSFC 및 공유 디스크를 사용한 SAP ASCS/SCS용 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-shared-disk.md), [WSFC 및 Azure 공유 디스크를 사용한 SAP ASCS/SCS용 다중 SID HA 지침](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md) 및 [WSFC 및 공유 디스크를 사용한 SAP ASCS/SCS용 다중 SID HA 지침](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 문서의 변경으로 보조 IP의 부하 분산 시나리오에서는 부동 IP가 지원되지 않는다는 문장 추가 
- 2020년 10월 16일: [HANA 대규모 인스턴스의 SAP HANA 백업 및 복원](./hana-backup-restore.md) 문서에 HANA 대규모 인스턴스의 스토리지 스냅샷 제어 설명서 추가
- 2020년 10월 15일: Azure 설명서 [Azure의 SAP BusinessObjects BI 플랫폼 계획 및 구현 지침](businessobjects-deployment-guide.md) 및 [Azure의 Linux용 SAP BusinessObjects BI 플랫폼 배포 지침](businessobjects-deployment-guide-linux.md)에 SAP BusinessObjects BI 릴리스
- 2020년 10월 5일: [RHEL의 Azure VM에서 Pacemaker를 사용한 SAP HANA 스케일 아웃 HSR](./sap-hana-high-availability-scale-out-hsr-rhel.md) 구성 지침 릴리스
- 2020년 9월 30일: [RHEL의 Azure VM에서 SAP HANA의 고가용성](./sap-hana-high-availability-rhel.md), [RHEL에서 ANF를 사용한 SAP HANA 스케일 업의 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 및 [Azure에서 RHEL의 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 문서를 변경하여 RHEL 8.1 관련 설명 적용
- 2020년 9월 29일: [SAP 애플리케이션을 이용한 최적화 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](./sap-proximity-placement-scenarios.md) 문서에 PPG 사용량을 더 명확하게 하기 위한 제한 사항과 권장 사항을 추가 
- 2020년 9월 28일: [SAP HANA용 Azure NetApp Files의 NFS v4.1 볼륨](./hana-vm-operations-netapp.md) 문서에 Azure NetApp Files를 이용한 SAP HANA용 새 스토리지 작업 지침 추가
- 2020년 9월 23일: [HLI에 사용 가능한 SKU](./hana-available-skus.md) 문서에 HLI용으로 새로 인증된 SKU를 추가 
- 2020년 9월 20일: [SAP 워크로드용 Azure Virtual Machines DBMS 배포의 주의사항](./dbms_guide_general.md), [SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포](./dbms_guide_sqlserver.md), [SAP 워크로드용 Azure Virtual Machines Oracle DBMS 배포](./dbms_guide_oracle.md), [SAP 워크로드용 IBM Db2 Azure Virtual Machines DBMS 배포](./dbms_guide_ibm.md) 문서 변경으로 DBMS 이진 파일과 SAP 이진 파일을 다른 Azure 디스크로 분리하는 새로운 구성 제안을 적용. 또한 다양한 지침에 Ultra Disk 권장 사항을 추가합니다.
- 2020년 9월 8일: [SLES의 Azure VM에서 SAP HANA의 고가용성](./sap-hana-high-availability.md) 문서의 변경으로 STONITH 정의를 명확히 함
- 2020년 9월 3일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서 변경으로 Ultra Disk를 사용하여 1GB 용량 당 최소 2 IOPS로 적용
- 2020년 9월 2일: [HLI에 사용 가능한 SKU](./hana-available-skus.md) 문서의 변경으로 어떤 SKU가 HANA 인증을 받았는지 더 명확히 서술
- 2020년 8월 25일: [ANF를 사용한 SLES의 Azure VM에서 SAP NW의 고가용성](./high-availability-guide-suse-netapp-files.md) 문서 변경으로 오타 수정
- 2020년 8월 25일: [WSFC 및 공유 디스크를 사용한 SAP ASCS/SCS용 고가용성 지침](./sap-high-availability-guide-wsfc-shared-disk.md), [WSFC 및 공유 디스크를 사용한 SAP ASCS/SCS를 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-shared-disk.md) 및 [WSFC 및 공유 디스크를 사용한 SAP NW 고가용성 설치](./sap-high-availability-guide-wsfc-shared-disk.md) 문서 변경으로 Azure 공유 디스크 사용 옵션과 SAP ERS2 아키텍처 설명서 소개 추가
- 2020년 8월 25일: [WSFC 및 Azure 공유 디스크를 사용한 SAP ASCS/SCS에 대한 다중 SID 고가용성 지침](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md) 문서 릴리스
- 2020년 8월 25일: [WSFC 및 Azure NetApp Files(SMB)를 사용한 SAP ASCS/SCS에 대한 고가용성 지침](./high-availability-guide-windows-netapp-files-smb.md), [WSFC 및 파일 공유를 사용한 SAP ASCS/SCS를 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md), [WSFC 및 파일 공유를 사용한 SAP ASCS/SCS를 위한 다중 SID 고가용성 지침](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 및 [WSFC 및 SOFS 파일 공유를 사용한 SAP ASCS/SCS를 위한 다중 SID 고가용성 지침](./sap-ascs-ha-multi-sid-wsfc-file-share.md)문서 내용을 WFC와 공유 디스크를 이용한 SAP ASCS/SCS를 위한 고가용성 지침의 콘텐츠 업데이트와 재구성의 결과로 바꿈 
- 2020년 8월 21일: [HANA 대규모 인스턴스에 호환되는 운영 체제](./os-compatibility-matrix-hana-large-instance.md) 문서에 HLI 유닛 형식 I과 II에 사용 가능한 새 OS 릴리스 정보를 추가
- 2020년 8월 18일: [RHEL의 ANF를 사용한 SAP HANA 스케일 업의 고가용성](./sap-hana-high-availability-netapp-files-red-hat.md) 문서 릴리스
- 2020년 8월 17일: SAP NetWeaver 시스템을 온-프레미스에서 Azure로 이동시키는 Azure Site Recovery 의 이용 정보를 [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현](./planning-guide.md) 문서에 추가
- 08/14/2020: [SAP 워크로드용 IBM Db2 Azure Virtual Machines DBMS 배포](./dbms_guide_ibm.md) 문서에 Db2에 대한 디스크 구성 안내 추가
- 2020년 8월 11일: [HANA 대규모 인스턴스에 호환되는 운영 체제](./os-compatibility-matrix-hana-large-instance.md) 문서에 RHEL 7.6을 HLI 형식 1 유닛에 사용 가능한 운영 체제로 추가
- 2020년 8월 10일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에 비용을 고려한 SAP HANA 스토리지 구성 소개를 추가하고 [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](./sap-deployment-checklist.md) 문서 내용을 약간 업데이트
- 2020년 8월 4일: [Azure SLES에서 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 및 [Azure의 RHEL에서 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 문서를 변경해 Pacemaker 클러스터에 안정적인 이름 확인의 중요성을 강조
- 2020년 8월 4일: [파일 공유를 사용한 WFCS에서 SAP NW 고가용성](./sap-high-availability-installation-wsfc-file-share.md), [공유 디스크를 사용한 WFCS에서 SAP NW 고가용성](./sap-high-availability-installation-wsfc-shared-disk.md), [Azure VM의 SAP NW의 고가용성](./high-availability-guide.md), [SLES의 Azure VM에서 SAP NW의 고가용성](./high-availability-guide-suse.md), [ANF를 사용한 SLES에서 Azure VM의 SAP NW 고가용성](./high-availability-guide-suse-netapp-files.md), [SLES에서 Azure VM의 SAP NW 고가용성 다중 SID 지침](./high-availability-guide-suse-multi-sid.md), [RHEL의 Azure VM에서 SAP NetWeaver의 고가용성](./high-availability-guide-rhel.md), [ANF를 이용한 RHEL의 Azure VM에서 SAP NW 가용성](./high-availability-guide-rhel-netapp-files.md) 및 [RHEL의 Azure VM에서 SAP NW의 고가용성 다중 SID 지침](./high-availability-guide-rhel-multi-sid.md) 문서 변경으로 `enque/encni/set_so_keepalive` 매개 변수 사용을 명확히 함
- 2020년 7월 23일: [Azure 예약을 이용한 SAP HANA(대규모 인스턴스) 비용 절약](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) 문서에 SAP HANA(대규모 인스턴스) 예약 구매 전에 알아야 할 점과 구매하는 방법 설명을 추가
- 2020년 7월 16일: Azure PowerShell을 사용한 새로운 SAP용 VM 확장 기능 설치 방법에 대한 설명을 [배포 지침](deployment-guide.md)에 추가
- 2020년 7월 4일: [SAP 솔루션을 위한 Azure Monitor(미리 보기)](./monitor-sap-on-azure.md) 릴리스
- 2020년 7월 1일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에 Azure 프리미엄 스토리지 버스트 기능을 기반으로 한 더 저렴한 스토리지 구성을 제안 
- 2020년 6월 24일: [Azure에서 SLES의 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 문서를 변경하여 새롭게 향상된 Azure Fence 에이전트와, Azure Fence 에이전트를 기반으로 하는 디바이스 복원력이 강화된 STONITH 구성을 릴리스 
- 2020년 6월 24일: [Azure에서 RHEL의 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 문서를 변경하여 복원력이 강하된 STONITH 구성 릴리스
- 2020년 6월 23일: [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md) 지침 및 [SAP 워크로드에 대한 Azure Storage 유형](./planning-guide-storage.md) 소개 지침을 변경
- 06/22/2020: SAP용 새 VM 확장 기능 설치 단계를 [배포 지침](deployment-guide.md)에 추가
- 2020년 6월 16일: [SAP HA 시나리오에서 Azure 표준 ILB를 사용하여 VM에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md) 문서 변경으로 SUSE 퍼블릭 클라우드 인프라 101 설명서에 대한 링크 추가 
- 2020년 6월 10일: [HLI에 사용 가능한 SKU](./hana-available-skus.md) 및 [SAP HANA(대규모 인스턴스) 스토리지 아키텍처](./hana-storage-architecture.md) 문서에 새로운 HLI SKU 추가
- 2020년 5월 21일: [Azure에서 SLES의 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 및 [Azure에서 RHEL의 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 문서 변경으로 [SAP HA 시나리오에서 Azure 표준 ILB를 사용한 VM의 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md) 문서로 이어지는 링크 추가  
- 2020년 5월 19일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서에 HANA 관련 볼륨에 LVM을 사용하는 경우 루트 볼륨 그룹을 사용하지 말라는 중요 메시지를 추가
- 2020년 5월 19일: [HANA(대규모 인스턴스)와 호환되는 운영체제](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) 문서에 HANA(대규모 인스턴스) 유형 II를 지원하는 새로운 OS를 추가
- 2020년 5월 12일: [SAP HA 시나리오에서 Azure 표준 ILB를 사용하여 VM에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md) 문서 변경으로 링크를 업데이트하고 타사 방화벽 구성에 대한 정보 추가
- 2020년 5월 11일: [SLES의 Azure VM에서 SAP HANA의 고가용성](./sap-hana-high-availability.md) 문서 변경으로 장애 조치(failover)를 더 간소화하도록 `netcat` 리소스에 대한 리소스 연결 유지를 0으로 설정 
- 2020년 5월 5일: [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md) 문서 변경으로 Gen2 배포를 Mv1 VM 제품군에 사용할 수 있음을 표현
- 2020년 4월 24일: [SLES의 ANF를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md), [RHEL의 ANF를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-rhel.md), [ANF를 사용한 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-suse-netapp-files.md) 및 [ANF를 사용한 RHEL의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-rhel-netapp-files.md) 문서 변경으로 ANF 볼륨의 IP 주소는 자동으로 할당됨을 명확하게 설명
- 2020년 4월 22일: 클러스터를 유지 관리 모드 내외에 배치한다는 내용과 충돌하므로 [SLES의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability.md) 문서 내용 변경으로 지침에서 메타 특성 `is-managed`를 제거
- 2020년 4월 21일: SQL Azure DB를 SAP(Hybris) Commerce Platform 1811 이상을 지원하는 DBMS로 [Azure 배포를 지원하는 SAP 소프트웨어](./sap-supported-product-on-azure.md) 및 [Microsoft Azure에서 실행되는 SAP 인증 및 구성](./sap-certifications.md) 문서에 추가
- 2020년 4월 16일: SAP HANA를 SAP(Hybris) Commerce Platform을 지원하는 DBMS로 [Azure 배포를 지원하는 SAP 소프트웨어](./sap-supported-product-on-azure.md) 및 [Microsoft Azure에서 실행되는 SAP 인증 및 구성](./sap-certifications.md) 문서에 추가
- 2020년 4월 13일: [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](./dbms_guide_sapase.md) 문서에서 정확한 SAP ASE 릴리스 번호로 수정
- 2020년 4월 7일: [Azure에서 SLES의 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 문서를 변경하여 cloud-netconfig-azure 지침을 명확하게 설명
- 2020년 4월 6일: [SLES의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md) 및 [RHEL의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-rhel.md) 문서 변경으로 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)에 대한 참조를 제거([TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)로 바뀜)
- 2020년 3월 31일: [SLES의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability.md) 및 [RHEL의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability-rhel.md) 문서 변경으로 스트라이프 볼륨을 만들 때 스트라이프 크기를 지정하는 방법에 대한 지침을 추가
- 2020년 3월 27일: [SAP 애플리케이션용 ANF를 사용한 SLES의 Azure VM에서 SAP NW의 고가용성](./high-availability-guide-suse-netapp-files.md) 문서를 변경하여 파일 시스템 탑재 옵션을 NetApp TR-4746으로 맞춤(동기화 탑재 옵션 제거)
- 2020년 3월 26일: [SLES 다중 SID 가이드의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-suse-multi-sid.md) 문서 변경으로 NetApp TR-4746에 대한 참조 추가
- 2020년 3월 26일: [SAP 애플리케이션용 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-suse.md), [SAP 애플리케이션용 Azure NetApp Files를 사용한 SLES의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-suse-netapp-files.md), [SLES의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md), [RHEL 다중 SID 가이드의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-suse-multi-sid.md), [SAP 애플리케이션용 RHEL의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-rhel.md), [SAP 애플리케이션용 Azure NetApp Files를 사용한 RHEL의 Azure VM에 있는 SAP NetWeaver의 고가용성](./high-availability-guide-rhel-netapp-files.md) 문서 변경으로 다이어그램을 업데이트하고 Azure Load Balancer 백엔드 풀 만들기에 대한 지침을 명확하게 설명
- 2020년 3월 19일: [빠른 시작: Azure Virtual Machines에서 단일 인스턴스 SAP HANA 수동 설치](./hana-get-started.md) 문서를 [Azure Virtual Machines에 SAP HANA 설치](./hana-get-started.md)로 수정
- 2020년 3월 17일: [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](./high-availability-guide-suse-pacemaker.md) 문서에서 더 이상 필요하지 않은 SBD 구성 설정을 제거
- 2020년 3월 16일: [Azure 배포를 지원하는 SAP 소프트웨어](./sap-supported-product-on-azure.md) 문서에서 SAP HANA IaaS 인증 플랫폼의 열 인증 시나리오에 대한 명확한 설명
- 2020년 3월 11일: [Azure 가상 머신의 SAP 워크로드 지원 시나리오](./sap-planning-supported-configurations.md)를 변경하여 DBMS 인스턴스당 여러 데이터베이스를 명확하게 설명
- 2020년 3월 11일: 1세대 및 2세대 VM을 설명하는 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md) 문서 변경
- 2020년 3월 10일: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서 변경으로 ANF의 실제 기존 처리량 한도를 명확히 설명
