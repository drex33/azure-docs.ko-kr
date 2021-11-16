---
title: 미국 정부 고객을 위한 Azure 서비스 클라우드 기능 가용성
description: 미국 정부 고객을 위한 Microsoft 센티널와 같은 Azure 보안 서비스의 기능 가용성을 나열 합니다.
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 9d2b984d78abd09e1fe4baf7e0c443e83ad8dc13
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523109"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>미국 정부 고객을 위한 클라우드 기능 가용성

이 문서는 다음 보안 서비스에 대한 Microsoft Azure 및 Azure Government 클라우드의 기능 가용성을 설명합니다.

- [Azure Information Protection](#azure-information-protection)
- [Microsoft Defender for Cloud](#microsoft-defender-for-cloud)
- [Microsoft Sentinel](#microsoft-sentinel)
- [Microsoft Defender for IoT](#microsoft-defender-for-iot)
- [Azure Attestation](#azure-attestation)

> [!NOTE]
> 이 문서에 추가 보안 서비스가 곧 추가될 예정입니다.
>

## <a name="azure-government"></a>Azure Government

Azure Government는 Azure(때때로 Azure Commercial 또는 Azure Public이라고 부름)와 동일한 기본 기술을 사용합니다. 여기에는 IaaS(Infrastructure as a Service), PaaS(Platform as a Service) 및 SaaS(Software as a Service)를 포함하고 있습니다. Azure와 Azure Government는 모두 포괄적인 보안 컨트롤을 보유하고 있으며, 고객 데이터 보호를 위한 Microsoft 약정이 적용됩니다.

Azure Government는 미국 연방, 주, 지방, 트라이벌 정부 및 각각의 파트너 전용 물리적으로 격리된 클라우드 환경입니다. 두 클라우드 환경은 FedRAMP High 영향 수준에서 평가되고 권한이 부여되는 반면, Azure Government는 미국의 고객 데이터 저장소와 관련된 계약 약정을 통해 고객에게 추가 보호 계층을 제공하고 고객 데이터를 처리하는 시스템에 대한 잠재적 액세스를 검열된 미국 사람으로 제한합니다. 이러한 약정은 클라우드를 사용하여 EAR, ITAR 및 DoE 10 CFR Part 810과 같은 미국 수출 통제 규정에 따라 데이터를 저장하거나 처리하는 고객에게 유용할 수 있습니다.

Azure Government에 대한 자세한 내용은 [Azure Government란?](../../azure-government/documentation-government-welcome.md)을 참조하세요.

## <a name="microsoft-365-integration"></a>Microsoft 365 통합

제품 간 통합에는 Azure와 Office 플랫폼 간의 상호 운용성이 중요합니다. Azure 환경에서 호스트되는 제품은 Microsoft 365 Enterprise 및 Microsoft 365 Government 플랫폼에서 액세스할 수 있습니다. Office 365와 Office 365 GCC는 Azure의 Azure Active Directory(Azure AD)와 페어링됩니다. Office 365 GCC High와 Office 365 DoD는 Azure Government에서 Azure AD와 페어링됩니다.

다음 다이어그램은 Microsoft 클라우드의 계층 구조와 이러한 계층 구조가 서로 어떻게 관련되는지를 보여 줍니다.

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Microsoft 365 클라우드 통합":::

Office 365 GCC 환경은 고객이 FedRAMP High, CJIS, IRS 1075 등의 미국 정부 요구 사항을 준수하는 데 도움이 됩니다. Office 365 GCC High 및 DoD 환경은 DoD IL4/5, DFARS 7012, NIST 800-171 및 ITAR을 준수해야 하는 고객을 지원합니다.

Office 365 US Government 환경에 대한 자세한 내용은 다음을 참조하세요.

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High 및 DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)

다음 섹션에서는 서비스가 Microsoft 365와 통합된 경우가 언제인지 그리고 Office 365 GCC, Office 365 High 및 Office 365 DoD의 기능 가용성을 식별합니다.

## <a name="azure-information-protection"></a>Azure Information Protection

AIP(Azure Information Protection)는 조직에서 문서와 이메일의 콘텐츠에 레이블을 지정하여 검색, 분류 및 보호할 수 있도록 지원하는 클라우드 기반 솔루션입니다.

AIP는 MIP(Microsoft Information Protection) 솔루션의 일부이며 Microsoft 365에서 제공하는 [레이블 지정](/microsoft-365/compliance/sensitivity-labels) 및 [분류](/microsoft-365/compliance/data-classification-overview) 기능을 확장합니다.

자세한 내용은 [Azure Information Protection 제품 설명서](/azure/information-protection/)를 참조 하십시오.

- Office 365 GCC는 Azure에서 Azure Active Directory(Azure AD)와 페어링됩니다. Office 365 GCC High와 Office 365 DoD는 Azure Government에서 Azure AD와 페어링됩니다. [상호 운용이 가능한](#microsoft-365-integration) 위치를 이해하려면 Azure 환경에 주의해야 합니다. 다음 표에서는 *지원되지 않는* 상호 운용성을 대시(-)로 표시하여 지원 관계가 없음을 나타낼 수 있습니다.

- GCC-High 및 DoD 고객에 대 한 추가 구성이 필요 합니다. 자세한 내용은 [Azure Information Protection Premium 정부 서비스 설명](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description)을 참조 하세요.

> [!NOTE]
> 정부 고객에 대 한 지원에 대 한 자세한 내용은 표 아래의 각주에 나와 있습니다.
>
> GCC High 및 DoD 고객에 대 한 Azure Information Protection를 구성 하려면 추가 단계가 필요 합니다. 자세한 내용은 [Azure Information Protection Premium 정부 서비스 설명](/enterprise-mobility-security/solutions/ems-aip-premium-govt-service-description)을 참조 하세요.
>

|기능/서비스  |Azure  |Azure Government  |
|---------|---------|---------|
|**[Azure Information Protection 스캐너](/azure/information-protection/deploy-aip-scanner)** <sup> [1](#aipnote1)</sup>       |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|**관리**     |         |         |
|[스캐너 관리용 Azure Information Protection 포털](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)     |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **분류 및 레이블** 지정 <sup> [2](#aipnote2)</sup>   |         |         |
| [온-프레미스 파일 서버/리포지토리의 모든 파일에 *기본 레이블을* 적용 하는 aip 스캐너](/azure/information-protection/deploy-aip-scanner-configure-install?tabs=azure-portal-only)    |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| [지원 되는 온-프레미스 파일의 자동화 된 분류, 레이블 지정 및 보호를 위한 AIP 스캐너](/azure/information-protection/deploy-aip-scanner)    |         |         |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| |  |  |

<sup> <a name="aipnote1"></a> 1</sup> 스캐너가 파일을 스캔 하는 Office 365 없이 작동할 수 있습니다. 스캐너가 Office 365 없이 파일에 레이블을 적용할 수 없습니다.

<sup> <a name="aipnote2"></a> 2</sup> 분류 및 레이블 지정 추가 기능은 Professional 및 (ProPlus) 및 C2R (클릭 하 여 실행) 버전을 포함 하 여 Microsoft 365 앱 (버전 9126.1001 이상)를 사용 하는 정부 고객만 지원 합니다. Office 2010, Office 2013 및 기타 Office 2016 버전은 지원되지 않습니다.

### <a name="office-365-features"></a>Office 365 기능

|기능/서비스  |Office 365 GCC  |Office 365 GCC High |Office 365 DoD  |
|---------|---------|---------|---------|
|**관리**     |         |         | |
|- [RMS 서비스 관리용 PowerShell](/powershell/module/aipservice/)      |  GA       |    GA     |   GA      |
|- [AIP UL 용 PowerShell 클라이언트 대량 작업](/powershell/module/azureinformationprotection/)      |         |         |         |
|**SDK**     |         |         |         |
|- [밉 및 AIP SDK (소프트웨어 개발 키트)](/information-protection/develop/)     |     GA       |    GA     |   GA  |
|**사용자 지정**     |         |         |         |
|- [문서 추적 및 해지](/azure/information-protection/rms-client/track-and-revoke-admin)      |   GA      |  사용할 수 없음       |     사용할 수 없음    |
|**키 관리**      |         |         |         |
|- [Bring Your Own Key(BYOK)](/azure/information-protection/byok-price-restrictions)      |   GA       |    GA     |   GA   |
|- [DKE(이중 키 암호화)](/azure/information-protection/plan-implement-tenant-key)     |    GA       |    GA     |   GA    |
|**Office 파일** <sup> [3](#aipnote6)</sup>      |         |         |         |
|- [비즈니스용 Microsoft Exchange Online, Microsoft Office SharePoint Online 및 Microsoft OneDrive 보호](/azure/information-protection/requirements-applications)      |     GA    |  GA <sup> [4](#aipnote3)</sup>       |   GA <sup> [4](#aipnote3)</sup>      |
|- [Rights Management 커넥터를 통해 온-프레미스 Exchange 및 SharePoint 콘텐츠 보호](/azure/information-protection/deploy-rms-connector)     |    GA <sup> [5](#aipnote5)</sup>      |  사용할 수 없음       |     사용할 수 없음         |
|- [Office 365 메시지 암호화](/microsoft-365/compliance/set-up-new-message-encryption-capabilities)      |     GA       |    GA     |   GA        |
|- [Outlook 미리 구성된 M/MIME 보호를 자동으로 적용하도록 레이블 설정](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)      |         GA       |    GA     |   GA        |
|- [Outlook 사용하는 경우 정보의 oversharing 제어](/azure/information-protection/rms-client/clientv2-admin-guide-customizations)     |      GA   |  GA <sup> [6](#aipnote6)</sup>        |    GA <sup> [6](#aipnote6)</sup>      |
|**분류 및 레이블** <sup> [2](#aipnote2)  /  [7](#aipnote7)</sup>      |         |         |         |
|- 부서 템플릿을 포함한 사용자 지정 템플릿     |     GA       |    GA     |   GA         |
|- 수동, 기본 및 필수 문서 분류     |       GA       |    GA     |   GA       |
|- 자동 및 권장 분류 GA에 대한 조건 구성       |    GA     |   GA        |
|- [PTXT, PJPG 및 PFILE(일반 보호)을 포함하여 Microsoft Office 이외의 파일 형식에 대한 보호](/azure/information-protection/rms-client/clientv2-admin-guide-file-types)     |        GA       |    GA     |   GA       |
|     |         |         |         |

<sup> <a name="aipnote3"></a> 3</sup> AD RMS 모바일 디바이스 확장은 현재 정부 고객에게 제공되지 않습니다.

<sup> <a name="aipnote4"></a> 4</sup> SharePoint Online(IRM으로 보호된 사이트 및 라이브러리)과 함께 Rights Management 정보는 현재 사용할 수 없습니다.

<sup> <a name="aipnote5"></a> 5</sup> IRM(정보 Rights Management)은 Professional Plus(ProPlus) 및 C2R(간편 실행) 버전을 포함하여 Microsoft 365 앱(버전 9126.1001 이상)에서만 지원됩니다. Office 2010, Office 2013 및 기타 Office 2016 버전은 지원되지 않습니다.

<sup> <a name="aipnote6"></a> 6</sup> 현재 정부 클라우드에서 상용 클라우드의 사용자로 보호된 문서 및 메일을 공유할 수 없습니다. 상용 클라우드의 Microsoft 365 앱 사용자, 상용 클라우드의 Microsoft 365 앱 사용자 및 개인용 RMS 라이선스가 있는 사용자를 포함합니다.

<sup> <a name="aipnote7"></a> 7</sup> Microsoft 365 Security & Compliance Center의 [중요한 정보 유형](/microsoft-365/compliance/sensitive-information-type-entity-definitions) 수는 지역에 따라 달라질 수 있습니다.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

Microsoft Defender for Cloud는 데이터 센터의 보안 태세를 강화하고 온-프레미스뿐만 아니라 Azure에 있든 아니든 클라우드의 하이브리드 워크로드에서 고급 위협 방지 기능을 제공하는 통합 인프라 보안 관리 시스템입니다.

자세한 내용은 Microsoft [Defender for Cloud 제품 설명서를 참조하세요.](../../defender-for-cloud/defender-for-cloud-introduction.md)

다음 표에서는 Azure 및 Azure Government 현재 Defender for Cloud 기능 가용성을 표시합니다.

| 기능/서비스                                                                                                                                                                      | Azure          | Azure Government               |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **클라우드용 Microsoft Defender 무료 기능**                                                                                                                                       |                |                                |
| <li> [연속 내보내기](../../defender-for-cloud/continuous-export.md)                                                                                                                 | GA             | GA                             |
| <li> [워크플로 자동화](../../defender-for-cloud/workflow-automation.md)                                                                                                               | GA             | GA                             |
| <li> [권장 사항 제외 규칙](../../defender-for-cloud/exempt-resource.md)                                                                                                      | 공개 미리 보기 | 사용할 수 없음                  |
| <li> [경고 비표시 규칙](../../defender-for-cloud/alerts-suppression-rules.md)                                                                                                    | GA             | GA                             |
| <li> [보안 경고에 대한 이메일 알림](../../defender-for-cloud/configure-email-notifications.md)                                                            | GA             | GA                             |
| <li> [에이전트 및 확장에 대한 자동 프로비저닝](../../defender-for-cloud/enable-data-collection.md)                                                                  | GA             | GA                             |
| <li> [자산 인벤토리](../../defender-for-cloud/asset-inventory.md)                                                                                                                     | GA             | GA                             |
| <li> [Microsoft Defender for Cloud의 통합 문서 갤러리에 있는 Azure Monitor 통합 문서 보고서](../../defender-for-cloud/custom-dashboards-azure-workbooks.md)                               | GA             | GA                             |
| **Microsoft Defender 계획 및 확장**                                                                                                                                          |                |                                |
| <li> [서버용 Microsoft Defender](../../defender-for-cloud/defender-for-servers-introduction.md)                                                                                    | GA             | GA                             |
| <li> [Microsoft Defender for App Service](../../defender-for-cloud/defender-for-app-service-introduction.md)                                                                            | GA             | 사용할 수 없음                  |
| <li> [Microsoft Defender for DNS](../../defender-for-cloud/defender-for-dns-introduction.md)                                                                                            | GA             | GA                             |
| <li> [컨테이너 레지스트리용 Microsoft Defender](../../defender-for-cloud/defender-for-container-registries-introduction.md) <sup> [1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> |
| <li> [CI/CD 워크플로에서 이미지를 검사하는 컨테이너 레지스트리용 Microsoft Defender](../../defender-for-cloud/defender-for-container-registries-cicd.md) <sup> [3](#footnote3)</sup> | 공개 미리 보기 | 사용할 수 없음                  |
| <li> [Microsoft Defender for Kubernetes](../../defender-for-cloud/defender-for-kubernetes-introduction.md) <sup> [4](#footnote4)</sup>                                                   | GA             | GA                             |
| <li> [Azure Arc 용 Defender 확장 사용 Kubernetes 클러스터](../../defender-for-cloud/defender-for-kubernetes-azure-arc.md) <sup> [5](#footnote5)</sup>                           | 공개 미리 보기 | 사용할 수 없음                  |
| <li> [Microsoft Defender for Azure SQL 데이터베이스 서버](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                     | GA             | GA                             |
| <li> [머신의 Microsoft Defender for SQL 서버](../../defender-for-cloud/defender-for-sql-introduction.md)                                                                        | GA             | GA                             |
| <li> [오픈 소스 관계형 데이터베이스용 Microsoft Defender](../../defender-for-cloud/defender-for-databases-introduction.md)                                                         | GA             | 사용할 수 없음                  |
| <li> [Microsoft Defender for Key Vault](../../defender-for-cloud/defender-for-key-vault-introduction.md)                                                                                | GA             | 사용할 수 없음                  |
| <li> [Microsoft Defender for Resource Manager](../../defender-for-cloud/defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             |
| <li> [Storage 6 용 Microsoft Defender](../../defender-for-cloud/defender-for-storage-introduction.md) <sup> [](#footnote6)</sup>                                                         | GA             | GA                             |
| <li> [Cosmos DB에 대한 위협 방지](../../defender-for-cloud/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                              | 공개 미리 보기 | 사용할 수 없음                  |
| <li> [Kubernetes 워크로드 보호](../../defender-for-cloud/kubernetes-workload-protections.md)                                                                                      | GA             | GA                             |
| <li> [Sentinel과 양방향 경고 동기화](../../sentinel/connect-azure-security-center.md)                                                                           | 공개 미리 보기 | 사용할 수 없음                  |
| **서버용 Microsoft Defender 기능** <sup>[7](#footnote7)</sup>                                                                                                               |                |                                |
| <li> [Just-in-time VM 액세스](../../defender-for-cloud/just-in-time-access-overview.md)                                                                                                 | GA             | GA                             |
| <li> [파일 무결성 모니터링](../../defender-for-cloud/file-integrity-monitoring-overview.md)                                                                                 | GA             | GA                             |
| <li> [적응 애플리케이션 컨트롤](../../defender-for-cloud/adaptive-application-controls.md)                                                                                  | GA             | GA                             |
| <li> [적응형 네트워크 강화](../../defender-for-cloud/adaptive-network-hardening.md)                                                                               | GA             | 사용할 수 없음                  |
| <li> [Docker 호스트 강화](../../defender-for-cloud/harden-docker-hosts.md)                                                                                                           | GA             | GA                             |
| <li> [머신의 통합 취약성 평가](../../defender-for-cloud/deploy-vulnerability-assessment-vm.md)                                                                 | GA             | 사용할 수 없음                  |
| <li> [규정 준수 대시보드 & 보고서](../../defender-for-cloud/regulatory-compliance-dashboard.md) <sup> [8](#footnote8)</sup>                                           | GA             | GA                             |
| <li> [엔드포인트용 Microsoft Defender 배포 및 통합 라이선스](../../defender-for-cloud/integration-defender-for-endpoint.md)                                                             | GA             | GA                             |
| <li> [AWS 계정 연결](../../defender-for-cloud/quickstart-onboard-aws.md)                                                                                                          | GA             | 사용할 수 없음                  |
| <li> [GCP 계정 연결](../../defender-for-cloud/quickstart-onboard-gcp.md)                                                                                                          | GA             | 사용할 수 없음                  |
|                                                                                                                                                                                      |                |                                |

<sup><a name="footnote1"></a>1</sup> 부분 GA: 취약성 검색에서 특정 결과를 사용하지 않도록 설정하는 기능은 공개 미리 보기로 제공됩니다.

<sup><a name="footnote2"></a>2</sup> Azure Gov의 컨테이너 레지스트리에 대한 취약성 검색은 푸시 시 검색 기능을 통해서만 수행할 수 있습니다.

<sup><a name="footnote3"></a>3</sup> 컨테이너 레지스트리용 Microsoft Defender가 필요합니다.

<sup><a name="footnote4"></a>4</sup> 부분 GA: Azure Arc 지원 클러스터에 대한 지원은 공개 미리 보기로 제공되며 Azure Government에서 사용할 수 없습니다.

<sup><a name="footnote5"></a>5</sup> Microsoft Defender for Kubernetes가 필요합니다.

<sup><a name="footnote6"></a>6</sup> 부분 GA: Microsoft Defender for Storage의 일부 위협 방지 경고는 공개 미리 보기로 제공됩니다.

<sup><a name="footnote7"></a>7</sup> 이러한 기능에는 모두 [서버용 Microsoft Defender](../../defender-for-cloud/defender-for-servers-introduction.md)가 필요합니다.

<sup><a name="footnote8"></a>8</sup> 클라우드 유형별로 제공되는 표준에 차이가 있을 수 있습니다.

<a name="azure-sentinel"></a>

## <a name="microsoft-sentinel"></a>Microsoft Sentinel

Microsoft 센티널은 확장 가능한 클라우드 기본, SIEM (보안 정보 이벤트 관리) 및 대화 충성도 (보안 오케스트레이션 자동화 된 응답) 솔루션입니다. Microsoft 센티널은 엔터프라이즈 전체에서 지능형 보안 분석과 위협 인텔리전스를 제공 하 여 경고 검색, 위협 가시성, 자동 관리 검색 및 위협 대응을 위한 단일 솔루션을 제공 합니다.

자세한 내용은 [Microsoft 센티널 제품 설명서](../../sentinel/overview.md)를 참조 하십시오.

다음 표에서는 Azure 및 Azure Government의 현재 Microsoft 센티널 기능 가용성을 보여 주는 표를 제공 합니다.

| 기능 | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [자동화 규칙](../../sentinel/automate-incident-handling-with-automation-rules.md) | 공개 미리 보기 | 공개 미리 보기 |
|- [Bring Your Own ML(BYO-ML)](../../sentinel/bring-your-own-ml.md) | 공개 미리 보기 | 공개 미리 보기 |
| - [테넌트 간/작업 영역 간 인시던트 보기](../../sentinel/multiple-workspace-view.md) |공개 미리 보기 | 공개 미리 보기 |
| - [엔터티 인사이트](../../sentinel/enable-entity-behavior-analytics.md) | GA | 공개 미리 보기 |
| - [퓨전](../../sentinel/fusion.md)<br>고급 다단계 공격 탐지<sup>[1](#footnote1)</sup> | GA | GA |
| - [헌팅](../../sentinel/hunting.md) | GA | GA |
|- [Notebooks](../../sentinel/notebooks.md) | GA | GA |
|- [SOC 인시던트 감사 메트릭](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [관심 목록](../../sentinel/watchlists.md) | GA | GA |
| **위협 인텔리전스 지원** | | |
| - [위협 인텔리전스 - TAXII 데이터 커넥터](../../sentinel/understand-threat-intelligence.md)  | GA | GA |
| - [위협 인텔리전스 플랫폼 데이터 페이지](../../sentinel/understand-threat-intelligence.md)  | 공개 미리 보기 | 사용할 수 없음 |
| - [위협 인텔리전스 리서치 블레이드](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | GA | GA |
| - [URL 데토네이션](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | 공개 미리 보기 | 사용할 수 없음 |
| - [위협 인텔리전스 통합 문서](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | GA |
|**검색 지원** | | |
| - [비정상 Windows 파일 공유 액세스 감지](../../sentinel/fusion.md)  | 공개 미리 보기 | 사용할 수 없음 |
| - [비정상 RDP 로그인 검색](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>기본 제공 ML 검색 | 공개 미리 보기 | 사용할 수 없음 |
| - [비정상적인 SSH 로그인 검색](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>기본 제공 ML 검색 | 공개 미리 보기 | 사용할 수 없음 |
| **Azure 서비스 커넥터** |  |  |
| - [Azure 활동 로그](../../sentinel/data-connectors-reference.md#azure-activity) | GA | GA |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | GA | GA |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | GA | GA |
| - [Azure DDoS Protection](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | GA | GA |
| - [클라우드 용 Microsoft Defender](../../sentinel/connect-azure-security-center.md) | GA | GA |
| - [IoT 용 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-iot) | 공개 미리 보기 | 사용할 수 없음 |
| - [Azure Firewall](../../sentinel/data-connectors-reference.md#azure-firewall) | GA | GA |
| - [Azure Information Protection](../../sentinel/data-connectors-reference.md#azure-information-protection) | 공개 미리 보기 | 사용할 수 없음 |
| - [Azure Key Vault ](../../sentinel/data-connectors-reference.md#azure-key-vault) | 공개 미리 보기 | 사용할 수 없음 |
| - [AKS(Azure Kubernetes Services)](../../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks) | 공개 미리 보기 | 사용할 수 없음 |
| - [Azure SQL 데이터베이스](../../sentinel/data-connectors-reference.md#azure-sql-databases) | GA | GA |
| - [Azure WAF](../../sentinel/data-connectors-reference.md#azure-web-application-firewall-waf) | GA | GA |
| **Windows 커넥터** |  |  |
| - [Windows 방화벽](../../sentinel/data-connectors-reference.md#windows-firewall) | GA | GA |
| - [Windows 보안 이벤트](../../sentinel/connect-windows-security-events.md) | GA | GA |
| **외부 커넥터** |  |  |
| - [Agari 피싱 방어 및 브랜드 보호](../../sentinel/data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md) | 공개 미리 보기 | 공개 미리 보기 |
| - [AI Vectra Detect](../../sentinel/data-connectors-reference.md#ai-vectra-detect-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [Akamai 보안 이벤트](../../sentinel/data-connectors-reference.md#akamai-security-events-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [Alcide kAudit](../../sentinel/data-connectors-reference.md#alcide-kaudit) | 공개 미리 보기 | 사용할 수 없음 |
| - [Active Directory용 Alsid](../../sentinel/data-connectors-reference.md#alsid-for-active-directory) | 공개 미리 보기 | 사용할 수 없음 |
| - [Apache HTTP 서버](../../sentinel/data-connectors-reference.md#apache-http-server) | 공개 미리 보기 | 사용할 수 없음 |
| - [Aruba ClearPass](../../sentinel/data-connectors-reference.md#aruba-clearpass-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [AWS](../../sentinel/connect-data-sources.md) | GA | GA |
| - [Barracuda CloudGen 방화벽](../../sentinel/data-connectors-reference.md#barracuda-cloudgen-firewall) | GA | GA |
| - [Barracuda 웹앱 방화벽](../../sentinel/data-connectors-reference.md#barracuda-waf) | GA | GA |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/data-connectors-reference.md#better-mobile-threat-defense-mtd-preview) | 공개 미리 보기 | 사용할 수 없음 |
| - [Beyond Security beSECURE](../../sentinel/data-connectors-reference.md#beyond-security-besecure) | 공개 미리 보기 | 사용할 수 없음 |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | 공개 미리 보기 | 공개 미리 보기 |
| - [Broadcom Symantec DLP](../../sentinel/data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [Check Point](../../sentinel/data-connectors-reference.md#check-point) | GA | GA |
| - [Cisco ASA](../../sentinel/data-connectors-reference.md#cisco-asa) | GA | GA |
| - [Cisco Meraki](../../sentinel/data-connectors-reference.md#cisco-meraki-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco Umbrella](../../sentinel/data-connectors-reference.md#cisco-umbrella-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco UCS](../../sentinel/data-connectors-reference.md#cisco-unified-computing-system-ucs-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md) | 공개 미리 보기 | 공개 미리 보기 |
| - [Citrix Analytics WAF](../../sentinel/data-connectors-reference.md#citrix-web-app-firewall-waf-preview) | GA | GA |
| - [Common Event Format(CEF)](../../sentinel/connect-common-event-format.md) | GA | GA |
| - [CyberArk EPV(Enterprise Password Vault) 이벤트](../../sentinel/data-connectors-reference.md#cyberark-enterprise-password-vault-epv-events-preview) | 공개 미리 보기 | 공개 미리 보기 |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | 공개 미리 보기 | 사용할 수 없음      |
| - [Eset 보안 관리 센터](../../sentinel/connect-data-sources.md)                  | 공개 미리 보기 | 사용할 수 없음      |
| - [ExtraHop Reveal(x)](../../sentinel/data-connectors-reference.md#extrahop-revealx)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/data-connectors-reference.md#f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/data-connectors-reference.md#f5-networks-asm)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Forcepoint CASB](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Forcepoint DLP](../../sentinel/data-connectors-reference.md#forcepoint-data-loss-prevention-dlp-preview)                                   | 공개 미리 보기 | 사용할 수 없음      |
| - [CEF용 ForgeRock Common Audit](../../sentinel/connect-data-sources.md)                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Fortinet](../../sentinel/data-connectors-reference.md#fortinet)                                         | GA             | GA             |
| - [Google 작업 영역(G 제품군) ](../../sentinel/data-connectors-reference.md#google-workspace-g-suite-preview)                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Illusive 공격 관리 시스템](../../sentinel/data-connectors-reference.md#illusive-attack-management-system-ams-preview)                | 공개 미리 보기 | 공개 미리 보기 |
| - [Imperva WAF 게이트웨이](../../sentinel/data-connectors-reference.md#imperva-waf-gateway-preview)                             | 공개 미리 보기 | 공개 미리 보기 |
| - [Infoblox NIOS](../../sentinel/data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)                                    | 공개 미리 보기 | 공개 미리 보기 |
| - [Juniper SRX](../../sentinel/data-connectors-reference.md#juniper-srx-preview)                                      | 공개 미리 보기 | 공개 미리 보기 |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | 공개 미리 보기 | 공개 미리 보기 |
| - [NXLog Windows DNS](../../sentinel/data-connectors-reference.md#nxlog-dns-logs-preview)                                             | 공개 미리 보기 | 사용할 수 없음      |
| - [NXLog LinuxAudit](../../sentinel/data-connectors-reference.md#nxlog-linuxaudit-preview)                                 | 공개 미리 보기 | 사용할 수 없음      |
| - [Okta Single Sign-On](../../sentinel/data-connectors-reference.md#okta-single-sign-on-preview)                              | 공개 미리 보기 | 공개 미리 보기 |
| - [Onapsis 플랫폼](../../sentinel/connect-data-sources.md)                                 | 공개 미리 보기 | 공개 미리 보기 |
| - [One Identity Safeguard](../../sentinel/data-connectors-reference.md#one-identity-safeguard-preview)                          | GA             | GA             |
| - [보안 경고](../../sentinel/data-connectors-reference.md#orca-security-preview)                            | 공개 미리 보기 | 사용할 수 없음      |
| - [Palo Alto Networks](../../sentinel/data-connectors-reference.md#palo-alto-networks)                               | GA             | GA             |
| - [Perimeter 81 활동 로그](../../sentinel/data-connectors-reference.md#perimeter-81-activity-logs-preview)                      | GA             | 사용할 수 없음      |
| - [Proofpoint 온 디맨드 전자 메일 보안](../../sentinel/data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)             | 공개 미리 보기 | 사용할 수 없음      |
| - [Proofpoint TAP](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                                   | 공개 미리 보기 | 공개 미리 보기 |
| - [Pulse Connect Secure](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                             | 공개 미리 보기 | 공개 미리 보기 |
| - [Qualys 취약성 관리](../../sentinel/data-connectors-reference.md#qualys-vulnerability-management-vm-preview)                  | 공개 미리 보기 | 공개 미리 보기 |
| - [Salesforce 서비스 클라우드](../../sentinel/data-connectors-reference.md#salesforce-service-cloud-preview)                         | 공개 미리 보기 | 사용할 수 없음      |
| - [SonicWall 방화벽 ](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                              | 공개 미리 보기 | 공개 미리 보기 |
| - [Sophos Cloud Optix](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                               | 공개 미리 보기 | 사용할 수 없음      |
| - [Sophos XG 방화벽](../../sentinel/data-connectors-reference.md#sophos-xg-firewall-preview)                               | 공개 미리 보기 | 공개 미리 보기 |
| - [Squadra Technologies secRMM](../../sentinel/data-connectors-reference.md#squadra-technologies-secrmm)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/data-connectors-reference.md#squid-proxy-preview)                                      | 공개 미리 보기 | 사용할 수 없음      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/data-connectors-reference.md#symantec-integrated-cyber-defense-exchange-icdx)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/data-connectors-reference.md#symantec-proxysg-preview)                                | 공개 미리 보기 | 공개 미리 보기 |
| - [Symantec VIP](../../sentinel/data-connectors-reference.md#symantec-vip-preview)                                     | 공개 미리 보기 | 공개 미리 보기 |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [Thycotic 비밀 서버](../../sentinel/data-connectors-reference.md#thycotic-secret-server-preview)                          | 공개 미리 보기 | 공개 미리 보기 |
| - [Trend Micro Deep Security](../../sentinel/data-connectors-reference.md#trend-micro-deep-security)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/data-connectors-reference.md#trend-micro-tippingpoint-preview)                         | 공개 미리 보기 | 공개 미리 보기 |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | 공개 미리 보기 | 사용할 수 없음      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)           | 공개 미리 보기 | 공개 미리 보기 |
| - [VMware ESXi](../../sentinel/data-connectors-reference.md#vmware-esxi-preview)                                      | 공개 미리 보기 | 공개 미리 보기 |
| - [WireX 네트워크 포렌식 플랫폼](../../sentinel/data-connectors-reference.md#wirex-network-forensics-platform-preview)                | 공개 미리 보기 | 공개 미리 보기 |
| - [Zimperium Mobile Threat Defense](../../sentinel/data-connectors-reference.md#zimperium-mobile-thread-defense-preview)                  | 공개 미리 보기 | 사용할 수 없음      |
| - [Zscaler](../../sentinel/data-connectors-reference.md#zscaler)                                         | GA             | GA             |
| | | |

<sup><a name="footnote1"></a>1</sup> Databricks ML 플랫폼을 사용할 수 없기 때문에 소버린 클라우드에서 SSH 및 RDP 검색이 지원되지 않습니다.

### <a name="microsoft-365-data-connectors"></a>Microsoft 365 데이터 커넥터

Office 365 GCC는 Azure에서 Azure Active Directory(Azure AD)와 페어링됩니다. Office 365 GCC High와 Office 365 DoD는 Azure Government에서 Azure AD와 페어링됩니다.

> [!TIP]
> [상호 운용이 가능한](#microsoft-365-integration) 위치를 이해하려면 Azure 환경에 주의해야 합니다. 다음 표에서는 *지원되지 않는* 상호 운용성을 대시(-)로 표시하여 지원 관계가 없음을 나타낼 수 있습니다.
>

| 커넥터 | Azure | Azure Government |
|--|--|--|
| **[Dynamics365](../../sentinel/data-connectors-reference.md#dynamics-365)** |  |  |
| - Office 365 GCC | 공개 미리 보기 | - |
| - Office 365 GCC High | - | 사용할 수 없음 |
| - Office 365 DoD | - | 사용할 수 없음 |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | 공개 미리 보기 | - |
| - Office 365 GCC High | - | 사용할 수 없음 |
| - Office 365 DoD | - | 사용할 수 없음 |
| **[클라우드 앱 용 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[클라우드 앱 용 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)** <br>섀도 IT 로그 |  |  |
| - Office 365 GCC | 공개 미리 보기 | - |
| - Office 365 GCC High | - | 공개 미리 보기 |
| - Office 365 DoD | - | 공개 미리 보기 |
| **[클라우드 앱 용 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-cloud-apps)**                  <br>경고 |  |  |
| - Office 365 GCC | 공개 미리 보기 | - |
| - Office 365 GCC High | - | 공개 미리 보기 |
| - Office 365 DoD | - | 공개 미리 보기 |
| **[엔드포인트에 대한 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-endpoint)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender for Identity](../../sentinel/data-connectors-reference.md#microsoft-defender-for-identity)** |  |  |
| - Office 365 GCC | 공개 미리 보기 | - |
| - Office 365 GCC High | - | 사용할 수 없음 |
| - Office 365 DoD | - | 사용할 수 없음 |
| **[Office 365용 Microsoft Defender](../../sentinel/data-connectors-reference.md#microsoft-defender-for-office-365)** |  |  |
| - Office 365 GCC | 공개 미리 보기 | - |
| - Office 365 GCC High | - | 사용할 수 없음 |
| - Office 365 DoD | - | 사용할 수 없음 |
| **[Office 365](../../sentinel/data-connectors-reference.md#microsoft-office-365)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|  |  |

<a name="azure-defender-for-iot"></a>

## <a name="microsoft-defender-for-iot"></a>Microsoft Defender for IoT

IoT 용 Microsoft Defender를 사용 하면 모든 IoT/OT 장치에서 포괄적인 보안으로 IoT/OT 혁신을 가속화할 수 있습니다.최종 사용자 조직의 경우 IoT 용 Microsoft Defender는 에이전트 없는, 네트워크 계층 보안을 신속 하 게 배포 하 고, 다양 한 산업 장비에서 작동 하 고, Microsoft 센티널 및 기타 SOC 도구와 상호 운용 하는 기능을 제공 합니다. 온-프레미스 또는 Azure 연결 환경에 배포합니다.iot 장치 빌더의 경우 iot 용 Microsoft Defender 보안 에이전트를 사용 하 여 새 iot 장치 및 Azure IoT 프로젝트에 직접 보안을 빌드할 수 있습니다. 마이크로 에이전트는 이진 패키지로 배포하거나 소스 코드를 수정하는 기능을 비롯한 유연한 배포 옵션을 제공합니다. 그리고 마이크로 에이전트는 Linux나 Azure RTOS 같은 표준 IoT 운영 체제에 사용할 수 있습니다. 자세한 내용은 [IoT 용 Microsoft Defender 제품 설명서](../../defender-for-iot/index.yml)를 참조 하세요.

다음 표에서는 Azure에서 제공 되는 IoT 용 Microsoft Defender 기능 가용성 및 Azure Government을 보여 줍니다.

### <a name="for-organizations"></a>조직용

| 기능 | Azure | Azure Government |
|--|--|--|
| [온-프레미스 디바이스 검색 및 인벤토리](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | GA | GA |
| [취약점 관리](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | GA | GA |
| [IoT 및 OT 동작 분석을 사용한 위협 감지](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | GA | GA |
| [수동 및 자동 위협 인텔리전스 업데이트](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | GA | GA |
| **SIEM, SOAR 및 XDR을 사용하여 IT 및 OT 보안 통합** |  |  |
| [Active Directory](../../defender-for-iot/organizations/how-to-create-and-manage-users.md#integrate-with-active-directory-servers) | GA | GA |
| [ArcSight](../../defender-for-iot/organizations/how-to-accelerate-alert-incident-response.md#accelerate-incident-workflows-by-using-alert-groups) | GA | GA |
| [ClearPass(경고 & 인벤토리)](../../defender-for-iot/organizations/how-to-install-software.md#attach-a-span-virtual-interface-to-the-virtual-switch) | GA | GA |
| [CyberArk PSM](../../defender-for-iot/organizations/concept-key-concepts.md#integrations) | GA | GA |
| [Email](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#email-address-action) | GA | GA |
| [FortiGate](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [FortiSIEM](../../defender-for-iot/organizations/tutorial-fortinet.md) | GA | GA |
| [Microsoft Sentinel](../../defender-for-iot/organizations/how-to-configure-with-sentinel.md) | 공개 미리 보기 | 공개 미리 보기 |
| [NetWitness](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#netwitness-action) | GA | GA |
| [Palo Alto NGFW](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [Palo Alto Panorama](../../defender-for-iot/organizations/tutorial-palo-alto.md) | GA | GA |
| [ServiceNow(경고 & 인벤토리)](../../defender-for-iot/organizations/tutorial-servicenow.md) | GA | GA |
| [SNMP MIB 모니터링](../../defender-for-iot/organizations/how-to-set-up-snmp-mib-monitoring.md) | GA | GA |
| [Splunk](../../defender-for-iot/organizations/tutorial-splunk.md) | GA | GA |
| [SYSLOG 서버(CEF 형식)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG 서버(LEEF 형식)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG 서버(개체)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [SYSLOG 서버(문자 메시지)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#syslog-server-actions) | GA | GA |
| [웹 콜백(웹후크)](../../defender-for-iot/organizations/how-to-forward-alert-information-to-partners.md#webhook-server-action) | GA | GA |

### <a name="for-device-builders"></a>디바이스 빌더의 경우

| 기능 | Azure | Azure Government |
|--|--|--|
| [Azure RTOS용 마이크로 에이전트](../../defender-for-iot/iot-security-azure-rtos.md) | GA | GA |
| [Microsoft Defender for IoT를 사용하여 Sentinel 구성](../../defender-for-iot/how-to-configure-with-sentinel.md) | 공개 미리 보기 | 공개 미리 보기 |
| **Linux용 독립 실행형 마이크로 에이전트** |  |  |
| [독립 실행형 에이전트 이진 설치](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | 공개 미리 보기 | 공개 미리 보기 |

## <a name="azure-attestation"></a>Azure Attestation

Microsoft Azure Attestation은 플랫폼의 신뢰성과 플랫폼 내부에서 실행되는 이진 파일의 무결성을 원격으로 확인하기 위한 통합 솔루션입니다. 서비스는 플랫폼에서 증거를 수신하고, 보안 표준으로 유효성을 검사하고, 구성 가능한 정책에 대해 평가하고, 클레임 기반 애플리케이션(예: 신뢰 당사자, 감사 기관)에 대한 증명 토큰을 생성합니다. 

Azure Attestation 현재 Azure 퍼블릭 및 정부 클라우드의 여러 지역에서 사용할 수 있습니다. Azure Government 서비스는 US Gov 버지니아 및 US Gov 애리조나 미리 보기 상태로 제공됩니다. 

자세한 내용은 Azure Attestation 공용 [설명서를 참조하세요.](/azure/attestation/overview) 

| 기능 | Azure | Azure Government |
|--|--|--|
| 제어 평면 및 데이터 평면 작업을 수행하는 [포털 환경](/azure/attestation/quickstart-portal) | GA | - |
| 컨트롤 플레인 및 데이터 평면 작업을 수행하는 [PowerShell 환경](/azure/attestation/quickstart-powershell)  | GA | GA |
| TLS 1.2 적용   | GA | GA |
| BCDR 지원   | GA | - |
| [서비스 태그 통합](/azure/virtual-network/service-tags-overview) | GA | GA |
| [불변 로그 스토리지](/azure/attestation/audit-logs) | GA | GA |
| 프라이빗 링크를 사용하여 네트워크 격리 | 공개 미리 보기 | - |
| [FedRAMP High 인증](/azure/azure-government/compliance/azure-services-in-fedramp-auditscope) | GA | - |
| 고객 lockbox | GA | - |

## <a name="next-steps"></a>다음 단계

- [공유 책임](shared-responsibility.md) 모델 및 클라우드 공급자가 처리하는 보안 작업과 고객이 처리하는 보안 작업을 이해합니다.
- 이 문서는 연방, 주 및 지방 정부 기관과 그 파트너에 적용되는 규정 준수를 지원하는 데 사용되는 [Azure Government 클라우드](../../azure-government/documentation-government-welcome.md)의 기능 및 신뢰할 수 있는 디자인과 보안에 대한 개요를 제공합니다.
- [Office 365 Government 플랜](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments)을 이해합니다.
- 법률 및 규제 표준에 대한 [Azure의 규정 준수](../../compliance/index.yml)를 이해합니다.
