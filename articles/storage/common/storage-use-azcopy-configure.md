---
title: AzCopy(Azure Storage)에서 로그를 사용하여 오류 찾기 및 작업 계속하기 | Microsoft Docs
description: 로그를 사용하여 오류를 진단하고 플랜 파일을 사용하여 일시 중지된 작업을 계속하는 방법을 알아봅니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8fc9934c5d524550929c3400af9f257c4cbcccc8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665913"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>AzCopy에서 로그 및 플랜 파일을 사용하여 오류 찾기 및 작업 계속하기

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 로그를 사용하여 오류를 진단한 다음, 플랜 파일을 사용하여 작업을 계속하는 데 도움이 됩니다. 또한 이 문서에서는 로그 및 플랜 파일의 세부 정보 표시 수준과 저장되는 기본 위치를 변경하여 로그 및 플랜 파일을 구성하는 방법을 보여 줍니다.

> [!NOTE]
> AzCopy를 시작하는 데 도움이 되는 콘텐츠를 찾고 있다면 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요. 이 문서는 현재 지원되는 AzCopy 버전이므로 AzCopy **V10** 에 적용됩니다. 이전 버전의 AzCopy를 사용해야 하는 경우, [이전 버전의 AzCopy 사용](storage-use-azcopy-v10.md#previous-version)을 참조하세요.

## <a name="log-and-plan-files"></a>로그 및 계획 파일

AzCopy는 모든 작업에 대해 *로그* 및 *플랜* 파일을 만듭니다. 이러한 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다.

로그에는 실패 상태(`UPLOADFAILED`, `COPYFAILED` 및 `DOWNLOADFAILED`), 전체 경로 및 실패 이유가 포함됩니다.

기본적으로 로그 및 플랜 파일은 Windows의 `%USERPROFILE%\.azcopy` 디렉터리 또는 Mac 및 Linux의 `$HOME$\.azcopy` 디렉터리에 있지만, 해당 위치를 변경할 수 있습니다.

관련 오류가 파일에 표시되는 첫 번째 오류가 아닐 수도 있습니다. 네트워크 오류, 시간 제한 및 서버 사용 중 오류와 같은 오류의 경우 AzCopy는 최대 20회 다시 시도하고 일반적으로 다시 시도 프로세스는 성공합니다.  표시되는 첫 번째 오류는 성공적으로 재시도한 무해한 오류일 수 있습니다.  따라서 파일의 첫 번째 오류를 확인하는 대신 `UPLOADFAILED`, `COPYFAILED` 또는 `DOWNLOADFAILED` 근처에 있는 오류를 확인합니다.

> [!IMPORTANT]
> Microsoft 지원에 요청을 제출하거나 타사 관련 문제를 해결할 때 실행하려는 명령의 수정된 버전을 공유합니다. 이렇게 하면 실수로 다른 사용자와 SAS를 공유하지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

## <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령은 `04dc9ca9-158f-7945-5933-564021086c79` 로그에서 `UPLOADFAILED` 상태의 모든 오류를 가져옵니다.

**Windows(PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 다음 명령을 사용하여 작업 기록을 확인합니다.

```
azcopy jobs list
```

작업 상태를 보려면 다음 명령을 사용합니다.

```
azcopy jobs show <job-id>
```

전송을 상태별로 필터링하려면 다음 명령을 사용합니다.

```
azcopy jobs show <job-id> --with-status=Failed
```

다음 명령을 사용하여 실패/취소된 작업을 계속합니다. 이 명령은 보안상의 이유로 영구적이지 않기 때문에 SAS 토큰과 함께 식별자를 사용합니다.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>" --destination-sas="<sas-token>"
```

> [!TIP]
> SAS 토큰과 같은 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

작업을 계속하면 AzCopy에서 작업 플랜 파일을 찾습니다. 플랜 파일에는 작업을 처음 만들 때 처리하기 위해 식별된 모든 파일이 나열되어 있습니다. 작업을 계속하면 AzCopy는 아직 전송되지 않은 플랜 파일에 나열된 모든 파일을 전송하려고 합니다.

## <a name="change-the-location-of-plan-files"></a>플랜 파일의 위치를 변경합니다.

해당 명령 중 하나를 사용합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> 명령 프롬프트에서 `set AZCOPY_JOB_PLAN_LOCATION=<value>`를 사용합니다. |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

`azcopy env`를 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 플랜 파일이 기본 위치에 작성됩니다.

## <a name="change-the-location-of-log-files"></a>로그 파일의 위치 변경

해당 명령 중 하나를 사용합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> 명령 프롬프트에서 `set AZCOPY_LOG_LOCATION=<value>`를 사용합니다.|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env`를 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 로그가 기본 위치에 작성됩니다.

## <a name="change-the-default-log-level"></a>기본 로그 수준 변경

기본적으로 AzCopy 로그 수준은 `INFO`로 설정됩니다. 디스크 공간 절약을 위해 로그의 세부 정보 표시를 줄이려는 경우 ``--log-level`` 옵션을 사용하여 설정을 덮어씁니다.

사용 가능한 로그 수준은 `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` 및 `FATAL`입니다.

## <a name="remove-plan-and-log-files"></a>플랜 및 로그 파일 제거

디스크 공간을 절약하기 위해 로컬 머신에서 모든 플랜과 로그 파일을 제거하려면 `azcopy jobs clean` 명령을 사용합니다.

하나의 작업과 연관된 플랜과 로그 파일을 제거하려면 `azcopy jobs rm <job-id>`를 사용합니다. 이 예에서 `<job-id>` 자리 표시자를 작업의 작업 ID로 바꿉니다.

## <a name="see-also"></a>참조

- [AzCopy 시작](storage-use-azcopy-v10.md)
