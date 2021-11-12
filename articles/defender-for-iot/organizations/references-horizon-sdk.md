---
title: Horizon SDK
description: Horizon SDK를 사용하면 Microsoft Defender for IoT 개발자가 자동화된 Defender for IoT 네트워크 분석 프로그램에서 처리할 수 있도록 네트워크 트래픽을 디코딩하는 배포자 플러그 인을 디자인할 수 있습니다.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 9affe81bc905e2852732b3892aaac26003c1e8f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286757"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Horizon 독점 프로토콜 디섹터

Horizon은 독점 프로토콜을 실행하는 IoT 및 ICS 디바이스를 보호하는 데 사용되는 ODE(Open Development Environment)입니다.

이 환경은 고객과 기술 파트너에게 다음과 같은 솔루션을 제공합니다.

- 공용, 독점, 사용자 지정 프로토콜 또는 모든 표준에서 벗어난 프로토콜에 대한 무제한적이고 완벽한 지원 

- 새로운 차원의 DPI 개발 유연성 및 범위

- Defender for IoT 플랫폼 버전으로 업그레이드할 필요 없이 OT 표시 여부 및 제어 능력을 대폭 확장하는 도구

- 중요한 정보 유출 없이 독점 개발할 수 있도록 지원하는 보안 기능

Horizon SDK를 사용하면 Microsoft Defender for IoT 개발자가 자동화된 Defender for IoT 네트워크 분석 프로그램에서 처리할 수 있도록 네트워크 트래픽을 디코딩하는 배포자 플러그 인을 디자인할 수 있습니다.

프로토콜 디섹터는 외부 플러그 인으로 개발되며 포괄적인 Defender for IoT 서비스 범위와 통합됩니다. 예를 들면 모니터링, 경고, 보고 기능을 제공하는 서비스입니다.

## <a name="secure-development-environment"></a>개발 환경 보안 

Horizon ODE를 사용하면 조직 외부에서 공유할 수 없는 사용자 지정 또는 독점 프로토콜을 개발할 수 있습니다. 예를 들면 법률 규정 또는 회사 정책 때문일 수 있습니다.

다음을 수행하지 않고 디섹터 플러그 인을 개발합니다. 

- 프로토콜이 정의되는 방식에 대한 비공개 정보 노출.

- 중요한 PCAP 공유.

- 규정 준수 규정 위반.

프로토콜 플러그 인을 개발하는 방법에 대한 내용은 <ms-horizon-support@microsoft.com>에 문의하세요.
## <a name="customization-and-localization"></a>사용자 지정 및 지역화  

