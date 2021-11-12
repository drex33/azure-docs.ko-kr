---
title: Azure 디스크 풀(미리 보기) 문제 해결 개요
description: Azure 디스크 풀 관련 문제 해결 일반적인 오류 코드 및 해결 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 878cec7021ebcd7c3b63ae4af1f29a0793977280
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277282"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Azure 디스크 풀(미리 보기) 문제 해결

이 문서에서는 Azure 디스크 풀(미리 보기)과 관련된 몇 가지 일반적인 오류 코드를 나열합니다. 또한 디스크 풀 상태에 대한 가능한 해결 방법 및 명확성을 제공합니다.

## <a name="disk-pool-status"></a>디스크 풀 상태

디스크 풀 및 iSCSI 대상 각각에는 네 개의 상태, **알 수 없음**, **실행 중**, **업데이트 중** 및 **중지됨(할당 취소됨)** 이 있습니다.

**알 수 없음** 은 리소스가 불량 또는 알 수 없는 상태임을 의미합니다. 복구를 시도하려면 리소스에 대한 업데이트 작업(예: 디스크/LUNS 추가 또는 제거)을 수행하거나 디스크 풀을 삭제하고 다시 배포합니다.

**실행 중** 은 리소스가 실행 중이며 정상 상태임을 의미합니다.

**업데이트 중** 은 리소스가 업데이트를 진행 중임을 의미합니다. 이는 일반적으로 배포 중에 또는 디스크 또는 LUN 추가와 같은 업데이트를 적용할 때 발생합니다.

**중지됨(할당 취소됨)** 은 리소스가 중지되고 해당 기본 리소스의 할당이 중단된 것을 의미합니다. 리소스를 다시 시작하여 디스크 풀 또는 iSCSI 대상을 복구할 수 있습니다.

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>디스크 풀을 배포할 때 발생하는 일반적인 오류 코드
 
|코드  |Description  |
|---------|---------|
|UnexpectedError     |일반적으로 백 엔드 복구할 수 없는 오류가 발생할 때 발생합니다. 배포를 다시 시도합니다. 문제가 해결되지 않으면 Azure 지원에 문의하고 오류 메시지의 추적 ID를 제공합니다.         |
|DeploymentFailureZonalAllocationFailed     |이 문제는 Azure에서 지정된 지역/영역에서 VM을 프로비전할 용량이 부족할 때 발생합니다. 다른 시간에 배포를 다시 시도합니다.         |
|DeploymentFailureQuotaExceeded     |디스크 풀을 배포하는 데 사용되는 구독이 이 지역의 VM 코어 할당량을 벗어났습니다. Dsv3 시리즈에 대해 [Azure VM 시리즈당 vCPU 할당량 한도 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)할 수 있습니다.         |
|DeploymentFailurePolicyViolation     |구독에 대한 정책으로 인해 디스크 풀을 지원하는 데 필요한 Azure 리소스를 배포할 수 없습니다. 자세한 내용은 오류를 참고하세요.         |
|DeploymentTimeout     |이 오류는 디스크 풀 인프라의 배포가 고정되고 할당된 시간에 완료되지 않을 때 발생합니다. 배포를 다시 시도합니다. 문제가 지속되면 Azure 지원에 문의하고 오류 메시지의 추적 ID를 제공합니다.         |
|GoalStateApplicationTimeoutError     |디스크 풀 인프라가 리소스 공급자에 대한 응답을 중지할 때 발생합니다. 네트워킹 필수 구성 조건이 충족하는지 확인한 다음 배포를 다시 [시도합니다.](disks-pools-deploy.md#prerequisites) 문제가 지속되면 Azure 지원에 문의하고 오류의 추적 ID를 제공합니다.         |
|OngoingOperationInProgress     |디스크 풀에서 진행 중인 작업이 진행 중입니다. 해당 작업이 완료될 때까지 기다린 다음, 배포를 다시 시도합니다.         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>디스크 풀에서 iSCSI를 사용할 때 발생하는 일반적인 오류 코드

|코드  |Description  |
|---------|---------|
|GoalStateApplicationError     |iSCSI 대상 구성이 잘못되어 디스크 풀에 적용할 수 없는 경우에 발생합니다. 배포를 다시 시도합니다. 문제가 지속되면 Azure 지원에 문의하고 오류의 추적 ID를 제공합니다.         |
|GoalStateApplicationTimeoutError     |디스크 풀 인프라가 리소스 공급자에 대한 응답을 중지할 때 발생합니다. 네트워킹 필수 구성 조건이 충족하는지 확인한 다음 배포를 다시 [시도합니다.](disks-pools-deploy.md#prerequisites) 문제가 지속되면 Azure 지원에 문의하고 오류의 추적 ID를 제공합니다.         |
|OngoingOperationInProgress     |디스크 풀에서 진행 중인 작업이 진행 중입니다. 해당 작업이 완료될 때까지 기다린 다음, 배포를 다시 시도합니다.         |

## <a name="next-steps"></a>다음 단계

[디스크 풀 관리(미리 보기)](disks-pools-manage.md)
