---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>필수 필드. 자세한 내용은 aka.ms/skyeye/meta를 참조하세요.
제목: Streaming Endpoints (Origin) : Azure Media Services 설명: 콘텐츠를 클라이언트 플레이어 앱 또는 CDN(Content Delivery Network)으로 직접 배달하는 동적 패키징 및 스트리밍 서비스인 스트리밍 엔드포인트(원본)에 대해 알아봅니다. services: media-services documentationcenter: '' author: IngridAtMicrosoft 관리자: femila editor: ''

ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 02/13/2020 ms.author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure Media Services의 스트리밍 엔드포인트(원본)

Microsoft Azure Media Services에서 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)는 일반적인 스트리밍 미디어 프로토콜(HLS 또는 DASH)을 사용하여 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 앱에 직접 배달할 수 있는 원본 서비스와 동적(Just-In-Time) 패키징을 나타냅니다. 또한 **스트리밍 엔드포인트** 는 업계 최고의 DRM에 동적(Just-In-Time) 암호화를 제공합니다. 

Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. 계정으로 더 많은 스트리밍 엔드포인트를 만들 수 있습니다([할당량 및 제한](limits-quotas-constraints-reference.md) 참조).

> [!NOTE]
> 비디오 스트리밍을 시작하려면 비디오를 스트리밍하려는 **스트리밍 엔드포인트** 를 시작해야 합니다.
>
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.

또한 [동적 패키징](encode-dynamic-packaging-concept.md) 항목을 검토해야 합니다. 

## <a name="naming-convention"></a>명명 규칙

스트리밍 URL의 호스트 이름 형식은 `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`이며, 여기서 `servicename`은 스트리밍 엔드포인트 이름 또는 라이브 이벤트 이름입니다.

기본 스트리밍 엔드포인트를 사용하는 경우 `servicename` 이 생략되므로 URL은 `{accountname}-{regionname}.streaming.azure.net`입니다.

### <a name="limitations"></a>제한 사항

