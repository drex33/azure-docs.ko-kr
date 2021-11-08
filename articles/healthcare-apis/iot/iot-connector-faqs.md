---
title: IoT 커넥터에 대 한 Faq-Azure 의료 Api
description: 이 문서에서는 IoT 커넥터에 대해 자주 묻는 질문에 대 한 답변을 제공 합니다.
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 153a4c86d0f6f1b2617327360569cd92d1a318ca
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990247"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>IoT 커넥터에 대 한 질문과 대답

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

IoT 커넥터에 대 한 몇 가지 질문과 대답은 다음과 같습니다.

## <a name="iot-connector-the-basics"></a>IoT 커넥터: 기본 사항

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>FHIR IoT connector 용 Azure API (미리 보기)와 Azure 의료 Api IoT 커넥터 간의 차이점은 무엇 인가요?

Azure 의료 Api IoT 커넥터는 FHIR&#174; (신속한 의료) IoT 커넥터 (미리 보기)를 위한 Azure API의 후속 작업입니다. 

고객 호스팅 장치 메시지 수집 끝점 (예: Azure 이벤트 허브), 관리 Id 사용 및 azure RBAC (Azure Role-Based Access Control)를 비롯 한 몇 가지 향상 된 기능이 도입 되었습니다.

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>Azure 의료 Api FHIR 서비스 외에 다른 FHIR 서비스에서 IoT 커넥터를 사용할 수 있나요?

아니요. Azure 의료 Api IoT 커넥터는 현재 데이터의 지 속성을 위한 Azure 의료 Api FHIR 서비스만 지원 합니다. IoT 커넥터의 오픈 소스 버전은 다양 한 FHIR 서비스의 사용을 지원 합니다. 자세한 내용은 [오픈 소스 프로젝트](iot-git-projects.md) 섹션을 참조 하세요.  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>IoT 커넥터에서 지 원하는 FHIR 버전은 무엇 인가요?

IoT 커넥터는 현재 [HL7 FHIR&#174;](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)4/지 속성을 지원 합니다. 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>IoT 커넥터에 대 한 구독 할당량 제한은 무엇 인가요?

* 구독 당 25 개 IoT 커넥터 (조정 불가능)
* 작업 영역 당 10 개의 IoT 커넥터 (조정 불가능)
* IoT 커넥터 당 단일 FHIR 대상 * (조정 불가능)

(*-FHIR 대상은 IoT 커넥터의 자식 리소스)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>Apple&#174;, Google&#174; 또는 Fitbit&#174; 장치의 장치 메시지에 IoT 커넥터를 사용할 수 있나요?

예. IoT 커넥터는 이러한 모든 플랫폼의 장치 메시지를 지원 합니다. 자세한 내용은 [오픈 소스 프로젝트](iot-git-projects.md) 섹션을 참조 하세요.  

## <a name="more-frequently-asked-questions"></a>기타 질문과 대답
[Azure 의료 Api에 대 한 Faq](../healthcare-apis-faqs.md)

[Azure 의료 Api FHIR 서비스에 대 한 Faq](../fhir/fhir-faq.md)

[Azure 의료 Api DICOM service에 대 한 Faq](../dicom/dicom-services-faqs.yml)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
