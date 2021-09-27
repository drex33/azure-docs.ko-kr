---
title: 지침 및 모범 사례
description: 클라우드 및 온-프레미스 워크로드를 클라우드로 백업하기 위한 모범 사례 및 지침을 알아봅니다.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: ecac56034c78cba09133fc12ca4ac8827afb0ea2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806724"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>클라우드 및 온-프레미스 워크로드를 클라우드로 백업

## <a name="introduction"></a>소개

Azure Backup은 인프라를 사용하지 않는 단순하고 안전하며 비용 효율적인 솔루션을 통해 Azure에서 데이터 자산을 포괄적으로 보호합니다. 광범위한 워크로드에 대한 Azure 기본 제공 데이터 보호 솔루션입니다. 이를 통해 클라우드에서 실행되는 중요 업무용 워크로드를 보호하고 백업을 항상 사용할 수 있으며 전체 백업 공간에서 대규모로 관리할 수 있습니다.

### <a name="intended-audience"></a>대상 그룹

이 문서의 주요 대상 사용자는 IT 및 애플리케이션 관리자와 Azure의 기본 제공 데이터 보호 기술인 Azure Backup의 기능과 배포를 보다 효과적으로 보호하는 솔루션을 효율적으로 구현하는 방법을 알아보려는 중대기업의 구현 담당자입니다. 이 문서에서는 핵심 Azure 기술 및 데이터 보호 개념에 대한 지식과 백업 솔루션을 사용한 경험이 있다고 가정합니다. 이 문서에서 설명하는 지침을 통해 설정된 패턴을 사용하여 Azure에서 백업 솔루션을 보다 쉽게 디자인하고 알려진 문제를 방지할 수 있습니다.

### <a name="how-this-article-is-organized"></a>이 문서의 구성 방식

Azure에서 인프라 및 애플리케이션 보호를 시작하는 것은 쉽지만, 기본 Azure 리소스를 올바르게 설정하고 최적으로 사용하면 가치를 창출하는 데 걸리는 기간을 단축할 수 있습니다. 이 문서에서는 Azure Backup 배포를 최적으로 구성하기 위한 디자인 고려 사항 및 지침을 간략하게 설명합니다. 핵심 구성 요소(예: Recovery Services 자격 증명 모음, 백업 정책) 및 개념(예: 거버넌스)과 해당 기능을 고려하는 방법을 검토하며 자세한 제품 설명서에 대한 링크를 제공합니다.

## <a name="architecture"></a>Architecture

