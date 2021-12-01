---
title: Azure 의료 Api 제한 포함 파일
description: Azure 의료 Api 포함 파일에 대 한 제한
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: include
ms.date: 11/30/2021
ms.author: v-stevewohl
ms.custom: include file
ms.openlocfilehash: ddf429fd61a2aac55c8797f28c4ad6483a7bb851
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133365336"
---
Azure 의료 Api는 개방형 표준 및 프레임 워크를 기반으로 하는 관리 되는 API 서비스 집합입니다. 이 서비스는 워크플로를 사용 하 여 의료을 개선 하 고 확장 가능 하 고 안전한 의료 솔루션을 제공 합니다. 현재 공개 미리 보기 상태입니다. Azure 의료 Api에는 신속한 의료 상호 운용성 리소스 (FHIR) 서비스, 디지털 이미징 및 의약품 (DICOM) 서비스의 통신 및 IoT 커넥터가 포함 되어 있습니다.

FHIR 서비스는 Azure 의료 Api 내에서 FHIR 사양을 구현 하는 것입니다. 이를 통해 선택적 DICOM 서비스 인스턴스와 IoT 커넥터를 사용 하 여 단일 작업 영역에서 하나 이상의 FHIR 서비스 인스턴스를 결합할 수 있습니다. FHIR 용 Azure API는 GA (일반 공급) 이며 독립 실행형 서비스 제공으로 사용할 수 있습니다.

| **할당량 이름** | **기본 제한**| **최대 제한** | **참고 사항** |
|---|---|---|---|
|작업 영역 |10|[고객 지원 문의](https://azure.microsoft.com/support/options/) |구독 당 제한|
|FHIR |10|[고객 지원 문의](https://azure.microsoft.com/support/options/) |작업 영역 당 제한|
|DICOM |10|[고객 지원 문의](https://azure.microsoft.com/support/options/) |작업 영역 당 제한|
|IoT 커넥터 |10|N/A |작업 영역 당 한도를 늘릴 수 없습니다.|
