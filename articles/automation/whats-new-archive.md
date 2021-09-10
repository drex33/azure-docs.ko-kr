---
title: Azure Automation의 새로운 기능 보관
description: 이 콘텐츠 세트의 개요 섹션에 있는 새로운 기능 릴리스 정보에는 6개월간의 작업이 포함됩니다. 이후 해당 항목은 본문에서 제거되어 이 문서에 추가됩니다.
services: automation
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 459bfea7504bb9d9078dadabf7443e995d5c90bc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117733"
---
# <a name="archive-for-whats-new-in-azure-automation"></a>Azure Automation의 새로운 기능 보관?

주요 [Azure Automation의 새로운 기능?](whats-new.md) 문서에는 지난 6개월의 업데이트가 포함되어 있으며, 이 문서에는 모든 이전 정보가 포함되어 있습니다.

Azure Automation의 새로운 기능은 무엇인가요? 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

---

## <a name="january-2021"></a>2021년 1월

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>스위스 서부의 Automation 및 State Configuration 선언 GA 지원

**유형:** 새로운 기능

스위스 서부 지역의 Automation 계정 및 State Configuration 가용성. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)를 참조하세요.

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>여러 종속성이 있는 모듈을 가져오는 Python 3 스크립트를 추가함

**유형:** 새로운 기능

이 스크립트는 [GitHub 리포지토리](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py)에서 다운로드할 수 있습니다. 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Centos 8.x/RHEL 8.x/SLES 15에 대한 Hybrid Runbook Worker 역할 지원

**유형.** 새로운 기능

Hybrid Runbook Worker 기능은 Hybrid Runbook Worker의 프로세스 자동화에 대해서만 CentOS 8.x, REHL 8.x 및 SLES 15 배포판을 지원합니다. 이러한 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 운영 체제](automation-linux-hrw-install.md#supported-linux-operating-systems)를 참조하세요.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>오스트레일리아 동부, 동아시아, 미국 서부, 미국 중부 지역의 업데이트 관리 및 변경 내용 추적 가용성

**유형:** 새로운 기능

Automation 계정, 변경 내용 추적 및 인벤토리, 업데이트 관리를 오스트레일리아 동부, 동아시아, 미국 서부, 미국 중부 지역에서 사용할 수 있습니다. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>미국 정부 클라우드의 Python 3 Runbook에 대한 퍼블릭 미리 보기를 도입함

**유형:** 새 기능인 Azure Automation의 경우 미국 정부 클라우드 지역에서 Python 3 클라우드 및 하이브리드 Runbook 실행의 퍼블릭 미리 보기 지원이 도입되었습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)를 참조하세요.

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>TechNet Script Center에서 GitHub로 이동한 Azure Automation Runbook

**유형:** 변경 계획

TechNet Script Center는 더 이상 사용되지 않으며 Runbook 갤러리에 호스팅된 모든 Runbook은 [Automation GitHub 조직](https://github.com/azureautomation)으로 이동되었습니다. 자세한 내용은 [GitHub로 이동하는 Azure Automation Runbook](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)을 참조하세요.

## <a name="december-2020"></a>2020년 12월

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation 및 업데이트 관리 프라이빗 링크 GA

**유형:** 새로운 기능

Azure Automation 및 업데이트 관리 지원은 Azure 글로벌 및 정부 클라우드를 위한 GA로 발표되었습니다. Azure Automation은 Private Link를 지원하여 하이브리드 작업자 역할의 Runbook을 안전하게 실행하고, Update Management를 사용하여 머신을 패치하고, 웹후크를 통해 Runbook을 호출하고, State Configuration 서비스를 사용하여 머신의 불편 사항을 계속 제기하게 합니다. 자세한 내용은 [Azure Automation Private Link 지원](https://azure.microsoft.com/updates/azure-automation-private-link)을 참조하세요.

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>접근성에 대해 Grade-C 인증으로 분류된 Azure Automation

**유형:** 새로운 기능

Microsoft 제품의 접근성 기능은 기관이 글로벌 접근성 요구 사항을 해결하는 데 도움이 됩니다. [블로그 공지](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) 페이지에서 **Azure Automation** 을 검색하여 Automation 서비스에 대한 접근성 부합 보고서를 읽습니다.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>아랍에미리트 북부의 Automation 및 State Configuration GA 지원

**유형:** 새로운 기능

아랍에미리트 북부 지역의 Automation 계정 및 State Configuration 가용성. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)를 참조하세요.

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>독일 중서부의 Automation 및 State Configuration GA 지원

**유형:** 새로운 기능

독일 서부 지역의 Automation 계정 및 State Configuration 가용성. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)를 참조하세요.