![Azure Backup 아키텍처](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>워크로드

Azure Backup를 사용하면 다양한 워크로드(온-프레미스 및 클라우드)에서 데이터를 보호할 수 있습니다. Azure Backup은 Azure의 안전하고 신뢰할 수 있는 기본 제공 데이터 보호 메커니즘입니다. 관리 오버헤드 없이 여러 워크로드에 걸쳐 보호를 원활하게 확장할 수 있습니다. PowerShell, CLI, Azure Resource Manager 템플릿 및 REST API 등 여러 자동화 채널도 있습니다.

* **확장성, 내구성, 보안이 우수한 스토리지 -** Azure Backup은 보안 및 고가용성 기능을 기본 제공하는 안정적인 Blob Storage를 사용합니다. 백업 데이터에 대해 LRS, GRS 또는 RA-GRS 스토리지를 선택할 수 있습니다.  

* **기본 워크로드 통합 -** Azure Backup은 Azure 워크로드(VM, SAP HANA, Azure VM의 SQL, Azure Files)와의 기본 통합을 제공하므로 사용자가 자동화 또는 인프라를 관리하여 에이전트를 배포하거나, 새 스크립트를 작성하거나, 스토리지를 프로비전하지 않아도 됩니다.

### <a name="data-plane"></a>데이터 평면

* **자동화된 스토리지 관리** - Azure Backup은 백업 데이터용 스토리지 계정 프로비전 및 관리를 자동화하여 백업 데이터 증가에 따라 확장되도록 합니다.

* **악성 삭제 방지 –** 백업 일시 삭제를 통해 실수로 인한 삭제 또는 악의적 삭제 시도로부터 백업을 보호합니다. 삭제된 백업 데이터는 14일 동안 무료로 저장되며 이 상태에서 복구할 수 있습니다.

* **암호화된 백업 -** Azure Backup은 Azure RBAC 및 암호화와 같은 Azure 플랫폼의 기본 제공 보안 기능을 활용하여 백업 데이터가 안전한 방식으로 저장되도록 합니다.

* **백업 데이터 수명 주기 관리 -** Azure Backup은 이전 백업 데이터를 자동으로 정리하여 보존 정책을 준수합니다. 또한 운영 스토리지에서 자격 증명 모음 스토리지까지 데이터를 계층화할 수 있습니다.

### <a name="management-plane"></a>관리 평면

* **액세스 제어** – 자격 증명 모음(Recovery Services 및 백업 자격 증명 모음)은 관리 기능을 제공하며, Azure Portal, 백업 센터, 자격 증명 모음 대시보드, SDK, CLI, 심지어 REST API를 통해 액세스할 수 있습니다. 또한 권한 있는 백업 관리자에게만 백업에 대한 액세스를 제한하는 옵션을 제공하는 Azure RBAC 경계이기도 합니다.

* **정책 관리** – 각 자격 증명 모음 내의 Azure Backup 정책은 백업이 트리거되는 시기 및 보존 기간을 정의합니다. 이러한 정책을 관리하고 여러 항목에서 적용할 수도 있습니다.

* **모니터링 및 보고** – Azure Backup은 Log Analytics와 통합되며 통합 문서를 통해 보고서를 볼 수 있는 기능도 제공합니다.

* **스냅샷 관리** – Azure Backup은 일부 Azure 네이티브 워크로드(VM 및 Azure Files)에 대한 스냅샷을 생성하고 관리하므로 이러한 스냅샷에서 신속하게 복원할 수 있습니다. 이 옵션은 데이터를 원본 스토리지로 복구하는 데 걸리는 시간을 대폭 단축합니다.

## <a name="vault-considerations"></a>자격 증명 모음 고려 사항

Azure Backup은 자격 증명 모음(Recovery Services 및 Backup 자격 증명 모음)을 사용하여 백업을 오케스트레이션하고 관리합니다. 또한 자격 증명 모음을 사용하여 백업 데이터를 저장합니다. 조직은 효과적인 자격 증명 모음 디자인을 통해 Azure에서 백업 자산을 구성 및 관리하는 구조를 구축하여 비즈니스 우선 순위를 지원할 수 있습니다. 자격 증명 모음을 만들 때 다음과 같은 지침을 고려합니다.  

### <a name="align-to-subscription-design-strategy"></a>구독 디자인 전략에 부합

자격 증명 모음은 구독으로 범위가 지정되므로 자격 증명 모음 디자인은 구독이 특정 애플리케이션 또는 서비스에 따라 또는 애플리케이션 아키타입을 따라 격리되는 '애플리케이션 범주 전략'과 같은 구독 디자인 전략을 충족해야 합니다. 자세한 내용은 [이 문서](/azure/cloud-adoption-framework/decision-guides/subscriptions/)를 참조하세요.

### <a name="single-or-multiple-vault"></a>단일 또는 다중 자격 증명 모음

단일 자격 증명 모음 또는 여러 자격 증명 모음을 사용하여 백업을 구성하고 관리할 수 있습니다. 다음 지침을 고려하세요.

* 워크로드가 모두 단일 구독과 단일 리소스에 의해 관리되는 경우 단일 자격 증명 모음을 사용하여 백업 공간을 모니터링하고 관리할 수 있습니다.

* 워크로드가 여러 구독에 걸쳐 분산된 경우 구독당 하나 이상의 자격 증명 모음을 만들 수 있습니다.
  * 백업 센터를 사용하면 단일 창에서 백업과 관련된 모든 작업을 관리할 수 있습니다. [여기를 참조하세요]().
  * 통합 문서 템플릿을 사용하여 보기를 사용자 지정할 수 있습니다. Backup 탐색기는 Azure VM을 위한 이러한 템플릿 중 하나입니다. [여기를 참조하세요](monitor-azure-backup-with-backup-explorer.md).
  * 여러 자격 증명 모음에서 일관된 정책이 필요한 경우 Azure Policy를 사용하여 여러 자격 증명 모음에 백업 정책을 전파할 수 있습니다. ['deployifnotexists'](../governance/policy/concepts/effects.md#deployifnotexists) 효과를 사용하여 여러 자격 증명 모음에 백업 정책을 전파하는 사용자 지정 [Azure Policy 정의](../governance/policy/concepts/definition-structure.md)를 작성할 수 있습니다. 또한 이 Azure Policy 정의를 특정 범위(구독 또는 RG)에 [할당](../governance/policy/assign-policy-portal.md)할 수도 있습니다. 그러면 Azure Policy 할당 범위에서 모든 Recovery Services 자격 증명 모음에 '백업 정책' 리소스를 배포할 수 있습니다. 백업 빈도, 보존 등의 백업 정책 설정은 사용자가 Azure Policy 할당에서 매개 변수로 지정해야 합니다.

* 조직 규모가 증가함에 따라 백업 정책 정렬, 자격 증명 모음 통합, 비용 절감을 위한 중복 수준 절충(GRS에서 LRS로 이동) 등의 이유로 구독 간에 워크로드를 이동해야 할 수 있습니다.  Azure Backup은 Azure 구독 간에 또는 동일한 구독 내 다른 리소스 그룹으로 Recovery Services 자격 증명 모음 이동을 지원합니다. [여기를 참조하세요](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>기본 설정 검토

자격 증명 모음에서 백업을 구성하기 전에 스토리지 복제 유형 및 보안 설정에 대한 기본 설정을 검토해야 합니다.

* 기본적으로 '스토리지 복제 유형'은 GRS(지역 중복)로 설정됩니다. 백업을 구성한 후에는 수정할 수 없습니다. 설정을 검토하고 수정하려면 [다음](backup-create-rs-vault.md#set-storage-redundancy) 단계를 수행하세요.

* 새로 만든 자격 증명 모음에서는 기본적으로 '일시 삭제'가 설정되어 실수로 인한 삭제 또는 악의적인 삭제로부터 백업 데이터를 보호합니다. 설정을 검토하고 수정하려면 [다음](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 단계를 수행하세요.

* '지역 간 복원'을 사용하여 쌍으로 연결된 Azure 지역인 보조 지역에서 Azure VM을 복원할 수 있습니다. 이 옵션을 사용하면 감사 또는 규정 준수 요구 사항을 충족하는 드릴을 수행하고 주 지역에서 재해가 발생한 경우 VM 또는 해당 디스크를 복원할 수 있습니다. CRR은 모든 GRS 자격 증명 모음에 대한 옵트인 기능입니다. [여기를 참조하세요](backup-create-rs-vault.md#set-cross-region-restore).

* 자격 증명 모음 디자인을 완료하기 전에 [자격 증명 모음 지원 매트릭스](backup-support-matrix.md#vault-support)를 검토하여 디자인 선택에 영향을 주거나 제한할 수 있는 요인을 파악합니다.

## <a name="backup-policy-considerations"></a>백업 정책 고려 사항

Azure Backup 정책에는 '일정'(백업 수행 시기) 및 '보존'(백업 유지 기간)의 두 가지 구성 요소가 있습니다. 백업 중인 데이터 유형, RTO/RPO 요구 사항, 운영 또는 규정 준수 요구 사항, 워크로드 유형(예: VM, 데이터베이스, 파일)에 따라 정책을 정의할 수 있습니다. [여기를 참조하세요](backup-architecture.md#backup-policy-essentials).

백업 정책을 작성할 때 다음 지침을 고려합니다.

### <a name="schedule-considerations"></a>일정 고려 사항

* 단일 정책 내에서 동일한 일정 시작 시간, 빈도 및 보존 설정이 필요한 VM을 그룹화하는 것이 좋습니다.

* 예약 백업 시작 시간이 사용량이 많지 않은 프로덕션 애플리케이션 시간 동안 발생하도록 해야 합니다.

* 백업 트래픽이 분산되도록 VM마다 다른 일중 시간에 백업하는 것을 고려하고 시간이 겹치지 않도록 합니다.

### <a name="retention-considerations"></a>보존 고려 사항

* 단기 보존은 '분' 단위 또는 '매일'일 수 있습니다. 백업 지점의 '주간', '월간' 또는 '연간' 보존은 장기 보존이라고 합니다.

* 장기 보존:
  * 계획됨(규정 준수 요구 사항) - 현재 시점으로부터 수년 후에 데이터가 필요할 것을 미리 아는 경우 장기 보존을 사용합니다.
  * 계획되지 않음(주문형 요구 사항) - 미리 알지 못하는 경우 특정 사용자 지정 보존 설정과 함께 주문형으로 사용할 수 있습니다(이러한 사용자 지정 보존 설정은 정책 설정의 영향을 받지 않음).

* 사용자 지정 보존을 통한 주문형 백업 - 백업 정책을 통해 예약되지 않은 백업을 수행해야 하는 경우 주문형 백업을 사용할 수 있습니다. 이는 예약 백업에 적합하지 않은 백업이나 세분화된 백업을 수행하는 데 유용할 수 있습니다. 예약 백업은 하루에 하나의 백업만 허용하므로 예를 들어 매일 여러 번 IaaS VM을 백업하는 경우가 해당됩니다. 예약된 정책에 정의된 보존 정책은 주문형 백업에 적용되지 않는다는 점에 유의해야 합니다.

### <a name="optimize-backup-policy"></a>백업 정책 최적화

* 비즈니스 요구 사항이 변경되면 보존 기간을 늘리거나 줄여야 할 수도 있습니다. 이 경우 다음을 예상할 수 있습니다.  
  * 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 표시되고 유지됩니다.
  * 보존 기간을 줄이면 복구 지점이 다음 정리 작업에서 정리하도록 표시되고, 결국 삭제됩니다.
  * 최신 보존 규칙은 모든 보존 시점(주문형 보존 지점은 제외)에 적용됩니다. 따라서 보존 기간을 늘려(예: 100일로) 백업이 수행된 후 보존을 줄이면(예: 100일에서 7일로) 모든 백업 데이터는 마지막으로 지정된 보존 기간(7일)에 따라 보존됩니다.

* Azure Backup은 '보호를 중지하고 백업을 관리'할 수 있는 유연성을 제공합니다.
  * '보호를 중지하고 백업 데이터를 보존'합니다. 데이터 원본(VM, 애플리케이션)을 사용 중지하거나 폐기하지만 감사 또는 규정 준수를 위해 데이터를 유지해야 하는 경우 이 옵션을 사용하여 이후의 모든 백업 작업에서 데이터 원본 보호를 중지하고 백업된 복구 지점을 보존할 수 있습니다. 그런 다음 VM 보호를 복원하거나 다시 시작할 수 있습니다.
  * '보호를 중지하고 백업 데이터를 삭제'합니다. 이 옵션을 선택하면 VM을 보호는 이후의 모든 백업 작업이 중지되고 모든 복구 지점을 삭제합니다. VM을 복원하거나 백업 재개 옵션을 사용할 수 없습니다.

  * 데이터 보존을 사용하여 보호가 중지된 데이터 원본의 보호를 다시 시작하는 경우 보존 규칙이 적용됩니다. 예약된 시간에 만료된 복구 지점이 제거됩니다.

* 정책 디자인을 완료하기 전에 디자인 선택에 영향을 줄 수 있는 다음 요소에 대해 알고 있어야 합니다.
  * 백업 정책은 자격 증명 모음으로 범위가 지정됩니다.
  * 정책당 항목 수에는 제한이 있습니다(예: 100개 VM). 크기를 조정하려면 동일한 일정 또는 다른 일정으로 중복 정책을 작성할 수 있습니다.
  * 특정 복구 지점을 선택적으로 삭제할 수는 없습니다.
  * 예약 백업을 완전히 비활성화하고 데이터 원본을 보호된 상태로 유지할 수는 없습니다. 정책을 사용하여 구성할 수 있는 가장 낮은 백업 빈도는 매주 한 번의 예약 백업입니다. 또는, 데이터 보존을 사용하여 보호를 중지하고 백업이 필요할 때마다 보호를 활성화하고, 주문형 백업을 수행한 다음 보호를 해제하지만 백업 데이터를 보존할 수 있습니다. [여기를 참조하세요](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>보안 고려 사항

백업 데이터를 보호하고 비즈니스의 보안 요구를 충족하는 데 도움이 되도록, Azure Backup은 중요한 데이터 및 시스템의 고의적인 공격 및 남용에 대해 기밀성, 무결성 및 가용성 보증을 제공합니다. Azure Backup 솔루션에 대한 다음 보안 지침을 고려하세요.

### <a name="authentication-and-authorization"></a>인증 및 권한 부여

* Azure RBAC(역할 기반 액세스 제어)를 사용하면 세부적으로 액세스를 관리하고, 팀 내 업무를 분장하고, 사용자에게 작업을 수행하는 데 필요한 만큼의 액세스 권한만 부여할 수 있습니다. [여기를 참조하세요](backup-rbac-rs-vault.md).

* Azure Backup은 백업 관리 작업을 제어할 수 있는 세 가지 기본 제공 역할인 백업 기여자, 운영자 및 읽기 권한자 역할을 제공합니다. [여기를 참조하세요](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup에는 보안 취약성을 예방, 감지 및 대응하기 위해 서비스에 기본 제공되는 몇 가지 보안 컨트롤이 있습니다(자세히 알아보기).

* Recovery Services 자격 증명 모음에 사용되는 스토리지 계정은 격리되며 악의적인 목적을 위해 사용자가 액세스할 수 없습니다. 액세스는 복원과 같은 Azure Backup 관리 작업을 통해서만 허용됩니다.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>전송 중 및 미사용 데이터 암호화

암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다.

* Azure 내에서 Azure 스토리지와 자격 증명 모음 간에 이동하는 데이터는 HTTPS를 통해 보호됩니다. 이 데이터는 Azure 네트워크에 남아 있습니다.

* 백업 데이터는 Microsoft 관리 키를 사용하여 자동으로 암호화됩니다. 또는 [고객 관리형 키](encryption-at-rest-with-cmk.md)라고도 하는 고유한 키를 사용할 수 있습니다.

* Azure Backup은 ADE(Azure Disk Encryption)로 암호화된 OS/데이터 디스크가 있는 Azure VM의 백업 및 복원을 지원합니다. [여기를 참조하세요](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>의도하지 않은 삭제로부터 백업 데이터 보호

Azure Backup은 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다. 일시 삭제를 사용하는 경우 사용자가 VM, SQL Server 데이터베이스, Azure 파일 공유, SAP HANA 데이터베이스의 백업을 삭제하면 백업 데이터가 추가로 14일 동안 보존되어 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. '일시 삭제' 상태의 백업 데이터에 대한 14일의 추가 보존은 고객에게 비용을 부과하지 않습니다. [여기를 참조하세요](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>의심스러운 활동의 모니터링 및 경고

Azure Backup은 Azure Backup과 관련된 이벤트에 대한 작업을 보고 구성할 수 있는 기본 제공 모니터링 및 경고 기능을 제공합니다. [여기를 참조하세요](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>하이브리드 백업을 보호하기 위한 보안 기능

Azure Backup 서비스는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업하고 복원합니다. 이제 MARS는 보안 기능을 제공합니다. 즉, 암호는 업로드하기 전에 암호화한 후 Azure Backup에서 다운로드한 후 암호 해독하고, 삭제된 백업 데이터는 삭제 날짜부터 14일 동안 보존되고, 중요한 작업(예: 암호 변경)은 유효한 Azure 자격 증명이 있는 사용자만 수행할 수 있습니다. [여기를 참조하세요](backup-azure-security-feature.md).

## <a name="network-considerations"></a>네트워크 고려 사항

Azure Backup을 사용하면 데이터를 워크로드에서 Recovery Services 자격 증명 모음으로 이동해야 합니다. Azure Backup은 백업 데이터가 실수로 노출되지 않도록 보호하는 몇 가지 기능을 제공합니다(예: 네트워크에서의 중간자(man-in-the-middle) 공격). 다음 지침을 고려하세요.

### <a name="internet-connectivity"></a>인터넷 연결

* Azure VM 백업 - 스토리지와 Azure Backup 서비스 간에 필요한 모든 통신 및 데이터 전송은 가상 네트워크에 액세스할 필요 없이 Azure 네트워크 내에서 발생합니다. 따라서 보안 네트워크 내에 배치된 Azure VM을 백업하려면 모든 IP 또는 FQDN에 대한 액세스를 허용하지 않아도 됩니다.

* Azure VM의 SAP HANA 데이터베이스, Azure VM의 SQL Server 데이터베이스 - Azure Backup 서비스, Azure Storage 및 Azure Active Directory에 연결해야 합니다. 프라이빗 엔드포인트를 사용하거나 필요한 공용 IP 주소 또는 FQDN에 대한 액세스를 허용하면 됩니다. 필요한 Azure 서비스에 대한 적절한 연결을 허용하지 않으면 데이터베이스 검색, 백업 구성, 백업 수행, 데이터 복원과 같은 작업 시 오류가 발생할 수 있습니다. NSG 태그, Azure Firewall 및 HTTP 프록시를 사용하는 동안 전체 네트워크 지침은 이러한 [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) 및 [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) 문서를 참조하세요.

* 하이브리드 - MARS(Microsoft Azure Recovery Services) 에이전트는 모든 중요 작업(설치, 구성, 백업 및 복원)에서 네트워크 액세스가 필요합니다. MARS 에이전트는 공용 피어링(이전 회로에 사용 가능) 및 Microsoft 피어링을 사용하거나, [프라이빗 엔드포인트](install-mars-agent.md#private-endpoint-support)를 사용하거나, [적절한 액세스 제어와 함께 프록시/방화벽](install-mars-agent.md#verify-internet-access)을 사용하여 [Azure ExpressRoute](install-mars-agent.md#azure-expressroute-support)를 통해 Azure Backup 서비스에 연결할 수 있습니다.

### <a name="private-endpoints-for-azure-backup"></a>Azure Backup 프라이빗 엔드포인트

Azure [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. Azure Backup에서는 프라이빗 엔드포인트를 사용하여 Recovery Services 자격 증명 모음에서 데이터를 안전하게 백업하고 복원할 수 있습니다.

* 자격 증명 모음에 프라이빗 엔드포인트를 사용하도록 설정하는 경우 Azure VM 및 MARS 에이전트 백업에서 SQL 및 SAP HANA 워크로드의 백업 및 복원에만 사용됩니다.  다른 워크로드의 백업에도 자격 증명 모음을 사용할 수 있습니다. 그러나 프라이빗 엔드포인트는 필요하지 않습니다. 프라이빗 엔드포인트는 SQL 및 SAP HANA 워크로드 백업 및 MARS 에이전트를 사용한 백업 외에도 Azure VM 백업 시 파일 복구를 수행하는 데 사용됩니다. [여기를 참조하세요](private-endpoints-overview.md#recommended-and-supported-scenarios).

* Azure Active Directory는 현재 프라이빗 엔드포인트를 지원하지 않습니다. 따라서 Azure Active Directory에 필요한 IP 및 FQDN은 Azure VM에서 데이터베이스의 백업을 수행하고 MARS 에이전트를 사용하여 백업할 때 보안 네트워크에서의 아웃바운드 액세스가 허용되어야 합니다. 해당하는 경우 Azure AD 액세스를 허용하기 위해 NSG 태그 및 Azure Firewall 태그를 사용할 수도 있습니다. [여기에서 필수 구성 요소](./private-endpoints.md#before-you-start)에 대해 자세히 알아보세요.

## <a name="governance-considerations"></a>거버넌스 고려 사항

Azure에서 거버넌스는 주로 [Azure Policy](../governance/policy/overview.md) 및 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 사용하여 구현됩니다. [Azure Policy](../governance/policy/overview.md)를 사용하면 정책 정의를 만들고 할당하고 관리하여 리소스에 대 한 규칙을 적용할 수 있습니다. 이 기능은 해당 리소스가 회사 표준을 준수하게 합니다. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 사용하면 Azure 리소스 및 기타 클라우드 공급 기업에 대한 클라우드 사용량 및 비용을 추적할 수 있습니다. 또한 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 및 [Azure Advisor](../advisor/advisor-overview.md) 같은 도구는 비용 관리 프로세스에서 중요한 역할을 합니다.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup은 기본 제공 Azure Policy를 통해 두 가지 주요 시나리오를 지원

* 새로 만든 업무상 중요한 컴퓨터가 올바른 보존 설정으로 자동으로 백업되도록 합니다. Azure Backup은 구독 또는 리소스 그룹 내의 지정된 위치에 있는 모든 Azure VM에 할당될 수 있는 기본 제공 정책(Azure Policy를 사용)을 제공합니다. 이 정책이 지정된 범위에 할당되면 해당 범위에서 만들어진 모든 새 VM은 동일한 위치 및 구독의 기존 자격 증명 모음에 백업하도록 자동으로 구성됩니다. 사용자는 백업된 VM과 연결할 자격 증명 모음 및 보존 정책을 지정할 수 있습니다. [여기를 참조하세요](backup-azure-auto-enable-backup.md).

* 새로 만든 자격 증명 모음에서 보고서를 지원하기 위해 진단 기능을 사용하도록 설정합니다. 자격 증명 모음마다 진단 설정을 수동으로 추가하는 작업은 번거로울 수 있습니다. 또한 새로 만든 자격 증명 모음에서 보고서를 볼 수 있도록 진단 설정을 사용하도록 설정해야 합니다. Azure Backup은 Log Analytics를 대상으로 사용하는 대규모 진단 설정을 간단하게 만들 수 있도록 기본 제공 Azure Policy를 제공합니다. 이 정책은 각 구독 또는 리소스 그룹의 모든 자격 증명 모음에 LA 진단 설정을 추가합니다. 다음 섹션에서는 이 정책을 사용하는 방법에 대한 지침을 제공합니다. [여기를 참조하세요](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup 비용 고려 사항

Azure Backup 서비스의 기능은 비용을 효과적으로 관리하면서도 BCDR(비즈니스 연속성 및 재해 복구) 비즈니스 요구 사항을 충족하는 유연성을 제공합니다. 다음 지침을 고려하세요.

* 가격 계산기를 사용하여 다양한 옵션을 조정하며 비용을 평가하고 최적화합니다. [여기서 자세히 알아보세요.](azure-backup-pricing.md)

* Backup 탐색기: Backup 탐색기 또는 Backup 보고서를 사용하여 백업 스토리지 증가를 이해하고 최적화하여 중요하지 않은 작업 또는 삭제된 VM에 대한 백업을 중지합니다. 백업 관점에서 전체 공간에 대한 집계 보기를 볼 수 있습니다. 여기에는 백업 항목에 대한 정보 뿐만 아니라 백업이 구성되지 않은 리소스도 포함됩니다. 이렇게 하면 증가하는 공간에서 중요한 데이터에 대한 보호를 누락하지 않도록 하고, 중요하지 않은 작업 또는 삭제된 워크로드에 대한 백업을 최적화할 수 있습니다.

* 백업 정책을 최적화합니다.
  * 워크로드 아키타입(예: 중요 업무용, 중요하지 않음)에 따라 일정 및 보존 설정을 최적화합니다.
  * 즉시 복원에 대한 보존 설정을 최적화합니다.
  * Azure Backup에서 워크로드에 대해 지원하는 백업 유형(전체, 증분, 로그, 차등)을 고려하여 요구 사항에 맞게 올바른 백업 유형을 선택합니다.

* 선택적으로 디스크를 백업합니다. 디스크 제외(미리 보기 기능)는 중요한 데이터를 선택적으로 백업하는 효율적이고 비용 효과적인 선택지를 제공합니다. 예를 들어 VM에 연결된 디스크 중 하나만 백업하고 나머지는 백업하지 않을 수 있습니다. 이는 여러 백업 솔루션이 있는 경우에도 유용합니다. 예를 들어 워크로드 백업 솔루션을 사용하여 데이터베이스 또는 데이터를 백업할 때(Azure VM의 SQL Server 데이터베이스 백업) 선택한 디스크에 대해 Azure VM 수준 백업을 사용하려는 경우가 해당됩니다.

* Azure Backup은 Azure VM의 스냅샷을 생성하여 디스크와 함께 저장함으로써 복구 지점 만들기를 강화하고 복원 작업 속도를 향상합니다. 이를 즉시 복원이라고 합니다. 기본적으로 즉시 복원 스냅샷은 2일 동안 유지됩니다. 이 기능을 사용하면 복원 시간을 줄여 이러한 스냅샷에서 복원 작업을 수행할 수 있습니다. 자격 증명 모음에서 데이터를 다시 변환하고 복사하는 데 필요한 시간을 줄일 수 있습니다. 따라서 이 기간에 만든 스냅샷에 해당하는 스토리지 비용이 발생합니다. [여기를 참조하세요](backup-instant-restore-capability.md#configure-snapshot-retention).

* 기본적으로 Azure Backup 자격 증명 모음의 스토리지 복제 유형은 GRS(지역 중복)로 설정됩니다. 항목을 보호한 후에는 이 옵션을 변경할 수 없습니다. GRS(지역 중복 스토리지)는 LRS(로컬 중복 스토리지)보다 높은 수준의 데이터 내구성을 제공하며 지역 간 복원을 옵트인할 수 있지만 비용이 더 많이 듭니다. 비용과 내구성 간의 절충을 검토하여 시나리오에 가장 적합한 옵션을 결정합니다. [여기서 자세히 알아보세요.](backup-create-rs-vault.md#set-storage-redundancy)

* VM에서 실행되는 워크로드와 VM 자체를 모두 보호하는 경우 이 이중 보호가 필요한지 확인합니다.

## <a name="monitoring-and-alerting-considerations"></a>모니터링 및 경고 고려 사항

백업 사용자 또는 관리자는 모든 백업 솔루션을 모니터링하고 중요한 시나리오에 대한 알림을 받을 수 있어야 합니다. 이 섹션에서는 Azure Backup 서비스에서 제공하는 모니터링 및 알림 기능에 대해 자세히 설명합니다.

### <a name="monitoring"></a>모니터링

* Azure Backup은 백업 구성, 백업, 복원, 삭제 등의 작업에 대해 **기본 제공 작업 모니터링** 을 제공합니다. 이것은 자격 증명 모음으로 범위가 지정되고 단일 자격 증명 모음 모니터링에 적합합니다. [여기를 참조하세요](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* 대규모로 운영 활동을 모니터링해야 하는 경우 **Backup 탐색기** 는 전체 백업 공간에 대한 집계 보기를 제공하여 세부적인 드릴다운 분석 및 문제 해결이 가능합니다. 테넌트, 위치, 구독, 리소스 그룹, 자격 증명 모음 등 Azure의 전체 백업 공간에서 운영 활동을 모니터링하는 데 사용할 수 있는 단일 중앙 위치를 제공하는 기본 제공 Azure Monitor 통합 문서입니다. [여기를 참조하세요](monitor-azure-backup-with-backup-explorer.md).
  * 이를 사용하여 백업이 구성되지 않은 리소스를 식별하고 증가하는 공간에서 중요한 데이터에 대한 보호가 누락되지 않도록 합니다.
  * 대시보드는 지난 7일간(최대) 운영 활동을 제공합니다. 이 데이터를 보존해야 하는 경우 Excel 파일로 내보낼 수 있습니다.
  * Azure Lighthouse 사용자인 경우 여러 테넌트 간에 정보를 확인할 수 있는 경계 없는 모니터링이 가능합니다.

* 장기적으로 운영 활동을 보존하고 확인해야 하는 경우 **보고서** 를 사용합니다. 백업 관리자의 일반적인 요구 사항은 장기간에 걸친 데이터를 기반으로 백업에 대한 인사이트를 획득하는 것입니다. 이러한 솔루션에 대한 사용 사례는 다음과 같습니다.
  * 사용된 클라우드 스토리지를 할당하고 예측합니다.
  * 백업 및 복원을 감사합니다.
  * 여러 세분성 수준에서 주요 추세를 확인합니다.

* 기타
  * 데이터(예: 작업, 정책 등)를 **Log Analytics** 작업 영역으로 보낼 수 있습니다. 이렇게 하면 Azure Monitor 로그의 기능을 사용하여 해당 데이터와 Azure Monitor에서 수집된 다른 모니터링 데이터와의 상관 관계를 설정하고, 여러 Azure 구독 및 테넌트의 로그 항목을 함께 분석하도록 단일 위치로 통합하고, 로그 쿼리를 사용하여 복잡한 분석을 수행하고, 로그 항목에 대한 심층적인 통찰을 얻을 수 있습니다. [여기를 참조하세요](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * 데이터를 Event Hub로 보내 타사 SIEM(보안 정보 및 이벤트 관리) 또는 다른 로그 분석 솔루션과 같은 Azure 외부에 항목을 보낼 수 있습니다. [여기를 참조하세요](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * 감사, 정적 분석 또는 백업을 위해 90일을 초과하여 로그 데이터를 보존하려면 Azure Storage 계정으로 데이터를 보낼 수 있습니다. 90일 이내로 이벤트를 보관해야 하는 경우 활동 로그 이벤트는 Azure 플랫폼에 90일 동안 보관되므로 스토리지 계정에 보관을 설정할 필요가 없습니다. [자세한 정보를 알아보세요](../azure-monitor/essentials/activity-log.md#send-to-azure-storage).

### <a name="alerting"></a>경고

* 경고는 주로 관련 작업을 수행하라는 알림을 받는 방법입니다. 백업 경고 섹션에는 Azure Backup 서비스에서 생성된 경고가 표시됩니다.

* Azure Backup은 오류, 경고 및 중요한 작업에 대해 이메일을 사용한 **기본 제공 경고** 알림 메커니즘을 제공합니다. 경고가 생성될 때 알림을 받을 개별 메일 주소 또는 메일 그룹을 지정할 수 있습니다. 각 개별 경고에 대한 알림을 받을지 또는 시간별 요약으로 그룹화한 후 알림을 받을지 선택할 수도 있습니다.
  * 이러한 경고는 서비스에 의해 정의되며, 백업/복원 실패, 데이터 보존을 사용하여 보호 중지/데이터 삭제를 사용하여 보호 중지 등 제한된 시나리오에 대한 지원을 제공합니다. [여기를 참조하세요](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * 데이터 삭제를 사용하여 보호 중지와 같은 파괴적인 작업을 수행하면 Recovery Services 자격 증명 모음에서 알림을 구성하지 **않았더라도** 경고가 발생한 후 메일이 구독 소유자, 관리자 및 공동 관리자에게 전송됩니다.
  * 특정 워크로드는 높은 빈도(예: SQL Server의 경우 15분마다)로 오류를 생성할 수 있습니다. 각 오류 발생에서 지나치게 많은 경고가 생성되는 것을 방지하기 위해 경고가 통합됩니다. [여기를 참조하세요](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * 기본 제공 경고는 사용자 지정할 수 없으며 Azure Portal에 정의된 이메일로 제한됩니다.

* **사용자 지정 경고를 만들어야** 하는 경우(예: 성공한 작업에 대한 경고) Log Analytics를 사용합니다. Azure Monitor에서는 Log Analytics 작업 영역에 고유 경고를 만들 수 있습니다. 하이브리드 워크로드(DPM/MABS)는 데이터를 LA로 보내고 LA를 사용하여 Azure Backup에서 지원하는 워크로드 간에 공통된 경고를 제공할 수도 있습니다.

* 또한 기본 제공 Recovery Services 자격 증명 모음 **활동 로그** 를 통해 알림을 받을 수도 있습니다. 그러나 제한된 시나리오만 지원하며 예약 백업과 같은 작업에는 적합하지 않습니다. 활동 로그보다는 리소스 로그에 더 잘 부합합니다. 이러한 제한 사항 및 Azure Backup에서 보호되는 모든 워크로드에 대한 대규모 모니터링 및 경고에 Log Analytics 작업 영역을 사용할 수 있는 방법에 대한 자세한 내용은 이 [문서](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Backup을 사용하기 위한 시작점으로 다음 문서를 참조하는 것이 좋습니다.

* [Azure Backup 개요](backup-overview.md)
* [질문과 대답](backup-azure-backup-faq.yml)
