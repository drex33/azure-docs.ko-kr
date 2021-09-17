---
title: Azure IoT Central의 고객 데이터 요청 기능 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central 애플리케이션에서 고객 데이터를 식별하고, 삭제하고, 내보내는 방법을 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 704e5a8624f07e7d52729454a8956256208df6fa
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436035"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Azure IoT Central의 고객 데이터 요청 기능

Azure IoT Central은 대규모 IoT 자산을 연결, 모니터링 및 관리하고, IoT 데이터에서 심층 정보를 만들고, 정보를 바탕으로 작업을 수행하도록 도와주는 완전히 관리되는 IoT(사물 인터넷) SaaS(Software-as-a-Service) 솔루션입니다.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>고객 데이터 식별

Azure Active Directory 개체 ID는 사용자를 식별하고 역할을 할당하는 데 사용됩니다. Azure IoT Central 포털은 역할 할당을 위해 사용자 전자 메일 주소를 표시하지만 Azure Active Directory 개체 ID만 저장되고 전자 메일 주소는 Azure Active Directory에서 동적으로 쿼리됩니다. Azure IoT Central 관리자는 Azure IoT Central 애플리케이션의 사용자 관리 섹션에서 애플리케이션 사용자를 보고, 내보내고, 삭제할 수 있습니다.

애플리케이션 내에서 경고를 수신하도록 전자 메일 주소를 구성할 수 있습니다. 이 경우 전자 메일 주소는 IoT Central 내에 저장되므로 앱 내 계정 관리 페이지에서 관리해야 합니다.

Microsoft는 디바이스에 대한 정보를 유지 관리하지 않으며, 디바이스와 사용자의 상관 관계를 지원하는 데이터에 액세스할 수 없습니다. Azure IoT Central에서 관리되는 디바이스는 대부분 개인 디바이스가 아닙니다(예: 자동 판매기 또는 커피 메이커). 그러나 고객은 일부 디바이스를 개인적으로 식별 가능한 것으로 간주할 수 있으며, 재량에 따라 디바이스를 개인과 연결하는 자체 자사 또는 재고 추적 시스템을 유지할 수 있습니다. Azure IoT Central은 디바이스와 연결된 모든 데이터를 개인 데이터인 것처럼 관리 및 저장합니다.

Microsoft 엔터프라이즈 서비스를 사용하는 경우 Microsoft는 시스템 생성 로그라는 일부 정보를 생성합니다. 이러한 로그는 서비스 내에서 수행된 실제 작업 및 개별 디바이스와 관련된 진단 데이터로 구성되며, 사용자 활동과 관련이 없습니다. Azure IoT Central 시스템 생성 로그는 애플리케이션 관리자가 액세스하거나 내보낼 수 없습니다.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

사용자 데이터를 삭제하는 기능은 IoT Central 관리 페이지를 통해서만 제공됩니다. 애플리케이션 관리자는 삭제할 사용자를 선택하고 애플리케이션의 오른쪽 위에서 **삭제** 를 선택하여 레코드를 삭제할 수 있습니다. 또한 애플리케이션 관리자는 해당 애플리케이션과 더 이상 연결되지 않는 개별 계정을 제거할 수 있습니다.

사용자가 삭제된 후에는 해당 사용자의 전자 메일로 더 이상 경고가 전송되지 않습니다. 그러나 구성된 각 경고에서 이들의 전자 메일 주소를 개별적으로 제거해야 합니다.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

데이터를 내보내는 기능은 IoT Central 관리 페이지를 통해서만 제공됩니다. 애플리케이션 관리자는 할당된 역할을 비롯한 고객 데이터를 선택하고 복사하여 붙여 넣을 수 있습니다.

## <a name="links-to-additional-documentation"></a>추가 설명서에 대한 링크

역할 정의를 포함하여 계정 관리에 대한 자세한 내용은 [애플리케이션을 관리하는 방법](howto-administer.md)을 참조하세요.
