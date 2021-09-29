---
title: 메시지 복제 작업 및 애플리케이션 - Azure Service Bus | Microsoft Docs
description: 이 문서에서 Azure Functions를 사용하여 메시지 복제 작업 및 애플리케이션을 빌드하는 방법에 대한 개요 제공
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 78598401816adc32eecb34e2bd11fa49e8a44b0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211813"
---
# <a name="message-replication-tasks-and-applications"></a>메시지 복제 작업 및 애플리케이션

[메시지 복제 및 지역 간 페더레이션](service-bus-federation-overview.md) 문서에서 설명하는 것처럼 Service Bus 엔터티 쌍 간, Service Bus와 기타 메시지 원본 및 대상 간 메시지 시퀀스 복제는 일반적으로 Azure Functions에 의존합니다.

[Azure Functions](../azure-functions/functions-overview.md)는 [메시지 복제 및 페더레이션](service-bus-federation-overview.md) 작업을 포함하여 서버리스 애플리케이션을 구성하고 실행하기 위한 확장 가능하고 안정적인 실행 환경입니다.

이 개요에서는 해당 애플리케이션에 대한 Azure Functions의 기본 제공 기능, 변환 작업에 맞게 조정 및 수정할 수 있는 코드 블록 및 Service Bus 및 다른 Azure Messaging 서비스와 이상적으로 통합되도록 Azure Functions 애플리케이션을 구성하는 방법에 대해 알아봅니다. 자세한 내용은 이 문서에서 가리키는 Azure Functions 설명서를 참조하세요.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
