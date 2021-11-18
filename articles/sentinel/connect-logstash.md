---
title: Logstash를 통해 Microsoft Sentinel | 데이터 원본 커넥트 Microsoft Docs
description: Logstash를 사용하여 외부 데이터 원본에서 Microsoft Sentinel로 로그를 전달하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 827fd0f5fcc42639b435184a051edbd040602e7d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724922"
---
# <a name="use-logstash-to-connect-data-sources-to-microsoft-sentinel"></a>Logstash를 사용하여 Microsoft Sentinel에 데이터 원본 연결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Logstash 출력 플러그 인을 사용한 데이터 수집은 현재 퍼블릭 미리 보기 상태입니다. 이 기능은 Service Level Agreement(서비스 수준 약정) 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Logstash 데이터 수집 엔진 에 Microsoft Sentinel의 출력 플러그 인을 사용하면 **Logstash를** 통해 원하는 모든 유형의 로그를 Microsoft Sentinel의 Log Analytics 작업 영역으로 직접 보낼 수 있습니다. 로그는 출력 플러그 인을 사용하여 정의하는 사용자 지정 테이블로 전송됩니다.

Logstash 데이터 수집 엔진 사용에 대해 자세히 알아보려면 [Getting started with Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)(Logstash 시작)를 참조하세요.

## <a name="overview"></a>개요

### <a name="architecture-and-background"></a>아키텍처 및 배경

![Logstash 아키텍처의 다이어그램](./media/connect-logstash/logstash-architecture.png)

Logstash 엔진은 다음과 같은 세 가지 구성 요소로 구성됩니다.

- 입력 플러그 인: 다양한 원본의 사용자 지정된 데이터 컬렉션입니다.
- 필터 플러그 인: 지정된 조건에 따라 데이터를 조작하고 정규화합니다.
- 출력 플러그 인: 수집되고 처리된 데이터를 다양한 대상으로 보내기를 사용자 지정합니다.

