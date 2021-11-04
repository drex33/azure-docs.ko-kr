---
title: 서비스 커넥터와 Azure Queue Storage 통합
description: 서비스 커넥터를 사용 하 여 Azure Queue Storage를 응용 프로그램에 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: eec504cadbe4c039219ca58bd23b283c40d3785e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052884"
---
# <a name="integrate-azure-queue-storage-with-service-connector"></a>서비스 커넥터와 Azure Queue Storage 통합

이 페이지에는 서비스 커넥터를 사용 하 여 Azure Queue Storage의 지원 되는 인증 유형 및 클라이언트 유형이 표시 됩니다. 서비스 커넥터를 사용 하지 않고 다른 프로그래밍 언어로 Azure Queue Storage에 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 제공 되는 기본 환경 변수 이름 및 값 (또는 스프링 부팅 구성)도 표시 됩니다. [서비스 커넥터 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원 되는 계산 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원 되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 Id | 비밀/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .Net | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Java | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Java-스프링 부팅 | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Node.js | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |
| Python | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) | ![예 아이콘](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 응용 프로그램 속성

### <a name="net-java-nodejs-python"></a>.NET, Java, Node.JS, Python

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_CONNECTIONSTRING | 큐 저장소 연결 문자열 | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |

**시스템 할당 관리 ID**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | 큐 저장소 끝점 | `https://{StorageAccountName}.queue.core.windows.net/` |

**사용자가 할당한 관리 ID**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | 큐 저장소 끝점 | `https://{storageAccountName}.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID | 클라이언트 ID | `{yourClientID}` |

Service Principal

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_STORAGEQUEUE_RESOURCEENDPOINT | 큐 저장소 끝점 | `https://{storageAccountName}.queue.core.windows.net/` |
| AZURE_STORAGEQUEUE_CLIENTID | 클라이언트 ID | `{yourClientID}` |
| AZURE_STORAGEQUEUE_CLIENTSECRET | 클라이언트 암호 | `{yourClientSecret}` |
| AZURE_STORAGEQUEUE_TENANTID | 테넌트 ID | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java-스프링 부팅

**비밀/ConnectionString**

| 애플리케이션 속성 | 설명 | 예제 값 |
| --- | --- | --- |
| azure. 계정-이름 | 큐 저장소 계정 이름 | `{storageAccountName}` |
| azure. 계정-키 | 큐 저장소 계정 키 | `{yourSecret}` |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
