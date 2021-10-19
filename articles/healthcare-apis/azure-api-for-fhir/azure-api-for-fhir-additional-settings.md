---
title: FHIR 용 Azure API에 대 한 추가 설정
description: Azure API for FHIR에 대해 설정할 수 있는 추가 설정의 개요
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435078"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>FHIR 용 Azure API에 대 한 추가 설정

이 방법 가이드에서는 FHIR 용 Azure API에서 설정 하려는 추가 설정을 검토 합니다. 훨씬 더 자세한 정보를 자세히 설명 하는 추가 페이지가 있습니다.

## <a name="configure-database-settings"></a>데이터베이스 설정 구성

Azure API for FHIR가 데이터베이스를 사용하여 해당 데이터를 저장합니다. 기본 데이터베이스의 성능은 서비스를 프로비저닝하는 동안 선택된 RU(요청 단위) 수 또는 서비스가 프로비저닝된 후의 데이터베이스 설정에 따라 달라집니다.

데이터베이스에 대 한 충분 한 시스템 리소스를 항상 사용할 수 있도록 처리량을 프로 비전 해야 합니다. 응용 프로그램에 필요한 RUs 수는 수행 하는 작업에 따라 달라 집니다. 작업의 범위는 단순 읽기 및 쓰기에서 보다 복잡 한 쿼리에 이르기까지 다양 합니다.

기본 설정을 변경 하는 방법에 대 한 자세한 내용은 [데이터베이스 설정 구성](configure-database.md)을 참조 하세요.

## <a name="access-control"></a>Access Control

FHIR 용 Azure API를 사용 하면 권한 있는 사용자만 FHIR API에 액세스할 수 있습니다. 권한 있는 사용자는 두 가지 메커니즘을 통해 구성할 수 있습니다. 액세스 제어를 구성 하는 기본 및 권장 방법은 **액세스 제어 (IAM)** 블레이드를 통해 액세스할 수 있는 azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/index.yml)를 사용 하는 것입니다. Azure RBAC는 구독과 연결 된 Azure Active Directory 테 넌 트를 사용 하 여 데이터 평면 액세스를 보호 하려는 경우에만 작동 합니다. 다른 테 넌 트를 사용 하려는 경우 FHIR 용 Azure API는 로컬 FHIR 데이터 평면 액세스 제어 메커니즘을 제공 합니다. 로컬 RBAC 메커니즘을 사용 하는 경우 구성 옵션은 풍부 하지 않습니다. 자세한 내용을 보려면 다음 옵션 중 하나를 선택 하십시오.

* [FHIR 데이터 평면에 대 한 AZURE RBAC](configure-azure-rbac.md). 구독과 연결 된 Azure Active Directory 테 넌 트를 사용 하는 경우이 옵션을 사용 하는 것이 좋습니다.
* [로컬 FHIR 데이터 평면 액세스 제어](configure-local-rbac.md)입니다. 데이터 평면 액세스 제어에 외부 Azure Active Directory 테 넌 트를 사용 해야 하는 경우에만이 옵션을 사용 합니다. 

## <a name="enable-diagnostic-logging"></a>진단 로깅 사용
서비스를 모니터링 하 고 규정 준수를 정확 하 게 보고 하기 위해 설치의 일부로 진단 로깅을 사용 하도록 설정할 수 있습니다. 진단 로깅을 설정 하는 방법에 대 한 자세한 내용은 일부 샘플 쿼리와 함께 진단 로깅을 설정 하는 방법에 대 한 [지침](enable-diagnostic-logging.md) 을 참조 하세요. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>사용자 지정 헤더를 사용 하 여 감사 로그에 데이터 추가
FHIR 용 Azure API에서 로그에 추가 정보를 포함 하는 것이 좋습니다 .이 정보는 호출 시스템에서 제공 됩니다. 이 정보를 포함 하려면 사용자 지정 헤더를 사용할 수 있습니다.

사용자 지정 헤더를 사용하여 여러 유형의 정보를 캡처할 수 있습니다. 예를 들면 다음과 같습니다.

* ID 또는 권한 부여 정보
* 호출자의 원점
* 원본 조직
* 클라이언트 시스템 세부 정보(전자 건강 기록, 환자 포털)

감사 로그에이 데이터를 추가 하려면 [사용자 지정 HTTP 헤더를 사용 하 여 감사 로그에 데이터 추가](use-custom-headers.md) 방법 가이드를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 FHIR 용 Azure API에 대 한 추가 설정을 설정 합니다.

그런 다음 일련의 자습서를 확인 하 여 FHIR 데이터를 읽는 웹 응용 프로그램을 만듭니다.

>[!div class="nextstepaction"]
>[JavaScript 애플리케이션 배포](tutorial-web-app-fhir-server.md)