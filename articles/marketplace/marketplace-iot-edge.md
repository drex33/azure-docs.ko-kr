---
title: Azure Marketplace IoT Edge 모듈 제품
description: Azure Marketplace에서 IoT Edge 모듈 제품 게시에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/30/2021
ms.openlocfilehash: 8773957a2b34851babb42c6804fc21d6a9d5112b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657465"
---
# <a name="plan-an-iot-edge-modules-offer"></a>IoT Edge 모듈 제품 계획

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 플랫폼은 Microsoft Azure에서 지원됩니다.  사용자는 IoT 디바이스에서 직접 실행할 클라우드 워크로드를 배포할 수 있습니다.  IoT Edge 모듈은 오프라인 워크로드를 실행할 수 있으며 로컬에서 데이터 분석을 수행합니다. 이러한 유형의 플랫폼을 사용하면 대역폭을 절약하고 로컬 데이터와 중요한 데이터를 보호할 수 있으며 응답 시의 대기 시간을 줄일 수 있습니다.  이제는 미리 작성된 워크로드를 활용하는 옵션이 제공됩니다. 지금까지는 소수의 Microsoft 솔루션만 제공되었습니다.  그러므로 시간과 리소스를 투자하여 사용자 지정 IoT 솔루션을 직접 빌드해야 했습니다.

[Azure Marketplace의 IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)을 통해 이제 게시자는 하나의 플랫폼에서 IoT 고객을 대상으로 솔루션을 알리고 판매할 수 있습니다. 그리고 IoT 개발자는 솔루션을 신속하게 개발하는 데 사용할 수 있는 기능을 찾아서 구매할 수 있습니다.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure Marketplace의 IoT Edge 모듈이 제공하는 주요 이점

| **게시자**    | **고객(IoT 개발자)**  |
| :------------------- | :-------------------|
| IoT Edge 솔루션을 빌드 및 배포하고자 하는 수백만 명의 개발자에게 솔루션을 제공할 수 있습니다.  | 테스트를 통해 검증된 안전한 구성 요소를 사용하는 믿을 수 있는 IoT Edge 솔루션을 작성할 수 있습니다. |
| 솔루션을 한 번만 게시하면 컨테이너를 지원하는 모든 IoT Edge 하드웨어에서 실행할 수 있습니다. | 특정 요구 사항에 맞게 자사 및 타사 IoT Edge 모듈을 찾고 배포하여 출시 시간을 줄일 수 있습니다. |
| 유동적인 청구 옵션을 활용하여 수익을 창출할 수 있습니다. <ul><li>무료 및 BYOL(Bring Your Own License) 중에서 선택할 수 있습니다.</li></ul> | 원하는 청구 모델을 선택하여 솔루션을 구매할 수 있습니다.<ul><li>무료 및 BYOL(Bring Your Own License) 중에서 선택할 수 있습니다.</li></ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge 모듈 소개

Azure IoT Edge를 사용하면 에지에서 비즈니스 논리를 모듈 형태로 배포하고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 최소 계산 단위이며 Microsoft 서비스(예: Azure Stream Analytics), 타사 서비스 또는 고유한 솔루션별 코드를 포함할 수 있습니다. IoT Edge 모듈에 대한 자세한 내용은 [Azure IoT Edge 모듈 이해](../iot-edge/iot-edge-modules.md)를 참조하세요.

### <a name="what-is-the-difference-between-a-container-offer-type-and-an-iot-edge-module-offer-type"></a>컨테이너 제품 유형과 IoT Edge 모듈 제품 유형 간의 차이점

IoT Edge 모듈 제품 유형은 IoT Edge 디바이스에서 실행되는 특정 유형의 컨테이너로, IoT Edge 컨텍스트에서 실행하기 위한 기본 구성 설정이 적용된 상태로 제공되며 필요에 따라 IoT Edge 런타임과의 통합을 위해 IoT Edge 모듈 SDK를 사용합니다.

## <a name="select-the-right-online-store"></a>올바른 온라인 저장소 선택

IoT Edge 모듈은 Azure Marketplace에만 게시되며 AppSource는 적용되지 않습니다. 온라인 상점 간의 차이점에 대한 자세한 내용은 [게시 옵션 결정](determine-your-listing-type.md)을 참조하세요.

## <a name="technical-requirements"></a>기술적인 요구 사항

Azure Marketplace IoT Edge 모듈을 인증하고 게시하기 위한 기술 요구 사항은 [IoT Edge 모듈 기술 자산 준비에 자세히 설명되어 있습니다.](iot-edge-technical-asset.md)

## <a name="eligibility-prerequisites"></a>자격 필수 조건

IoT Edge 모듈 제품에는 Microsoft Azure Marketplace 계약 및 정책의 모든 약관이 적용됩니다.  또한 IoT Edge 모듈 관련 필수 조건과 기술 요구 사항도 적용됩니다.  

IoT Edge 모듈을 Azure Marketplace에 게시하려면 다음 필수 구성 요소를 갖춰야 합니다.

