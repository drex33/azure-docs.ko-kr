---
title: IoT 커넥터에 대한 FAQ - Azure Healthcare API
description: 이 문서에서는 IoT 커넥터에 대한 질문과 대답을 제공합니다.
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/03/2021
ms.author: jasteppe
ms.openlocfilehash: 00f3153981a07e436b714dadfd2785aac61dd8ab
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578897"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>IoT 커넥터에 대한 질문과 대답

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

다음은 IoT 커넥터에 대한 질문과 대답입니다.

## <a name="iot-connector-the-basics"></a>IoT 커넥터: 기본 사항

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>Azure API for FHIR IoT 커넥터(미리 보기)와 Azure Healthcare API IoT 커넥터 간의 차이점은 무엇인가요?

Azure Healthcare API IoT 커넥터는 Azure API for 전자 의료 기록 교환(FHIR&#174;) IoT 커넥터(미리 보기)의 후속 기능입니다. 

고객 호스팅 디바이스 메시지 검색 엔드포인트(예: Azure Event Hub), 관리 ID 사용 및 Azure RBAC(Azure Role-Based Access Control)를 포함하여 몇 가지 개선이 도입되었습니다.

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>Azure Healthcare API FHIR 서비스 이외의 다른 FHIR 서비스에서 IoT 커넥터를 사용할 수 있나요?

아니요. Azure Healthcare API IoT 커넥터는 현재 데이터 지속성을 위해 Azure Healthcare API FHIR 서비스만 지원합니다. IoT 커넥터의 오픈 소스 버전은 다른 FHIR 서비스의 사용을 지원합니다. 자세한 내용은 오픈 [소스 프로젝트 섹션을](iot-git-projects.md) 참조하세요.  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>IoT 커넥터에서 지원하는 FHIR 버전은 무엇인가요?

IoT 커넥터는 현재 [HL7 FHIR&#174; R4의](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)지속성만 지원합니다. 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>IoT 커넥터에 대한 구독 할당량 한도는 무엇인가요?

* 구독당 25개의 IoT 커넥터(Azure 지원 요청으로 조정 가능)
* 작업 영역당 10개의 IoT 커넥터(Azure 지원 요청으로 조정 가능)
* IoT Connector당 하나의 FHIR 대상*(조정할 수 없습니다.)

(* - FHIR 대상은 IoT 커넥터의 자식 리소스임)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>Apple&#174;, Google&#174; 또는 Fitbit&#174; 디바이스의 디바이스 메시지와 함께 IoT 커넥터를 사용할 수 있나요?

예. IoT 커넥터는 이러한 모든 플랫폼의 디바이스 메시지를 지원합니다. 자세한 내용은 오픈 [소스 프로젝트 섹션을](iot-git-projects.md) 참조하세요.  

## <a name="more-frequently-asked-questions"></a>기타 질문과 대답
[Azure Healthcare API에 대한 FAQ](../healthcare-apis-faqs.md)

[Azure Healthcare API FHIR 서비스에 대한 FAQ](../fhir/fhir-faq.md)

[Azure Healthcare API DICOM 서비스에 대한 FAQ](../dicom/dicom-services-faqs.yml)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 권한으로 사용됩니다.