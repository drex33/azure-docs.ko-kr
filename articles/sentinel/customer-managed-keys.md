---
title: Microsoft 센티널에서 고객 관리 키 설정 | Microsoft Docs
description: Microsoft 센티널에서 고객 관리 키 (CMK)를 설정 하는 방법에 대해 알아봅니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4151045e40e456f5457c137fae3b22422012a6aa
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757425"
---
# <a name="set-up-microsoft-sentinel-customer-managed-key"></a>Microsoft 센티널 고객 관리 키 설정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널에 대해 [CMK (고객이 관리 하는 키)](../azure-monitor/logs/customer-managed-keys.md) 를 구성 하는 단계 및 배경 정보를 제공 합니다. CMK를 사용 하면 microsoft에 저장 된 모든 데이터를 모든 관련 저장소 리소스에 제공할 수 있습니다. 즉, 사용자가 만들고 소유 하 고 [Azure Key Vault](../key-vault/general/overview.md)에 저장 된 암호화 키를 사용 하 여 추가 보호 계층을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- CMK 기능을 사용 하려면 최소 500 g b/일 약정 계층이 있는 Log Analytics 전용 클러스터가 필요 합니다. 여러 작업 영역은 동일한 전용 클러스터에 연결 될 수 있으며, 동일한 고객 관리 키를 공유 합니다.

- 이 가이드의 단계를 완료 한 후 작업 영역을 사용 하기 전에 온 보 딩 확인에 대해 [Microsoft 센티널 제품 그룹](mailto:azuresentinelCMK@microsoft.com)에 문의 하십시오.

- [Log Analytics 전용 클러스터 가격 책정](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model)에 대해 알아보세요.

## <a name="considerations"></a>고려 사항

- Sentinel에 CMK 작업 영역 온보딩은 Azure Portal이 아닌 REST API를 통해서만 지원됩니다. Azure Resource Manager 템플릿(ARM 템플릿)은 현재 CMK 온보딩에 지원되지 않습니다.

- Microsoft 센티널 CMK 기능은 *microsoft 등록로 아직 되지* 않은 *전용 클러스터 Log Analytics의 작업 영역* 에만 제공 됩니다.

- 다음 CMK 관련 변경 사항은 비효율적 이므로 *지원 되지* 않습니다. microsoft 센티널 데이터는 cmk가 아닌 microsoft 관리 키로만 암호화 됩니다.

  - Microsoft 센티널에 *이미 등록* 된 작업 영역에서 cmk를 사용 하도록 설정 합니다.
  - Sentinel 온보딩 작업 영역이 포함된 클러스터에서 CMK를 사용으로 설정합니다.
  - Sentinel에 온보딩된 CMK가 아닌 작업 영역을 CMK 사용 클러스터에 연결합니다.

- 다음 CMK 관련 변경 사항은 정의되지 않고 문제가 있는 동작을 유발할 수 있으므로 ‘지원되지 않습니다’.

  - 작업 영역에서 CMK를 사용 하지 않도록 설정 하는 것은 이미 Microsoft 센티널로 등록.
  - Sentinel이 온보딩된 CMK 사용 작업 영역을 CMK 사용 전용 클러스터에서 연결 해제하여 CMK가 아닌 작업 영역으로 설정합니다.
  - CMK 사용 Log Analytics 전용 클러스터에서 CMK를 사용하지 않게 설정합니다.

- Microsoft 센티널은 CMK 구성에서 시스템 할당 Id를 지원 합니다. 따라서 전용 Log Analytics 클러스터의 ID는 **시스템 할당** 유형이어야 합니다. 생성 시 Log Analytics 클러스터에 자동으로 할당되는 ID를 사용하는 것이 좋습니다.

