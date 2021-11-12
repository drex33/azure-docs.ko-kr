---
title: 서비스 커넥터 문제 해결 지침
description: 서비스 커넥터의 오류 목록 및 제안된 작업
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8a1379495dacba7d2c0cf21af3a1e5ec2f45ed41
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283186"
---
# <a name="how-to-troubleshoot-with-service-connector"></a>Service Connector를 사용하여 문제를 해결하는 방법

문제가 발생하면 오류 메시지를 참조하여 제안된 작업 또는 수정 사항을 찾을 수 있습니다. 이 방법 가이드에서는 Service Connector 문제를 해결하는 옵션을 보여줍니다.

## <a name="error-message-and-suggested-actions-from-portal"></a>포털에서 오류 메시지 및 제안된 작업

| 오류 메시지 | 권장 조치 |
| --- | --- |
| 알 수 없는 리소스 종류 | <ul><li>원본 및 대상 리소스를 확인하여 서비스 유형이 서비스 커넥터에서 지원되는지 확인합니다.</li><li>지정된 원본-대상 연결 조합이 Service Connector에서 지원되는지 확인합니다.</li></ul> |
| 알 수 없는 리소스 종류 | <ul><li>대상 리소스가 있는지 확인합니다.</li><li>대상 리소스 ID의 정확성을 확인합니다.</li></ul> |
| 지원되지 않는 리소스 | <ul><li>지정된 원본-대상 연결 조합에서 인증 유형을 지원하는지 확인합니다.</li></ul> |

### <a name="error-typeerror-message-and-suggested-actions-using-azure-cli"></a>오류 유형, 오류 메시지 및 Azure CLI 사용하는 제안된 작업

### <a name="invalidargumentvalueerror"></a>InvalidArgumentValueError


| 오류 메시지 | 권장 조치 |
| --- | --- |
| 원본 리소스 ID가 잘못되었습니다. `{SourceId}` | <ul><li>Service Connector에서 지원하는 원본 리소스 ID인지 확인합니다.</li><li>원본 리소스 ID의 정확성을 확인합니다.</li></ul> |
| 대상 리소스 ID가 잘못되었습니다. `{TargetId}` | <ul><li>대상 서비스 유형이 Service Connector에서 지원되는지 확인합니다.</li><li>대상 리소스 ID의 정확성을 확인합니다.</li></ul> |
| 연결 ID가 잘못되었습니다. `{ConnectionId}` | <ul><li>연결 ID의 정확성을 확인합니다.</li></ul> |


### <a name="requiredargumentmissingerror"></a>RequiredArgumentMissingError

| 오류 메시지 | 권장 조치 |
| --- | --- |
| `{Argument}` 비어 있지 않아야 합니다. | 사용자가 대화형 입력에 대한 인수 값을 제공해야 합니다. |
| 매개 변수 에 대한 필수 키가 `{Parameter}` 누락되었습니다. 가능한 모든 키는 다음과 같습니다. `{Keys}` | 일반적으로 형식으로 인증 정보 매개 변수에 대한 값을 `--param key1=val1 key2=val2` 제공합니다. |
| 필수 인수가 없습니다. 인수를 제공하세요. `{Arguments}` | 필수 인수를 제공합니다. | 

### <a name="validationerror"></a>ValidationError

| 오류 메시지 | 권장 조치 |
| --- | --- |
| 인증 정보는 하나만 필요합니다. | 사용자는 인증 정보 매개 변수를 하나만 제공할 수 있습니다. 인증 정보가 제공되지 않았는지 또는 여러 인증 정보 매개 변수가 제공되었는지 확인합니다. |
| 연결을 업데이트할 때 인증 정보 인수를 제공해야 합니다. `{ConnectionName}` | 비밀 형식 연결을 업데이트할 때 인증 정보 매개 변수를 제공해야 합니다. (사용자의 비밀은 ARM API를 통해 액세스할 수 없으므로) |
| 업데이트하려면 클라이언트 유형 또는 인증 정보를 지정해야 합니다. | 연결을 업데이트할 때 클라이언트 유형 또는 인증 정보를 제공해야 합니다. |
| 사용 오류: {} [KEY=VALUE ...] | 사용 가능한 키를 확인하고 일반적으로 형식의 인증 정보 매개 변수에 대한 값을 `--param key1=val1 key2=val2` 제공합니다. |
| 지원되지 않는 `{Key}` 키는 매개 변수 에 대해 `{Parameter}` 제공됩니다. 가능한 모든 키는 다음과 같습니다. `{Keys}` | 사용 가능한 키를 확인하고 일반적으로 형식의 인증 정보 매개 변수에 대한 값을 `--param key1=val1 key2=val2` 제공합니다. |
| 프로비전에 실패했습니다. 대상 리소스를 수동으로 만든 다음 연결을 만드세요. 오류 세부 정보: `{ErrorTrace}` | <ul><li>다시 시도</li><li>대상 리소스를 수동으로 만든 다음 연결을 만듭니다.</li></ul> |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
