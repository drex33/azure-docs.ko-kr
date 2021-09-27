---
title: 랜섬웨어로부터 보호하기 위한 Azure 백업 및 복원 계획 | Microsoft Docs
description: 랜섬웨어 공격 전후에 중요한 비즈니스 시스템을 보호하고 비즈니스 운영을 신속하게 복구하기 위해 수행할 작업에 대해 알아봅니다.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 08/27/2021
ms.openlocfilehash: 83caa5e93a8a9fd9df13646f867b176208655450
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763351"
---
# <a name="backup-and-restore-plan-to-protect-against-ransomware"></a>랜섬웨어로부터 보호하기 위한 백업 및 복원 계획

랜섬웨어 공격은 조직에서 공격자에게 비용을 지불하도록 데이터와 시스템을 의도적으로 암호화하거나 지웁니다. 이러한 공격은 공격자에게 비용을 지불하지 않고 복구하는 데 필요한 데이터, 백업, 주요 문서를 대상으로 합니다(조직이 지불할 확률을 높이기 위한 수단).

이 문서에서는 중요한 비즈니스 시스템을 보호하기 위해 공격 전과 공격 중에 비즈니스 운영을 신속하게 복구하기 위해 수행할 작업을 다룹니다.

> [!NOTE]
> 랜섬웨어 대비는 [WannaCry](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack) & [(Not)Petya](https://attack.mitre.org/software/S0368/) 등의 재해 및 빠른 공격에 대한 복원력을 향상시킵니다.

## <a name="what-is-ransomware"></a>랜섬웨어란?

랜섬웨어는 파일과 폴더를 암호화하여 중요한 데이터 및 시스템에 액세스하지 못하게 만드는 강탈 공격의 유형입니다. 공격자는 랜섬웨어를 사용하여 암호 해독 키를 교환하거나, 중요한 데이터를 다크 웹 또는 공용 인터넷에 공개하지 않는 대신, 일반적으로 암호화폐 형태의 돈을 요구하여 공격자로부터 돈을 갈취합니다.

초기 랜섬웨어는 대부분 피싱 또는 디바이스 간에 확산되는 맬웨어를 사용했지만, 인간 공격 운영자에 의해 구동되는 활성 공격자가 디바이스가 아닌 조직의 모든 시스템을 대상으로 하는 [인간이 운영하는 랜섬웨어](/security/compass/human-operated-ransomware)가 등장했습니다. 공격은 다음을 수행할 수 있습니다.

- 데이터 암호화
- 데이터 유출
- 백업 손상

랜섬웨어는 일반적인 시스템과 잘못된 보안 구성 및 취약성에 대한 인간 공격자의 지식을 활용하여 조직에 침입하고, 엔터프라이즈 네트워크를 탐색하고, 환경 및 약점에 적응합니다.

랜섬웨어는 랜섬웨어가 실제로 특정 날짜에 실행되기 전 몇 주 또는 몇 달 동안 데이터를 먼저 유출하도록 준비할 수 있습니다.

랜섬웨어는 시스템 키를 유지하면서 데이터를 느리게 암호화할 수도 있습니다. 키를 계속 사용할 수 있으면 데이터를 사용할 수 있으며, 랜섬웨어가 눈에 띄지 않습니다. 백업은 암호화된 데이터입니다. 모든 데이터가 암호화되고 최근 백업도 암호화된 데이터가 되면 더 이상 데이터를 읽을 수 없도록 키가 제거됩니다.

실제 손상은 종종 향후 악의적인 활동을 위해 백도어에 남아 있는 동안 공격이 파일을 유출할 때 발생하며, 이러한 위험은 몸값 지불 여부와 관계없이 지속됩니다. 이러한 공격은 비즈니스 운영에 치명적일 수 있으며 정리가 어렵습니다. 또한 향후 공격으로부터 보호하기 위해 완전한 악의적 사용자를 제거해야 합니다. 맬웨어만 수정하던 초기 랜섬웨어와는 달리 인간이 운영하는 랜섬웨어는 최초 공격 후 계속해서 비즈니스 운영에 위협을 가할 수 있습니다.

### <a name="impact-of-an-attack"></a>공격의 영향

랜섬웨어 공격이 조직에 미치는 영향은 정확하게 정량화하기 어렵습니다. 공격의 범위에 따라 다음과 같은 영향이 있을 수 있습니다.

- 데이터 액세스 손실
- 비즈니스 운영 중단
- 재정 손실
- 지적 재산권 도난
- 고객 신뢰 및 평판 손상
- 법적 비용

## <a name="how-can-you-protect-yourself"></a>자신을 보호하려면 어떻게 해야 할까요?

랜섬웨어의 피해를 방지하는 가장 좋은 방법은 예방 조치를 구현하고 공격자가 시스템에 침입하기 위해 취하는 모든 단계에서 조직을 보호하는 도구를 갖추는 것입니다.

조직을 클라우드 서비스로 이동하여 온-프레미스 노출을 줄일 수 있습니다. Microsoft는 랜섬웨어 공격에 대해 Microsoft Azure의 복원력을 높이고 조직이 랜섬웨어 공격 기술을 무효화하는 데 도움이 되는 네이티브 보안 기능에 투자했습니다. 랜섬웨어와 강탈에 대한 포괄적인 보기와 조직을 보호하는 방법은 [인간이 운영하는 랜섬웨어 완화 프로젝트 계획](https://download.microsoft.com/download/7/5/1/751682ca-5aae-405b-afa0-e4832138e436/RansomwareRecommendations.pptx) PowerPoint 프레젠테이션의 정보를 참조하세요.

어느 시점에 랜섬웨어 공격의 피해를 입을 수 있다고 가정해야 합니다. 데이터를 보호하고 비용을 지불하지 않도록 하기 위해 취할 수 있는 가장 중요한 단계 중 하나는 비즈니스에 중요한 정보에 대한 안정적인 백업 및 복원 계획을 수립하는 것입니다. 랜섬웨어 공격자는 볼륨 섀도 복사본과 같은 백업 애플리케이션 및 운영 체제 기능을 중립화하는 데 많은 투자를 했으므로 악의적인 공격자가 액세스할 수 없는 백업이 있어야 합니다.

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](../../backup/backup-overview.md)은 데이터를 전송 중이거나 휴지 상태일 때 백업 환경에 대한 보안을 제공합니다. Azure Backup 를 사용하면 다음을 [백업할 수 있습니다](../../backup/backup-overview.md#what-can-i-back-up).

- 온-프레미스 파일, 폴더, 시스템 상태
- 전체 Windows/Linux VM
- Azure Managed Disks
- 스토리지 계정에 대한 Azure 파일 공유
- Azure VM에서 실행되는 SQL Server 데이터베이스

백업 데이터는 Azure 스토리지에 저장되며 게스트 및 공격자는 백업 스토리지 또는 해당 콘텐츠에 직접 액세스할 수 없습니다. 가상 머신 백업을 사용하여 백업 스냅샷 만들기 및 스토리지는 게스트 및 공격자가 애플리케이션 일치 백업에 대한 워크로드를 정지하는 것 외에는 참여할 수 없는 Azure 패브릭에 의해 수행됩니다. SQL 및 SAP HANA를 사용하는 경우 백업 확장 프로그램은 특정 Blob에 쓰기 위한 임시 액세스 권한을 가집니다. 이러한 방식으로 손상된 환경에서도 기존 백업은 공격자에 의해 변조 또는 삭제될 수 없습니다.

Azure Backup은 Azure Backup과 관련된 이벤트에 대한 작업을 보고 구성할 수 있는 기본 제공 모니터링 및 경고 기능을 제공합니다. Backup 보고서는 사용량 추적, 백업 및 복원 감사, 다양한 세분성 수준에서 키 추세 식별을 위한 하나의 중지 대상 역할을 합니다. Azure Backup의 모니터링 및 보고 도구를 사용하면 무단, 의심스러운 또는 악의적인 활동을 발생하는 즉시 사용자에게 경고할 수 있습니다.

유효한 사용자만 다양한 작업을 수행할 수 있는지 확인하기 위한 검사가 추가되었습니다. 여기에는 추가적인 인증 계층 제공 및 복구가 포함됩니다. 중요한 작업에 대한 추가 인증 계층을 추가하는 과정의 일환으로, [온라인 백업 수정](../../backup/backup-azure-security-feature.md#prevent-attacks) 전에 보안 PIN을 입력하라는 메시지가 표시됩니다.

Azure Backup에 기본 제공되는 [보안 기능](../../backup/security-overview.md)에 대해 자세히 알아보세요.

### <a name="validate-backups"></a>백업 확인

백업이 생성되고 복원하기 전에 백업이 양호한지 확인합니다. Azure에서 데이터를 저장하는 스토리지 엔터티인 [Recovery Services 자격 증명 모음](../../backup/backup-azure-recovery-services-vault-overview.md)을 사용하는 것을 권장합니다. 데이터는 일반적으로 데이터의 복사본 또는 가상 머신(VM), 워크로드, 서버 또는 워크스테이션에 대한 구성 정보입니다. Recovery Services 자격 증명 모음을 사용하여 IaaS VM(Linux 또는 Windows) 및 Azure SQL 데이터베이스, 온-프레미스 자산 등 다양한 Azure 서비스에 대한 백업 데이터를 저장할 수 있습니다. Recovery Services 자격 증명 모음을 사용하면 백업 데이터를 쉽게 구성하고 다음과 같은 기능을 제공할 수 있습니다.

- 강화된 기능을 통해 백업을 보호하고 프로덕션 및 백업 서버가 손상된 경우에도 데이터를 안전하게 복구할 수 있습니다. [자세히 알아보기](../../backup/backup-azure-security-feature.md).
- 중앙 포털에서 하이브리드 IT 환경(Azure IaaS VM 및 온-프레미스 자산)에 대한 모니터링. [자세히 알아보기](../../backup/backup-azure-monitoring-built-in-monitor.md).
- Azure RBAC(Azure 역할 기반 액세스 제어)과의 호환성은 백업 및 복원 액세스 권한을 정의된 사용자 역할 세트로 제한합니다. Azure RBAC는 다양한 기본 제공 역할을 제공하고 Azure Backup에는 3가지 복구 지점을 관리하는 기본 제공 역할이 포함됩니다. [자세히 알아보기](../../backup/backup-rbac-rs-vault.md).
- 악의적인 행위자가 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 일시 삭제를 보호합니다. 백업 데이터는 14일 동안 보존되어 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. [자세히 알아보기](../../backup/backup-azure-security-feature-cloud.md).
- 지역 간 복원을 사용하여 쌍으로 연결된 Azure 지역인 보조 지역에서 Azure VM을 복원할 수 있습니다. 언제든지 원하는 경우 보조 지역에서 복제된 데이터를 복원할 수 있습니다. 이렇게 하면 자격 증명 모음의 GRS 설정과 달리 Azure에서 재해를 선언할 때까지 기다리지 않고 감사 준수 및 중단 시나리오 동안 보조 지역 데이터를 복원할 수 있습니다. [자세히 알아봅니다](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore).

> [!NOTE]
> Azure Backup에는 두 가지 유형의 자격 증명 모음이 있습니다. Recovery Services 자격 증명 모음 외에도, Azure Backup이 지원되는 최신 워크로드에 대한 하우스 데이터를 보관하는 [Backup 자격 증명 모음](../../backup/backup-vault-overview.md)도 있습니다.

## <a name="what-to-do-before-an-attack"></a>공격 전에 수행할 일

앞에서 설명한 대로, 어느 시점에 랜섬웨어 공격의 피해를 입을 수 있다고 가정해야 합니다. 중요 비즈니스용 시스템을 식별하고 공격 전에 모범 사례를 적용하면 최대한 빨리 백업하고 실행할 수 있습니다. 

### <a name="determine-what-is-most-important-to-you"></a>가장 중요한 것을 결정합니다

랜섬웨어는 공격을 대비하는 동안에도 공격할 수 있으므로, 가장 중요한 비즈니스용 시스템을 식별하고 해당 시스템에서 정기적인 백업을 시작하는 것이 가장 중요합니다.

경험상, 이 우선 순위 순서에서 고객에게 가장 중요한 5가지 애플리케이션은 다음 범주에 속합니다.

- ID 시스템 – 사용자가 Active Directory, [Azure AD Connect](../../active-directory/hybrid/whatis-azure-ad-connect.md), AD 도메인 컨트롤러와 같은 시스템(아래에 설명된 다른 모든 시스템 포함)에 액세스하는 데 필요합니다
- 사람의 생명 – 의료 또는 생명 지원 시스템, 안전 시스템(장비, 발송 시스템, 신호등 제어), 대형 기계, 화학/화학 시스템, 음식 또는 개인 제품 생산 등 위험에 노출될 수 있는 시스템
- 재무 시스템 – 통화 거래를 처리하고 결제 시스템 및 관련 데이터베이스와 같은 비즈니스 운영을 유지하는 시스템, 분기별 보고를 위한 재무 시스템
- 제품 또는 서비스 사용 – 비즈니스 서비스를 제공하거나 고객이 지불하는 물리적 제품을 생산/제공하는 데 필요한 모든 시스템, 공장 제어 시스템, 제품 배달/발송 시스템 등
- 보안(최소) – 공격을 모니터링하고 최소 보안 서비스를 제공하는 데 필요한 보안 시스템에 우선 순위를 지정해야 합니다. 현재 공격(또는 쉬운 기회 공격)이 복원된 시스템에 대한 액세스 권한을 즉시 얻거나 다시 얻을 수 없도록 하는 데 중점을 두어야 합니다

우선 순위 백업 목록도 우선 순위 복원 목록이 됩니다. 중요한 시스템을 식별하고 정기적인 백업을 수행하고 나면, 노출 수준을 줄이는 단계를 수행합니다.

### <a name="steps-to-take-before-an-attack"></a>공격 전에 수행해야 하는 단계

공격 전에 이러한 모범 사례를 적용합니다.

| Task | 세부 정보 |
| --- | --- |
| 먼저 온라인 상태로 전환해야 하는 중요한 시스템을 확인하고(위의 상위 5개 범주 사용) 해당 시스템의 정기적 백업을 즉시 시작합니다. | 공격 후에 최대한 빨리 백업 및 실행하려면 가장 중요한 것을 확인하세요. |
| 클라우드로 조직을 마이그레이션합니다. <br><br>클라우드로의 이동을 지원하기 위해 Microsoft 통합 지원 계획을 구입하거나 Microsoft 파트너와 협력하는 것이 좋습니다. | 자동 백업 및 셀프 서비스 롤백을 사용하여 클라우드 서비스로 데이터를 이동하여 온-프레미스 노출을 줄입니다. Microsoft Azure에는 업무상 중요한 시스템을 백업하고 백업을 더 빠르게 복원하는 데 도움이 되는 강력한 도구 집합이 있습니다. <br><br>[Microsoft 통합 지원](https://www.microsoft.com/en-us/msservices/unified-support-solutions)은 필요할 때마다 도움을 주는 클라우드 서비스 지원 모델입니다. 통합 지원: <br><br>필요한 문제 해결 및 중요한 인시던트 에스컬레이션으로 매일 24시간 사용할 수 있는 지정된 팀을 제공합니다. <br><br>IT 환경의 상태를 모니터링하고 문제를 방지하기 위해 사전에 작동하도록 지원합니다 |
| [버전 관리 및 휴지통 기능](/compliance/assurance/assurance-malware-and-ransomware-protection#sharepoint-online-and-onedrive-for-business-protection-against-ransomware)을 이용하려면 사용자 데이터를 OneDrive 및 SharePoint 같은 클라우드 솔루션으로 이동합니다. <br><br>지연 및 복구 비용을 줄이기 위해 직접 파일을 복구하는 방법을 사용자에게 교육합니다.   예를 들어, 사용자의 OneDrive 파일이 맬웨어에 감염된 경우, 전체 OneDrive를 이전 시간으로 [복원](https://support.microsoft.com/office/restore-your-onedrive-fa231298-759d-41cf-bcd0-25ac53eb8a15?ui=en-US&rs=en-US&ad=US)할 수 있습니다. <br><br>사용자가 자신의 파일을 복원할 수 있도록 하기 전에, [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender)와 같은 방어 전략을 고려해야 합니다. | Microsoft Cloud의 사용자 데이터는 기본 제공 보안 및 데이터 관리 기능을 통해 보호할 수 있습니다. <br><br>사용자에게 자신의 파일을 복원하는 방법을 학습시키는 것이 좋지만, 공격을 수행하는 데 사용된 맬웨어를 복원하지 않도록 주의해야 합니다. 그러려면 다음 작업을 수행해야 합니다. <br><br>공격자가 제거된 것이 확실 해질 때까지 사용자가 파일을 복원하지 않도록 합니다 <br><br>사용자가 일부 맬웨어를 복원하는 경우 완화 조치 <br><br>Microsoft 365 Defender는 AI 기반 자동 작업 및 플레이 북을 사용하여 영향을 받는 자산을 보안 상태로 다시 관리합니다. Microsoft 365 Defender는 도구 모음 제품의 자동 수정 기능을 활용하여 인시던트에 관련된 모든 영향을 받는 자산이 가능한 경우 자동으로 재구성 되도록 합니다. |
| [Azure Security Benchmark](/security/benchmark/azure/introduction) 실행. | Azure Security Benchmark는 NIST SP800-53, CIS Controls v7.1과 같은 산업 기반 보안 제어 프레임워크를 기반으로 하는 Azure의 고유한 보안 제어 프레임워크입니다. 이는 조직에게 Azure 및 Azure 서비스를 구성하고 보안 컨트롤을 구현하는 방법에 대한 참고 자료를 제공합니다. [백업 및 복원](/security/benchmark/azure/security-controls-v2-backup-recovery)을 참조하세요. |
| BC/DR(비즈니스 연속성/재해 복구) 계획을 정기적으로 연습합니다. <br><br>인시던트 응답 시나리오를 시뮬레이션합니다. 공격을 대비하기 위해 수행하는 연습은 백업 및 복원 목록에 대해 계획적이고 우선 순위가 지정되어 수행되어야 합니다. <br><br>'0에서부터 복구' 시나리오를 정기적으로 테스트하여 BC/DR이 제로 기능(모든 시스템 중단)에서 중요한 비즈니스 작업을 온라인으로 신속하게 가져올 수 있도록 합니다. | 랜섬웨어 또는 강탈 공격을 자연 재해와 동일한 중요도로 다루어 비즈니스 운영의 신속한 복구를 보장합니다. <br><br>대역 외 직원과 고객 통신을 비롯하여, 팀 간 프로세스 및 기술 절차의 유효성을 검사하는 연습을 수행합니다(모든 이메일과 채팅은 중단 되었다고 가정). |
| 잠재적 위험을 식별하고 예방 제어 및 작업을 중재하는 방법을 해결하기 위해 위험 레지스터를 생성하는 것이 좋습니다. 높은 가능성 및 높은 영향 시나리오를 가진 랜섬웨어를 위험 레지스터에 추가합니다. | 위험 레지스터는 위험이 발생할 가능성 및 비즈니스에 대한 심각도를 기준으로 위험의 우선 순위를 지정하는 데 도움이 될 수 있습니다. <br><br>[ERM(기업 위험 관리)](/compliance/assurance/assurance-risk-management) 평가 주기를 통해 위험 완화 상태를 추적합니다. |
| 정기적인 일정에 따라 모든 중요 비즈니스 시스템을 자동으로 백업합니다(예: Active Directory와 같은 중요 종속성의 백업 포함). <br><br>백업이 생성되면 백업이 양호한 지 확인합니다. | 마지막 백업까지 데이터를 복구할 수 있습니다. |
| 복구에 필요한 지원 문서 및 시스템(예: 복원 절차 문서, CMDB 및 네트워크 다이어그램, SolarWinds 인스턴스)를 보호(또는 인쇄)합니다. | 복구 능력에 영향을 미치기 때문에 공격자는 이러한 리소스를 의도적으로 표적화합니다. |
| 타사 지원, 특히 위협 인텔리전스 공급자, 맬웨어 방지 솔루션 공급자, 맬웨어 분석 공급자 등을 지원하기 위한 잘 문서화된 절차가 있는지 확인합니다. 이러한 절차를 보호(또는 인쇄)합니다. | 타사 연락처는 지정된 랜섬웨어 변형에 알려진 약점 또는 암호 해독 도구를 사용할 수 있는 경우에 유용할 수 있습니다. |
| 백업 및 복구 전략에 다음이 포함되는지 확인합니다. <br><br>특정 시점에 데이터를 백업하는 기능. <br><br>여러 백업 복사본이 격리된 오프라인(air-gapped) 위치에 저장됩니다. <br><br>백업 정보를 신속하게 검색하고 프로덕션 환경에 배치하는 방법을 설정하는 복구 시간 목표. <br><br>프로덕션 환경/샌드박스 백업의 신속한 복원. | 백업은 조직이 공격당한 후의 복원력에 필수적입니다. 최대 보호 및 가용성에 대해 3-2-1 규칙 적용: 3개 사본 (원본 + 2개 백업), 2개의 저장소 유형, 1개의 오프사이트 또는 콜드 복사를 적용합니다. |
| 고의적인 삭제 및 암호화로부터 백업을 보호합니다. <br><br>오프라인 또는 오프사이트 저장소 및 변경할 수 없는 저장소에 백업을 저장합니다. <br><br>온라인 백업을 수정하거나 지울 수 있도록 허용하기 전에, 대역 외 단계(예: [MFA](../../active-directory/authentication/concept-mfa-howitworks.md) 또는 보안 PIN)가 필요합니다. <br><br>Azure Virtual Network 내에서 프라이빗 엔드포인트를 만들어 Recovery Services 자격 증명 모음에서 데이터를 안전하게 백업하고 복원합니다. | 공격자가 액세스할 수 있는 백업은 비즈니스 복구에 사용할 수 없는 것으로 렌더링될 수 있습니다. <br><br>오프라인 스토리지를 통해 네트워크 대역폭을 사용하지 않고도 백업 데이터를 강력하게 전송할 수 있습니다. Azure Backup은 네트워크 대역폭을 사용하지 않고 오프라인으로 초기 백업 데이터를 전송하는 [오프라인 백업](../../backup/offline-backup-overview.md)을 지원합니다. 백업 데이터를 실제 스토리지 디바이스에 복사하는 메커니즘을 제공합니다. 그런 다음, 디바이스가 가까운 Azure 데이터 센터에 배송되어 [Recovery Services 자격 증명 모음](../../backup/backup-azure-recovery-services-vault-overview.md)에 업로드됩니다. <br><br>[Azure Blob](../../storage/blobs/immutable-storage-overview.md) 등 변경 불가능한 온라인 스토리지를 사용하면, 사용자가 중요 비즈니스용 데이터 개체를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다. <br><br>[다단계 인증(MFA)](../../active-directory/authentication/concept-mfa-howitworks.md)는 모든 관리자 계정에 반드시 필요하며, 모든 사용자에게 적극 권장됩니다. 가능하면 SMS 또는 음성이 아닌 인증자 앱을 사용하는 것이 좋습니다. Azure Backup을 설정할 때, Azure Portal에서 생성된 보안 PIN을 사용하여 MFA를 사용하도록 복구 서비스를 구성할 수 있습니다. 이렇게 하면 복구 지점 업데이트 또는 제거와 같은 중요한 작업을 수행하기 위해 보안 pin이 생성됩니다. |
| [보호된 폴더](/windows/security/threat-protection/microsoft-defender-atp/controlled-folders)를 지정합니다. | 권한 없는 애플리케이션이 이러한 폴더의 데이터를 수정하기가 더 어려워집니다. |
| 사용 권한을 검토합니다. <br><br>파일 공유, SharePoint 및 기타 솔루션에 대한 광범위한 쓰기/삭제 권한을 검색합니다. 광범위란 중요 비즈니스용 데이터에 대한 쓰기/삭제 권한이 있는 여러 사용자로 정의됩니다. <br><br>비즈니스 협업 요구 사항을 충족하는 한편, 광범위한 사용 권한을 줄입니다. <br><br>광범위한 권한이 다시 나타나지 않도록 감사 및 모니터링을 수행합니다. | 광범위한 액세스를 허용하는 랜섬웨어 활동의 위험을 줄입니다. |
| 피싱 시도로부터 보호: <br><br>사용자가 피싱 시도를 식별하는 데 도움이 되는 보안 인식 교육을 정기적으로 수행하고 손상에 대한 초기 진입점이 되는 항목을 클릭하지 않도록 합니다. <br><br>이메일에 보안 필터링 컨트롤을 적용하여 성공적인 피싱 시도의 가능성을 감지하고 최소화합니다. | 공격자가 조직을 침입하는 데 사용하는 가장 일반적인 방법은 이메일을 통한 피싱 시도입니다. [EOP(Exchange Online Protection)](/microsoft-365/security/office-365-security/exchange-online-protection-overview)는 메일, 맬웨어 및 기타 이메일 위협으로부터 조직을 보호하는 클라우드 기반 필터링 서비스입니다. EOP는 Exchange Online 사서함이 있는 모든 Microsoft 365 조직에 포함됩니다. <br><br>이메일에 대한 보안 필터링 제어의 예로는 [Safe Links](/microsoft-365/security/office-365-security/safe-links)가 있습니다. Safe Links는 메일 흐름에서 인바운드 이메일 메시지의 URL 검색 및 재작성, URL 및 이메일 메시지와 기타 위치의 링크의 클릭 시 검증을 제공하는 Office 365에 대한 Defender의 기능입니다. EOP의 인바운드 메일 메시지에서 정기 스팸 방지 및 맬웨어 방지와 함께 Safe Links 검색이 수행됩니다. Safe Links 검색을 사용하면 피싱 및 기타 공격에 사용되는 악성 링크를 통해 조직을 보호할 수 있습니다. <br><br>[피싱 방지 보호](/microsoft-365/security/office-365-security/tuning-anti-phishing)에 대해 자세히 알아보세요. |

## <a name="what-to-do-during-an-attack"></a>공격 중 수행할 작업

공격을 받는 경우, 우선 순위 백업 목록이 우선 순위 복원 목록이 됩니다. 복원하기 전에 백업이 양호한 지 다시 확인합니다. 백업 내에서 맬웨어를 검색할 수 있습니다.

### <a name="steps-to-take-during-an-attack"></a>공격 중에 수행할 단계

공격 중에 이러한 모범 사례를 적용합니다.

| Task | 세부 정보 |
| --- | --- |
| 공격 초기에는 위협 인텔리전스 공급자, 맬웨어 방지 솔루션 공급자, 맬웨어 분석 공급자 등의 타사 지원을 사용합니다. | 이러한 연락처는 지정된 랜섬웨어 변형에 알려진 약점 또는 암호 해독 도구를 사용할 수 있는 경우에 유용할 수 있습니다. <br><br>[Microsoft 검색 및 대응 팀(DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/)는 공격으로부터 보호하는 데 도움이 됩니다. DART는 전 세계의 고객에게 발생하고 공격을 방지하고 공격을 방지하는 데 도움이 되며, 공격이 발생했을 때 조사하고 수정합니다. <br><br>Microsoft는 또한 신속한 랜섬웨어 복구 서비스도 제공합니다. 서비스는 Microsoft Global [Compromise Recovery Security Practice (CRSP)](https://www.microsoft.com/security/blog/2021/06/09/crsp-the-emergency-team-fighting-cyber-attacks-beside-customers/)에 의해 독점적으로 제공됩니다. 랜섬웨어 공격 중 팀의 초점은 인증 서비스를 복원하고 랜섬웨어의 영향을 제한하는 것입니다. <br><br>DART 및 CRSP는 Microsoft의 [Industry Solutions Delivery](https://www.microsoft.com/en-us/msservices/security) 보안 서비스 라인의 일부입니다. |
| 지방 또는 연방 법 집행 기관에 문의하세요. | 미국에 있는 경우, FBI에 문의하여 [IC3 Complaint Referral Form](https://ransomware.ic3.gov/default.aspx)으로 랜섬웨어 위반을 보고합니다. |
| 사용자 환경에서 맬웨어 또는 랜섬웨어 페이로드를 제거하고 확산을 중지하는 단계를 수행합니다. <br><br>의심되는 모든 컴퓨터 및 장치에서 전체 바이러스 백신 검사를 실행하여 랜섬웨어와 관련된 페이로드를 검색하고 제거합니다. <br><br>데이터를 동기화하는 장치 또는 매핑된 네트워크 드라이브 대상을 검색합니다. | [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) 또는 [Microsoft Security Essentials](https://www.microsoft.com/download/details.aspx?id=5201)(이전 클라이언트의 경우)를 사용할 수 있습니다. <br><br>[악성 소프트웨어 제거 도구(MSRT)](https://www.microsoft.com/download/details.aspx?id=9905)는 랜섬웨어 또는 맬웨어를 제거하는 데 도움이 되는 대안입니다. |
| 업무상 중요한 시스템을 먼저 복원합니다. 복원하기 전에 백업이 양호한 지 다시 확인해야 합니다.| 이 때, 모든 항목을 복원할 필요가 없습니다. 복원 목록에서 상위 5개 업무상 중요한 시스템에 집중합니다. |
| 오프라인 백업이 있는 경우, 사용자 환경에서 랜섬웨어 페이로드(맬웨어)를 제거한 **후** 암호화된 데이터를 복원할 수 있습니다. | 추가 공격을 방지하려면, 복원하기 전에 랜섬웨어 또는 맬웨어가 오프라인 백업에 있지 않은지 확인하세요. |
| 감염되지 않은 안전한 특정 시점 백업 이미지를 식별합니다. <br><br>Recovery Services 자격 증명 모음을 사용하는 경우, 인시던트 시간대를 신중하게 검토하여 백업을 복원하는 데 적합한 시점을 파악합니다. | 추가 공격을 방지하려면, 복원 전에 랜섬웨어 또는 맬웨어에 대한 백업을 검색합니다. |
| 데이터 복원 시나리오 뿐만 아니라 전체 운영 체제 복원에 보안 스캐너 및 기타 도구를 사용합니다. | [Microsoft 보안 스캐너](/windows/security/threat-protection/intelligence/safety-scanner-download)는 Windows 컴퓨터에서 맬웨어를 찾아서 제거하도록 설계된 검사 도구입니다. 간단히 다운로드하고 검색을 실행하여 맬웨어를 찾은 후 확인된 위협에 따라 변경 내용을 되돌릴 수 있습니다. |
| 안티 바이러스 또는 엔드포인트 검색 및 응답(EDR) 솔루션이 최신 상태 인지 확인합니다. 또한 최신 패치가 있어야 합니다. | [Microsoft Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) 등의 EDR 솔루션이 적합합니다. |
| 업무상 중요한 시스템이 실행되고 나면, 다른 시스템을 복원합니다. <br><br>시스템이 복원되면, 복원하는 항목에 대한 중요한 의사 결정을 내릴 수 있도록 원격 분석 데이터 수집을 시작합니다. | 원격 분석 데이터는 맬웨어가 여전히 시스템에 있는지 여부를 식별하는 데 도움이 됩니다. |

## <a name="post-attack-or-simulation"></a>게시 공격 또는 시뮬레이션

랜섬웨어 공격 또는 인시던트 응답 시뮬레이션 후, 다음 단계를 수행하여 백업 및 복원 계획 뿐만 아니라 보안 상태를 향상시킵니다.

1. 프로세스가 정상적으로 작동하지 않는 경우의 대안(그리고 프로세스를 간소화, 가속화 또는 향상시킬 수 있는 기회)을 파악합니다.
2. 가장 큰 문제에 대한 근본 원인 분석을 수행합니다(사용자, 프로세스 및 기술을 고려하여 솔루션이 올바른 문제를 해결하기 위해 충분한 세부 정보를 확인하세요)
3. 기존 위반을 조사하고 수정합니다([Microsoft 검색 및 대응 팀(DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/)이 참여하여 도움을 줍니다)
4. 얻은 교훈 및 기회에 따라 백업 및 복원 전략을 업데이트합니다. 가장 높은 영향과 가장 빠른 구현 단계를 기준으로 우선 순위를 설정합니다

## <a name="next-steps"></a>다음 단계

이 문서에서는 랜섬웨어로부터 보호하기 위해 백업 및 복원 계획을 개선하는 방법을 알아 보았습니다. 랜섬웨어 보호 배포에 대한 모범 사례는 [랜섬웨어 및 강탈에 대한 신속한 보호](/security/compass/protect-against-ransomware)를 참조하세요.

주요 산업 정보:

- [Microsoft 디지털 방어 보고서](https://www.microsoft.com/security/business/security-intelligence-report?rtc=1)(페이지 22-24 참조)

Microsoft Azure:

- [Microsoft Azure Backup으로 랜섬웨어로부터 보호하기](https://www.youtube.com/watch?v=VhLOr2_1MCg)(26분 동영상)
- [시스템 ID 손상으로부터 복구](./recover-from-identity-compromise.md)
- [Azure Sentinel의 고급 다단계 공격 감지](../../sentinel/fusion.md#ransomware)

Microsoft 365:

- [랜섬웨어 공격으로부터 복구](/microsoft-365/security/office-365-security/recover-from-ransomware)
- [맬웨어 및 랜섬웨어 보호](/compliance/assurance/assurance-malware-and-ransomware-protection)
- [랜섬웨어로부터 Windows 10 PC 보호](https://support.microsoft.com/windows/protect-your-pc-from-ransomware-08ed68a7-939f-726c-7e84-a72ba92c01c3)
- [SharePoint Online에서 랜섬웨어 처리](/sharepoint/troubleshoot/security/handling-ransomware-in-sharepoint-online)

Microsoft 365 Defender:

- [고급 헌팅으로 랜섬웨어 찾기](/microsoft-365/security/defender/advanced-hunting-find-ransomware)

Microsoft 보안 팀 블로그 게시물:

- [사이버 보안 위험을 이해하여 복원력 강화: 4부—현재 위협 탐색(2021년 5월)](https://www.microsoft.com/security/blog/2021/05/26/becoming-resilient-by-understanding-cybersecurity-risks-part-4-navigating-current-threats/). 랜섬웨어 섹션을 참조하세요
- [사람이 조작하는 랜섬웨어 공격: 예방 가능한 재해(2020년 3월)](https://www.microsoft.com/security/blog/2020/03/05/human-operated-ransomware-attacks-a-preventable-disaster/). 실제 사람이 조작하는 랜섬웨어 공격에 대한 공격 체인 분석이 포함됩니다
- [랜섬웨어 응답—지불 여부(2019년 12월)](https://www.microsoft.com/security/blog/2019/12/16/ransomware-response-to-pay-or-not-to-pay/)
- [Norsk Hydro, 랜섬웨어 공격에 투명하게 대응(2019년 12월)](https://www.microsoft.com/security/blog/2019/12/17/norsk-hydro-ransomware-attack-transparency/)