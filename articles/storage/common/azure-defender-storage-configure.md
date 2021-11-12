---
title: Storage 용 Microsoft Defender 구성
titleSuffix: Azure Storage
description: Storage에 대해 Microsoft Defender를 구성 하 여 계정 활동의 변칙을 감지 하 고 잠재적으로 위험한 계정 액세스 시도에 대 한 통지를 받습니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 17cc82bd448910cc92eec42db889605c48b65fb5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297015"
---
# <a name="configure-microsoft-defender-for-storage"></a>Storage 용 Microsoft Defender 구성

Storage 용 Microsoft Defender는 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 이 보호 레이어를 사용하면 보안 전문가가 되거나 보안 모니터링 시스템을 관리하지 않고도 위협을 해결할 수 있습니다.

보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 [클라우드 용 Microsoft Defender](https://azure.microsoft.com/services/security-center/)와 통합 되어 있으며, 의심 스러운 활동의 세부 정보와 위협 조사 및 해결 방법에 대 한 권장 사항을 포함 하는 전자 메일을 통해 구독 관리자에 게 전송 됩니다.

서비스는 위협 탐지를 위해 Blob Storage 및 Azure Files에 대한 읽기, 쓰기 및 삭제 요청의 리소스 로그를 수집합니다. Microsoft Defender for Cloud에서 경고를 조사 하려면 스토리지 분석 로깅을 사용 하 여 관련 저장소 작업을 볼 수 있습니다. 자세한 내용은 [Azure Portal에서 스토리지 계정 모니터링](./manage-storage-analytics-logs.md#configure-logging)에서 **로깅 구성** 을 참조하세요.

## <a name="availability"></a>가용성

Microsoft Defender for Storage는 현재 Blob Storage, Azure Files 및 Azure Data Lake Storage Gen2에 사용할 수 있습니다. Storage 용 Microsoft Defender를 지 원하는 계정 유형은 범용 v2, 블록 blob 및 blob Storage 계정을 포함 합니다. Microsoft Defender for Storage은 모든 공용 클라우드 및 미국 정부 클라우드에서 사용할 수 있지만 다른 소 버린 또는 Azure Government 클라우드 지역에서는 사용할 수 없습니다.

Data Lake Storage에 계층 구조 네임스페이스를 사용하도록 설정된 계정은 Azure Blob 스토리지 API와 Data Lake Storage API를 사용하여 트랜잭션을 지원합니다. Azure 파일 공유는 SMB를 통한 트랜잭션을 지원합니다.

무료 30 일 평가판을 비롯 한 가격 책정 정보는 [Microsoft Defender For Cloud 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.

다음 목록에는 Storage에 대 한 Microsoft Defender의 가용성이 요약 되어 있습니다.

- 릴리스 상태:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)(일반 공급)
  - [Azure Files](../files/storage-files-introduction.md)(일반 공급)
  - Azure Data Lake Storage Gen2(일반 공급)
- 클라우드: ✔ 상용 클라우드<br>
    ✔ Azure Government<br>
    ✘ Azure 중국 21Vianet

## <a name="set-up-microsoft-defender-for-cloud"></a>클라우드 용 Microsoft Defender 설정

다음 섹션에서 설명 하는 여러 가지 방법으로 Storage에 대해 Microsoft Defender를 구성할 수 있습니다.

### <a name="microsoft-defender-for-cloud"></a>[Microsoft Defender for Cloud](#tab/azure-security-center)

microsoft defender for Storage는 microsoft defender for Cloud에 내장 되어 있습니다. 구독에서 클라우드의 향상 된 보안 기능에 대해 microsoft defender를 사용 하도록 설정 하면 모든 저장소 계정에 대해 microsoft defender for Storage가 자동으로 사용 하도록 설정 됩니다. 특정 구독에서 개별 저장소 계정에 대 한 Storage Defender를 사용 하거나 사용 하지 않도록 설정 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **클라우드 용 Microsoft Defender** 를 시작 합니다.
1. Defender for Cloud 주 메뉴에서 **환경 설정** 을 선택합니다.
1. 클라우드 용 Microsoft Defender를 사용 하거나 사용 하지 않도록 설정할 구독을 선택 합니다.
1. **모든 Microsoft defender 요금제 사용** 을 선택 하 여 구독에서 클라우드에 대해 microsoft defender를 사용 하도록 설정 합니다.
1. **리소스 유형별 Microsoft Defender 계획 선택** 에서 **Storage** 행을 찾고 **계획** 열에서 **사용** 을 선택 합니다.
1. 변경 내용을 저장합니다.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Storage에 대해 Microsoft Defender를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

현재이 구독의 모든 저장소 계정에 대해 Microsoft Defender for Storage를 사용할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)을 시작합니다.
1. 스토리지 계정으로 이동합니다. **설정** 아래에서 **고급 보안** 을 선택합니다.
1. **Storage에 대해 Microsoft Defender 사용을** 선택 합니다.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Storage에 대해 Microsoft Defender 계정을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

현재이 저장소 계정에 대해 Microsoft Defender for Storage가 사용 하도록 설정 되었습니다.

### <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용 하 여 Microsoft Defender에서 Storage 사용 하도록 설정 된 Azure Storage 계정을 배포 합니다. 자세한 내용은 [Advanced Threat Protection이 있는 스토리지 계정](https://azure.microsoft.com/resources/templates/storage-advanced-threat-protection-create/)을 참조하세요.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure Policy를 사용 하 여 특정 구독 또는 리소스 그룹의 저장소 계정에서 클라우드에 대해 Microsoft Defender를 사용 하도록 설정할 수 있습니다.

1. Azure **정책 - 정의** 페이지를 시작합니다.
1. **Storage 계정에 대 한 Microsoft Defender 배포** 정책을 검색 합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Storage 계정에 Microsoft Defender를 사용 하도록 설정 하는 정책 적용":::

1. Azure 구독 또는 리소스 그룹을 선택합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="정책 범위에 대한 구독 또는 리소스 그룹 선택":::

1. 정책을 할당합니다.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Storage에 대해 Microsoft Defender를 사용 하도록 설정 하는 정책 할당":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 통해 저장소 계정에 대 한 Storage에 대해 Microsoft Defender를 사용 하도록 설정 하려면 먼저 [Az. Security](https://www.powershellgallery.com/packages/Az.Security) 모듈을 설치 했는지 확인 합니다. 그런 다음, [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) 명령을 호출합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

PowerShell을 통해 Microsoft Defender에서 저장소 계정에 대 한 Storage 설정을 확인 하려면 [AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) 명령을 호출 합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 통해 저장소 계정에 대 한 Storage에 대해 Microsoft Defender를 사용 하도록 설정 하려면 [az security atp Storage update](/cli/azure/security/atp/storage#az_security_atp_storage_update) 명령을 호출 합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Azure CLI을 통해 저장소 계정에 대 한 Storage 용 Microsoft Defender 설정을 확인 하려면 [az security atp Storage show](/cli/azure/security/atp/storage#az_security_atp_storage_show) 명령을 호출 합니다. 꺾쇠 괄호로 묶인 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>보안 변칙 탐색

스토리지 작업에서 잘못된 부분이 발생하면 의심스러운 보안 이벤트에 대한 정보가 포함된 이메일 알림을 받게 됩니다. 이벤트 세부 정보에는 다음과 같은 내용이 포함됩니다.

- 변칙의 특성
- 스토리지 계정 이름
- 이벤트 시간
- 스토리지 유형
- 가능한 원인
- 조사 단계
- 수정 단계

또한 이메일에는 가능한 원인에 대한 세부 정보와 잠재적 위협을 조사하고 완화하기 위한 권장 조치가 포함됩니다.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Microsoft Defender for Storage 경고 전자 메일":::

Microsoft Defender for Cloud의 [보안 경고 타일](../../security-center/security-center-managing-and-responding-alerts.md)에서 현재 보안 경고를 검토 하 고 관리할 수 있습니다. 현재 위협을 조사 하 고 향후 위협을 해결 하기 위한 세부 정보 및 작업에 대 한 경고를 선택 합니다.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Microsoft Defender for Storage 경고":::

## <a name="security-alerts"></a>보안 경고

경고는 평소와 다르고 잠재적 위험이 있는 액세스 시도 또는 스토리지 계정 악용에 의해 생성됩니다. Azure Storage에 대한 경고 목록은 [Azure Storage에 대한 경고](../../security-center/alerts-reference.md#alerts-azurestorage)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft Defender for Storage 소개](../../security-center/defender-for-storage-introduction.md)
- [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)
- [Azure Storage 계정의 로그](/rest/api/storageservices/About-Storage-Analytics-Logging)
