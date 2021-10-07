---
title: 가상 네트워크에 대한 Azure IoT DPS(Device Provisioning Service) 지원
description: Azure IoT DPS(Device Provisioning Service)에서 가상 네트워크 연결 패턴을 사용하는 방법입니다.
services: iot-dps
author: anastasia-ms
ms.service: iot-dps
manager: lizross
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: v-stharr
ms.openlocfilehash: 8d90a033f5af5afb55be9585756a7235dc6d89d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659320"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>가상 네트워크에 대한 Azure IoT Hub DPS(Device Provisioning Service) 지원

이 문서에서는 DPS를 사용하여 IoT 허브에서 프로비전하는 IoT 디바이스에 대한 VNET(가상 네트워크) 연결 패턴을 소개합니다. 이 패턴은 고객 소유의 Azure VNET 내에서 디바이스, DPS 및 IoT 허브 간에 프라이빗 연결을 제공합니다. 

DPS가 VNET으로 구성된 대부분의 시나리오에서 IoT Hub도 동일한 VNET에서 구성됩니다. IoT Hub에 대한 VNET 지원 및 구성에 대한 자세한 내용은 [IoT Hub 가상 네트워크 지원](../iot-hub/virtual-network-support.md)을 참조하세요.

## <a name="introduction"></a>소개

기본적으로 DPS 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소가 있는 퍼블릭 엔드포인트에 매핑됩니다. 이 퍼블릭 엔드포인트는 모든 고객에게 표시됩니다. 퍼블릭 엔드포인트에 대한 액세스는 IoT 디바이스에서 광역 네트워크 및 온-프레미스 네트워크를 통해 시도할 수 있습니다.

고객은 여러 가지 이유로 DPS와 같은 Azure 리소스에 대한 연결을 제한할 수 있습니다. 그 이유는 다음과 같습니다.

* 공용 인터넷을 통한 연결 노출을 방지합니다. IoT 허브 및 DPS 리소스에 대한 네트워크 수준 격리를 통해 더 많은 보안 계층을 도입하여 노출을 줄일 수 있습니다.

* 온-프레미스 네트워크 자산에서 프라이빗 연결 환경을 사용하도록 설정하여 데이터와 트래픽이 Azure 백본 네트워크로 직접 전송되도록 합니다.

* 중요한 온-프레미스 네트워크에서의 반출 공격을 방지합니다. 

* [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 Azure 전역의 연결 패턴을 설정합니다.

연결을 제한하는 일반적인 방법으로 [DPS IP 필터 규칙](./iot-dps-ip-filtering.md) 및 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)가 있는 가상 네트워킹(VNET)이 있습니다. 이 문서의 목표는 프라이빗 엔드포인트를 사용하는 DPS에 대한 VNET 접근 방식을 설명하는 것입니다. 