- 고객 관리형 키를 다른 키(다른 URI 포함)로 변경하는 것은 현재 ‘지원되지 않습니다’. 키를 [순환](../azure-monitor/logs/customer-managed-keys.md#key-rotation)하여 변경해야 합니다.

- 프로덕션 작업 영역 또는 Log Analytics 클러스터로 CMK를 변경 하기 전에 [Microsoft 센티널 제품 그룹](mailto:azuresentinelCMK@microsoft.com)에 문의 하십시오.

## <a name="how-cmk-works"></a>CMK 작동 방법 

Microsoft 센티널 솔루션은 Log Analytics 전용 클러스터를 포함 하 여 로그 수집 및 기능에 여러 저장소 리소스를 사용 합니다. Microsoft 센티널 CMK 구성의 일부로 관련 Log Analytics 전용 클러스터에서 CMK 설정을 구성 해야 합니다. Log Analytics 이외의 저장소 리소스에서 Microsoft 센티널에서 저장 한 데이터는 전용 Log Analytics 클러스터에 대해 구성 된 고객 관리 키를 사용 하 여 암호화 됩니다.

다음 추가 관련 설명서를 참조하세요.
- [Azure Monitor CMK(고객 관리형 키)](../azure-monitor/logs/customer-managed-keys.md)
- [Azure Key Vault](../key-vault/general/overview.md)
- [Log Analytics 전용 클러스터](../azure-monitor/logs/logs-dedicated-clusters.md).

> [!NOTE]
> Microsoft 센티널에서 CMK를 사용 하도록 설정 하는 경우 CMK를 지원 하지 않는 공개 미리 보기 기능은 사용 하도록 설정 되지 않습니다.

## <a name="enable-cmk"></a>CMK 사용 

CMK를 프로비저닝하려면 다음 단계를 수행합니다. 

1.  Azure Key Vault를 만들고 키를 생성하거나 가져옵니다.

2.  Log Analytics 작업 영역에 CMK를 사용하도록 설정합니다.

3.  Cosmos DB 리소스 공급자에 등록합니다.

4.  Azure Key Vault 인스턴스에 액세스 정책을 추가합니다.

5.  [온 보 딩 API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx)를 통해 Microsoft 센티널에 작업 영역을 등록 합니다.

### <a name="step-1-create-an-azure-key-vault-and-generate-or-import-a-key"></a>1단계: Azure Key Vault를 만들고 키를 생성하거나 가져오기

1. [Azure Key Vault 리소스를 만든](/azure-stack/user/azure-stack-key-vault-manage-portal) 다음 데이터 암호화에 사용할 키를 생성하거나 가져옵니다.

    > [!NOTE]
    >  키와 액세스를 보호하기 위해 Azure Key Vault를 복구 가능으로 구성해야 합니다.

1.  [복구 옵션 설정:](../key-vault/general/key-vault-recovery.md)

    -   [일시 삭제](../key-vault/general/soft-delete-overview.md)가 설정되어 있는지 확인합니다.

    -   일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제하지 않도록 보호하려면 [제거 보호](../key-vault/general/soft-delete-overview.md#purge-protection)를 설정합니다.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>2단계: Log Analytics 작업 영역에서 CMK 사용

다음 단계에서 Microsoft 센티널 작업 영역으로 사용할 CMK 작업 영역을 만들려면 [고객이 관리 하는 키 구성 Azure Monitor](../azure-monitor/logs/customer-managed-keys.md) 의 지침을 따르세요.

### <a name="step-3-register-to-the-cosmos-db-resource-provider"></a>3단계: Cosmos DB 리소스 공급자에 등록

Microsoft 센티널은 Cosmos DB 추가 저장소 리소스로 작동 합니다. Cosmos DB 리소스 공급자에 등록하세요.

Cosmos DB 지침에 따라 Azure 구독에 대한 [Azure Cosmos DB 리소스 공급자를 등록](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)합니다.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>4단계: Azure Key Vault 인스턴스에 액세스 정책 추가

Cosmos DB에서 Azure Key Vault 인스턴스에 대한 액세스를 추가해야 합니다. Cosmos DB 지침에 따라 Azure Cosmos DB 보안 주체를 사용하여 [Azure Key Vault 인스턴스에 액세스 정책을 추가](../cosmos-db/how-to-setup-cmk.md#add-access-policy)합니다.

### <a name="step-5-onboard-the-workspace-to-microsoft-sentinel-via-the-onboarding-api"></a>5 단계: 온 보 딩 API를 통해 Microsoft 센티널에 작업 영역 등록

[온 보 딩 API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx)를 통해 Microsoft 센티널에 작업 영역을 등록 합니다.

## <a name="key-encryption-key-revocation-or-deletion"></a>키 암호화 키 해지 또는 삭제

사용자가 키 암호화 키 (cmk)를 삭제 하거나 전용 클러스터 및 Cosmos DB 리소스 공급자에 대 한 액세스 권한을 제거 하 여 해당 키 암호화 키 (cmk)를 해지 하는 경우 Microsoft 센티널은 변경 내용을 적용 하 고 한 시간 내에 데이터를 더 이상 사용할 수 없는 것 처럼 동작 합니다. 이 시점에서는 데이터 수집, 영구 구성 변경, 인시던트 생성과 같은 영구 스토리지 리소스를 사용하는 작업은 금지됩니다. 이전에 저장된 데이터는 삭제되지 않지만 액세스할 수 없는 상태가 됩니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 의해 제어되며 해당 정책에 따라 제거됩니다.

암호화 키가 해지되거나 삭제된 후에는 계정 삭제 작업만 수행할 수 있습니다.

해지 후 액세스가 복원 되는 경우 Microsoft 센티널은 한 시간 이내에 데이터에 대 한 액세스를 복원 합니다.

전용 Log Analytics 클러스터 및 Cosmos DB 모두 키 자격 증명 모음에서 고객 관리형 키를 사용하지 않게 설정하거나 키에 대한 액세스 정책을 삭제하여 데이터에 대한 액세스 권한을 철회할 수 있습니다. 전용 Log Analytics 클러스터에서 키를 제거하거나 전용 Log Analytics 클러스터와 연결된 ID를 제거하여 액세스 권한을 철회하는 것은 지원되지 않습니다.

Azure Monitor에서 이 기능이 작동하는 방식에 대한 자세한 내용은 [Azure Monitor CMK 해지](../azure-monitor/logs/customer-managed-keys.md#key-revocation)를 참조하세요.

## <a name="customer-managed-key-rotation"></a>고객 관리형 키 순환

Microsoft 센티널 및 Log Analytics 키 회전을 지원 합니다. 사용자가 Key Vault에서 키 회전을 수행 하는 경우 Microsoft 센티널은 1 시간 이내에 새 키를 지원 합니다.

Key Vault에서 키의 새 버전을 만들어 키 순환을 수행할 수 있습니다.

![키 회전](./media/customer-managed-keys/key-rotation.png)

24시간 후 또는 Azure Key Vault 감사 로그에 이전 버전을 사용하는 작업이 더 이상 나타나지 않으면 이전 버전의 키를 사용하지 않도록 설정할 수 있습니다.

키를 순환시킨 후 Log Analytics의 전용 Log Analytics 클러스터 리소스를 새 Azure Key Vault 키 버전으로 명시적으로 업데이트해야 합니다. 자세한 내용은 [Azure Monitor CMK 회전](../azure-monitor/logs/customer-managed-keys.md#key-rotation)을 참조하세요.

## <a name="replacing-a-customer-managed-key"></a>고객 관리형 키 대체

Microsoft 센티널은 고객이 관리 하는 키를 대체 하는 기능을 지원 하지 않습니다. 대신 [키 순환 기능](#customer-managed-key-rotation)을 사용해야 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft 센티널에서 고객 관리 키를 설정 하는 방법을 알아보았습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](./detect-threats-built-in.md)시작 하세요.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
