---
title: Azure Automation의 새로운 기능
description: 매월 업데이트되는 Azure Automation에 대한 중요 업데이트입니다.
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 84644759a3f2f887662faae686814f032410266b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123107936"
---
# <a name="whats-new-in-azure-automation"></a>Azure Automation의 새로운 기능

Azure Automation은 지속적으로 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월이 넘은 항목을 찾으려는 경우 [Azure Automation의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

## <a name="august-2021"></a>2021년 8월

### <a name="azure-policy-guest-configuration"></a>Azure Policy 게스트 구성

**유형:** 변경 계획

고객은 Azure Automation 상태 구성에서 Azure Policy 게스트 구성으로의 마이그레이션을 평가하고 계획해야 합니다. 자세한 내용은 [Azure Policy 게스트 구성](../governance/policy/concepts/guest-configuration.md)을 참조하세요.

## <a name="july-2021"></a>2021년 7월

### <a name="preview-support-for-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID에 대한 미리 보기 지원

**유형:** 새로운 기능

이제 Azure Automation은 Azure 글로벌, Azure Government 및 Azure 중국 지역의 클라우드 작업에 대해 [사용자 할당 관리 ID](automation-secure-asset-encryption.md)를 지원합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/)를 참조하세요.

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Azure Automation용 고객 관리형 키의 일반 공급

**유형:** 새로운 기능

고객은 자신의 관리형 키를 사용하여 Azure Automation 자산의 암호화를 관리하고 보호할 수 있습니다. 고객 관리형 키를 도입하여 Azure Key Vault에서 만들고 관리하는 키를 사용하여 추가 암호화 계층을 통해 기본 암호화를 보완할 수 있습니다. 이 추가 암호화는 조직의 규정 또는 규정 준수 요구 사항을 충족하는 데 도움이 됩니다.

자세한 내용은 [고객 관리형 키 사용](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)을 참조하세요.

## <a name="june-2021"></a>2021년 6월

### <a name="security-update-for-log-analytics-contributor-role"></a>Log Analytics 기여자 역할에 대한 보안 업데이트

**유형:** 변경 계획

