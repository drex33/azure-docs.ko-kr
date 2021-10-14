---
title: 가상 네트워크에 Azure Spring Cloud 배포
description: Azure Spring Cloud를 Azure 가상 네트워크에 배포합니다(VNet 삽입).
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli, subject-rbac-steps
ms.openlocfilehash: 86b594487a007e33d4f1428312f8ac3ce29fe833
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984029"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>가상 네트워크에 Azure Spring Cloud 배포

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 자습서에서는 Azure Spring Cloud 인스턴스를 가상 네트워크에 배포하는 방법에 대해 설명합니다. 이 배포를 VNet 삽입이라고도 합니다.

배포를 통해 다음을 수행할 수 있습니다.

* 회사 네트워크의 인터넷에서 Azure Spring Cloud 앱 및 서비스 런타임 격리
* Azure Spring Cloud에서 온-프레미스 데이터 센터의 시스템 또는 다른 가상 네트워크의 Azure 서비스와 상호 작용
* Azure Spring Cloud에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어하는 고객의 역량 강화

다음 비디오에서는 관리 가상 네트워크를 사용하여 Spring Boot 애플리케이션을 보호하는 방법을 설명합니다.

<br>

> [!VIDEO https://www.youtube.com/embed/LbHD0jd8DTQ?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

> [!Note]
> 새 Azure Spring Cloud 서비스 인스턴스를 만드는 경우에만 Azure 가상 네트워크를 선택할 수 있습니다. Azure Spring Cloud를 만든 후에는 다른 가상 네트워크를 사용하도록 변경할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Portal에서 리소스 공급자 등록](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 지침을 따르거나 다음 Azure CLI 명령을 실행하여 Azure Spring Cloud 리소스 공급자(**Microsoft.AppPlatform** 및 **Microsoft.ContainerService**)를 등록합니다.

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>가상 네트워크 요구 사항

Azure Spring Cloud 인스턴스를 배포하는 가상 네트워크는 다음 요구 사항을 충족해야 합니다.

* **위치**: 가상 네트워크는 Azure Spring Cloud 인스턴스와 동일한 위치에 있어야 합니다.
* **구독**: 가상 네트워크는 Azure Spring Cloud 인스턴스와 동일한 구독에 있어야 합니다.
* **서브넷**: 가상 네트워크는 Azure Spring Cloud 인스턴스 전용의 다음 두 서브넷을 포함해야 합니다.

    * 서비스 런타임용 서브넷
    * Spring Boot 마이크로서비스 애플리케이션용 서브넷
    * 이러한 서브넷과 Azure Spring Cloud 인스턴스 간에는 일대일 관계가 있습니다. 새 서브넷을 배포하는 각 서비스 인스턴스에 사용합니다. 각 서브넷은 단일 서비스 인스턴스만 포함할 수 있습니다.
* **주소 공간**: CIDR은 서비스 런타임 서브넷과 Spring Boot 마이크로서비스 애플리케이션 서브넷 모두에 대해 최대 */28* 까지 차단합니다.
* **경로 테이블**: 기본적으로 서브넷에는 연결된 기존 경로 테이블이 필요하지 않습니다. [사용자 고유의 경로 테이블을 가져올](#bring-your-own-route-table) 수 있습니다.

다음 절차에서는 Azure Spring Cloud 인스턴스를 포함하도록 가상 네트워크를 설정하는 방법에 대해 설명합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

#### <a name="portal"></a>[포털](#tab/azure-portal)
Azure Spring Cloud 인스턴스를 호스팅하는 가상 네트워크가 이미 있는 경우 1, 2 및 3단계를 건너뜁니다. 4단계부터 시작하여 가상 네트워크에 대한 서브넷을 준비할 수 있습니다.

1. Azure Portal 메뉴에서 **리소스 만들기** 를 선택합니다. Azure Marketplace에서 **네트워킹** > **가상 네트워크** 를 차례로 선택합니다.

1. **가상 네트워크 만들기** 대화 상자에서 다음 정보를 입력하거나 선택합니다.

    |설정          |값                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |구독을 선택합니다.                         |
    |Resource group   |리소스 그룹을 선택하거나 새로 만듭니다.  |
    |Name             |**azure-spring-cloud-vnet** 을 입력합니다.                 |
    |위치         |**미국 동부** 를 선택합니다.                               |

1. 완료되면 **다음: IP 주소** 를 선택합니다.

1. IPv4 주소 공간에 대해 **10.1.0.0/16** 을 입력합니다.

1. **서브넷 추가** 를 선택합니다. 그런 다음, **서브넷 이름** 에 대해 **service-runtime-subnet** 을 입력하고, **서브넷 주소 범위** 에 대해 **10.1.0.0/28** 을 입력합니다. 그런 다음, **추가** 를 선택합니다.

1. **서브넷 추가** 를 다시 선택한 다음, **서브넷 이름** 및 **서브넷 주소 범위** 를 입력합니다. 예를 들어 **apps-subnet** 및 **10.1.1.0/28** 을 입력합니다. 그런 다음, **추가** 를 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 나머지 항목은 기본값으로 두고, **만들기** 를 선택합니다.

#### <a name="cli"></a>[CLI](#tab/azure-CLI)
Azure 스프링 클라우드 인스턴스를 호스트할 가상 네트워크가 이미 있는 경우 1, 2, 3 및 4 단계를 건너뜁니다. 5 단계에서 시작 하 여 가상 네트워크에 대 한 서브넷을 준비할 수 있습니다.

1. 구독, 리소스 그룹 및 Azure 스프링 클라우드 인스턴스에 대 한 변수를 정의 합니다. 실제 환경에 따라 값을 사용자 지정합니다.

   ```azurecli
   SUBSCRIPTION='subscription-id'
   RESOURCE_GROUP='my-resource-group'
   LOCATION='eastus'
   SPRING_CLOUD_NAME='spring-cloud-name'
   VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
   ```

1. Azure CLI에 로그인하고 활성 구독을 선택합니다.

   ```azurecli
   az login
   az account set --subscription ${SUBSCRIPTION}
   ```

1. 리소스에 대 한 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name $RESOURCE_GROUP --location $LOCATION
   ```

1. 가상 네트워크 만들기

   ```azurecli
   az network vnet create --resource-group $RESOURCE_GROUP \
       --name $VIRTUAL_NETWORK_NAME \
       --location $LOCATION \
       --address-prefix 10.1.0.0/16
   ```

1. 이 가상 네트워크에 2 개의 서브넷을 만듭니다. 

   ```azurecli
   az network vnet subnet create --resource-group $RESOURCE_GROUP \
       --vnet-name $VIRTUAL_NETWORK_NAME \
       --address-prefixes 10.1.0.0/28 \
       --name service-runtime-subnet 
   az network vnet subnet create --resource-group $RESOURCE_GROUP \
       --vnet-name $VIRTUAL_NETWORK_NAME \
       --address-prefixes 10.1.1.0/28 \
       --name apps-subnet 
   ```

---

## <a name="grant-service-permission-to-the-virtual-network"></a>가상 네트워크에 서비스 권한 부여

Azure Spring Cloud에는 추가 배포 및 유지 관리를 위해 가상 네트워크에서 전용 및 동적 서비스 주체를 부여하려면 가상 네트워크에 대한 **소유자** 권한이 있어야 합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

이전에 만든 **azure-spring-cloud-vnet** 가상 네트워크를 선택합니다.

1. **액세스 제어(IAM)** 를 선택한 다음, **추가** > **역할 할당 추가** 를 차례로 선택합니다.

    ![액세스 제어 화면을 보여 주는 스크린샷](./media/spring-cloud-v-net-injection/access-control.png)

1. **Azure Spring Cloud 리소스 공급자** 에 *소유자* 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md#step-2-open-the-add-role-assignment-pane)을 참조하세요.

    ![리소스 공급자에 대한 소유자 할당을 보여 주는 스크린샷.](./media/spring-cloud-v-net-injection/assign-owner-resource-provider.png)

    또한 다음 Azure CLI 명령을 실행하여 이 단계를 수행할 수도 있습니다.

    ```azurecli
    VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
        --name ${NAME_OF_VIRTUAL_NETWORK} \
        --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
        --query "id" \
        --output tsv`

    az role assignment create \
        --role "Owner" \
        --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
        --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
    ```

#### <a name="cli"></a>[CLI](#tab/azure-CLI)

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name $VIRTUAL_NETWORK_NAME \
    --resource-group $RESOURCE_GROUP \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

---

## <a name="deploy-an-azure-spring-cloud-instance"></a>Azure Spring Cloud 인스턴스 배포

#### <a name="portal"></a>[포털](#tab/azure-portal)
Azure Spring Cloud 인스턴스를 가상 네트워크에 배포하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 위쪽 검색 상자에서 **Azure Spring Cloud** 를 검색합니다. 결과에서 **Azure Spring Cloud** 를 선택합니다.

1. **Azure Spring Cloud** 페이지에서 **+ 추가** 를 선택합니다.

1. Azure Spring Cloud **만들기** 페이지에 있는 양식을 채웁니다.

1. 가상 네트워크와 동일한 리소스 그룹 및 지역을 선택합니다.

1. **서비스 세부 정보** 아래의 **이름** 에 대해 **azure-spring-cloud-vnet** 을 선택합니다.

1. **네트워킹** 탭을 선택하고, 다음 값을 선택합니다.

    |설정                                |값                                             |
    |---------------------------------------|--------------------------------------------------|
    |사용자 고유의 가상 네트워크에 배포     |**예** 를 선택합니다.                                   |
    |가상 네트워크                        |**azure-spring-cloud-vnet** 을 선택합니다.               |
    |서비스 런타임 서브넷                 |**service-runtime-subnet** 을 선택합니다.                |
    |Spring Boot 마이크로 서비스 앱 서브넷   |**apps-subnet** 을 선택합니다.                           |

    ![Azure Spring Cloud 만들기 페이지의 네트워킹 탭을 보여 주는 스크린샷](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. **검토 및 만들기** 를 선택합니다.

1. 사양을 확인하고, **만들기** 를 선택합니다.

    ![사양 확인을 보여 주는 스크린샷](./media/spring-cloud-v-net-injection/verify-specifications.png)

#### <a name="cli"></a>[CLI](#tab/azure-CLI)
Azure Spring Cloud 인스턴스를 가상 네트워크에 배포하려면 다음을 수행합니다.

방금 만든 가상 네트워크 및 서브넷을 지정 하 여 Azure 스프링 클라우드 인스턴스를 만듭니다.

   ```azurecli
   az spring-cloud create  \
       --resource-group "$RESOURCE_GROUP" \
       --name "$SPRING_CLOUD_NAME" \
       --vnet $VIRTUAL_NETWORK_NAME \
       --service-runtime-subnet service-runtime-subnet \
       --app-subnet apps-subnet \
       --enable-java-agent \
       --sku standard \
       --location $LOCATION
   ```

---

배포 후 구독에서 Azure Spring Cloud 인스턴스에 대한 네트워크 리소스를 호스팅하는 두 개의 추가 리소스 그룹이 만들어집니다. **홈** 으로 이동한 다음, 위쪽 메뉴 항목에서 **리소스 그룹** 을 선택하여 다음과 같은 새 리소스 그룹을 찾습니다.

**ap-svc-rt_{서비스 인스턴스 이름}_{서비스 인스턴스 지역}** 이라는 리소스 그룹에는 서비스 인스턴스의 서비스 런타임에 대한 네트워크 리소스가 포함되어 있습니다.

  ![서비스 런타임을 보여 주는 스크린샷.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

**ap-app_{서비스 인스턴스 이름}_{서비스 인스턴스 지역}** 이라는 리소스 그룹에는 서비스 인스턴스의 Spring Boot 마이크로서비스 애플리케이션에 대한 네트워크 리소스가 포함되어 있습니다.

  ![앱 리소스 그룹을 보여 주는 스크린샷](./media/spring-cloud-v-net-injection/apps-resource-group.png)

이러한 네트워크 리소스는 이전 이미지에서 만든 가상 네트워크에 연결됩니다.

  ![연결된 디바이스가 있는 가상 네트워크를 보여 주는 스크린샷](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > 리소스 그룹은 Azure Spring Cloud 서비스에서 완전히 관리됩니다. 내부에 있는 리소스를 수동으로 삭제하거나 수정하지 *마세요*.


## <a name="using-smaller-subnet-ranges"></a>더 작은 서브넷 범위 사용

이 표에서는 더 작은 서브넷 범위를 사용하여 Azure Spring Cloud가 지원하는 최대 앱 인스턴스 수를 보여줍니다.

| 앱 서브넷 CIDR | 총 IP 수 | 사용 가능한 IP 수 | 최대 앱 인스턴스 수                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> 1개 코어를 사용하는 앱:  96 <br/> 2개 코어를 사용하는 앱: 48<br/>  3개 코어를 사용하는 앱: 32 <br/> 4개 코어를 사용하는 앱: 24 </p> |
| /27             | 32        | 24            | <p> 1개 코어를 사용하는 앱:  228<br/> 2개 코어를 사용하는 앱: 144<br/>  3개 코어를 사용하는 앱: 96 <br/>  4개 코어를 사용하는 앱: 72</p> |
| /26             | 64        | 56            | <p> 1개 코어를 사용하는 앱:  500<br/> 2개 코어를 사용하는 앱: 336<br/>  3개 코어를 사용하는 앱: 224<br/>  4개 코어를 사용하는 앱: 168</p> |
| /25             | 128       | 120           | <p> 1개 코어를 사용하는 앱:  500<br> 2개 코어를 사용하는 앱:  500<br>  3개 코어를 사용하는 앱:  480<br>  4개 코어를 사용하는 앱: 360</p> |
| /24             | 256       | 248           | <p> 1개 코어를 사용하는 앱:  500<br/> 2개 코어를 사용하는 앱:  500<br/>  3개 코어를 사용하는 앱: 500<br/>  4개 코어를 사용하는 앱: 500</p> |

서브넷의 경우 Azure에서 5개의 IP 주소를 예약하며, Azure Spring Cloud에는 3개 이상의 IP 주소가 필요합니다. 따라서 8개 이상의 IP 주소가 필요하므로 /29 및 /30은 작동하지 않습니다.

서비스 런타임 서브넷의 경우 최소 크기는 /28입니다. 이 크기는 앱 인스턴스 수와 관련이 없습니다.

## <a name="bring-your-own-route-table"></a>사용자 고유의 경로 테이블 가져오기

Azure Spring Cloud는 기존 서브넷 및 경로 테이블 사용을 지원합니다.

사용자 지정 서브넷에 경로 테이블이 포함되어 있지 않은 경우 Azure Spring Cloud는 각 서브넷에 대해 테이블을 만들고 인스턴스 수명 주기 동안 규칙을 추가합니다. 사용자 지정 서브넷에 경로 테이블이 포함되어 있는 경우 Azure Spring Cloud는 인스턴스 작업 중에 기존 경로 테이블을 확인하고 작업에 따라 추가/업데이트 및/또는 규칙을 추가합니다.

> [!Warning]
> 사용자 지정 규칙을 사용자 지정 경로 테이블에 추가하고 업데이트할 수 있습니다. 그러나 규칙은 Azure Spring Cloud에서 추가되며 업데이트하거나 제거하면 안됩니다. 0\.0.0.0/0과 같은 규칙은 항상 지정된 경로 테이블에 존재해야 하며 NVA 또는 다른 송신 게이트웨이와 같은 인터넷 게이트웨이의 대상에 매핑되어야 합니다. 사용자 지정 규칙만 수정되는 경우 규칙을 업데이트할 때 주의해야 합니다.

### <a name="route-table-requirements"></a>경로 테이블 요구 사항

사용자 지정 vnet이 연결된 경로 테이블은 다음 요구 사항을 충족해야 합니다.

* 새 Azure Spring Cloud 서비스 인스턴스를 만드는 경우에만 Azure 경로 테이블을 vnet과 연결할 수 있습니다. Azure Spring Cloud를 만든 후에는 다른 경로 테이블을 사용하도록 변경할 수 없습니다.
* 마이크로서비스 애플리케이션 서브넷과 서비스 런타임 서브넷은 모두 다른 경로 테이블과 연결되거나 둘 다에 연결되지 않아야 합니다.
* 인스턴스를 만들기 전에 권한을 할당해야 합니다. **Azure Spring Cloud 리소스 공급자** 에 라우팅 테이블에 대한 *소유자* 권한을 부여해야 합니다.
* 클러스터를 만든 후에는 연결된 경로 테이블 리소스를 업데이트할 수 없습니다. 경로 테이블 리소스를 업데이트할 수 없지만 경로 테이블에서 사용자 지정 규칙을 수정할 수 있습니다.
* 잠재적으로 충돌하는 라우팅 규칙으로 인해 여러 인스턴스에서 경로 테이블을 재사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [VNet에서 애플리케이션을 Azure Spring Cloud에 배포](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)
- [VNET에서 Azure Spring Cloud 문제 해결](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