- 파트너 센터에 액세스합니다. 자세한 내용은 [파트너 센터에서 상업용 Marketplace 계정 만들기](create-account.md)를 참조하세요.
- Azure Container Registry에서 IoT Edge 모듈 호스팅
- 다음과 같은 IoT Edge 모듈 메타데이터 준비(아래 목록에는 일부 메타데이터만 나와 있음)
    - 제목
    - 설명(HTML 형식)
    - 로고 이미지(48 x 48(선택 사항), 90 x 90(선택 사항) 및 216 x 216 ~ 350 x 350픽셀 크기, 모두 PNG 형식)
    - 사용 약관 및 개인정보처리방침
    - 기본 모듈 구성(경로, 원하는 속성 쌍, createOptions, 환경 변수)
    - 문서화
    - 지원 연락처

## <a name="licensing-options"></a>라이선싱 옵션

Azure Container 제품에 사용할 수 있는 라이선싱 옵션은 다음과 같습니다.

| 라이선싱 옵션 | 트랜잭션 프로세스 |
| --- | --- |
| 무료 | 고객에게 평가판으로 제공하는 제품을 나열합니다. |
| BYOL | 사용자 라이선스 필요 옵션을 사용하면 고객이 기존 소프트웨어 라이선스를 Azure로 가져올 수 있습니다.\* |
|

\* 게시자는 주문, 처리, 계량, 청구, 송장, 결제, 수금을 포함하지만 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원합니다.

## <a name="publishing-options"></a>게시 옵션

모든 경우에 IoT Edge 모듈은 **거래** 게시 옵션을 선택해야 합니다.  게시 옵션에 대한 자세한 내용은 [게시 옵션 선택](determine-your-listing-type.md)을 참조하세요.  

## <a name="customer-leads"></a>잠재 고객

파트너 센터를 통해 제품을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 시험 사용을 하도록 설정하려면 CRM에 연결해야 합니다. 그러지 않으면 CRM에 연결하는 것은 선택 사항입니다. 파트너 센터는 Azure 테이블, Dynamics 365 Customer Engagement, HTTPS 엔드포인트, Marketo 및 Salesforce를 지원합니다.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 Marketplace에서 제품에 사용할 수 있는 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번 읽고 동의만 하면 되고 사용자 지정 사용 약관을 만들 필요는 없습니다.

표준 계약 대신 사용자 고유의 계약조건을 제공하도록 선택할 수 있습니다. 고객은 이 약관에 동의해야 제품을 사용할 수 있습니다.

## <a name="offer-listing-details"></a>제품 목록 세부 정보

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 명시된 경우를 제외하고 모두 필요합니다.

- **이름** - 이 이름은 상업용 Marketplace에서 제품 목록의 제목으로 표시됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약** - 줄 바꿈 없이 100자 이하의 한 문장으로 제품의 목적 또는 기능을 설명합니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **간단한 설명** - 줄 바꿈이 없는 일반 텍스트로 작성된 제품의 용도 또는 기능에 대한 세부 정보입니다. 이는 제품의 세부 정보 페이지에 표시됩니다.
- **설명** - 이 설명은 상업용 Marketplace 목록 개요에 표시됩니다. 가치 제안, 주요 이점, 대상 사용자 베이스, 범주 또는 산업 협회, 앱 내 구매 기회, 필요한 정보를 공개하고 자세한 정보를 볼 수 있는 링크를 포함하는 것이 좋습니다. 이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. 필요에 따라 서식을 지정하기 위해 HTML 태그를 사용합니다.
- **개인정보처리방침 링크** - 회사의 개인정보처리방침에 대한 URL입니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인할 책임은 본인에게 있습니다.
- **유용한 링크**(선택 사항): 제품의 사용자를 위한 다양한 리소스에 대한 링크입니다. 예를 들어 포럼, FAQ, 릴리스 정보 등이 있습니다.
- **연락처 정보**
  - **고객 지원팀 연락처** - 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 이메일입니다. 고객 지원팀 웹 사이트의 URL도 포함해야 합니다.
  - **엔지니어링 연락처** - 제품에 문제가 있을 때 Microsoft에서 직접 사용할 수 있는 이름, 전화 번호 및 이메일입니다. 이 연락처 정보는 상업용 Marketplace에 표시되지 않습니다.
  - **CSP 프로그램 연락처**(선택 사항): CSP(클라우드 솔루션 공급자) 프로그램을 옵트인하는 경우 이름, 전화번호, 메일입니다. 해당 파트너가 질문이 있을 때 이를 통해 연락할 수 있습니다. 마케팅 자료의 URL을 포함할 수도 있습니다.
- **미디어**
    - **로고** - **큰** 로고에 대한 PNG 파일입니다. 파트너 센터는 이를 사용하여 기타 필요한 로고 크기를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.
    - **스크린샷** - 제품의 작동 방식을 보여 주는 1~5개의 스크린샷입니다. 이미지는 PNG 형식에 1280 x 720 픽셀이며 캡션을 포함해야 합니다.
    - **동영상**(선택 사항) - 제품을 설명하는 동영상을 4개까지 추가할 수 있습니다. 이름, YouTube 또는 Vimeo URL 및 1280 x 720 픽셀 PNG 썸네일을 포함합니다.

> [!Note]
> 제품은 상업용 Marketplace에 게시되는 일반 [상업용 Marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general) 충족해야 합니다.

## <a name="next-steps"></a>다음 단계

- 파트너 센터에서 [IoT Edge 모듈 제품을 만듭니다](./iot-edge-offer-setup.md).