Microsoft는 Log Analytics 기여자 역할에서 Automation 계정 권한을 제거하려고 합니다. 현재 기본 제공 [Log Analytics 기여자](./automation-role-based-access-control.md#log-analytics-contributor) 역할은 구독 [기여자](./../role-based-access-control/built-in-roles.md#contributor) 역할로 권한을 에스컬레이션할 수 있습니다. Automation 계정 실행 계정은 처음에 구독에 대한 기여자 권한으로 구성되므로 공격자가 새 Runbook을 만들고 구독에 대한 기여자로서 코드를 실행하는 데 사용할 수 있습니다.

이러한 보안 위험으로 인해 Automation 작업을 실행하는 데 Log Analytics 기여자 역할을 사용하지 않는 것이 좋습니다. 대신 Azure Automation 기여자 사용자 지정 역할을 만들고 Automation 계정과 관련된 작업에 사용합니다. 구현 단계는 [사용자 지정 Azure Automation 기여자 역할](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)을 참조하세요.

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>미국 서부 3에서 사용할 수 있는 Automation 및 State Configuration 지원

**유형:** 새로운 기능

자세한 내용은 [Azure의 데이터 보존](https://azure.microsoft.com/global-infrastructure/data-residency/)을 참조하고 드롭다운 목록에서 지리를 선택하세요.

## <a name="may-2021"></a>2021년 5월

### <a name="startstop-vms-during-off-hours-v1"></a>작업 시간 외 VM 시작/중지(v1)

**유형:** 변경 계획

작업 시간 외 VM 시작/중지(v1)는 2022년 5월 21일에 사용 중단됩니다. 고객은 VM 시작/중지 v2(미리 보기)로의 마이그레이션을 평가하고 계획해야 합니다. 자세한 내용은 [시작/중지 v2 개요](../azure-functions/start-stop-vms/overview.md)(미리 보기)를 참조하세요.

## <a name="april-2021"></a>2021년 4월

### <a name="support-for-update-management-and-change-tracking"></a>업데이트 관리 및 변경 내용 추적 지원

**유형:** 새로운 기능

지역 매핑은 한국 중부, 노르웨이 동부, 아랍에미리트 북부, 미국 중북부, 브라질 남부에서 업데이트 관리와 변경 내용 추적을 지원하도록 업데이트되었습니다. 자세한 내용은 [지원되는 매핑](./how-to/region-mappings.md#supported-mappings)을 참조하세요.

### <a name="support-for-system-assigned-managed-identities"></a>시스템이 할당한 관리 ID 지원

**유형:** 새로운 기능

이제 Azure Automation은 Azure 글로벌 및 Azure Government 지역의 클라우드 및 하이브리드 작업에 대해 [시스템이 할당한 관리 ID](./automation-security-overview.md#managed-identities-preview)를 지원합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/)를 참조하세요.

## <a name="march-2021"></a>2021년 3월

### <a name="new-azure-automation-built-in-policy-definitions"></a>새 Azure Automation 기본 제공 정책 정의

**유형:** 새로운 기능

Azure Automation에는 5개의 새로운 기본 제공 정책 정의가 추가되었습니다.

- Automation 계정은 공용 네트워크 액세스를 사용하지 않도록 설정해야 함
- Azure Automation 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함
- 공용 네트워크 액세스를 사용하지 않도록 Azure Automation 계정 구성
- Azure Automation 계정에서 프라이빗 엔드포인트 연결 구성
- Automation 계정에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함

자세한 내용은 [Azure Policy 참조](./policy-reference.md)를 참조하세요.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>인도 남부의 Automation 및 State Configuration 선언 GA 지원

**유형:** 새로운 기능

인도 남부에서 프로세스 Automation 및 State Configuration 기능을 사용합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/)를 참조하세요.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>영국 서부의 Automation 및 State Configuration 선언 GA 지원

**유형:** 새로운 기능

영국 서부에서 프로세스 Automation 및 State Configuration 기능을 사용합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)를 참조하세요.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>아랍에미리트 중부의 Automation 및 State Configuration 선언 GA 지원

**유형:** 새로운 기능

아랍에미리트 중부에서 프로세스 Automation 및 State Configuration 기능을 사용합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/)를 참조하세요.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>오스트레일리아 중부 2, 노르웨이 서부, 프랑스 남부에서 사용할 수 있는 Automation 및 State Configuration 지원

**유형:** 새로운 기능

각 지역의 지리를 선택하여 [데이터 보존 페이지](https://azure.microsoft.com/global-infrastructure/data-residency/)에서 자세한 내용을 참조하세요.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Windows 및 Linux에서 Hybrid Worker를 설치하기 위해 추가된 새 스크립트

**유형:** 새로운 기능

Windows 또는 Linux 컴퓨터에서 Hybrid Runbook Worker를 설정하는 Azure Automation 주요 시나리오 중 하나를 해결하는 Azure Automation [GitHub 리포지토리](https://github.com/azureautomation)에 두 개의 새로운 스크립트가 추가되었습니다. 이 스크립트는 새 VM을 만들거나 기존 VM을 사용하고, 필요한 경우 Log Analytics 작업 영역을 만들고, Windows용 Log Analytics 에이전트 또는 Linux용 Log Analytics 에이전트를 설치하고, 컴퓨터를 Log Analytics 작업 영역에 등록합니다. Windows 스크립트 이름은 **Create Automation Windows HybridWorker** 이고 Linux 스크립트 이름은 **Create Automation Linux HybridWorker** 입니다.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Azure Resource Manager 템플릿 웹후크를 통해 Runbook 호출

**유형:** 새로운 기능

자세한 내용은 [ARM 템플릿에서 웹후크 사용](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template)을 참조하세요.

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>이제 Azure 업데이트 관리에서 Centos 8.x, Red Hat Enterprise Linux Server 8.x, SUSE Linux Enterprise Server 15를 지원합니다.

**유형:** 새로운 기능

자세한 내용은 지원되는 Linux 운영 체제의 [전체 목록](./update-management/operating-system-requirements.md)을 참조하세요.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>브라질 남부 및 동남아시아에 대한 지역 내 데이터 보존 지원

**유형:** 새로운 기능

브라질 남부 및 동남 아시아를 제외한 모든 지역에서 Azure Automation 데이터는 BCDR(비즈니스 연속성 및 재해 복구)을 제공하기 위해 다른 지역(Azure 쌍을 이루는 지역)에 저장됩니다. 브라질 및 동남 아시아 지역에 한해, 이러한 지역에 대한 데이터 보존 요구 사항을 수용하기 위해 동일한 지역에 Azure Automation 데이터를 저장합니다. 자세한 내용은 [Azure Automation의 지역에서 복제](./automation-managing-data.md#geo-replication-in-azure-automation)를 참조하세요.

## <a name="february-2021"></a>2021년 2월

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>일본 서부의 Automation 및 State Configuration 선언 GA 지원

**유형:** 새로운 기능

일본 서부 지역의 Automation 계정 및 State Configuration 가용성 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)를 참조하세요.

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Hybrid Worker에서 Runbook 실행을 적용하는 사용자 지정 Azure Policy 준수가 도입됨

**유형:** 새로운 기능

새 Azure Policy 준수 규칙을 사용하면 작업, 웹후크 및 작업 일정 만들기가 Hybrid Worker 그룹에서만 실행되도록 할 수 있습니다.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>미국 동부, 프랑스 중부 및 북유럽 지역의 업데이트 관리 가용성

**유형:** 새로운 기능

Automation 업데이트 관리 기능은 미국 동부, 프랑스 중부 및 북유럽 지역에서 사용할 수 있습니다. 이 변경을 반영하는 설명서 업데이트 내용은 [지원되는 지역 매핑](how-to/region-mappings.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Automation 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.
