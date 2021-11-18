---
title: 기존 SIEM에서 Microsoft 센티널로 마이그레이션합니다.
description: 조직 전체에서 확장 가능한 지능형 보안 분석을 위해 기존 SIEM에서 Microsoft 센티널로 가장 잘 마이그레이션하는 방법에 대해 알아봅니다.
author: batamig
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 13ce232cf2f7ea113ddfb8e28455b53cd5790ac8
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755966"
---
# <a name="migrate-to-microsoft-sentinel-from-an-existing-siem"></a>기존 SIEM에서 Microsoft 센티널로 마이그레이션

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

SOC(보안 운영 센터) 팀은 중앙 집중식 SIEM(보안 정보 및 이벤트 관리) 및 SOAR(보안 오케스트레이션, 자동화 및 대응) 솔루션을 사용하여 점점 더 분산되는 디지털 자산을 보호합니다.

레거시 SIEM은 온-프레미스에 있는 경우가 많으며 온-프레미스 자산에 대해 양호한 적용 범위를 유지할 수 있습니다. 하지만, 온-프레미스 아키텍처는 Azure, Microsoft 365, AWS 또는 GCP(Google Cloud Platform)와 같은 클라우드 자산에 대한 적용 범위가 충분하지 않을 수 있습니다. 이와 대조적으로 Microsoft 센티널은 온-프레미스 및 클라우드 자산의 데이터를 수집할 수 있으므로 전체 공간에 대 한 적용을 보장 합니다.

이 문서에서는 side-by-side 구성에서 또는 전체 Microsoft 센티널 배포로 전환 하 여 기존 기존 SIEM에서 Microsoft 센티널로 마이그레이션하는 방법에 대해 설명 합니다.

## <a name="plan-your-migration"></a>마이그레이션 계획

비즈니스 요구 사항과 사용 가능한 리소스에 따라 Microsoft 센티널로 직접 또는 점진적 전환을 시작 하도록 결정 했을 수 있습니다.

전환으로 인해 적용 범위에 공백이 생겨 조직의 보안이 위태로워지는 일이 없도록 마이그레이션을 적절하게 계획할 필요가 있습니다.

시작하려면 주요 핵심 기능과 최우선 요구 사항을 식별합니다. 현재 SIEM에 포함 된 주요 사용 사례를 평가 하 고 Microsoft 센티널에서 계속 해 서 검사를 제공 해야 하는 검색 및 기능을 결정 합니다.