SDK는 다음과 같은 다양한 사용자 지정 옵션을 지원합니다.

  - 함수 코드에 대한 텍스트. 

  - 경고, 이벤트, 프로토콜 매개 변수에 대한 전체 지역화 텍스트. 자세한 내용은 [매핑 파일(JSON) 만들기](#create-mapping-files-json)를 참조하세요.

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="완전히 지역화된 경고 보기.":::

## <a name="horizon-architecture"></a>Horizon 아키텍처

아키텍처 모델에는 세 가지 제품 계층이 포함되어 있습니다.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender for IoT 플랫폼 계층

Defender for IoT 플랫폼 버전을 업그레이드할 필요 없이 Defender for IoT 플랫폼에서 사용자 지정 디섹터 플러그 인을 즉시 통합하고 실시간으로 모니터링할 수 있습니다.

## <a name="defender-for-iot-services-layer"></a>Defender for IoT 서비스 계층

각 서비스는 특정 프로토콜에서 분리된 파이프라인으로 설계되어 더 효율적이고 독립적인 개발을 가능하게 합니다.

각 서비스는 특정 프로토콜에서 분리된 파이프라인으로 설계되었습니다. 서비스는 파이프라인에서 트래픽을 수신 대기합니다. 서비스는 플러그 인 데이터 및 센서에서 캡처한 트래픽과 상호 작용하여 배포된 프로토콜을 인덱싱하고 트래픽 페이로드를 분석하고 더 효율적이고 독립적인 개발을 가능하게 합니다.

## <a name="custom-dissector-layer"></a>사용자 지정 디섹터 계층 

Defender for IoT 독점 SDK(C++ 구현 및 JSON 구성 포함)를 사용하여 다음을 수행하는 플러그 인을 만들 수 있습니다. 

- 프로토콜을 식별하는 방법 정의

- 트래픽에서 추출하려는 필드를 매핑하고 필드를 추출하는 방법 정의 

- Defender for IoT 서비스와 통합하는 방법 정의

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="기본 제공 계층.":::

Defender for IoT는 일반 프로토콜에 대한 기본 디섹터를 제공합니다. 프로토콜 맨 위에 디섹터를 빌드할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

## <a name="what-this-sdk-contains"></a>이 SDK에 포함된 내용 

이 키트에는 개발에 필요한 헤더 파일이 들어 있습니다. 개발 프로세스에는 이 SDK에서 설명하는 기본 단계와 선택적 고급 단계가 필요합니다.

헤더 파일 및 기타 리소스를 받는 방법에 대한 정보는 <ms-horizon-support@microsoft.com>에 문의하세요.

## <a name="about-the-environment-and-setup"></a>환경 및 설치 정보 

### <a name="requirements"></a>요구 사항

- 선호하는 개발 환경은 Linux입니다. Windows 환경에서 개발하는 경우 Linux 시스템에서 VM을 사용하는 것이 좋습니다.

- 컴파일 프로세스에서는 GCC 7.4.0 이상을 사용합니다. C++17에서 지원되는 stdlib의 모든 표준 클래스를 사용합니다.

- Defender for IoT 버전 3.0 이상.

### <a name="process"></a>Process

1. Eclipse IDE for C/C++ Developers [다운로드](https://www.eclipse.org/)합니다. 원하는 다른 IDE를 사용해도 됩니다. 이 문서에서는 Eclipse IDE를 사용한 구성을 안내합니다.

1. Eclipse IDE를 시작하고 프로젝트를 저장할 작업 영역을 구성한 후 **Ctrl+n** 을 눌러 C++ 프로젝트로 만듭니다.

1. 다음 화면에서 개발하려는 프로토콜로 이름을 설정하고 프로젝트 형식을 `Shared Library` 및 `AND Linux GCC`로 선택합니다.

1. **C/C++ 빌드** > **설정** > **도구 설정** > **GCC C++ 컴파일러** > **기타** > **눈금 위치 독립적 코드** 에서 프로젝트 속성을 편집합니다.

1. SDK를 사용하여 받은 예제 코드를 붙여넣고 컴파일합니다.

1. tar.gz 파일에 아티팩트(라이브러리, config.json 및 메타데이터)를 추가하고 파일 확장명을 \<XXX>.hdp로 변경합니다. 여기서 \<XXX>는 플러그 인의 이름입니다.

### <a name="research"></a>연구 

시작하기 전에 다음을 수행했는지 확인합니다.

- 가능한 경우 프로토콜 사양을 읽습니다.

- 추출하려는 프로토콜 필드를 파악합니다.

- 매핑 목표를 계획했습니다.

## <a name="about-plugin-files"></a>플러그 인 파일 정보 

개발 프로세스 중에 세 개의 파일이 정의됩니다.

### <a name="json-configuration-file-required"></a>JSON 구성 파일(필수) 

이 파일에서 디섹터 ID와 선언, 종속성, 통합 요구 사항, 유효성 검사 매개 변수, 매핑 정의를 정의하여 값을 이름으로, 숫자를 텍스트로 변환해야 합니다. 자세한 내용은 다음 링크를 참조하세요.

- [구성 파일(JSON) 준비](#prepare-the-configuration-file-json)

- [구현 코드 유효성 검사 준비](#prepare-implementation-code-validations)

- [디바이스 메타데이터 추출](#extract-device-metadata)

- [인덱싱 서비스에 연결(기준)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>구현 코드: C++(필수)

구현 코드(CPP)는 원시 트래픽을 구문 분석하여 서비스, 클래스, 함수 코드와 같은 값에 매핑합니다. 계층 필드를 추출하고 JSON 구성 파일의 인덱스 이름에 매핑합니다. CPP에서 추출할 필드가 구성 파일에 정의되어 있습니다. 자세한 내용은 [구현 코드(C++) 준비](#prepare-the-implementation-code-c)를 참조하세요.

### <a name="mapping-files-optional"></a>매핑 파일(옵션)

엔터프라이즈 환경의 요구 사항을 충족하도록 플러그 인 출력 텍스트를 사용자 지정할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="마이그레이션":::

코드를 변경하지 않고 텍스트를 업데이트하는 매핑 파일을 정의하고 업데이트할 수 있습니다. 각 파일은 하나 이상의 필드를 매핑할 수 있습니다.

  - 필드 값을 이름에 매핑(예: 1:Reset, 2:Start, 3:Stop).

  - 여러 언어를 지원하도록 텍스트 매핑.

자세한 내용은 [매핑 파일(JSON) 만들기](#create-mapping-files-json)를 참조하세요.

## <a name="create-a-dissector-plugin-overview"></a>디섹터 플러그 인 만들기(개요)

1. [환경 및 설정 정보](#about-the-environment-and-setup) 섹션을 검토합니다.

2.  [구현 코드(C++)를 준비](#prepare-the-implementation-code-c)합니다. **template.json** 파일을 복사하고 요구 사항에 맞게 편집합니다. 키를 변경하지 마세요. 

3. [구성 파일(JSON)을 준비](#prepare-the-configuration-file-json)합니다. **template.cpp** 파일을 복사하고 재정의 메서드를 구현합니다. 자세한 내용은 [horizon::protocol::BaseParser](#horizonprotocolbaseparser)를 참조하세요.

4. [구현 코드 유효성 검사를 준비](#prepare-implementation-code-validations)합니다.

## <a name="prepare-the-implementation-code-c"></a>구현 코드(C++) 준비

CPP 파일은 다음을 수행하는 파서입니다.

- 패킷 헤더 및 페이로드의 유효성 검사(예: 헤더 길이 또는 페이로드 구조).

- 헤더 및 페이로드에서 정의된 필드로 데이터 추출.

- JSON 파일에서 구성된 필드 추출 구현.

### <a name="what-to-do"></a>알아두어야 할 사항

**template.cpp** 템플릿 파일을 복사하고 재정의 메서드를 구현합니다. 자세한 내용은 [horizon::protocol::BaseParser](#horizonprotocolbaseparser)를 참조하세요.

### <a name="basic-c-template-sample"></a>기본 C++ 템플릿 샘플 

이 섹션에서는 샘플 Defender for IoT Horizon 프로토콜에 대한 표준 함수와 함께 기본 프로토콜 템플릿을 제공합니다.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>기본 C++ 템플릿 설명  

이 섹션에서는 샘플 Defender for IoT Horizon 프로토콜에 대한 표준 함수 설명과 함께 기본 프로토콜 템플릿을 제공합니다. 

### <a name="include-pluginpluginh"></a>#include “plugin/plugin.h”

플러그 인에서 사용하는 정의입니다. 헤더 파일에는 개발을 완료하는 데 필요한 모든 항목이 들어 있습니다.

### <a name="horizonprotocolbaseparser"></a>horizon::protocol::BaseParser

Horizon 인프라와 플러그 인 계층 간의 통신 인터페이스입니다. 자세한 내용은 [Horizon 아키텍처](#horizon-architecture)에서 계층에 대한 개요를 참조하세요.

processLayer는 데이터를 처리하는 데 사용되는 메서드입니다.

- 함수 코드의 첫 번째 매개 변수는 이전에 처리된 데이터를 검색하고, 새 필드 및 계층을 만드는 데 사용되는 처리 유틸리티입니다.

- 함수 코드의 두 번째 매개 변수는 이전 파서에서 전달된 현재 데이터입니다.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

파서의 인스턴스를 만들 때 사용합니다.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>프로토콜 함수 코드 샘플 

이 섹션에서는 코드 번호(2바이트)와 메시지 길이(4바이트)를 추출하는 방법의 예를 제공합니다.

이 작업은 JSON 구성 파일에 제공된 endian에 따라 수행됩니다. 즉, 프로토콜이 *little endian* 이고 센서가 little endian을 사용하여 머신에서 실행되는 경우 변환됩니다.

또한 데이터를 저장할 계층이 생성됩니다. 처리 유틸리티에서 *fieldsManager* 를 사용하여 새 필드를 만듭니다. 필드에는 *문자열*, *숫자*, *원시 데이터*, *배열*(특정 형식) 또는 *복합* 형식 중 하나만 사용할 수 있습니다. 이 계층에는 숫자, 원시 또는 문자열 ID가 포함될 수 있습니다.

아래 샘플에서는 다음 두 필드가 추출됩니다.

- `function_code_number`

- `headerLength`

새 계층이 만들어지고 추출된 필드가 여기에 복사됩니다.

아래 샘플에서는 플러그 인 처리에 대해 구현된 기본 논리인 특정 함수에 대해 설명합니다.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>관련 JSON 필드 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="관련 json 필드.":::

## <a name="prepare-the-configuration-file-json"></a>구성 파일(JSON) 준비

Horizon SDK는 데이터를 저장하고 전송하는 간단한 형식인 표준 JSON(JavaScript Object Notation)을 사용하며, 전용 스크립팅 언어가 필요하지 않습니다.

이 섹션에서는 최소 JSON 구성 선언 및 관련된 구조를 설명하고 프로토콜을 정의하는 샘플 구성 파일을 제공합니다. 이 프로토콜은 디바이스 검색 서비스와 자동으로 통합됩니다.

## <a name="file-structure"></a>파일 구조

아래 샘플에서는 파일 구조에 대해 설명합니다.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="파일 구조의 샘플.":::

### <a name="what-to-do"></a>알아두어야 할 사항

템플릿 `config.json` 파일을 복사하고 요구 사항에 맞게 편집합니다. 키를 변경하지 마세요. 키는 [샘플 JSON 구성 파일](#sample-json-configuration-file)에서 빨간색으로 표시됩니다. 

### <a name="file-naming-requirements"></a>파일 명명 요구 사항 

JSON 구성 파일은 `config.json`으로 저장해야 합니다.

### <a name="json-configuration-file-fields"></a>JSON 구성 파일 필드

이 섹션에서는 정의할 JSON 구성 필드에 대해 설명합니다. 필드 *레이블* 은 변경하지 마세요.

### <a name="basic-parameters"></a>기본 매개 변수

이 섹션에서는 기본 매개 변수에 대해 설명합니다.

| 매개 변수 레이블 | Description | Type |
|--|--|--|
| **ID** | 프로토콜의 이름입니다. 기본값을 삭제하고 표시되는 프로토콜의 이름을 추가합니다. | String |
| **endianess** | 멀티바이트 데이터를 인코딩하는 방법을 정의합니다. "little" 또는 "big" 용어만 사용합니다. 프로토콜 사양 또는 트래픽 기록에서 가져옵니다. | String |
| **sanity_failure_codes** | 코드 ID와 관련하여 온전성 충돌이 있을 때 파서에서 반환되는 코드입니다. C++ 섹션에서 매직 넘버 유효성 검사를 참조하세요. | String |
| **malformed_codes** | 올바르게 식별되었지만 오류가 탐지되는 코드입니다. 예를 들어 필드 길이가 너무 짧거나 너무 길거나, 값이 잘못된 경우입니다. | String |
| **dissect_as** | 특정 프로토콜 트래픽이 도착해야 하는 위치를 정의하는 배열입니다. | TCP/UDP, 포트 등 |
| **필드** | 트래픽에서 추출되는 필드의 선언입니다. 각 필드에는 고유한 ID(이름)와 형식(숫자, 문자열, 원시, 배열, 복합)이 있습니다. 예를 들면 구현 파서 파일에서 추출된 필드 [함수](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k)가 있습니다. 구성 파일에 작성된 필드는 계층에 추가할 수 있는 유일한 필드입니다. |  |

### <a name="other-advanced-fields"></a>기타 고급 필드 

이 섹션에서는 기타 필드에 대해 설명합니다.

| 매개 변수 레이블 | Description |
|-----------------|--------|
| **allow_lists** | 프로토콜 값을 인덱싱하고 데이터 마이닝 보고서에 표시할 수 있습니다. 데이터 마이닝 보고서는 네트워크 기준을 반영합니다. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="데이터 마이닝 보기의 샘플."::: <br /> 자세한 내용은 [인덱싱 서비스에 연결(기준)](#connect-to-an-indexing-service-baseline)을 참조하세요. |
| **firmware** | 펌웨어 정보를 추출하고, 인덱스 값을 정의하고, 플러그 인 프로토콜에 대한 펌웨어 경고를 트리거할 수 있습니다. 자세한 내용은 [펌웨어 데이터 추출](#extract-firmware-data)을 참조하세요. |
| **value_mapping** | 매핑 파일을 정의하고 업데이트하여 엔터프라이즈 환경의 요구 사항에 맞게 플러그 인 출력 텍스트를 사용자 지정할 수 있습니다. 예를 들어 언어 파일에 매핑할 수 있습니다. 코드를 변경하거나 영향을 주지 않고 텍스트에 변경 내용을 쉽게 구현할 수 있습니다. 자세한 내용은 [매핑 파일(JSON) 만들기](#create-mapping-files-json)를 참조하세요. |

## <a name="sample-json-configuration-file"></a>샘플 JSON 구성 파일

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>구현 코드 유효성 검사 준비

이 섹션에서는 C++ 코드 유효성 검사 함수 구현에 대해 설명하고 샘플 코드를 제공합니다. 다음 두 계층의 유효성 검사를 사용할 수 있습니다.

- 온전성.

- 잘못된 형식의 코드.

작동하는 플러그 인을 빌드하기 위해 유효성 검사 코드를 만들 필요가 없습니다. 유효성 검사 코드를 준비하지 않은 경우 성공적으로 처리되었음을 나타내는 센서 데이터 마이닝 보고서를 검토할 수 있습니다.

필드 값은 매핑 파일의 텍스트에 매핑되며 처리에 영향을 주지 않고 원활하게 업데이트할 수 있습니다.

## <a name="sanity-code-validations"></a>온전성 코드 유효성 검사 

전송된 패킷이 프로토콜의 유효성 검사 매개 변수와 일치하는지 확인하는 것으로, 트래픽 내에서 프로토콜을 식별하는 데 도움이 됩니다.

예를 들어 처음 8바이트를 *매직 넘버* 로 사용합니다. 온전성에 오류가 발생하는 경우 온전성 오류 응답이 반환됩니다.

예를 들면 다음과 같습니다.

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

기타 관련 플러그 인을 배포한 경우 해당 플러그 인에 대해 패킷의 유효성이 검사됩니다.

## <a name="malformed-code-validations"></a>잘못된 형식의 코드 유효성 검사 

프로토콜이 유효성 검사를 통해 문제가 없다고 판명된 이후에는 잘못된 형식에 대한 유효성 검사가 사용됩니다.

프로토콜을 기반으로 한 패킷을 처리하는 데 실패하면 실패 응답이 반환됩니다.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="잘못된 형식의 코드":::

## <a name="c-sample-with-validations"></a>유효성 검사를 사용한 C++ 샘플

함수에 따라 프로세스는 아래 예제와 같이 수행됩니다.

### <a name="function-20"></a>함수 20 

- 펌웨어로 처리됩니다.

- 함수에 따라 필드를 읽습니다.

- 필드가 계층에 추가됩니다.

### <a name="function-10"></a>함수 10 

- 함수에 보다 구체적인 작업인 다른 하위 함수가 포함되어 있습니다.

- 하위 함수를 읽고 계층에 추가합니다.

이 작업을 완료하면 처리가 완료됩니다. 반환 값은 디섹터 계층이 성공적으로 처리되었는지를 나타냅니다. 성공적으로 처리된 경우 계층을 사용할 수 있게 됩니다.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>디바이스 메타데이터 추출

자산에 대한 다음 메타데이터를 추출할 수 있습니다.

  - `Is_distributed_control_system` - 프로토콜이 분산 제어 시스템에 포함되어 있는지를 나타냅니다. 예를 들면 SCADA 프로토콜은 false여야 합니다.

  - `Has_protocol_address` – 프로토콜 주소, 즉 현재 프로토콜에 대한 특정 주소(예: MODBUS 단위 식별자)가 있는지를 나타냅니다.

  - `Is_scada_protocol` - 프로토콜이 OT 네트워크와 관련이 있는지를 나타냅니다.

  - `Is_router_potential` - 프로토콜을 주로 라우터에서 사용하는지를 나타냅니다. 예를 들면 LLDP, CDP 또는 STP가 있습니다.

이 작업을 수행하려면 메타데이터 속성을 사용하여 JSON 구성 파일을 업데이트해야 합니다.

## <a name="json-sample-with-metadata"></a>메타데이터를 사용한 JSON 샘플 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>프로그래밍 코드 추출 

프로그래밍 이벤트가 발생하는 경우 코드 콘텐츠를 추출할 수 있습니다. 추출된 콘텐츠를 통해 다음을 수행할 수 있습니다.

- 다른 프로그래밍 이벤트의 코드 파일 콘텐츠를 비교합니다.

- 무단 프로그래밍에 대한 경고를 트리거합니다.  

- 프로그래밍 코드 파일에 대한 이벤트를 트리거합니다.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="프로그래밍 변경 로그.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="단추를 클릭하여 프로그래밍 보기.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="무단 PLC 프로그래밍 경고.":::

이 작업을 수행하려면 `code_extraction` 속성을 사용하여 JSON 구성 파일을 업데이트해야 합니다. 

### <a name="json-configuration-fields"></a>JSON 구성 필드 

이 섹션에서는 JSON 구성 필드에 대해 설명합니다. 

- **method**

  프로그래밍 이벤트 파일을 수신하는 방법을 나타냅니다. 

  모두(각 프로그래밍 작업으로 인해 변경되지 않은 파일이 있는 경우에도 모든 코드 파일이 수신됩니다.)

- **file_type**  

  코드 콘텐츠 형식을 나타냅니다.  

  텍스트(각 코드 파일에는 텍스트 정보가 포함되어 있습니다.)

- **code_data_field**
  
  코드 콘텐츠를 제공하기 위해 사용할 구현 필드를 나타냅니다.

  필드.

- **code_name_field**

  코딩 파일의 이름을 제공하기 위해 사용할 구현 필드를 나타냅니다.

  필드.

- **size_limit**

  각 코딩 파일 콘텐츠의 크기 제한(바이트)을 나타냅니다. 코드 파일이 설정된 제한을 초과하는 경우 삭제됩니다. 이 필드를 지정하지 않으면 기본값은 15MB(1,500만)가 됩니다.

  숫자.

- **metadata**

  코드 파일에 대한 추가 정보를 나타냅니다.

  다음 두 개의 속성이 있는 개체를 포함하는 배열.

    - 이름(문자열) - 메타데이터 키 XSense가 현재 사용자 이름 키만 지원함을 나타냅니다.
 
    - 값(필드) - 메타데이터 데이터를 제공하기 위해 사용할 구현 필드를 나타냅니다.

## <a name="json-sample-with-programming-code"></a>프로그래밍 코드를 사용한 JSON 샘플

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Horizon 경고 사용자 지정

일부 프로토콜 함수 코드가 오류를 나타낼 수 있습니다. 예를 들어 프로토콜이 항상 특정 온도에 보관해야 하는 특정 화학약품이 들어 있는 컨테이너를 제어하는 경우입니다. 이 경우 온도계의 오류를 나타내는 함수 코드가 있을 수 있습니다. 예를 들어 함수 코드가 25이면 컨테이너에 문제가 있음을 나타내는 경고를 웹 콘솔에서 트리거할 수 있습니다. 이러한 경우 심층 패킷 경고를 정의할 수 있습니다.

플러그 인의 `config.json`에 **경고** 매개 변수를 추가합니다.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON 구성 필드

이 섹션에서는 JSON 구성 필드에 대해 설명합니다. 

| 필드 이름 | Description | 가능한 값 |
|--|--|--|
| **ID** | 단일 경고 ID를 나타냅니다. 이 컨텍스트에서 고유해야 합니다. | 숫자 값 0~10,000 |
| **message** | 사용자에게 표시되는 정보입니다. 이 필드를 사용하여 다른 필드를 사용할 수 있습니다. | 프로토콜의 모든 필드 또는 더 낮은 계층 프로토콜을 사용합니다. |
| **title** | 경고 제목 |  |
| **expression** | 이 경고를 표시하려는 경우. | 하위 계층이나 현재 계층에 있는 숫자 필드를 사용합니다.</br></br> SDK에서 이 숫자 필드를 필드로 감지하려면 각 필드가 `{}`를 사용한 래퍼여야 합니다. 지원되는 논리 연산자는 다음과 같습니다.</br> == - 같음</br> <= - 작거나 같음</br> >= - 크거나 같음</br> > - 보다 큼</br> < - 보다 작음</br> ~= - 같지 않음 |

## <a name="more-about-expressions"></a>식에 대한 정보

Horizon SDK가 식을 호출하고 해당 식이 *true* 가 될 때마다 센서에서 경고가 트리거됩니다.

동일한 경고에 여러 식을 포함할 수 있습니다. 예를 들면 다음과 같습니다.

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

이 식은 패킷 ipv4 src가 10.10.10.10인 경우에만 함수 코드의 유효성을 검사합니다. 숫자로 표현한 IP 주소의 원시 표현입니다.

식을 연결하는 데 `and` 또는 `or`를 사용할 수 있습니다.

## <a name="json-sample-custom-horizon-alerts"></a>JSON 샘플 사용자 지정 Horizon 경고

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>인덱싱 서비스에 연결(기준)

프로토콜 값을 인덱싱하고 데이터 마이닝 보고서에 표시할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="데이터 마이닝 옵션의 보기.":::

나중에 프로토콜 값을 특정 텍스트에 매핑할 수 있습니다. 예를 들어 숫자를 텍스트로 매핑하거나 모든 언어로 정보를 추가할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="마이그레이션":::

자세한 내용은 [매핑 파일(JSON) 만들기](#create-mapping-files-json)를 참조하세요.

이전에 구문 분석된 프로토콜의 값을 사용하여 추가 정보를 추출할 수도 있습니다.

예를 들어 TCP를 기반으로 하는 값의 경우 IPv4 계층의 값을 사용할 수 있습니다. 이 계층에서 패킷의 원본 및 대상과 같은 값을 추출할 수 있습니다.

이 작업을 수행하려면 `whitelists` 속성을 사용하여 JSON 구성 파일을 업데이트해야 합니다.

## <a name="allow-list-data-mining-fields"></a>허용 목록(데이터 마이닝) 필드

다음 허용 목록 필드를 사용할 수 있습니다.

- name – 인덱싱에 사용되는 이름입니다.

- alert_title – 이벤트를 설명하는 짧고 고유한 제목입니다.

- alert_text – 추가 정보입니다.

여러 허용 목록을 추가하여 인덱싱을 유연하게 사용할 수 있습니다.

## <a name="json-sample-with-indexing"></a>인덱싱을 사용한 JSON 샘플 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>펌웨어 데이터 추출

펌웨어 정보를 추출하고, 인덱스 값을 정의하고, 플러그 인 프로토콜에 대한 펌웨어 경고를 트리거할 수 있습니다. 예제:

- 펌웨어 모델 또는 버전을 추출합니다. 이 정보를 활용하여 CVE를 식별할 수 있습니다.

- 새 펌웨어 버전이 검색되면 경고를 트리거합니다.

이 작업을 수행하려면 펌웨어 속성을 사용하여 JSON 구성 파일을 업데이트해야 합니다.

## <a name="firmware-fields"></a>펌웨어 필드

이 섹션에서는 JSON 펌웨어 구성 필드에 대해 설명합니다.

- **name**
  
  센서 콘솔에서 필드가 표시되는 방법을 나타냅니다.

- **value**

 데이터를 제공하기 위해 사용할 구현 필드를 나타냅니다. 

- **firmware_index:**

  다음 중 하나를 선택합니다.  
  - **model**: 디바이스 모델입니다. CVE 검색을 사용하도록 설정합니다.
  - **serial**: 디바이스 일련 번호입니다. 모든 프로토콜에 대해 일련 번호를 항상 사용할 수 있는 것은 아닙니다. 이 값은 디바이스마다 고유합니다.
  - **rack**: 디바이스가 랙의 일부인 경우 랙 식별자를 나타냅니다.
  - **slot**: 디바이스가 랙의 일부인 경우 슬롯 식별자입니다.  
  - **module_address**: 모듈을 다른 디바이스 뒤에 표시할 수 있는 경우 계층을 나타내는 데 사용합니다. 더 간단한 프레젠테이션인 랙 슬롯 조합이 있는 경우 대신 적용됩니다.  
  - **firmware_version**: 디바이스 버전을 나타냅니다. CVE 검색을 사용하도록 설정합니다.
  - **alert_text**: 펌웨어 편차를 설명하는 텍스트를 나타냅니다(예: 버전 변경).  
  - **index_by**: 디바이스를 식별하고 인덱싱하는 데 사용되는 필드를 나타냅니다. 아래 예제에서 디바이스는 IP 주소로 식별됩니다. 특정 프로토콜에서는 더 복잡한 인덱스를 사용할 수 있습니다. 예를 들어 다른 디바이스가 연결 어 있는 경우 내부 경로를 추출하는 방법을 알고 있습니다. 예를 들어 MODBUS 단위 ID는 IP 주소와 단위 식별자의 다른 조합으로 인덱스의 일부로 사용할 수 있습니다.
  - **firmware_fields**: 필드가 디바이스 메타데이터 필드임을 나타냅니다. 이 예제에서는 모델, 수정 버전, 이름을 사용합니다. 각 프로토콜은 자체 펌웨어 데이터를 정의할 수 있습니다.

## <a name="json-sample-with-firmware"></a>펌웨어를 사용한 JSON 샘플 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>디바이스 특성 추출 

인벤토리, 데이터 마이닝, 기타 보고서의 디바이스에서 사용할 수 있는 디바이스 정보를 향상할 수 있습니다.

- 이름

- 유형

- Vendor

- 운영 체제

이 작업을 수행하려면 **속성** 속성을 사용하여 JSON 구성 파일을 업데이트해야 합니다. 

기본 플러그 인을 작성하고 필수 필드를 추출한 후에 이 작업을 수행할 수 있습니다.

## <a name="properties-fields"></a>속성 필드

이 섹션에서는 JSON 속성 구성 필드에 대해 설명합니다. 

**config_file** 

`key` 필드의 각 키를 처리하는 방법을 정의하는 파일 이름을 포함합니다. 구성 파일 자체는 JSON 형식이어야 하고 플러그 인 프로토콜 폴더의 일부로 포함해야 합니다.

JSON의 각 키는 패킷에서 이 키를 추출할 때 수행해야 하는 작업 세트를 정의합니다.

각 키에는 다음이 포함될 수 있습니다.

- **패킷 데이터** - 패킷(해당 데이터를 제공하는 데 사용되는 구현 필드)에서 추출된 데이터에 기반하여 업데이트되는 속성을 나타냅니다.

- **정적 데이터** - 업데이트해야 하는 미리 정의된 `property-value` 작업 세트를 나타냅니다.

이 파일에서 구성할 수 있는 속성은 다음과 같습니다. 

- **Name** - 디바이스 이름을 나타냅니다.

- **Type** - 디바이스 유형을 나타냅니다.

- **Vendor** - 디바이스 공급업체를 나타냅니다.

- **Description** - 디바이스 펌웨어 모델("모델"보다 낮은 우선 순위)을 나타냅니다.

- **operatingSystem** - 디바이스 운영 체제를 나타냅니다.

### <a name="fields"></a>필드

| 필드 | Description |
|--|--|
| key | 키를 나타냅니다. |
| 값 | 데이터를 제공하기 위해 사용할 구현 필드를 나타냅니다. |
| is_static_key | `key` 필드가 패킷의 값으로 파생되었는지 아니면 미리 정의된 값인지를 나타냅니다. |

### <a name="working-with-static-keys-only"></a>정적 키만 사용

정적 키를 사용하는 경우 `config.file`을 구성할 필요가 없습니다. JSON 파일만 구성할 수 있습니다.

## <a name="json-sample-with-properties"></a>속성을 사용한 JSON 샘플 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>매핑 파일(JSON) 만들기

매핑 파일을 정의하고 업데이트하여 엔터프라이즈 환경의 요구 사항에 맞게 플러그 인 출력 텍스트를 사용자 지정할 수 있습니다. 코드를 변경하거나 영향을 주지 않고 텍스트에 변경 내용을 쉽게 구현할 수 있습니다. 각 파일은 하나 이상의 필드를 매핑할 수 있습니다.

- 필드 값을 이름에 매핑(예: 1:Reset, 2:Start, 3:Stop).

- 여러 언어를 지원하도록 텍스트 매핑.

두 가지 유형의 매핑 파일을 정의할 수 있습니다.

  - [단순 매핑 파일](#simple-mapping-file).

  - [종속성 매핑 파일](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="이더넷":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="처리되지 않은 경고 보기.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="알려진 정책 위반 목록.":::

## <a name="file-naming-and-storage-requirements"></a>파일 이름 지정 및 스토리지 요구 사항 

매핑 파일은 메타데이터 폴더 아래에 저장해야 합니다.

파일 이름은 JSON 구성 파일 ID와 일치해야 합니다.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON 구성 파일의 샘플.":::

## <a name="simple-mapping-file"></a>단순 매핑 파일 

다음 샘플에서는 기본 JSON 파일을 키 값으로 제공합니다.

허용 목록을 만들 때 매핑된 필드가 하나 이상 포함됩니다. 값은 숫자, 문자열 또는 모든 형식에서 매핑 파일에 표시되는 서식 있는 텍스트로 변환됩니다.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>종속성 매핑 파일 

파일이 종속성 파일임을 나타내려면 `dependency` 키워드를 매핑 구성에 추가합니다.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

이 파일에는 종속성 필드와 함수 필드 간의 매핑이 들어 있습니다. 함수와 sub 함수 간의 매핑을 예로 들 수 있습니다. sub 함수는 제공된 함수에 따라 변경됩니다.

이전에 구성된 허용 목록에는 아래와 같은 종속성 구성이 없습니다.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

종속성은 특정 값이나 필드를 기반으로 할 수 있습니다. 아래 예제에서는 필드를 기반으로 합니다. 값을 기반으로 하는 경우 매핑 파일에서 읽을 추출 값을 정의합니다.

아래 예제에서는 필드의 동일한 값에 대한 종속성이 다음과 같이 수행됩니다.

예를 들어 sub 함수 5의 경우 의미는 함수에 따라 변경됩니다.

  - 읽기 함수인 경우 5는 메모리 읽기를 의미합니다.

  - 쓰기 함수인 경우 동일한 값을 사용하여 파일에서 읽습니다.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>샘플 파일

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>매핑을 사용한 JSON 샘플

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>플러그 인 패키지, 업로드, 모니터링 

이 섹션에서는 다음 작업을 수행하는 방법을 설명합니다.

  - 플러그 인을 패키지합니다.

  - 플러그 인을 업로드합니다.

  - 플러그 인을 모니터링하고 디버그하여 성능을 평가합니다.

플러그 인을 패키지하려면 다음을 수행합니다.

1. **아티팩트**(라이브러리, config.json 또는 메타데이터일 수 있음)를 `tar.gz` 파일에 추가합니다.

1. 파일 확장명을 \<XXX.hdp>로 변경합니다. 여기서 \<XXX>는 플러그 인의 이름입니다.

Horizon 콘솔에 로그인하려면 다음을 수행합니다.

1.  센서 CLI를 관리자, CyberX 또는 지원 사용자로 로그인합니다.

2.  `/var/cyberx/properties/horizon.properties` 파일에서 **ui.enabled** 속성을 **true** 로 변경합니다(`horizon.properties:ui.enabled=true`).

3.  센서 콘솔에 로그인합니다.

4.  주 메뉴에서 **Horizon** 옵션을 선택합니다.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="왼쪽 창에서 Horizon 옵션 선택.":::

    Horizon 콘솔이 열립니다.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Horizon 콘솔 및 모든 플러그 인 보기.":::

## <a name="plugins-pane"></a>플러그 인 창

플러그 인 창에는 다음 항목이 표시됩니다.

  - 인프라 플러그 인: Defender for IoT와 함께 기본적으로 설치되는 인프라 플러그 인.

  - 애플리케이션 플러그 인: Defender for IoT와 함께 기본적으로 설치되는 애플리케이션 플러그 인 및 Defender for IoT 또는 외부 개발자가 개발한 기타 플러그 인.
    
설정/해제를 사용하여 업로드된 플러그 인을 사용하거나 사용하지 않도록 설정합니다.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP 설정/해제.":::

### <a name="uploading-a-plugin"></a>플러그 인 업로드

플러그 인을 만들고 패키지한 후에 Defender for IoT 센서에 업로드할 수 있습니다. 네트워크의 전체 범위를 확보하려면 플러그 인을 조직의 각 센서에 업로드해야 합니다.

업로드하려면 다음을 수행합니다.

1.  센서에 로그인합니다.


2. **업로드** 를 선택합니다.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="플러그 인 업로드.":::

3. 플러그 인으로 이동하여 플러그 인 대화 상자로 끕니다. 접두사가 `.hdp`인지 확인합니다. 플러그 인이 로드됩니다.

## <a name="plugin-status-overview"></a>플러그 인 상태 개요 

Horizon 콘솔 **개요** 창에서는 업로드한 플러그 인에 대한 정보를 제공하며, 여기서 플러그 인을 사용하거나 사용하지 않도록 설정할 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Horizon 콘솔의 개요.":::

| 필드 | Description |
|--|--|
| 애플리케이션 | 업로드한 플러그 인의 이름입니다. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="켜기/끄기 스위치입니다."::: | 플러그 인을 **설정** 하거나 **해제** 합니다. Defender for IoT는 플러그 인을 해제하는 경우 플러그 인에 정의된 프로토콜 트래픽을 처리하지 않습니다. |
| 시간 | 데이터를 마지막으로 분석한 시간입니다. 5초마다 업데이트됩니다. |
| PPS | 초당 패킷 수입니다. |
| 대역폭 | 최근 5초 이내에 검색된 평균 대역폭입니다. |
| 잘못된 형식 | 프로토콜이 유효성 검사를 통해 문제가 없다고 판명된 이후에는 잘못된 형식에 대한 유효성 검사가 사용됩니다. 프로토콜을 기반으로 한 패킷을 처리하는 데 실패하면 실패 응답이 반환됩니다.   <br><br>이 열은 지난 5초간 발생한 잘못된 형식 오류의 개수를 나타냅니다. 자세한 내용은 [잘못된 형식의 코드 유효성 검사](#malformed-code-validations)를 참조하세요. |
| 경고 | 플러그인 경고 구성에 따르면, 패킷이 구조 및 사양에 부합하지만, 예기치 않은 동작이 있습니다. |
| 오류 | 기본 프로토콜 유효성 검사에 실패한 패킷 수입니다. 패킷이 프로토콜 정의와 일치하는지 확인합니다. 여기에 표시된 숫자는 지난 5초간 탐지된 오류 수를 나타냅니다. 자세한 내용은 [온전성 코드 유효성 검사](#sanity-code-validations)를 참조하세요. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="모니터 아이콘."::: | 플러그 인에서 탐지된 잘못된 형식 및 경고에 대한 세부 정보를 검토합니다. |

## <a name="plugin-details"></a>플러그 인 정보

플러그 인에서 탐지된 *잘못된 형식* 및 *경고* 개수를 분석하여 실시간 플러그 인 동작을 모니터링할 수 있습니다. 추가 조사를 위해 화면을 고정하고 내보낼 수 있습니다.

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP 모니터 화면.":::

모니터링하려면 다음을 수행합니다.

개요에서 플러그 인의 모니터 단추를 선택합니다.

다음 단계

[Horizon API](references-horizon-api.md) 설정
