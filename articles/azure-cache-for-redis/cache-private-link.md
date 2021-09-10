---
title: Azure Private Link를 사용하는 Azure Cache for Redis
description: Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 Azure Cache for Redis에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 이 문서에서는 Azure Portal을 사용하여 Azure Cache, Azure Virtual Network 및 프라이빗 엔드포인트를 만드는 방법에 대해 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 37955c748135768701fd3429c2e4182f5d18e70c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566230"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>Azure Private Link를 사용하는 Azure Cache for Redis

이 문서에서는 Azure Portal을 사용하여 가상 네트워크 및 프라이빗 엔드포인트가 있는 Azure Cache for Redis 인스턴스를 만드는 방법에 대해 알아봅니다. 기존 Azure Cache for Redis 인스턴스에 프라이빗 엔드포인트를 추가하는 방법에 대해서도 알아봅니다.

Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 Azure Cache for Redis에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> 현재 포털 콘솔 지원과 방화벽 스토리지 계정에 대한 지속성은 지원되지 않습니다. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>새 Azure Cache for Redis 인스턴스를 사용하여 프라이빗 엔드포인트 만들기 

이 섹션에서는 프라이빗 엔드포인트를 사용하여 새 Azure Cache for Redis 인스턴스를 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기 

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="리소스 만들기를 선택합니다.":::

2. **새** 페이지에서 **네트워킹** 을 선택한 다음 **가상 네트워크** 를 선택합니다.

3. **추가** 를 선택하여 가상 네트워크를 만듭니다.

4. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 가상 네트워크를 만들 구독입니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 가상 네트워크 및 기타 리소스를 만들 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **이름** | 가상 네트워크 이름을 입력합니다. | 이름은 문자나 숫자로 시작하고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. | 
   | **지역** | 드롭다운하여 지역을 선택합니다. | 가상 네트워크를 사용할 다른 서비스와 가까이 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

5. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

6. **IP 주소** 탭에서 **IPv4 주소 공간** 을 CIDR 표기법을 따른 하나 이상의 주소 접두사로 지정합니다(예: 192.168.1.0/24).

7. **서브넷 이름** 아래의 **기본값** 을 선택하여 서브넷의 속성을 편집합니다.

8. **서브넷 편집** 창에서 **서브넷 이름** 및 **서브넷 주소 범위** 를 지정합니다. 서브넷의 주소 범위는 CIDR 표기법을 따라야 합니다(예: 192.168.1.0/24). 가상 네트워크의 주소 공간에 포함되어야 합니다.

9. **저장** 을 선택합니다.

10. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

11. 모든 정보가 올바른지 확인하고 **만들기** 를 선택하여 가상 네트워크를 프로비전합니다.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하여 Azure Cache for Redis 인스턴스 만들기

캐시 인스턴스를 만들려면 다음 단계를 수행합니다.

1. Azure Portal 홈페이지로 돌아가서 사이드바 메뉴를 열고 **리소스 만들기** 를 선택합니다. 
   
1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Azure Cache for Redis를 선택합니다.":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 이 캐시 이름은 1~63자 사이의 문자열이어야 합니다. 문자열에는 숫자, 문자 또는 하이픈만 포함해야 합니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. | 
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **가격 책정 계층** | 드롭다운하여 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cache/)을 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

1. **네트워킹** 탭을 선택하거나 페이지 하단에서 **네트워킹** 단추를 선택합니다.

1. **네트워킹** 탭에서 연결 방법에 대한 **프라이빗 엔드포인트** 를 선택합니다.

1. **추가** 단추를 선택하여 프라이빗 엔드포인트를 만듭니다.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="네트워킹에서 프라이빗 엔드포인트를 추가합니다.":::

1. **프라이빗 엔드포인트 만들기** 페이지에서, 마지막 섹션에서 만든 가상 네트워크 및 서브넷을 사용하여 프라이빗 엔드포인트에 대한 설정을 구성하고 **확인** 을 선택합니다. 

1. **다음: 고급** 탭을 선택하거나 페이지 하단에서 **다음: 고급** 단추를 선택합니다.

1. 기본 또는 표준 캐시 인스턴스의 **고급** 탭에서 TLS 포트가 아닌 다른 포트를 사용하도록 설정하려면 사용 토글을 선택합니다.

1. 프리미엄 캐시 인스턴스의 **고급** 탭에서 TLS가 아닌 다른 포트, 클러스터링 및 데이터 지속성에 대한 설정을 구성합니다.

1. **다음: 태그** 탭을 선택하거나 페이지 하단에서 **다음: 태그** 단추를 선택합니다.

1. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다. 

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다. 
    
> [!IMPORTANT]
> 
> 기본값으로 `Disabled`된 `publicNetworkAccess` 플래그가 있습니다. 
> 이 플래그가 `Enabled`로 설정된 경우 선택적으로 퍼블릭 및 프라이빗 엔드포인트 액세스를 모두 허용합니다. `Disabled`로 설정되면 프라이빗 엔드포인트 액세스만 허용됩니다. 값을 `Disabled` 또는 `Enabled`로 설정할 수 있습니다. 값을 변경하는 방법에 대한 자세한 내용은 [FAQ](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)를 참조하세요.
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>기존 Azure Cache for Redis 인스턴스를 사용하여 프라이빗 엔드포인트 만들기 

