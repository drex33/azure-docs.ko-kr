---
title: Service Connector와 Azure Table Storage 통합
description: Service Connector를 통해 Azure Table Storage 애플리케이션에 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 646abc1fc11de0c1d9d5f3c6382e0824c020e85c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052883"
---
# <a name="integrate-azure-table-storage-with-service-connector"></a>Service Connector와 Azure Table Storage 통합

이 페이지에는 Service Connector를 사용하여 지원되는 인증 유형 및 Azure Table Storage 클라이언트 유형이 표시됩니다. Service Connector를 사용하지 않고도 다른 프로그래밍 언어로 Azure Table Storage 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 얻을 수 있는 기본 환경 변수 이름 및 값(또는 Spring Boot 구성)도 표시됩니다. [Service Connector 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원되는 컴퓨팅 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 ID | Secret/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .Net | | | ![예 아이콘](./media/green-check.png) | |
| Java | | | ![예 아이콘](./media/green-check.png) | |
| Node.js | | | ![예 아이콘](./media/green-check.png) | |
| Python | | | ![예 아이콘](./media/green-check.png) | |


## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 애플리케이션 속성

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.JS 및 Python

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_STORAGETABLE_CONNECTIONSTRING | Table Storage 연결 문자열 | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |


## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