> [!NOTE]
> - Microsoft는 여기에서 설명한 Microsoft Sentinel 제공 Logstash 출력 플러그 인만 지원합니다. 플러그 인의 현재 버전은 v1.0.0이며 2020년 8월 25일에 릴리스되었습니다. 출력 플러그 인과 관련된 모든 문제에 대해 [지원 티켓을 열 수 있습니다](https://ms.portal.azure.com/#create/Microsoft.Support).
>
> - Microsoft는 Microsoft Sentinel에 대한 타사 Logstash 출력 플러그 인 또는 모든 유형의 다른 Logstash 플러그 인 또는 구성 요소를 지원하지 않습니다.
>
> - Microsoft Sentinel의 Logstash 출력 플러그 인은 **7.0~7.15의 Logstash 버전만** 지원합니다.

Logstash용 Microsoft Sentinel 출력 플러그 인은 Log Analytics HTTP 데이터 수집기 REST API 사용하여 JSON 형식의 데이터를 Log Analytics 작업 영역으로 보냅니다. 데이터는 사용자 지정 로그에 수집됩니다.

- [Log Analytics REST API](/rest/api/loganalytics/create-request)에 대해 자세히 알아봅니다.
- [사용자 지정 로그](../azure-monitor/agents/data-sources-custom-logs.md)에 대해 자세히 알아봅니다.

## <a name="deploy-the-microsoft-sentinel-output-plugin-in-logstash"></a>Logstash에서 Microsoft Sentinel 출력 플러그 인 배포

### <a name="step-1-installation"></a>1단계: 설치

Microsoft Sentinel 출력 플러그 인은 Logstash 컬렉션에서 사용할 수 있습니다.

- Logstash [Working with plugins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html)(플러그 인 사용) 문서의 지침에 따라 ***[microsoft-logstash-output-azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** 플러그 인을 설치합니다.
   
- Logstash 시스템에서 인터넷에 액세스할 수 없는 경우 Logstash [Offline Plugin Management](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html)(오프라인 플러그 인 관리) 문서의 지침에 따라 오프라인 플러그 인 팩을 준비하고 사용합니다. 이렇게 하려면 인터넷에 액세스할 수 있는 다른 Logstash 시스템을 빌드해야 합니다.

### <a name="step-2-configuration"></a>2단계: 구성

[구성 파일의](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) Logstash 구조 문서의 정보를 사용하고 다음 키와 값을 사용하여 Microsoft Sentinel 출력 플러그 인을 구성에 추가합니다. 테이블 다음에 적절한 구성 파일 구문이 표시됩니다.

| 필드 이름 | 데이터 형식 | Description |
|----------------|---------------|-----------------|
| `workspace_id` | 문자열 | 작업 영역 ID GUID를 입력합니다(팁 참조). |
| `workspace_key` | 문자열 | 작업 영역 기본 키 GUID를 입력합니다(팁 참조). |
| `custom_log_table_name` | 문자열 | 로그를 수집할 테이블의 이름을 설정합니다. 테이블 이름은 출력 플러그 인당 하나만 구성할 수 있습니다. 로그 테이블은 Microsoft Sentinel의 **로그** 아래에 표시되며 **사용자 지정 로그** 범주의 **테이블에는** `_CL` 접미사가 있습니다. |
| `endpoint` | 문자열 | 선택적 필드입니다. 기본적으로 Log Analytics 엔드포인트입니다. 이 필드를 사용하여 대체 엔드포인트를 설정합니다. |
| `time_generated_field` | 문자열 | 선택적 필드입니다. 이 속성은 Log Analytics의 기본 **TimeGenerated** 필드를 재정의합니다. 데이터 원본에 타임스탬프 필드의 이름을 입력합니다. 필드의 데이터는 ISO 8601 형식(`YYYY-MM-DDThh:mm:ssZ`)을 준수해야 합니다. |
| `key_names` | array | Log Analytics 출력 스키마 필드 목록을 입력합니다. 각 목록 항목은 작은따옴표로 묶고 쉼표로 구분하며 전체 목록은 대괄호로 묶어야 합니다. 아래 예제를 참조하세요. |
| `plugin_flush_interval` | number | 선택적 필드입니다. Log Analytics로의 메시지 전송 간 최대 간격(초)을 정의하려면 설정합니다. 기본값은 5입니다. |
| `amount_resizing` | boolean | True 또는 False입니다. 수신되는 로그 데이터의 볼륨에 따라 메시지 버퍼 크기를 조정하는 자동 스케일링 메커니즘을 사용하거나 사용하지 않도록 설정합니다. |
| `max_items` | number | 선택적 필드입니다. `amount_resizing`이 “false”로 설정된 경우에만 적용됩니다. 메시지 버퍼 크기(레코드 내)의 한도를 설정하려면 사용합니다. 기본값은 2000입니다.  |
| `azure_resource_id` | 문자열 | 선택적 필드입니다. 데이터가 상주하는 Azure 리소스의 ID를 정의합니다. <br>리소스 ID 값은 [리소스 컨텍스트 RBAC](resource-context-rbac.md)를 사용하여 특정 데이터에만 액세스를 제공하는 경우에 특히 유용합니다. |
| | | |

> [!TIP]
> - 작업 영역 ID 및 기본 키는 **에이전트 관리** 의 작업 영역 리소스에서 찾을 수 있습니다.
> - **그러나** 자격 증명 및 기타 중요한 정보가 일반 텍스트로 구성 파일에 저장되어 있으면 보안 모범 사례와 맞지 않으므로 **작업 영역 ID** 와 **작업 영역 기본 키** 를 구성에 안전하게 포함하려면 **Logstash 키 저장소** 를 사용하는 것이 좋습니다. 지침은 [Elastic의 설명서](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)를 참조하세요.

#### <a name="sample-configurations"></a>샘플 구성

다음은 몇 가지 옵션을 사용하는 일부 샘플 구성입니다.

- filebeat 입력 파이프를 사용하는 기본 구성:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- tcp 입력 파이프를 사용하는 기본 구성:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- 고급 구성:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > 내부 작업, 구성, 성능 설정에 관해 자세히 알아보려면 출력 플러그 인 [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)를 참조하세요.

### <a name="step-3-restart-logstash"></a>3단계: Logstash 다시 시작

### <a name="step-4-view-incoming-logs-in-microsoft-sentinel"></a>4단계: Microsoft Sentinel에서 들어오는 로그 보기

1. 메시지가 출력 플러그 인으로 전송되고 있는지 확인합니다.

1. Microsoft Sentinel 탐색 메뉴에서 **로그를** 클릭합니다. **Tables** 제목 아래에서 **사용자 지정 로그** 범주를 확장합니다. 구성에서 `_CL` 접미사를 사용하여 지정한 테이블의 이름을 찾아 클릭합니다.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Logstash 사용자 지정 로그 스크린샷":::

1. 테이블의 레코드를 보려면 테이블 이름을 스키마로 사용하여 테이블을 쿼리합니다.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Logstash 사용자 지정 로그 쿼리 스크린샷":::

## <a name="monitor-output-plugin-audit-logs"></a>출력 플러그 인 감사 로그 모니터링

Microsoft Sentinel 출력 플러그 인의 연결 및 작업을 모니터링하려면 적절한 Logstash 로그 파일을 사용하도록 설정합니다. 로그 파일 위치는 [Logstash Directory Layout](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout)(Logstash 디렉터리 레이아웃) 문서를 참조하세요.

이 로그 파일에 데이터가 표시되지 않는 경우 입력 및 필터 플러그 인을 통해 일부 이벤트를 생성하고 로컬로 전송하여 출력 플러그 인에서 데이터를 수신하는지 확인합니다. Microsoft Sentinel은 출력 플러그 인과 관련된 문제만 지원합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Logstash를 사용하여 외부 데이터 원본을 Microsoft Sentinel에 연결하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](detect-threats-built-in.md) 또는 [사용자 지정](detect-threats-custom.md) 규칙을 사용 하 여 Microsoft 센티널에서 위협을 검색 하기 시작 합니다.