이 섹션에서는 기존 Azure Cache for Redis 인스턴스에 프라이빗 엔드포인트를 추가합니다. 

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기** 를 선택합니다.

2. **새** 페이지에서 **네트워킹** 을 선택한 다음 **가상 네트워크** 를 선택합니다.

3. **추가** 를 선택하여 가상 네트워크를 만듭니다.

4. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 가상 네트워크를 만들 구독입니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 가상 네트워크 및 기타 리소스를 만들 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **이름** | 가상 네트워크 이름을 입력합니다. | 이름은 문자나 숫자로 시작하고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. | 
   | **지역** | 드롭다운하여 지역을 선택합니다. | 가상 네트워크를 사용할 다른 서비스와 가까이 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

5. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

6. **IP 주소** 탭에서 **IPv4 주소 공간** 을 CIDR 표기법을 따른 하나 이상의 주소 접두사로 지정합니다(예: 192.168.1.0/24).

7. **서브넷 이름** 아래의 **기본값** 을 선택하여 서브넷의 속성을 편집합니다.

8. **서브넷 편집** 창에서 **서브넷 이름** 및 **서브넷 주소 범위** 를 지정합니다. 서브넷의 주소 범위는 CIDR 표기법을 따라야 합니다(예: 192.168.1.0/24). 가상 네트워크의 주소 공간에 포함되어야 합니다.

9. **저장** 을 선택합니다.

10. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

11. 모든 정보가 올바른지 확인하고 **만들기** 를 선택하여 가상 네트워크를 프로비전합니다.

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기 

프라이빗 엔드포인트를 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure Cache for Redis** 를 검색합니다. 그런 다음 <Enter> 키를 누르거나 검색 제안에서 선택합니다.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Azure Cache for Redis를 검색합니다.":::

2. 프라이빗 엔드포인트를 추가하려는 캐시 인스턴스를 선택합니다.

3. 화면 왼쪽에서 **프라이빗 엔드포인트** 를 선택합니다.

4. **프라이빗 엔드포인트** 단추를 선택하여 프라이빗 엔드포인트를 만듭니다.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="프라이빗 엔드포인트를 추가합니다.":::

5. **프라이빗 엔드포인트 만들기 페이지** 에서 프라이빗 엔드포인트에 대한 설정을 구성합니다.

   | 설정      | 제안 값  | 설명 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 프라이빗 엔드포인트를 만들 구독입니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 프라이빗 엔드포인트 및 기타 리소스를 만들 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **이름** | 프라이빗 엔드포인트 이름을 입력합니다. | 이름은 문자 또는 숫자로 시작하고 문자, 숫자 또는 밑줄로 끝나야 하며 문자, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. | 
   | **지역** | 드롭다운하여 지역을 선택합니다. | 프라이빗 엔드포인트를 사용할 다른 서비스와 가까이 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

6. 페이지 하단에서 **다음: 리소스** 단추를 선택합니다.

7. **리소스** 탭에서 구독을 선택하고, 리소스 종류를 `Microsoft.Cache/Redis`로 선택한 다음 프라이빗 엔드포인트를 연결하려는 캐시를 선택합니다.

8. 페이지 하단에서 **다음: 구성** 단추를 선택합니다.

9. **구성** 탭에서 이전 섹션에서 만든 가상 네트워크 및 서브넷을 선택합니다.

10. 페이지 하단에서 **다음: 태그** 단추를 선택합니다.

11. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다.

12. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 탭으로 이동됩니다. 여기서 Azure가 구성의 유효성을 검사합니다.

13. 녹색 **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

> [!IMPORTANT]
> 
> 기본값으로 `Disabled`된 `publicNetworkAccess` 플래그가 있습니다. 
> 이 플래그가 `Enabled`로 설정된 경우 선택적으로 퍼블릭 및 프라이빗 엔드포인트 액세스를 모두 허용합니다. `Disabled`로 설정되면 프라이빗 엔드포인트 액세스만 허용됩니다. 값을 `Disabled` 또는 `Enabled`로 설정할 수 있습니다. 값을 변경하는 방법에 대한 자세한 내용은 [FAQ](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)를 참조하세요.
>
>


## <a name="faq"></a>FAQ

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결되지 않는 이유는 무엇인가요?

캐시가 이미 VNet에 주입된 캐시인 경우 프라이빗 엔드포인트는 캐시 인스턴스와 함께 사용할 수 없습니다. 캐시 인스턴스가 아래에 나열된 지원되지 않는 기능을 사용하고 있는 경우 프라이빗 엔드포인트 인스턴스에 연결할 수 없습니다.

### <a name="what-features-arent-supported-with-private-endpoints"></a>프라이빗 엔드포인트에서 지원되지 않는 기능은 무엇인가요?

