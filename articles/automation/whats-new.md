---
title: Azure Automation의 새로운 기능
description: 매월 업데이트되는 Azure Automation에 대한 중요 업데이트입니다.
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions
ms.openlocfilehash: ea18171bdd957781e22743c3e59690fc8d4703f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432437"
---
# <a name="whats-new-in-azure-automation"></a>Azure Automation의 새로운 기능

Azure Automation은 지속적으로 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월이 넘은 항목을 찾으려는 경우 [Azure Automation의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

## <a name="october-2021"></a>2021년 10월

### <a name="preview-support-for-hybrid-runbook-worker-extension-for-azure-vms-and-arc-enabled-servers"></a>Azure VM 및 Arc 지원 서버에 대한 Hybrid Runbook Worker 확장에 대한 미리 보기 지원

**유형:** 새로운 기능

Azure Automation은 Arc 지원 서버를 통해 Azure VM 및 비 Azure 머신에 대한 User Hybrid Runbook Worker 네이티브 통합을 릴리스했습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support)를 참조하세요.

### <a name="preview-support-for-azure-active-directory-authentication"></a>Azure Active Directory 인증에 대한 미리 보기 지원

**유형:** 새로운 기능

Azure Automation은 모든 Automation 서비스 퍼블릭 엔드포인트에 대한 Azure AD 인증 지원을 통해 중요한 보안 기능을 추가했습니다. 이 기능은 Azure VM 및 Arc 지원 서버에 대한 Hybrid Runbook Worker 확장 지원을 통해 구현되었습니다.

이렇게 하면 인증서에 대한 종속성이 제거되고 로컬 인증 방법을 사용하지 않음으로써 엄격한 감사 및 규정 준수 요구 사항을 충족할 수 있습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support)를 참조하세요.

### <a name="source-control-enabled-to-use-managed-identities"></a>관리 ID를 사용하도록 설정된 원본 제어

**유형:** 새로운 기능

이제 Azure Automation의 원본 제어 통합에서 실행 계정 대신 [관리 ID](automation-security-overview.md#managed-identities)를 사용할 수 있습니다. 자세한 내용은 [원본 제어 통합 필수 구성 요소](source-control-integration.md#prerequisites)를 참조하세요.

## <a name="september"></a>9월

### <a name="support-for-az-modules-by-default"></a>기본적으로 Az 모듈 지원

**형식:** 새로운 기능

이제 Azure Automation은 기본적으로 Az 모듈을 지원합니다. 새로 만든 Automation 계정에는 기본적으로 최신 버전의 Az 모듈 6.4.0이 포함됩니다. 또한 Automation에는 기존 Automation 계정에서 Az 모듈을 업데이트할 수 있도록 하는 Azure Portal - **Az 모듈 업데이트** 옵션이 포함되어 있습니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-az-module-support)를 참조하세요.

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

고객은 자신의 관리형 키를 사용하여 Azure Automation 자산의 암호화를 관리하고 보호할 수 있습니다. 고객 관리형 키를 도입하여 Azure Key Vault에서 만들고 관리하는 키를 사용하여 추가 암호화 계층을 통해 기본 암호화를 보완할 수 있습니다. 이 추가 암호화 수준은 조직의 규정 또는 규정 준수 요구 사항을 충족하는 데 도움이 됩니다.

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

이제 Azure Automation은 Azure 글로벌 및 Azure Government 지역의 클라우드 및 하이브리드 작업에 대해 [시스템이 할당한 관리 ID](./automation-security-overview.md#managed-identities)를 지원합니다. 자세한 내용은 [공지](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Automation 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.
