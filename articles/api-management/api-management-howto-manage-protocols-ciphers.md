---
title: Azure API Management에서 프로토콜 및 암호화 관리 | Microsoft Docs
description: Azure API Management에서 프로토콜(TLS) 및 암호화(DES)를 관리하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/07/2021
ms.author: danlep
ms.openlocfilehash: 5fc47f2238427c7875d2af29324e06d8b6487f1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001792"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Azure API Management에서 프로토콜 및 암호화 관리

Azure API Management는 다음과 같은 여러 버전의 TLS (Transport Layer Security) 프로토콜을 지원 합니다.
* 클라이언트 쪽
* 백 엔드 측
* 3DES 암호화

이 가이드에서는 Azure API Management 인스턴스용 프로토콜 및 암호화 구성을 관리하는 방법을 설명합니다.

![APIM에서 프로토콜 및 암호화 관리](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>사전 요구 사항

* API Management 인스턴스. [없다면 하나 생성합니다](get-started-create-service-instance.md).

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>TLS 프로토콜 및 3DES 암호화를 관리하는 방법

1. Azure Portal에서 **API Management 인스턴스** 로 이동합니다.
1. 측면 메뉴에서 **보안** 섹션으로 스크롤합니다.
1. 보안 섹션에서 **프로토콜 + 암호화** 를 선택 합니다.  
1. 원하는 프로토콜 또는 암호화를 사용하거나 사용하지 않도록 설정합니다.
1. **저장** 을 클릭합니다. 변경 내용은 1시간 이내에 적용됩니다.  

> [!NOTE]
> 일부 프로토콜 또는 암호 그룹 (예: 백 엔드 쪽 TLS 1.2)은 Azure Portal에서 사용 하거나 사용 하지 않도록 설정할 수 없습니다. 대신 REST 호출을 적용 해야 합니다. `properties.customProperties` [API Management 서비스 만들기/업데이트 REST API](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update) 문서의 구조를 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [TLS](/dotnet/framework/network-programming/tls)에 대해 자세히 알아봅니다.
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
