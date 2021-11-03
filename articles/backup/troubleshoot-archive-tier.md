---
title: 보관 계층 문제 해결
description: Azure Backup에 대 한 보관 계층 오류 문제를 해결 하는 방법을 알아봅니다.
ms.topic: troubleshooting
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 560c8900e5d0c06dc436ec1222583db58f7cdda3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053091"
---
# <a name="troubleshooting-recovery-point-archive-using-archive-tier"></a>보관 계층을 사용 하 여 복구 지점 보관 문제 해결

이 문서에서는 복구 지점을 보관으로 이동할 수 없는 경우 표시 되는 오류 코드에 대 한 문제 해결 세부 정보를에 제공 합니다.

## <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**오류 메시지**: Recovery-Point 유형은 보관 이동에 적합 하지 않습니다.

**설명**:이 오류 코드는 선택한 복구 지점 유형을 보관으로 이동할 수 없는 경우에 표시 됩니다.

**권장 조치**: 복구 지점의 자격을 확인 합니다. [지원 되는 워크 로드](archive-tier-support.md#supported-workloads)를 참조 하세요.

## <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**오류 메시지**: 복원에 대 한 활성 종속성이 있는 Recovery-Point 보관 이동에 적합 하지 않습니다.

**설명**: 선택한 복구 지점에 활성 종속성이 있으므로 보관으로 이동할 수 없습니다.

**권장 작업**"복구 지점의 자격을 확인 합니다. [지원 되는 워크 로드](archive-tier-support.md#supported-workloads)를 참조 하세요.

## <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**오류 메시지**: 자격 증명 모음에 소요 된 수명으로 보관 이동에 대 한 Recovery-Point 수 없습니다. 표준 계층은 필요한 최소 수준 보다 작습니다.

**설명**: azure virtual machines의 경우 최소 3 개월 동안 표준 계층에서 복구 지점을 유지 해야 하 고, azure virtual machines에서 SQL Server는 45 일을 유지 해야 합니다.

**권장 조치**: 복구 지점의 자격을 확인 합니다. [지원 되는 워크 로드](archive-tier-support.md#supported-workloads)를 참조 하세요.

## <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**오류 메시지**: Recovery-Point 남은 수명이 필요한 최소값 보다 작습니다.

**설명**: 보관 자격에 대 한 복구 지점에 필요한 최소 수명은 6 개월입니다.

**권장 조치**: 복구 지점의 자격을 확인 합니다. [지원 되는 워크 로드](archive-tier-support.md#supported-workloads)를 참조 하세요.

## <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**오류 메시지**: Recovery-Point는 이미 보관 계층으로 이동 되었으므로 보관 이동에 적합 하지 않습니다.

**설명**: 선택한 복구 지점이 이미 보관에 있습니다. 따라서 보관으로 이동하기에 적합하지 않습니다.

## <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**오류 메시지**: 데이터 원본 유형은 권장 사항 API에 적합 하지 않습니다.

**설명**: 추천 API는 Azure virtual machines에만 적용 됩니다. 선택한 데이터 원본 유형에는 적용되지 않습니다.

## <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**오류 메시지**: 복구 지점에 이미가 중입니다. 리하이드레이션은 이 RP에서 허용되지 않습니다.

**설명**: 선택한 복구 지점이 이미로 지정 되었습니다.

## <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**오류 메시지**: Recovery-Point은 보관 이동에 적합 하지 않습니다.

**설명**: 선택한 복구 지점은 보관 이동에 적합 하지 않습니다.

## <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**오류 메시지**: 보관 복구 지점은 복원 되지 않습니다. 보관 RP에서 리하이드레이션을 완료한 후 다시 복원해 보세요.

**설명**: 복구 지점의 위치가 잘못 되었습니다. 복구 지점을 리하이드레이션한 후 복원해 보세요.

## <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**오류 메시지**: 리하이드레이션는 보관 복구 지점만 지원 됩니다.-리하이드레이션는 보관 복구 지점만 지원 됩니다.

**설명**: 선택한 복구 지점에 대해 리하이드레이션이 허용 되지 않습니다.

## <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**오류 메시지**: 보관 복구 지점에 대 한 리하이드레이션 이미 In-Progress 되었습니다.

**설명**: 선택한 복구 지점에 대 한 리하이드레이션 이미 진행 중입니다.

## <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**오류 메시지** – 정책에 지정된 보존 기간이 충분하지 않아서 복구 지점을 보관 계층으로 이동할 수 없습니다.

**권장 작업**: 적절 한 보존 설정을 사용 하 여 보호 된 항목에 대 한 정책을 업데이트 하 고 다시 시도 하세요.

## <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**오류 메시지**:이 복구 지점을 이동할 수 있는지 여부를 계속 확인 하 고 있습니다.

**설명**: 복구 지점의 이동 준비를 아직 확인 하지 않았습니다.

**권장 작업**: 일정 시간 동안 기다린 후에 다시 확인 합니다.