### <a name="dsc-support-for-oracle-6-and-7"></a>Oracle 6 및 7에 대한 DSC 지원

**유형:** 새로운 기능

Automation State Configuration으로 Oracle Linux 6 및 7 시스템을 관리합니다. 이러한 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 Linux 배포판](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)을 참조하세요.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Automation의 Python3 Runbook에 대한 공개 미리 보기

**유형:** 새로운 기능

이제 Azure Automation은 Azure 글로벌 클라우드의 모든 지역에서 Python 3 클라우드 및 하이브리드 Runbook 실행을 공개 미리 보기로 지원합니다. 자세한 내용은 [공지]((https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) 를 참조하세요.

## <a name="november-2020"></a>2020년 11월

### <a name="dsc-support-for-ubuntu-1804"></a>Ubuntu 18.04에 대한 DSC 지원

**유형:** 새로운 기능

이러한 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 Linux 배포판](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)을 참조하세요.

## <a name="october-2020"></a>2020년 10월

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>스위스 북부의 Automation 및 State Configuration GA 지원

**유형:** 새로운 기능

스위스 북부의 Automation 계정 및 State Configuration 가용성. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)를 참조하세요.

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>브라질 남동부의 Automation 및 State Configuration GA 지원

**유형:** 새로운 기능

브라질 남동부의 Automation 계정 및 State Configuration 가용성. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)를 참조하세요.

### <a name="update-management-availability-in-south-central-us"></a>미국 중남부의 업데이트 관리 가용성

**유형:** 새로운 기능

미국 중남부 지역에서 업데이트 관리 기능을 지원하도록 Azure Automation 지역 매핑이 업데이트되었습니다. 이 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 지역 매핑](how-to/region-mappings.md#supported-mappings)을 참조하세요.

## <a name="september-2020"></a>2020년 9월

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>작업 시간 외 VM 시작/중지 Runbook이 Azure Az 모듈을 사용하도록 업데이트됨

**유형:** 새로운 기능

VM 시작/중지 Runbook이 Azure Resource Manager 모듈 대신 Az 모듈을 사용하도록 업데이트되었습니다. 이러한 변경 내용을 반영하기 위한 설명서 업데이트는 [작업 시간 외 VM 시작/중지](automation-solution-vm-management.md) 개요를 참조하세요.

## <a name="august-2020"></a>2020년 8월

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Azure Arc를 지원하기 위해 DSC 확장 게시됨

**유형:** 새로운 기능

Azure Automation State Configuration을 사용하여 구성을 중앙에 저장하고 Azure Arc 지원 서버 DSC VM 확장을 통해 하이브리드 연결 머신을 원하는 상태로 유지합니다. 자세한 내용은 [Arc 지원 서버 VM 확장 개요](../azure-arc/servers/manage-vm-extensions.md)를 참조하세요.

### <a name="july-2020"></a>2020년 7월

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Automation에서 Private Link 지원의 공개 미리 보기 도입

**유형:** 새로운 기능

Azure Private Link를 사용하여 프라이빗 엔드포인트를 사용하여 가상 네트워크를 Azure Automation에 안전하게 연결합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)를 참조하세요.

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Windows Server 2008 R2에 대한 Hybrid Runbook Worker 지원

**유형:** 새로운 기능

Automation Hybrid Runbook Worker는 Windows Server 2008 R2 운영 체제를 지원합니다. 이러한 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 운영 체제](automation-windows-hrw-install.md#supported-windows-operating-system)를 참조하세요.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Windows Server 2008 R2에 대한 업데이트 관리 지원

**유형:** 새로운 기능

업데이트 관리는 Windows Server 2008 R2 운영 체제의 평가 및 패치를 지원합니다. 이러한 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 운영 체제](update-management/operating-system-requirements.md)를 참조하세요.

### <a name="automation-diagnostic-logs-schema-update"></a>Automation 진단 로그 스키마 업데이트

**유형:** 새로운 기능

Log Analytics 서비스에서 Azure Automation 로그 데이터의 스키마를 변경했습니다. 자세한 내용은 [Azure Monitor 로그에 Azure Automation 작업 데이터 전달](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)을 참조하세요.

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse가 Automation 업데이트 관리 지원