마이그레이션하려는 정확한 데이터 원본 및 탐지 규칙을 고려하면서 마이그레이션 프로세스의 각 단계에서 In Process 계획을 더 추가할 수 있습니다. 자세한 내용은 [데이터 마이그레이션](#migrate-your-data) 및 [분석 규칙 마이그레이션](#migrate-analytics-rules)을 참조하세요.

> [!TIP]
> 현재 SIEM에 탐지 및 사용 사례가 너무 많을 수 있습니다. 그 중 어떤 것이 비즈니스에 가장 유용한지 판단하고 마이그레이션할 필요가 없는 것을 결정하세요. 예를 들어, 지난 1년 동안 어떤 탐지로 결과가 생성되었는지 확인하세요.
>

### <a name="compare-your-legacy-siem-to-microsoft-sentinel"></a>Microsoft 센티널과 레거시 SIEM 비교

레거시 SIEM을 Microsoft 센티널과 비교 하 여 마이그레이션 완료 조건을 구체화 하 고 Microsoft 센티널에서 더 많은 가치를 추출할 수 있는 위치를 파악 하세요.

예를 들어 다음 주요 영역을 평가합니다.

|평가 영역 |Description  |
|---------|---------|
|**공격 탐지 범위**     | [MITRE ATT&CK](https://attack.mitre.org/) 또는 유사한 프레임워크를 사용하여 각 SIEM이 전체 공격 범위를 얼마나 잘 탐지할 수 있는지 비교합니다.        |
|**대응성**     |   SIEM에 경고가 표시된 후 분석가가 관련 작업을 시작하기까지의 시간인 MTTA(평균 인지 시간)를 측정합니다. 이 시간은 대개 SIEM 간에 유사합니다.      |
|**MTTR(평균 수정 시간)**     |    분석가의 기술 수준이 동일하다고 가정하고 각 SIEM에서 조사한 인시던트에 대한 MTTR을 비교합니다.     |
|**헌팅 속도 및 민첩성**     | 완전히 형성된 가설에서 시작하여 데이터를 쿼리하고 각 SIEM 플랫폼에 대한 결과를 얻기까지 팀이 헌팅할 수 있는 속도가 얼마나 빠른지 측정합니다.        |
|**용량 증가 마찰.**     |  사용량이 증가함에 따라 용량을 추가하는 난이도를 비교합니다. 클라우드 서비스 및 애플리케이션은 기존 온-프레미스 워크로드보다 더 많은 로그 데이터를 생성하는 경향이 있습니다.       |
|     |         |

기존 온-프레미스 SIEM에 대 한 투자를 제한 하거나 투자 하지 않은 경우 Microsoft 센티널로 이동 하는 것은 간단 하 고 직접적인 배포가 될 수 있습니다. 하지만, 레거시 SIEM에 막대한 투자를 한 기업은 전환 작업을 수용하기 위해 일반적으로 다단계 프로세스가 필요합니다.

Microsoft 센티널은 온-프레미스 클라우드 모두에 대해 확장 된 데이터와 응답을 제공 하지만 Microsoft 센티널 및 레거시 [SIEM을 함께 실행](#select-a-side-by-side-approach-and-method)하 여 마이그레이션을 느리게 시작할 수 있습니다. 병렬 아키텍처에서 로컬 리소스는 온-프레미스 SIEM 및 클라우드 리소스를 사용할 수 있고 새 워크로드는 클라우드 기반 분석을 사용합니다.

장기 side-by-side 구성을 선택 하지 않는 한 전체 Microsoft 센티널 배포로의 마이그레이션을 완료 하 여 낮은 인프라 비용, 실시간 위협 분석 및 클라우드 확장성에 액세스할 수 있습니다.

## <a name="select-a-side-by-side-approach-and-method"></a>병렬 접근 방식 및 방법 선택

조직의 SIEM 요구 사항에 따라 병렬 아키텍처를 단기 과도기 단계(완전한 클라우드 호스팅 SIEM으로 이어짐)로 또는 중장기 운영 모델로 사용합니다.

예를 들어 권장되는 아키텍처는 마이그레이션을 완료하기에 충분한 기간 동안만 병렬 아키텍처를 사용하는 것이지만 조직에서 병렬 구성을 더 오래 유지하려고 할 수 있습니다(예를 들어 레거시 SIEM에서 벗어날 준비가 되지 않은 경우). 일반적으로 장기적인 병렬 구성을 사용 하는 조직은 Microsoft 센티널을 사용 하 여 클라우드 데이터만 분석 합니다.

마이그레이션에 어떤 방법을 사용할지 결정할 때는 각 접근 방식의 장단점을 고려합니다.

> [!NOTE]
> 비용과 복잡성 때문에 여러 온-프레미스 분석 솔루션을 실행하지 않는 조직이 많습니다.
>
> Microsoft 센티널은 [종 량 제 가격 책정](billing.md) 및 유연한 인프라를 제공 하 여 SOC 팀이 변경 내용을 적용할 수 있는 시간을 제공 합니다. 귀사에 가장 적합한 속도로 콘텐츠를 마이그레이션하고 테스트하세요.
>
### <a name="short-term-approach"></a>단기 접근 방식

:::row:::
   :::column span="":::
      **장점**

        - 워크로드 및 분석을 마이그레이션하면서 SOC 직원이 새 프로세스에 적응할 시간을 줍니다.

        - 헌팅 시나리오를 위해 모든 데이터 원본 간에 심층적인 상관 관계를 얻습니다.

        - SIEM 간에 분석을 수행하고, 전달 규칙을 만들고, 두 곳에서 면밀한 조사를 수행할 필요가 없습니다.

        - SOC 팀이 레거시 SIEM 솔루션을 신속하게 다운그레이드하여 인프라 및 라이선스 비용을 제거할 수 있습니다.
   :::column-end:::
   :::column span="":::
      **단점**

        - SOC 직원에게 가파른 학습 곡선이 필요할 수 있습니다.
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>중장기 접근 방식

:::row:::
   :::column span="":::
      **장점**

        -기존 siem에서 완전히 이동 하지 않고도 AI, ML 및 조사 기능과 같은 주요 Microsoft 센티널 혜택을 사용할 수 있습니다.

        -Microsoft 센티널의 클라우드 또는 Microsoft 데이터를 분석 하 여 레거시 SIEM에 비해 비용을 절약 합니다.
   :::column-end:::
   :::column span="":::
      **단점**

        - 여러 데이터베이스에서 분석을 구분하여 복잡성이 증가합니다.

        - 다중 환경 인시던트의 사례 관리 및 조사를 분할합니다.

        - 직원 및 인프라 비용이 더 많이 발생합니다.

        - SOC 직원이 두 가지 SIEM 솔루션에 대해 잘 알고 있어야 합니다.
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-microsoft-sentinel-recommended"></a>레거시 SIEM에서 Microsoft 센티널로 경고 보내기 (권장)

레거시 SIEM에서 Microsoft 센티널로의 비정상적인 활동에 대 한 경고 또는 표시기를 보냅니다.

- Microsoft 센티널에서 클라우드 데이터 수집 및 분석
- 레거시 SIEM을 사용하여 온-프레미스 데이터를 분석하고 경고를 생성합니다.
- 온-프레미스 SIEM의 경고를 Microsoft 센티널로 전달 하 여 단일 인터페이스를 설정 합니다.

예를 들어 [Logstash 태](connect-logstash.md)트, [api](/rest/api/securityinsights/)또는 [Syslog](connect-syslog.md)를 사용 하 여 경고를 전달 하 고 Microsoft 센티널 [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md)에 [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) 형식으로 저장 합니다.

Microsoft 센티널에서 레거시 SIEM으로 경고를 전송 하 여 팀은 Microsoft 센티널에서 이러한 경고를 교차 상호 연결 하 고 조사할 수 있습니다. 팀은 필요한 경우 심층 조사를 위해 레거시 SIEM에 계속 액세스할 수 있습니다. 한편, 연장된 전환 기간 동안 데이터 원본을 계속 마이그레이션할 수 있습니다.

이 권장 병렬 마이그레이션 방법은 Microsoft 센티널의 전체 가치 및 조직에 적합 한 속도로 데이터 원본을 마이그레이션하는 기능을 제공 합니다. 이 접근 방식은 데이터 원본을 이동하는 동안 데이터 스토리지 및 수집 비용이 중복되는 것을 방지합니다.

자세한 내용은 다음을 참조하세요.

- [Microsoft 센티널로 QRadar offenses 마이그레이션](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043)
- [Splunk에서 Microsoft 센티널로 데이터를 내보냅니다](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237).


### <a name="send-alerts-and-enriched-incidents-from-microsoft-sentinel-to-a-legacy-siem"></a>Microsoft 센티널에서 레거시 SIEM으로 경고 및 보강 인시던트 보내기

Microsoft 센티널에서 클라우드 데이터와 같은 일부 데이터를 분석 한 다음 생성 된 경고를 레거시 SIEM으로 보냅니다. Microsoft 센티널에서 생성 한 경고와 교차 상관 관계를 수행 하려면 단일 인터페이스로 *레거시* siem을 사용 합니다. Microsoft 센티널에서 생성 된 경고를 심층적으로 조사 하기 위해 여전히 Microsoft 센티널을 사용할 수 있습니다.

비용을 복제 하거나 데이터에 대 한 요금을 지불 하지 않고도 클라우드 데이터 분석을 Microsoft 센티널로 이동할 수 있으므로이 구성은 비용 효율적입니다. 원하는 속도로 자유롭게 마이그레이션할 수 있습니다. 계속 해 서 데이터 원본을 이동 하 고 Microsoft 센티널로 검색 하는 경우 기본 인터페이스로 Microsoft 센티널로 마이그레이션하는 것이 더 쉽습니다. 그러나 보강 인시던트를 레거시 SIEM으로 전달 하기만 하면 Microsoft 센티널의 조사, 구하기 및 자동화 기능에서 얻은 값이 제한 됩니다.

자세한 내용은 다음을 참조하세요.

- [보강 Microsoft 센티널 경고를 레거시 SIEM으로 보냅니다.](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [보강 Microsoft 센티널 경고를 IBM QRadar으로 보내기](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [Splunk에 Microsoft 센티널 경고 수집](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>다른 방법

다음 표에는 권장되지 않는 병렬 구성과 그 이유에 대한 세부 정보가 설명되어 있습니다.

|메서드  |Description  |
|---------|---------|
|**Microsoft 센티널 로그를 레거시 SIEM으로 보냅니다.**     |  이 방법을 사용하면 온-프레미스 SIEM의 비용 및 크기 조정 문제를 계속 경험하게 됩니다. <br><br>레거시 SIEM의 저장소 비용과 함께 Microsoft 센티널의 데이터 수집에 대 한 비용을 지불 하 고, Microsoft 센티널의 SIEM 및 대화 충성도 검색, 분석, 사용자 엔터티 동작 분석 (UEBA), AI 또는 조사 및 자동화 도구를 사용할 수 없습니다.       |
|**레거시 SIEM에서 Microsoft 센티널로 로그 보내기**     |   이 방법은 Microsoft 센티널의 전체 기능을 제공 하지만 조직에서는 두 개의 서로 다른 데이터 수집 원본에 대해 계속 지불 합니다. 이 모델은 아키텍처 복잡성을 추가하는 것 외에도 비용이 더 높아질 수 있습니다.     |
|**Microsoft 센티널 및 레거시 SIEM을 완전히 분리 된 두 솔루션으로 사용**     |  Microsoft 센티널을 사용 하 여 클라우드 데이터와 같은 일부 데이터 원본을 분석 하 고 다른 원본에 대해 온-프레미스 SIEM을 계속 사용할 수 있습니다. 이렇게 설정하면 각 솔루션을 언제 사용해야 하는지에 대한 명확한 경계가 가능하며 비용 중복이 방지됩니다. <br><br>하지만, 상호 비교가 어려워지고 두 데이터 원본 세트를 교차하는 공격을 완전히 진단할 수 없습니다. 현대 환경에서는 위협이 조직 전체를 측면으로 이동하는 경우가 많으며, 이러한 가시성 격차는 심각한 보안 위험을 초래할 수 있습니다.       |
|     |         |



## <a name="migrate-your-data"></a>데이터 마이그레이션

현재 주요 사용 사례를 나타내는 데이터만 마이그레이션해야 합니다.

1. 각 사용 사례를 지원하는 데 필요한 데이터를 결정합니다.

1. 현재 데이터 원본이 가치 있는 데이터를 제공하는지 여부를 확인합니다.

1. 현재 SIEM의 가시성 격차와 이를 해소할 수 있는 방법을 식별합니다.

1. 각 데이터 원본에 대해 원시 로그를 수집해야 하는지(비용이 많이 들 수 있음) 또는 보강된 경고가 주요 사용 사례에 대한 충분한 컨텍스트를 제공하는지를 고려합니다.

      예를 들어 조직의 보안 제품에서 보강 데이터를 수집 하 고, 데이터 원본 자체에서 원시 로그를 수집 하지 않고도 Microsoft 센티널을 사용 하 여 해당 데이터 간의 상관 관계를 지정할 수 있습니다.

1. 데이터를 수집하려면 다음 리소스를 사용합니다.

    - **Microsoft 센티널의 [기본 제공 데이터 커넥터](connect-data-sources.md)** 를 사용 하 여 수집 데이터를 시작 합니다. 예를 들어 클라우드 데이터로 [평가판](billing.md#free-trial)을 시작하거나 [무료 데이터 커넥터](billing.md#free-data-sources)를 사용하여 다른 Microsoft 제품에서 데이터를 수집할 수 있습니다.

    - **[Syslog](connect-data-sources.md#syslog), [CEF(Common Event Format)](connect-data-sources.md#common-event-format-cef) 또는 [REST API](connect-data-sources.md#rest-api-integration)** 를 사용하여 다른 데이터 원본을 연결합니다.

        자세한 내용은 [Microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md) 및 [microsoft 센티널 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조 하세요.

> [!TIP]
> - 무료 데이터 원본으로만 사용을 제한하면 중요한 데이터로 테스트하는 기능이 제한될 수 있습니다. 테스트할 때 무료 및 유료 데이터 커넥터 모두에서 제한된 데이터 수집을 고려해야 테스트 결과를 최대한 활용할 수 있습니다.
>
> - Microsoft 센티널에서 검색 및 빌드 사용 사례를 마이그레이션하는 경우 수집 하는 데이터를 확인 하 고 키 우선 순위에 대 한 값을 확인 합니다. 데이터 수집 대화를 다시 논의하여 사용 사례 전반에서 데이터의 깊이와 범위를 확인합니다.
>

## <a name="migrate-analytics-rules"></a>분석 규칙 마이그레이션

Microsoft 센티널은 machine learning analytics를 사용 하 여 고화질 및 실행 가능한 인시던트를 만들며, 기존 검색 중 일부가 Microsoft 센티널에서 중복 될 수 있습니다. 따라서 모든 탐지 및 분석 규칙을 마구잡이로 마이그레이션하지 마십시오.

- 비즈니스 우선 순위와 효율성을 고려하여 규칙 마이그레이션에 타당한 사용 사례를 선택해야 합니다.

- 이미 사용 사례를 처리할 수 있는 [기본 제공 분석 규칙](detect-threats-built-in.md)을 검토합니다. Microsoft 센티널에서 **구성 > 분석 > 규칙 템플릿** 탭으로 이동 하 여 기본 제공 템플릿을 기반으로 하는 규칙을 만듭니다.

- 지난 6~12개월 동안 경고를 트리거하지 않은 규칙을 검토하고 아직 관련성이 있는지 확인합니다.

- 일상적으로 무시하는 낮은 수준의 위협이나 경고를 제거합니다.

**분석 규칙을 Microsoft 센티널로 마이그레이션하려면**:

1. 마이그레이션하려는 각 규칙에 대한 테스트 시스템이 있는지 확인합니다.

    1. 전체 테스트 시나리오 및 스크립트를 포함하여 마이그레이션된 규칙에 대한 **유효성 검사 프로세스를 준비** 합니다.

    1. 마이그레이션된 규칙을 테스트하는 데 **유용한 리소스가 팀에 있는지 확인합니다**.

    1. **필요한 데이터 원본이 연결되어 있는지 확인** 하고 데이터 연결 방법을 검토합니다.

1. Microsoft 센티널의 기본 제공 템플릿으로 검색을 사용할 수 있는지 여부를 확인 합니다.

    - **기본 제공 규칙이 충분하면** 기본 제공 규칙 템플릿을 사용하여 자신의 작업 영역에 대한 규칙을 만듭니다.

        Microsoft 센티널에서 **구성 > 분석 > 규칙 템플릿** 탭으로 이동 하 고 관련 된 각 분석 규칙을 만들고 업데이트 합니다.

        자세한 내용은 [곧바로 위협 탐지](detect-threats-built-in.md)를 참조하세요.

    - **Microsoft 센티널의 기본 제공 규칙에 포함 되지 않은 검색 기능이 있는 경우** [Uncoder.io](https://uncoder.io/) 와 같은 온라인 쿼리 변환기를 사용해 쿼리를 KQL로 변환 합니다.

        트리거 조건 및 규칙 작업을 파악한 다음, KQL 쿼리를 구성하고 검토합니다.

    - **기본 제공 규칙과 온라인 규칙 변환기가 모두 충분하지 않으면** 규칙을 수동으로 만들어야 합니다. 이런 경우 다음 단계를 사용하여 규칙 만들기를 시작합니다.

        1. **규칙에서 사용할 데이터 원본을 식별합니다**. Microsoft 센티널의 데이터 테이블과 데이터 테이블 간에 매핑 테이블을 만들어 쿼리 하려는 테이블을 식별할 수 있습니다.

        1. 규칙에 사용할 데이터의 **특성, 필드 또는 엔터티를 식별** 합니다.

        1. **규칙 조건과 논리를 식별합니다**. 이 단계에서는 규칙 템플릿을 KQL 쿼리를 구성하는 방법에 대한 샘플로 사용할 수 있습니다.

            필터, 상관 관계 규칙, 활성 목록, 참조 집합, 관심 목록, 탐지 이상 항목, 집계 등을 고려합니다. 레거시 SIEM에서 제공한 참조를 사용하여 쿼리 구문을 가장 잘 매핑하는 방법을 파악할 수 있습니다.

            예를 들어 다음을 참조하세요.

            - [ArcSight/QRadar과 Microsoft 센티널 간의 샘플 규칙 매핑](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [SPL에서 KQL 매핑 샘플](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. **트리거 조건 및 규칙 작업을 파악한 다음, KQL 쿼리를 구성하고 검토합니다**. 쿼리를 검토할 때 KQL 최적화 지침 리소스를 고려하세요.

1. 각각의 관련 사용 사례로 규칙을 테스트합니다. 예상한 결과를 제공하지 않으면 KQL을 검토하고 다시 테스트하는 것이 좋습니다.

1. 만족스러우면 마이그레이션된 규칙을 고려할 수 있습니다. 필요에 따라 규칙 작업에 대한 플레이북을 만듭니다. 자세한 내용은 [Microsoft 센티널에서 플레이 북을 사용 하 여 위협 대응 자동화](automate-responses-with-playbooks.md)를 참조 하세요.

**자세한 내용은 다음을 참조하세요.**

- [**위협 탐지를 위한 사용자 지정 분석 규칙 만들기**](detect-threats-custom.md). [경고 그룹화](detect-threats-custom.md#alert-grouping)를 사용하여 지정된 기간 내에 발생하는 경고를 그룹화하여 경고 피로를 줄입니다.
- [**Microsoft 센티널의 엔터티에 데이터 필드를 매핑하여**](map-data-fields-to-entities.md) SOC 엔지니어가 조사 중 추적할 증명 정보의 일부로 엔터티를 정의할 수 있도록 합니다. 엔터티 매핑을 사용하면 SOC 분석가가 시간과 노력을 줄일 수 있는 직관적인 [조사 그래프(investigate-cases.md#use-the-investigation-graph-to-deep-dive)를 활용할 수 있습니다.
- 증거를 사용하여 인시던트 미리 보기 창에서 특정 인시던트와 관련된 이벤트, 경고 및 책갈피를 표시하는 방법의 예로 [**UEBA 데이터를 사용하여 인시던트를 조사**](investigate-with-ueba.md)합니다.
- [**KQL(Kusto 쿼리 언어)**](/azure/data-explorer/kusto/query/): 데이터를 처리하고 결과를 반환하기 위해 [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 데이터베이스에 읽기 전용 요청을 보내는 데 사용할 수 있습니다. KQL은 [엔드포인트용 Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender) 및 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 같은 다른 Microsoft 서비스에서도 사용됩니다.

## <a name="use-automation-to-streamline-processes"></a>자동화를 사용하여 프로세스 간소화

자동화된 워크플로를 사용하여 경고를 일반적인 인시던트로 그룹화하고 우선 순위를 지정하고 우선 순위를 수정할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [Microsoft 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도)](automation.md).
- [Microsoft 센티널에서 플레이 북을 사용 하 여 위협 대응 자동화](automate-responses-with-playbooks.md)
- [Automation 규칙을 사용 하 여 Microsoft 센티널에서 인시던트 처리 자동화](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>레거시 SIEM 사용 중지

다음 검사 목록을 사용 하 여 Microsoft 센티널로 완전히 마이그레이션 되었으며 레거시 SIEM을 사용 중지할 준비가 되었는지 확인 합니다.


|준비 영역  |세부 정보  |
|---------|---------|
|**기술 준비**     | **중요 한 데이터 확인**: 모든 원본 및 경고를 Microsoft 센티널에서 사용할 수 있는지 확인 합니다. <br><br>**모든 레코드 보관**: 중요한 과거 인시던트 및 사례 레코드를 저장하고 원시 데이터는 선택 사항으로 기관 기록을 유지합니다.   |
|**프로세스 준비**     |  플레이 **북**: Microsoft 센티널로 [조사 및 사냥 프로세스](investigate-cases.md) 를 업데이트 합니다.<br><br>**메트릭**: Microsoft 센티널에서 모든 주요 메트릭을 가져올 수 있는지 확인 합니다.<br><br>**통합 문서**: [데이터 원본에 연결](connect-data-sources.md)하는 즉시 인사이트를 얻을 수 있도록 [사용자 지정 통합 문서](monitor-your-data.md)를 만들거나 기본 제공 통합 문서 템플릿을 사용합니다.<br><br>**인시던트**: 필요한 원본 데이터를 포함한 현재 모든 인시던트를 새 시스템으로 전송해야 합니다.        |
|**인적 준비**     |  **SOC 분석가**: 팀의 모든 사용자가 Microsoft 센티널에 대해 학습 하 고 기존 siem에서 편안 하 게 유지 하도록 합니다.   |
|     |         |
## <a name="next-steps"></a>다음 단계

마이그레이션 후 microsoft의 Microsoft 센티널 리소스를 탐색 하 여 기술을 확장 하 고 Microsoft 센티널을 최대한 활용 하세요.

또한 [통합 위협 방지](https://www.microsoft.com/security/business/threat-protection)를 위해 [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) 및 [microsoft Defender for Cloud](../security-center/azure-defender.md) 를 함께 사용 하 여 위협 방지를 강화 하는 것이 좋습니다. Microsoft 센티널이 제공 하는 표시 범위를 활용 하는 동시에 자세한 위협 분석에 대해 자세히 알아보겠습니다.

자세한 내용은 다음을 참조하세요.

- [규칙 마이그레이션 모범 사례](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [웨비나: 탐지 규칙 변환 모범 사례](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Microsoft 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도)](automation.md)
- [인시던트 메트릭을 사용하여 SOC를 효율적으로 관리](manage-soc-with-incident-metrics.md)
- [Microsoft 센티널 학습 경로](/learn/paths/security-ops-sentinel/)
- [SC-200 Microsoft 보안 작업 분석가 인증](/learn/certifications/exams/sc-200)
- [Microsoft 센티널 Ninja 교육](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [Microsoft 센티널을 사용 하 여 하이브리드 환경에서 공격 조사](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)