온-프레미스 네트워크에서 작동하는 디바이스는 [VPN(가상 사설망)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 개인 피어링을 사용하여 Azure의 VNET에 연결하고, 프라이빗 엔드포인트를 통해 DPS 리소스에 액세스할 수 있습니다. 

프라이빗 엔드포인트는 Azure 리소스에 액세스할 수 있는 고객 소유의 VNET 내에 할당된 개인 IP 주소입니다. DPS 리소스에 대한 프라이빗 엔드포인트가 있으면 VNET 내에서 작동하는 디바이스에서 퍼블릭 엔드포인트로의 트래픽을 허용하지 않고 DPS 리소스를 통한 프로비전을 요청할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

계속하기 전에 다음과 같은 전제 조건을 충족하는지 확인하세요.

* DPS 리소스가 이미 만들어져 IoT 허브에 연결되어 있습니다. 새 DPS 리소스를 설정하는 방법에 대한 지침은 [Azure Portal을 사용하여 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)을 참조하세요.

* 프라이빗 엔드포인트를 만들 서브넷이 있는 Azure VNET을 프로비전했습니다. 자세한 내용은 [Azure CLI를 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-cli.md)를 참조하세요.

* 온-프레미스 네트워크 내에서 작동하는 디바이스의 경우 Azure VNET에 대한 [VPN(가상 사설망)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 개인 피어링을 설정합니다.

## <a name="private-endpoint-limitations"></a>프라이빗 엔드포인트 제한 사항

프라이빗 엔드포인트를 사용하는 경우 DPS에 대한 현재 제한 사항은 다음과 같습니다.

* DPS 리소스와 연결된 허브가 다른 클라우드에 있는 경우 프라이빗 엔드포인트는 DPS에서 작동하지 않습니다. 예를 들어 [Azure Government 및 글로벌 Azure](../azure-government/documentation-government-welcome.md)가 있습니다.

* 현재 Azure 함수가 VNET 및 프라이빗 엔드포인트로 잠겨 있으면 DPS에 대한 [Azure Functions를 사용하는 사용자 지정 할당 정책](how-to-use-custom-allocation-policies.md)이 작동하지 않습니다. 

* 현재 DPS VNET은 DPS로의 데이터 수신에만 지원됩니다. DPS에서 IoT Hub로의 트래픽인 데이터 송신은 전용 VNET이 아닌 내부 서비스 간 메커니즘을 사용합니다. DPS와 IoT Hub 간의 전체 VNET 기반 송신 잠금에 대한 지원은 현재 사용할 수 없습니다.

* 대기 시간이 가장 짧은 할당 정책은 디바이스를 대기 시간이 가장 짧은 IoT 허브에 할당하는 데 사용됩니다. 이 할당 정책은 가상 네트워크 환경에서 신뢰할 수 없습니다. 

>[!NOTE]
>**데이터 상주 고려 사항:**
>
>DPS는 **글로벌 장치 끝점** ()을 제공 `global.azure-devices-provisioning.net` 합니다. 그러나 전역 끝점을 사용 하는 경우 DPS 인스턴스가 처음 생성 된 지역 외부에서 데이터가 리디렉션될 수 있습니다. 초기 DPS 지역 내에서 데이터 상주을 보장 하려면 개인 끝점을 사용 합니다.

## <a name="set-up-a-private-endpoint"></a>프라이빗 엔드포인트 설정

프라이빗 엔드포인트를 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 DPS 리소스를 열고, **네트워킹** 탭을 클릭합니다. **프라이빗 엔드포인트 연결** 및 **+ 프라이빗 엔드포인트** 를 클릭합니다.

    ![DPS에 대한 새 프라이빗 엔드포인트 추가](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. _프라이빗 엔드포인트 만들기_ 기본 사항 페이지에서 아래 표에 설명된 정보를 입력합니다.

    ![프라이빗 엔드포인트 만들기 - 기본 사항](./media/virtual-network-support/create-private-endpoint-basics.png)

    | 필드 | 값 |
    | :---- | :-----|
    | **구독** | 프라이빗 엔드포인트를 포함할 원하는 Azure 구독을 선택합니다.  |
    | **리소스 그룹** | 프라이빗 엔드포인트를 포함할 리소스 그룹을 선택하거나 만듭니다. |
    | **이름**       | 프라이빗 엔드포인트에 대한 이름을 입력합니다. |
    | **지역**     | 선택한 지역은 VNET이 포함된 지역과 동일해야 하지만 DPS 리소스와 동일할 필요는 없습니다. |

    **다음: 리소스** 를 클릭하여 프라이빗 엔드포인트에서 가리킬 리소스를 구성합니다.

3. _프라이빗 엔드포인트 만들기 - 리소스_ 페이지에서 아래 표에 설명된 정보를 입력합니다.

    ![프라이빗 엔드포인트 리소스 만들기](./media/virtual-network-support/create-private-endpoint-resource.png)

    | 필드 | 값 |
    | :---- | :-----|
    | **구독**        | 프라이빗 엔드포인트에서 가리킬 DPS 리소스가 포함된 Azure 구독을 선택합니다.  |
    | **리소스 종류**       | **Microsoft.Devices/ProvisioningServices** 를 선택합니다. |
    | **리소스**            | 프라이빗 엔드포인트가 매핑될 DPS 리소스를 선택합니다. |
    | **대상 하위 리소스** | **iotDps** 를 선택합니다. |

    > [!TIP]
    > **리소스 ID 또는 별칭을 사용하여 Azure 리소스에 연결합니다.** 설정에 대한 정보는 이 문서의 [프라이빗 엔드포인트 요청](#request-a-private-endpoint) 섹션에 나와 있습니다.


    **다음: 구성** 을 클릭하여 프라이빗 엔드포인트에 대한 VNET을 구성합니다.

4. _프라이빗 엔드포인트 만들기 - 구성_ 페이지에서 프라이빗 엔드포인트를 만들 가상 네트워크 및 서브넷을 선택합니다.

    **다음: 태그** 를 클릭하여 필요에 따라 리소스에 대한 태그를 제공합니다.

    ![프라이빗 엔드포인트 구성](./media/virtual-network-support/create-private-endpoint-configuration.png)

5. **검토 + 만들기** 를 클릭한 다음, **만들기** 를 클릭하여 프라이빗 엔드포인트 리소스를 만듭니다.

## <a name="use-private-endpoints-with-devices"></a>디바이스에서 프라이빗 엔드포인트 사용

디바이스 프로비저닝 코드를 통해 프라이빗 엔드포인트를 사용하려면 [Azure Portal](https://portal.azure.com)에서 DPS 리소스에 대한 개요 페이지에 표시된 대로 프로비전 코드에서 DPS 리소스에 대한 특정 **서비스 엔드포인트** 를 사용해야 합니다. 서비스 엔드포인트의 형식은 다음과 같습니다.

`<Your DPS Tenant Name>.azure-devices-provisioning.net`

설명서 및 SDK에 나와 있는 대부분의 샘플 코드는 **글로벌 디바이스 엔드포인트**(`global.azure-devices-provisioning.net`) 및 **ID 범위** 를 사용하여 특정 DPS 리소스를 확인합니다. 장치를 프로 비전 하기 위해 개인 끝점을 사용 하 여 DPS 리소스에 연결할 때 글로벌 장치 끝점 대신 서비스 끝점을 사용 합니다.

예를 들어 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 프로비전 디바이스 클라이언트 샘플([pro_dev_client_sample](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/prov_dev_client_sample))은 **글로벌 디바이스 엔드포인트** 를 [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c)의 글로벌 프로비전 URI(`global_prov_uri`)로 사용하도록 설계되었습니다.

:::code language="c" source="~/iot-samples-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c" range="60-64" highlight="4":::

:::code language="c" source="~/iot-samples-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c" range="138-144" highlight="3":::

샘플을 개인 끝점과 함께 사용 하기 위해 위의 강조 표시 된 코드는 DPS 리소스에 대 한 서비스 끝점을 사용 하도록 변경 됩니다. 예를 들어 서비스 엔드포인트가 `mydps.azure-devices-provisioning.net`인 경우 코드는 다음과 같습니다.

```C
static const char* global_prov_uri = "global.azure-devices-provisioning.net";
static const char* service_uri = "mydps.azure-devices-provisioning.net";
static const char* id_scope = "[ID Scope]";
```

```C
    PROV_DEVICE_RESULT prov_device_result = PROV_DEVICE_RESULT_ERROR;
    PROV_DEVICE_HANDLE prov_device_handle;
    if ((prov_device_handle = Prov_Device_Create(service_uri, id_scope, prov_transport)) == NULL)
    {
        (void)printf("failed calling Prov_Device_Create\r\n");
    }
```


## <a name="request-a-private-endpoint"></a>프라이빗 엔드포인트 요청

DPS 리소스에 대한 프라이빗 엔드포인트는 리소스 ID별로 요청할 수 있습니다. 이 요청을 수행하려면 리소스 소유자가 리소스 ID를 제공해야 합니다. 

1. 리소스 ID는 아래와 같이 DPS 리소스의 속성 탭에 제공됩니다.

    ![DPS 속성 탭](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > 리소스 ID에는 구독 ID가 포함되어 있습니다. 

2. 리소스 ID가 있으면 위의 [프라이빗 엔드포인트 설정](#set-up-a-private-endpoint) 단계에 있는 _3단계 프라이빗 엔드포인트 만들기 - 리소스_ 페이지의 단계를 따릅니다. **리소스 ID 또는 별칭을 사용하여 Azure 리소스에 연결합니다.** 를 클릭하고, 다음 표의 정보를 입력합니다. 

    | 필드 | 값 |
    | :---- | :-----|
    | **리소스 ID 또는 별칭** | DPS 리소스에 대한 리소스 ID를 입력합니다. |
    | **대상 하위 리소스** | **iotDps** 를 입력합니다. |
    | **메시지 요청** | DPS 리소스 소유자에 대한 요청 메시지를 입력합니다.<br>예제: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    **다음: 구성** 을 클릭하여 프라이빗 엔드포인트에 대한 VNET을 구성합니다.

3. _프라이빗 엔드포인트 만들기 - 구성_ 페이지에서 프라이빗 엔드포인트를 만들 가상 네트워크 및 서브넷을 선택합니다.
 
    **다음: 태그** 를 클릭하여 필요에 따라 리소스에 대한 태그를 제공합니다.

4. **검토 + 만들기** 를 클릭한 다음, **만들기** 를 클릭하여 프라이빗 엔드포인트 요청을 만듭니다.

5. DPS 소유자 쪽에 있는 DPS 네트워킹 탭의 **프라이빗 엔드포인트 연결** 목록에 프라이빗 엔드포인트 요청이 표시됩니다. 소유자는 이 페이지에서 아래와 같이 프라이빗 엔드포인트 요청을 **승인** 하거나 **거부** 할 수 있습니다.

    ![DPS 승인](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>프라이빗 엔드포인트 가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.



## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 DPS 보안 기능에 대해 자세히 알아봅니다.

* [보안](./concepts-service.md#attestation-mechanism)
* [TLS 1.2 지원](tls-support.md)