현재 포털 콘솔 지원과 방화벽 스토리지 계정에 대한 지속성은 지원되지 않습니다. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>공용 네트워크 액세스에서 프라이빗 엔드포인트를 사용하지 않도록 혹은 사용하도록 설정하려면 어떻게 해야 하나요?

기본적으로 `Disabled` 상태인 `publicNetworkAccess` 플래그가 있습니다. 이 플래그가 `Enabled`로 설정된 경우 캐시에 대한 퍼블릭 및 프라이빗 엔드포인트 액세스를 선택적으로 허용할 수 있습니다. `Disabled`로 설정되면 프라이빗 엔드포인트 액세스만 허용됩니다. Azure Portal에서 또는 Restful API PATCH 요청을 사용하여 값을 `Disabled` 또는 `Enabled`로 설정할 수 있습니다. 

Azure Portal에서 값을 변경하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure Cache for Redis** 를 검색합니다.  그런 다음 <Enter> 키를 누르거나 검색 제안에서 선택합니다.

2. 공용 네트워크 액세스 값을 변경하려는 캐시 인스턴스를 선택합니다.

3. 화면 왼쪽에서 **프라이빗 엔드포인트** 를 선택합니다.

4. **공용 네트워크 액세스 사용** 단추를 선택합니다.

Restful API PATCH 요청을 통해 값을 변경하려면 아래를 참조하여 캐시에 사용할 플래그를 반영하도록 값을 편집합니다.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-link-cache"></a>내 VNet에 삽입된 캐시를 프라이빗 링크 캐시로 마이그레이션하려면 어떻게 해야 하나요?

VNet에 삽입된 캐시를 프라이빗 링크 캐시로 마이그레이션하는 방법에 대한 다양한 접근 방식은 [마이그레이션 가이드](cache-vnet-migration.md)를 참조하세요. 

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>서로 다른 가상 네트워크에 여러 엔드포인트를 사용하려면 어떻게 해야 하나요?

서로 다른 가상 네트워크에 여러 프라이빗 엔드포인트를 사용하려면 프라이빗 엔드포인트를 만들기 _전에_ 프라이빗 DNS 영역을 여러 가상 네트워크에 수동으로 구성해야 합니다. 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요. 

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>내 캐시에서 모든 프라이빗 엔드포인트를 삭제하면 어떻게 되나요?

캐시에서 프라이빗 엔드포인트를 삭제한 후에는 명시적으로 공용 네트워크 액세스를 사용하도록 설정하거나 다른 프라이빗 엔드포인트를 추가할 때까지 캐시 인스턴스에 연결이 불가능할 수 있습니다. Azure Portal에서 또는 RESTFUL API PATCH 요청을 통해 `publicNetworkAccess` 플래그를 변경할 수 있습니다. 값을 변경하는 방법에 대한 자세한 내용은 [FAQ](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)를 참조하세요.

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>프라이빗 엔드포인트에 NSG(네트워크 보안 그룹)를 사용할 수 있나요?

아니요. 프라이빗 엔드포인트에는 사용할 수 없습니다. 프라이빗 엔드포인트를 포함하는 서브넷에 NSG가 연결되어 있을 수 있지만 규칙은 프라이빗 엔드포인트에서 처리하는 트래픽에 적용되지 않습니다. 서브넷에 프라이빗 엔드포인트를 배포하려면 [네트워크 정책 적용을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)해야 합니다. NSG는 동일한 서브넷에서 호스트되는 다른 워크로드에도 적용됩니다. 모든 클라이언트 서브넷의 경로는 /32 접두사를 사용하고 기본 라우팅 동작을 변경하려면 비슷한 UDR이 필요합니다. 

원본 클라이언트의 아웃바운드 트래픽에 대한 NSG 규칙을 사용하여 트래픽을 제어합니다. /32 접두사가 있는 개별 경로를 배포하여 프라이빗 엔드포인트 경로를 재정의합니다. 아웃바운드 연결에 대한 NSG 흐름 로그 및 모니터링 정보는 계속 지원되며 사용 가능합니다.

### <a name="my-private-endpoint-instance-isnt-in-my-vnet-so-how-is-it-associated-with-my-vnet"></a>내 프라이빗 엔드포인트 인스턴스가 VNet에 있지 않은데 VNet과 어떻게 연결되나요?

프라이빗 엔드포인트 인스턴스는 VNet에만 연결됩니다 해당 인스턴스가 VNet에 없으므로 종속 엔드포인트에 대한 NSG 규칙을 수정하지 않아도 됩니다.


## <a name="next-steps"></a>다음 단계

* Azure Private Link에 대한 자세한 정보는 [Azure Private Link 설명서](../private-link/private-link-overview.md)를 참조하세요.
* 캐시 인스턴스에 대한 다양한 네트워크 격리 옵션을 비교하려면 [Azure Cache For Redis 네트워크 격리 옵션 설명서](cache-network-isolation.md)를 참조하세요.