**유형:** 새로운 기능

Azure Lighthouse는 서비스 공급자 및 고객을 위한 업데이트 관리를 통해 위임된 리소스 관리를 지원합니다. 자세한 내용은 [여기](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/)를 읽어보세요.

## <a name="june-2020"></a>2020년 6월

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>US Gov 애리조나 지역의 Automation 및 업데이트 관리 가용성

**유형:** 새로운 기능

Automation 계정 및 업데이트 관리는 US Gov 애리조나에서 사용할 수 있습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)를 참조하세요.

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Az 모듈을 사용하도록 업데이트된 Hybrid Runbook Worker 온보딩 스크립트

**유형:** 새로운 기능

New-OnPremiseHybridWorker Runbook이 Az 모듈을 지원하도록 업데이트되었습니다. 자세한 내용은 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)의 패키지를 참조하세요.

### <a name="update-management-availability-in-china-east-2"></a>중국 동부 2의 업데이트 관리 가용성

**유형:** 새로운 기능

중국 동부 2 지역에서 업데이트 관리 기능을 지원하도록 Azure Automation 지역 매핑이 업데이트되었습니다. 이 변경 내용을 반영하기 위한 설명서 업데이트는 [지원되는 지역 매핑](how-to/region-mappings.md#supported-mappings)을 참조하세요.

## <a name="may-2020"></a>2020년 5월

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>지역별에서 Automation 계정별 URL로 업데이트된 Automation 서비스 DNS 레코드

**유형:** 새로운 기능

Azure Automation DNS 레코드가 Private Link를 지원하도록 업데이트되었습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)를 참조하세요.

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Automation Runbook 및 DSC 스크립트를 기본적으로 암호화하는 기능 추가

**유형:** 새로운 기능

자산의 보안을 개선할 뿐만 아니라 Runbook 및 DSC 스크립트도 암호화되어 Azure Automation 보안을 강화합니다.

## <a name="april-2020"></a>2020년 4월

### <a name="retirement-of-the-automation-watcher-task"></a>Automation Watcher 작업 중지

**유형:** 변경 계획

이제 Azure Logic Apps는 이벤트를 모니터링하고, 반복 작업을 예약하고, 동작을 트리거하는 데 권장되고 지원되는 방법입니다. Watcher 작업 기능에는 더 이상 투자하지 않습니다. 자세한 내용은 [Logic Apps를 사용하여 반복되는 자동화 작업 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조하세요.

## <a name="march-2020"></a>2020년 3월

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Azure 상용 및 정부 클라우드에서 IL5(Impact Level 5) 컴퓨팅 격리 지원

**유형:** .

Azure Automation Hybrid Runbook Worker는 영향 수준 5 워크로드를 지원하기 위해 Azure Government에서 사용할 수 있습니다. 자세히 알아보려면 [설명서](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)를 참조하세요.

## <a name="february-2020"></a>2020년 2월

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Azure 가상 네트워크 서비스 태그에 대한 지원 도입

**유형:** 새로운 기능

서비스 태그의 Automation 지원을 통해 Automation 서비스의 트래픽을 하위 시나리오에서 허용하거나 거부할 수 있습니다. 자세히 알아보려면 [설명서](automation-hybrid-runbook-worker.md#service-tags)를 참조하세요.

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Azure Automation 서비스에 대한 TLS 1.2 지원 사용

**유형:** 변경 계획

Azure Automation은 웹후크, DSC 노드 및 Hybrid Worker를 통해 TLS 1.2 및 모든 클라이언트 호출을 완벽하게 지원합니다. TLS 1.1 및 TLS 1.0은 고객이 표준화하고 TLS 1.2로 완전히 마이그레이션할 때까지 이전 클라이언트와의 호환성을 위해 계속 지원됩니다.

## <a name="january-2020"></a>2020년 1월

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Azure Automation에 대한 고객 관리형 키의 공개 미리 보기 도입

**유형:** 새로운 기능

고객은 자신의 관리형 키를 사용하여 Azure Automation 자산의 암호화를 관리하고 보호할 수 있습니다. 자세한 내용은 [고객 관리형 키 사용](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)을 참조하세요.

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Azure Automation용 ASM(Azure Service Management) REST API 사용 중지

**유형:** 사용 중지

Azure Automation용 ASM(Azure Service Management) REST API는 2020년 1월 30일 이후 사용 중지되며 더 이상 지원되지 않습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Automation 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.
