---
title: OS, 머신 유형 및 클라우드에 따른 Azure Security Center 기능
description: OS, 컴퓨터 유형 및 클라우드 배포에 따른 Azure Security Center 기능의 가용성에 대해 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/18/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 4bb46ed8146067c44f2d4a602f3c8b1831340d7a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003537"
---
# <a name="feature-coverage-for-machines"></a>머신에 대한 기능 적용 범위

아래 두 탭은 Windows 및 Linux 컴퓨터에서 사용할 수 있는 Azure Security Center 기능을 보여 줍니다.

## <a name="supported-features-for-virtual-machines-and-servers"></a>가상 머신 및 서버에 지원되는 기능<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows 컴퓨터**](#tab/features-windows)

|**기능**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Azure Arc 지원 머신**|**Azure Defender 필요**
|----|:----:|:----:|:----:|:----:|
|[엔드포인트용 Microsoft Defender 통합](security-center-wdatp.md)|✔</br>(지원되는 버전에서만)|✔</br>(지원되는 버전에서만)|✔|예|
|[가상 머신 동작 분석(및 보안 경고)](alerts-reference.md)|✔|✔|✔|예|
|[파일리스 보안 경고](alerts-reference.md#alerts-windows)|✔|✔|✔|예|
|[네트워크 기반 보안 경고](other-threat-protections.md#network-layer)|✔|✔|-|예|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|예|
|[통합 Qualys 취약성 검사기](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|예|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|예|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|예|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|예|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|예|
|[규정 준수 대시보드 및 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|예|
|[Docker 호스트 강화](./harden-docker-hosts.md)|-|-|-|예|
|누락된 OS 패치 평가|✔|✔|✔|Azure: 예<br><br>Azure Arc 지원: 예|
|잘못된 보안 구성 평가|✔|✔|✔|Azure: 예<br><br>Azure Arc 지원: 예|
|[Endpoint Protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: 예<br><br>Azure Arc 지원: 예|
|디스크 암호화 평가|✔</br>([지원되는 시나리오](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)에 해당)|✔|-|아니요|
|제3자 취약성 평가|✔|-|✔|아니요|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|아니요|
||||||

### <a name="linux-machines"></a>[**Linux 컴퓨터**](#tab/features-linux)

| **기능**                                                                                                               | **Azure Virtual Machines**                                                                                      | **Azure Virtual Machine Scale Sets** | **Azure Arc 지원 머신** | **Azure Defender 필요**       |
|---------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------:|:------------------------------------:|:------------------------------:|:---------------------------------:|
| [엔드포인트용 Microsoft Defender 통합](security-center-wdatp.md)                                                   | ✔                                                                                                               | -                                    | ✔                              | 예                              |
| [가상 머신 동작 분석(및 보안 경고)](./azure-defender.md)                                         | ✔</br>(지원되는 버전에서만)                                                                                  | ✔</br>(지원되는 버전에서만)        | ✔                             | 예                               |
| [파일리스 보안 경고](alerts-reference.md#alerts-windows)                                                            | -                                                                                                               | -                                    | -                              | 예                               |
| [네트워크 기반 보안 경고](other-threat-protections.md#network-layer)                                                | ✔                                                                                                              | ✔                                    | -                              | 예                               |
| [Just-in-time VM 액세스](security-center-just-in-time.md)                                                                 | ✔                                                                                                              | -                                    | -                              | 예                               |
| [통합 Qualys 취약성 검사기](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner) | ✔                                                                                                              | -                                    | ✔                             | 예                               |
| [파일 무결성 모니터링](security-center-file-integrity-monitoring.md)                                                 | ✔                                                                                                              | ✔                                    | ✔                             | 예                               |
| [적응 애플리케이션 컨트롤](security-center-adaptive-application.md)                                                  | ✔                                                                                                              | -                                    | ✔                             | 예                               |
| [네트워크 맵](security-center-network-recommendations.md#network-map)                                                     | ✔                                                                                                              | ✔                                    | -                              | 예                               |
| [적응형 네트워크 강화](security-center-adaptive-network-hardening.md)                                               | ✔                                                                                                              | -                                    | -                              | 예                               |
| [규정 준수 대시보드 및 보고서](security-center-compliance-dashboard.md)                                      | ✔                                                                                                              | ✔                                    | ✔                             | 예                               |
| [Docker 호스트 강화](./harden-docker-hosts.md)                                                                         | ✔                                                                                                              | ✔                                    | ✔                             | 예                               |
| 누락된 OS 패치 평가                                                                                             | ✔                                                                                                              | ✔                                    | ✔                             | Azure: 예<br><br>Azure Arc 지원: 예 |
| 잘못된 보안 구성 평가                                                                                     | ✔                                                                                                              | ✔                                    | ✔                             | Azure: 예<br><br>Azure Arc 지원: 예 |
| [Endpoint Protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)                    | -                                                                                                               | -                                    | -                              | 아니요                                |
| 디스크 암호화 평가                                                                                                | ✔</br>([지원되는 시나리오](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)에 해당) | ✔                                    | -                              | 아니요                                |
| 제3자 취약성 평가                                                                                      | ✔                                                                                                              | -                                    | ✔                             | 아니요                                |
| [네트워크 보안 평가](security-center-network-recommendations.md)                                                 | ✔                                                                                                              | ✔                                    | -                              | 아니요                                |
||||||

--- 


> [!TIP]
>Azure Defender에서만 사용할 수 있는 기능을 실험하려면 30일 평가판에 등록할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.


## <a name="supported-endpoint-protection-solutions"></a>지원되는 Endpoint Protection 솔루션 <a name="endpoint-supported"></a>

아래 표에는 다음 정보의 행렬이 나와 있습니다.

 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이 목록에서 Endpoint Protection 솔루션이 검색되더라도 Security Center에 솔루션을 설치하는 것이 좋다는 메시지가 표시되지는 않습니다.

이러한 각 보호에 대한 권장 사항이 생성되는 경우에 대한 자세한 내용은 [Endpoint Protection 평가 및 권장 사항](security-center-endpoint-protection.md)을 참조하세요.

| 솔루션                                                  | 지원 플랫폼                                    | Security Center 설치 | Security Center 검색 |
|-----------------------------------------------------------|--------------------------------------------------------|------------------------------|---------------------------|
| Microsoft Defender 바이러스 백신                              | Windows Server 2016 이상                           | 아니요(OS에 내장)           | 예                       |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2                                 | 확장을 통해                | 예                       |
| Trend Micro – Deep Security                               | Windows Server(전체)                                   | 예                           | 예                       |
| Symantec v12.1.1100+                                      | Windows Server(전체)                                   | 예                           | 예                       |
| McAfee v10+                                               | Windows Server(전체)                                   | 예                           | 예                       |
| McAfee v10+                                               | Linux(미리 보기)                                        | 예                           | 예                       |
| Linux의 엔드포인트용 Microsoft Defender<sup>[1](#footnote1)</sup>  | Linux(미리 보기)                                        | 확장을 통해                | No                        |  
| Sophos V9+                                                | Linux(미리 보기)                                        | 예                           | 예                       |
|                                                           |                                                        |                              |                           |

<sup><a name="footnote1" /></a>1</sup> Linux 머신에 엔드포인트용 Microsoft Defender를 사용하는 것만으로는 충분하지 않습니다. AV 구성 요소가 활성 상태인 경우에만 컴퓨터가 정상으로 표시됩니다.
기본적으로 AV 구성 요소는 다른 AV 소프트웨어와의 충돌을 방지하기 위해 **비활성화** 되어 있습니다.




## <a name="feature-support-in-government-and-sovereign-clouds"></a>정부 및 소버린 클라우드의 기능 지원

| 기능/서비스                                                                                                                                           | Azure          | Azure Government               | Azure 중국 21Vianet   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Security Center 무료 기능**                                                                                                                         |                |                                |               |
| - [연속 내보내기](./continuous-export.md)                                                                                                             | GA             | GA                             | GA            |
| - [워크플로 자동화](./continuous-export.md)                                                                                                           | GA             | GA                             | GA            |
| - [권장 사항 제외 규칙](./exempt-resource.md)                                                                                                  | 공개 미리 보기 | 사용할 수 없음                  | 사용할 수 없음 |
| - [경고 비표시 규칙](./alerts-suppression-rules.md)                                                                                                | GA             | GA                             | GA            |
| - [보안 경고에 대한 이메일 알림](./security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | GA            |
| - [에이전트 및 확장에 대한 자동 프로비저닝](./security-center-enable-data-collection.md)                                                              | GA             | GA                             | GA            |
| - [자산 인벤토리](./asset-inventory.md)                                                                                                                 | GA             | GA                             | GA            |
| - [Azure Security Center 통합 문서 갤러리의 Azure Monitor 통합 문서 보고서](./custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | GA            |
| **Azure Defender 계획 및 확장**                                                                                                                   |                |                                |               |
| - [서버용 Azure Defender](./defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | GA            |
| - [App Service용 Azure Defender](./defender-for-app-service-introduction.md)                                                                            | GA             | 사용할 수 없음                  | 사용할 수 없음 |
| - [Azure Defender for DNS](./defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | GA            |
| - [컨테이너 레지스트리용 Azure Defender](./defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [CI/CD 워크플로에 있는 이미지의 컨테이너 레지스트리 검사를 위한 Azure Defender](./defender-for-container-registries-cicd.md) <sup>[3](#footnote3)</sup> | 공개 미리 보기 | 사용할 수 없음                  | 사용할 수 없음 |
| - [Azure Defender for Kubernetes](./defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [Azure Arc 지원 Kubernetes 클러스터용 Azure Defender 확장](./defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | 공개 미리 보기 | 사용할 수 없음                  | 사용할 수 없음 |
| - [Azure SQL 데이터베이스 서버용 Azure Defender](./defender-for-sql-introduction.md)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [머신의 Azure Defender for SQL 서버](./defender-for-sql-introduction.md)                                                                        | GA             | GA                             | 사용할 수 없음 |
| - [오픈 소스 관계형 데이터베이스용 Azure Defender](./defender-for-databases-introduction.md)                                                         | GA             | 사용할 수 없음                  | 사용할 수 없음 |
| - [Key Vault용 Azure Defender](./defender-for-key-vault-introduction.md)                                                                                | GA             | 사용할 수 없음                  | 사용할 수 없음 |
| - [Azure Defender for Resource Manager](./defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             | GA            |
| - [스토리지용 Azure Defender](./defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | 사용할 수 없음 |
| - [Cosmos DB에 대한 위협 방지](./other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | 공개 미리 보기 | 사용할 수 없음                  | 사용할 수 없음 |
| - [Kubernetes 워크로드 보호](./kubernetes-workload-protections.md)                                                                                  | GA             | GA                             | GA            |
| - [Sentinel과 양방향 경고 동기화](../sentinel/connect-azure-security-center.md)                                                      | 공개 미리 보기 | 사용할 수 없음                  | 사용할 수 없음 |
| **서버용 Azure Defender 기능** <sup>[7](#footnote7)</sup>                                                                                        |                |                                |               |
| - [Just-in-Time VM 액세스](./security-center-just-in-time.md)                                                                                             | GA             | GA                             | GA            |
| - [파일 무결성 모니터링](./security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             | GA            |
| - [적응형 애플리케이션 제어](./security-center-adaptive-application.md)                                                                              | GA             | GA                             | GA            |
| - [적응형 네트워크 강화](./security-center-adaptive-network-hardening.md)                                                                           | GA             | 사용할 수 없음                  | 사용할 수 없음 |
| - [Docker 호스트 강화](./harden-docker-hosts.md)                                                                                                       | GA             | GA                             | GA            |
| - [통합 Qualys 취약성 검사기](./deploy-vulnerability-assessment-vm.md)                                                             | GA             | 사용할 수 없음                  | 사용할 수 없음 |
| - [규정 준수 대시보드 및 보고서](./security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [엔드포인트용 Microsoft Defender 배포 및 통합 라이선스](./security-center-wdatp.md)                                                         | GA             | GA                             | 사용할 수 없음 |
| - [AWS 계정 연결](./quickstart-onboard-aws.md)                                                                                                      | GA             | 사용할 수 없음                  | 사용할 수 없음 |
| - [GCP 계정 연결](./quickstart-onboard-gcp.md)                                                                                                      | GA             | 사용할 수 없음                  | 사용할 수 없음 |
|                                                                                                                                                           |                |                                |               |

<sup><a name="footnote1" /></a>1</sup> 부분 GA: 취약성 검색에서 특정 결과를 사용하지 않도록 설정하는 기능은 공개 미리 보기로 제공됩니다.

<sup><a name="footnote2" /></a>2</sup> Azure Government 클라우드의 컨테이너 레지스트리에 대한 취약성 검색은 푸시 시 검사 기능을 통해서만 수행할 수 있습니다.

<sup><a name="footnote3" /></a>3</sup> 컨테이너 레지스트리용 Azure Defender가 필요합니다.

<sup><a name="footnote4" /></a>4</sup> 부분 GA: Azure Arc 지원 클러스터에 대한 지원은 공개 미리 보기로 제공되며 Azure Government에서 사용할 수 없습니다.

<sup><a name="footnote5" /></a>5</sup> Azure Defender for Kubernetes가 필요합니다.

<sup><a name="footnote6" /></a>6</sup> 부분 GA: Azure Defender for Storage의 일부 위협 방지 경고는 공개 미리 보기로 제공됩니다.

<sup><a name="footnote7" /></a>7</sup> 이러한 기능에는 모두 [서버용 Azure Defender](./defender-for-servers-introduction.md)가 필요합니다.

<sup><a name="footnote8" /></a>8</sup> 클라우드 유형별로 제공되는 표준에 차이가 있을 수 있습니다.
 
<sup><a name="footnote9" /></a>9</sup> 부분 GA: SQL Server에 대한 경고 및 취약성 평가의 하위 집합입니다. 동작 위협 보호를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Security Center에서 Log Analytics 에이전트를 사용하여 데이터를 수집](security-center-enable-data-collection.md)하는 방법에 대해 알아봅니다.
- [Security Center에서 데이터를 관리하고 보호](security-center-data-security.md)하는 방법을 알아봅니다.
- [Security Center를 지원하는 플랫폼](security-center-os-coverage.md)을 검토합니다.
