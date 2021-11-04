---
title: 서비스 커넥터 문제 해결 지침
description: 서비스 커넥터의 오류 목록 및 제안 된 작업
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 56cde75f021b262cedc4c446f183b8920e78feed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053718"
---
# <a name="how-to-troubleshoot-with-service-connector"></a>서비스 커넥터를 사용 하 여 문제를 해결 하는 방법

문제가 발생 하는 경우 오류 메시지를 참조 하 여 제안 된 작업 또는 수정 사항을 찾을 수 있습니다. 이 방법 가이드에서는 서비스 커넥터 문제를 해결 하는 옵션을 보여 줍니다.

## <a name="error-message-and-suggested-actions-from-portal"></a>포털의 오류 메시지 및 제안 된 작업

| 오류 메시지 | 권장 조치 |
| --- | --- |
| 알 수 없는 리소스 유형 | <ul><li>원본 및 대상 리소스를 검사 하 여 서비스 커넥터에서 서비스 유형이 지원 되는지 확인 합니다.</li><li>지정 된 소스 대상 연결 조합이 서비스 커넥터에서 지원 되는지 여부를 확인 합니다.</li></ul> |
| 알 수 없는 리소스 유형 | <ul><li>대상 리소스가 있는지 여부를 확인 합니다.</li><li>대상 리소스 ID의 정확성을 확인 합니다.</li></ul> |
| 지원 되지 않는 리소스 | <ul><li>인증 유형이 지정 된 원본-대상 연결 조합에서 지원 되는지 여부를 확인 합니다.</li></ul> |

### <a name="error-typeerror-message-and-suggested-actions-using-azure-cli"></a>Azure CLI를 사용 하는 오류 유형, 오류 메시지 및 제안 된 작업

### <a name="invalidargumentvalueerror"></a>InvalidArgumentValueError


| 오류 메시지 | 권장 조치 |
| --- | --- |
| 원본 리소스 ID가 잘못 되었습니다. `{SourceId}` | <ul><li>서비스 커넥터에서 원본 리소스 ID가 지원 되는지 여부를 확인 합니다.</li><li>원본 리소스 ID의 정확성을 확인 합니다.</li></ul> |
| 대상 리소스 ID가 잘못 되었습니다. `{TargetId}` | <ul><li>서비스 커넥터에서 대상 서비스 형식이 지원 되는지 여부를 확인 합니다.</li><li>대상 리소스 ID의 정확성을 확인 합니다.</li></ul> |
| 연결 ID가 잘못 되었습니다. `{ConnectionId}` | <ul><li>연결 ID가 올바른지 확인 합니다.</li></ul> |


### <a name="requiredargumentmissingerror"></a>RequiredArgumentMissingError

| 오류 메시지 | 권장 조치 |
| --- | --- |
| `{Argument}` 비워 둘 수 없음 | 사용자는 대화형 입력의 인수 값을 제공 해야 합니다. |
| 매개 변수에 필요한 키가 없습니다 `{Parameter}` . 가능한 모든 키는 다음과 같습니다. `{Keys}` | 인증 정보 매개 변수에 대 한 값을 제공 합니다. 일반적으로 형식 `--param key1=val1 key2=val2` 입니다. |
| 필요한 인수가 누락 되었습니다. 인수를 제공 하십시오. `{Arguments}` | 필수 인수를 제공 합니다. | 

### <a name="validationerror"></a>ValidationError

| 오류 메시지 | 권장 조치 |
| --- | --- |
| 인증 정보는 하나만 필요 합니다. | 사용자는 하나의 인증 정보 매개 변수만 제공할 수 있으며, 인증 정보가 제공 되지 않거나 여러 인증 정보 매개 변수가 제공 되는지 여부를 확인 합니다. |
| 연결을 업데이트할 때 인증 정보 인수를 제공 해야 합니다. `{ConnectionName}` | 비밀 유형 연결을 업데이트할 때 인증 정보 매개 변수를 제공 해야 합니다. 이는 ARM api를 통해 사용자의 암호에 액세스할 수 없기 때문입니다. |
| 업데이트 하려면 클라이언트 유형 또는 인증 정보를 지정 해야 합니다. | 연결을 업데이트할 때 클라이언트 유형 또는 인증 정보를 제공 해야 합니다. |
| 사용법 오류: {} [KEY = VALUE ...] | 사용 가능한 키를 확인 하 고 인증 정보 매개 변수의 값을 일반적으로 형식으로 제공 합니다 `--param key1=val1 key2=val2` . |
| 매개 변수에 지원 되지 않는 키 `{Key}` 가 제공 되었습니다 `{Parameter}` . 가능한 모든 키는 다음과 같습니다. `{Keys}` | 사용 가능한 키를 확인 하 고 인증 정보 매개 변수의 값을 일반적으로 형식으로 제공 합니다 `--param key1=val1 key2=val2` . |
| 프로 비전 하지 못했습니다. 대상 리소스를 수동으로 만든 다음 연결을 만드십시오. 오류 세부 정보: `{ErrorTrace}` | <ul><li>다시 시도</li><li>대상 리소스를 수동으로 만든 다음 연결을 만듭니다.</li></ul> |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