* 스트리밍 엔드포인트 이름은 최대 24자입니다.
* 이름은 `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`와 같은 [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 따라야 합니다.

## <a name="types"></a>형식

**표준**(미리 보기) 및 **프리미엄** 의 두 가지 **스트리밍 엔드포인트** 유형이 있습니다. 이러한 유형은 스트리밍 엔드포인트에 할당하는 배율 단위(`scaleUnits`) 수로 정의됩니다.

최대 스트리밍 단위 한도는 일반적으로 10입니다. 계정에 대한 한도를 높이려면 [여기](https://azure.microsoft.com/support/create-ticket/)로 문의하세요.

다음 표에 해당 유형이 설명되어 있습니다.

|형식|배율 단위|설명|
|--------|--------|--------|  
|**Standard**|0|기본 스트리밍 엔드포인트는 **표준** 유형이며 `scaleUnits`를 조정하여 프리미엄 유형으로 변경할 수 있습니다.|
|**Premium**|>0|**프리미엄** 스트리밍 엔드포인트는 고급 워크로드에 적합하며, 스케일링 가능한 전용 대역폭 용량을 제공합니다. `scaleUnits`(스트리밍 단위)를 조정하여 **프리미엄** 유형으로 변경합니다. `scaleUnits`는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형을 사용할 경우 사용 설정된 각 단위가 앱에 추가 대역폭 용량을 제공합니다. |

> [!NOTE]
> 대규모 인터넷 대상 그룹에 콘텐츠를 배달하려는 고객은 스트리밍 엔드포인트에서 CDN을 사용하는 것이 좋습니다.

SLA에 대한 정보는 [가격 책정 및 SLA](https://azure.microsoft.com/pricing/details/media-services/)를 참조하세요.

## <a name="comparing-streaming-types"></a>스트리밍 유형 비교

기능|Standard|Premium
---|---|---
처리량 |최대 600Mbps이며, CDN을 사용하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.|SU(스트리밍 단위)당 200Mbps CDN을 사용하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.
CDN|Azure CDN, 타사 CDN 또는 CDN 없음.|Azure CDN, 타사 CDN 또는 CDN 없음.
청구를 계산합니다.| 매일|매일
동적 암호화|예|예
동적 패키징|예|예
확장|대상 처리량까지 자동으로 확장합니다.|추가 SU
IP 필터링/G20/사용자 지정 호스트 <sup>1</sup>|예|예
점진적 다운로드|예|예
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다.

<sup>1</sup> 엔드포인트에서 CDN을 사용하도록 설정하지 않은 경우 스트리밍 엔드포인트에서만 직접 사용됩니다.<br/>

### <a name="versions"></a>버전

|형식|StreamingEndpointVersion|ScaleUnits|CDN|결제|
|--------------|----------|-----------------|-----------------|-----------------|
|클래식|1.0|0|해당 없음|무료|
|표준 스트리밍 엔드포인트(미리 보기)|2.0|0|예|유료|
|프리미엄 스트리밍 단위|1.0|>0|예|유료|
|프리미엄 스트리밍 단위|2.0|>0|예|유료|

### <a name="features"></a>기능

기능|Standard|Premium
---|---|---
처리량 |최대 600Mbps이며, CDN을 사용하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.|SU(스트리밍 단위)당 200Mbps CDN을 사용하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.
CDN|Azure CDN, 타사 CDN 또는 CDN 없음.|Azure CDN, 타사 CDN 또는 CDN 없음.
청구를 계산합니다.| 매일|매일
동적 암호화|예|예
동적 패키징|예|예
확장|대상 처리량까지 자동으로 확장합니다.|추가 스트리밍 단위입니다.
IP 필터링/G20/사용자 지정 호스트 <sup>1</sup>|예|예
점진적 다운로드|예|예
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다. 

<sup>1</sup> 엔드포인트에서 CDN을 사용하도록 설정하지 않은 경우 스트리밍 엔드포인트에서만 직접 사용됩니다.<br/>

SLA에 대한 정보는 [가격 책정 및 SLA](https://azure.microsoft.com/pricing/details/media-services/)를 참조하세요.

## <a name="migration-between-types"></a>유형 간의 마이그레이션

시작 | 대상 | 작업
---|---|---
클래식|Standard|옵트인할 필요
클래식|Premium| 크기 조정(추가 스트리밍 단위)
표준/프리미엄|클래식|사용할 수 없음(스트리밍 엔드포인트 버전이 1.0인 경우 scaleunits을 "0"으로 설정하도록 클래식을 변경할 수 있음)
표준(CDN 포함/없이)|동일한 구성을 포함한 프리미엄|**시작됨** 상태에서 허용됩니다. (Azure Portal을 통해)
프리미엄(CDN 포함/없이)|동일한 구성을 포함한 표준|**시작됨** 상태에서 허용됩니다(Azure Portal을 통해).
표준(CDN 포함/없이)|다른 구성을 포함한 프리미엄|**중지됨** 상태에서 허용됩니다(Azure Portal을 통해). 실행 상태에서 허용되지 않습니다.
프리미엄(CDN 포함/없이)|다른 구성을 포함한 표준|**중지됨** 상태에서 허용됩니다(Azure Portal을 통해). 실행 상태에서 허용되지 않습니다.
CDN 포함 SU >= 1인 버전 1.0|CDN 없는 표준/프리미엄|**중지됨** 상태에서 허용됩니다. **시작됨** 상태에서 허용되지 않습니다.
CDN 포함 SU >= 1인 버전 1.0|CDN 포함된/없는 표준|**중지됨** 상태에서 허용됩니다. **시작됨** 상태에서 허용되지 않습니다. CDN 버전 1.0을 삭제하고 새로 만들어서 시작합니다.
CDN 포함 SU >= 1인 버전 1.0|CDN 포함된/없는 프리미엄|**중지됨** 상태에서 허용됩니다. **시작됨** 상태에서 허용되지 않습니다. 클래식 CDN을 삭제하고 새로 만들어서 시작합니다.







## <a name="streaming-endpoint-properties"></a>스트리밍 엔드포인트 속성

이 섹션에서는 일부 스트리밍 엔드포인트의 속성에 대한 자세한 정보를 제공합니다. 새 스트리밍 엔드포인트를 만드는 방법의 예제와 모든 속성에 대한 설명을 보려면 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints/create)를 참조하세요.

- `accessControl`: 스트리밍 엔드포인트에 대한 보안 설정(Akamai 서명 헤더 인증 키 및 엔드포인트 연결이 허용되는 IP 주소)을 구성하는 데 사용됩니다. 이 속성은 `cdnEnabled`가 false로 설정된 경우에만 설정할 수 있습니다.

- `cdnEnabled`: 스트리밍 엔드포인트에 대한 Azure CDN 통합을 사용할 수 있는지를 나타냅니다(기본값으로 사용 안 함). `cdnEnabled`를 true로 설정하면 `customHostNames` 및 `accessControl` 구성이 사용되지 않도록 설정됩니다.

    모든 데이터 센터에서 Azure CDN 통합을 지원하는 것은 아닙니다. 데이터 센터에서 Azure CDN 통합을 사용할 수 있는지 확인하려면 다음 단계를 수행합니다.

  - `cdnEnabled`를 true로 설정합니다.
  - “현재 지역에서 CDN 기능을 사용할 수 없으므로 스트리밍 엔드포인트 CdnEnabled 속성을 true로 설정할 수 없습니다.” 메시지와 함께 `HTTP Error Code 412`(PreconditionFailed)에 대해 반환된 결과를 확인합니다.

    이 오류가 발생하면 데이터 센터에서 이를 지원하지 않습니다. 다른 데이터 센터를 사용해 보세요.

- `cdnProfile`: `cdnEnabled`가 true로 설정된 경우 `cdnProfile` 값도 전달할 수 있습니다. `cdnProfile`은 CDN 엔드포인트이 생성되는 CDN 프로필의 이름입니다. 기존 cdnProfile을 제공하거나 새로 만들 수 있습니다. 값이 NULL이고 `cdnEnabled`가 true이면 기본값 “AzureMediaStreamingPlatformCdnProfile”이 사용됩니다. 제공된 `cdnProfile`이 이미 있으면 그 아래에 엔드포인트가 생성됩니다. 프로필이 없으면 새 프로필이 자동으로 만들어집니다.
- `cdnProvider`: CDN을 사용하도록 설정된 경우 `cdnProvider` 값을 전달할 수도 있습니다. `cdnProvider`는 사용할 공급자를 제어합니다. 현재, "StandardVerizon", "PremiumVerizon" 및 "StandardAkamai"의 세 가지 값이 지원됩니다. 값이 제공되지 않고 `cdnEnabled`가 true인 경우 “StandardVerizon”(기본값)이 사용됩니다.
- `crossSiteAccessPolicies`: 다양한 클라이언트에 사이트 간 액세스 정책을 지정하는 데 사용됩니다. 자세한 내용은 [도메인 간 정책 파일 사양](https://www.adobe.com/devnet-docs/acrobatetk/tools/AppSec/CrossDomain_PolicyFile_Specification.pdf) 및 [여러 도메인 간에 서비스를 사용하도록 지정](/previous-versions/azure/azure-services/gg185950(v=azure.100))을 참조하세요. 설정은 부드러운 스트리밍에만 적용됩니다.
- `customHostNames`: 스트리밍 엔드포인트를 구성하는 데 사용되어 사용자 지정 호스트 이름으로 리디렉션되는 트래픽을 허용합니다. 이 속성은 표준 및 프리미엄 스트리밍 엔드포인트에서 유효하며 `cdnEnabled`: false일 때 설정할 수 있습니다.

    Media Services에서 도메인 이름의 소유권을 확인해야 합니다. Media Services는 Media Services 계정 ID를 포함하는 `CName` 레코드를 사용 중인 도메인에 추가할 구성 요소로 요구하여 도메인 이름 소유권을 확인합니다. 예를 들어 “sports.contoso.com”을 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용하려면 Media Services 확인 호스트 이름 중 하나를 가리키도록 `<accountId>.contoso.com`에 대한 레코드를 구성해야 합니다. 확인 호스트 이름은 verifydns.`\<mediaservices-dns-zone>`으로 구성됩니다.

    다음은 다른 Azure 지역의 확인 레코드에서 사용할 것으로 예상되는 DNS 영역입니다.
  
  - 북아메리카, 유럽, 싱가포르, 홍콩 특별행정구, 일본:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 중국:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    예를 들어 “945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com”을 “verifydns.media.azure.net”에 매핑하는 `CName` 레코드는 Azure Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad가 contoso.com 도메인의 소유권을 가지므로 contoso.com 아래의 모든 이름을 해당 계정 아래의 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용할 수 있음을 입증합니다. 미디어 서비스 ID 값을 찾으려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 미디어 서비스 계정을 선택합니다. **계정 ID** 는 페이지의 오른쪽 위에 표시됩니다.

    `CName` 레코드를 적절히 확인하지 않고 사용자 지정 호스트 이름을 설정하려고 하면 DNS 응답이 실패하고 일정 시간 동안 캐시됩니다. 적절한 레코드가 준비되고 나면, 캐시된 응답의 유효성이 다시 확인될 때까지 잠시 시간이 소요될 수 있습니다. 사용자 지정 도메인에 대한 DNS 공급자에 따라, 레코드의 유효성이 다시 확인되기까지 몇 분에서 1시간 정도 걸립니다.

    `<accountId>.<parent domain>`를 `verifydns.<mediaservices-dns-zone>`에 매핑하는 `CName` 외에도 사용자 지정 호스트 이름(예: `sports.contoso.com`)을 Media Services 스트리밍 엔드포인트의 호스트 이름(예: `amstest-usea.streaming.media.azure.net`)에 매핑하는 다른 `CName`을 만들어야 합니다.

    > [!NOTE]
    > 동일한 데이터 센터에 있는 스트리밍 엔드포인트는 동일한 사용자 지정 호스트 이름을 공유할 수 없습니다.

    현재 Media Services는 사용자 지정 도메인을 사용하는 TLS를 지원하지 않습니다.

- `maxCacheAge` - 미디어 조각 및 주문형 매니페스트에서 스트리밍 엔드포인트가 설정한 기본 최대 기간 HTTP 캐시 제어 헤더를 재정의합니다. 이 값은 초 단위로 설정됩니다.
- `resourceState` -

    - 중지됨: 스트리밍 엔드포인트를 만든 후의 초기 상태입니다.
    - 시작 중: 실행 상태로 전환되고 있습니다.
    - 실행 중: 클라이언트에 콘텐츠를 스트리밍할 수 있습니다.
    - 스케일링: 배율 단위가 증가하거나 감소하고 있습니다.
    - 중지 중: 중지됨 상태로 전환되고 있습니다.
    - 삭제 중: 작업이 삭제되는 중입니다.

- `scaleUnits`: 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형으로 전환해야 할 경우 `scaleUnits`를 조정합니다.

## <a name="why-use-multiple-streaming-endpoints"></a>여러 스트리밍 엔드포인트를 사용하는 이유

단일 스트리밍 엔드포인트는 라이브 및 주문형 비디오를 모두 스트리밍할 수 있으며, 대부분의 고객은 하나의 스트리밍 엔드포인트만 사용합니다. 이 섹션에서는 여러 스트리밍 엔드포인트를 사용해야 하는 이유의 몇 가지 예시를 제공합니다.

* 각 예약 단위는 200Mbps의 대역폭을 허용합니다. 2,000Mbps(2Gbps) 이상의 대역폭이 필요한 경우 두 번째 스트리밍 엔드포인트와 부하 분산을 사용하여 추가 대역폭을 제공할 수 있습니다.

    그러나 CDN은 스트리밍 콘텐츠를 스케일 아웃하는 가장 좋은 방법이지만 너무 많은 콘텐츠를 배달하여 CDN이 2Gbps 이상 끌어오는 경우에는 추가 스트리밍 엔드포인트(origin)를 추가할 수 있습니다. 이 경우 두 스트리밍 엔드포인트에서 분산을 사용하는 콘텐츠 URL을 전달해야 합니다. 이 방법은 임의로 각 원본에 요청을 전송(예: Traffic Manager를 통해 전송)하는 것보다 더 나은 캐싱을 제공합니다. 
    
    > [!TIP]
    > 일반적으로 CDN이 2Gbps 이상을 끌어오는 경우 잘못 구성된 것일 수 있습니다(예: 원본 실딩 없음).
    
* 다른 CDN 공급자의 부하를 분산합니다. 예를 들어 Verizon CDN을 사용하도록 기본 스트리밍 엔드포인트를 설정하고 Akamai를 사용하는 두 번째 스트리밍 엔드포인트를 만들 수 있습니다. 그런 다음, 다중 CDN 분산을 얻기 위해 둘 사이에 부하 분산을 추가합니다. 

    그러나 고객은 단일 원본을 사용하여 여러 CDN 공급자 간 부하 분산을 수행하는 경우가 많습니다.
* 혼합 콘텐츠 스트리밍: 라이브 및 주문형 비디오입니다. 

    라이브 및 주문형 콘텐츠에 대한 액세스 패턴은 매우 다릅니다. 라이브 콘텐츠는 동일한 콘텐츠에 한 번에 많은 요청을 가져오는 경향이 있습니다. 주문형 비디오 콘텐츠(인스턴스의 롱테일 비상 보관 콘텐츠)는 동일한 콘텐츠에 대한 사용량이 적습니다. 따라서 캐싱은 라이브 콘텐츠에서 매우 잘 작동하지만 롱테일 비상 콘텐츠에서는 잘 작동하지 않습니다.

    고객이 라이브 콘텐츠를 주로 시청하지만 가끔 주문형 콘텐츠를 시청하고 이것이 동일한 스트리밍 엔드포인트에서 제공되는 시나리오를 고려해 봅시다. 주문형 콘텐츠의 낮은 사용량이 라이브 콘텐츠를 더 잘 저장할 수 있는 캐시 공간을 차지하게 됩니다. 이 시나리오에서는 하나의 스트리밍 엔드포인트에서 라이브 콘텐츠를 제공하고 다른 스트리밍 엔드포인트에서 롱테일 콘텐츠를 제공하는 것이 좋습니다. 이렇게 하면 라이브 이벤트 콘텐츠의 성능이 향상됩니다.
    
## <a name="scaling-streaming-with-cdn"></a>CDN을 사용하여 스트리밍 크기 조정

다음 문서를 참조하세요.

- [CDN 개요](../../cdn/cdn-overview.md)
- [CDN을 사용하여 스트리밍 크기 조정](stream-scale-streaming-cdn-concept.md)

## <a name="ask-questions-and--get-updates"></a>질문하고 업데이트 가져오기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

[동적 패키징](encode-dynamic-packaging-concept.md)

## <a name="next-steps"></a>다음 단계

[스트리밍 엔드포인트 관리](stream-manage-streaming-endpoints-how-to.md)
