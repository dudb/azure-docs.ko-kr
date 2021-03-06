Azure에서 가용성 그룹 수신기를 구성하려면 두 가지 방법을 실현하는 것이 중요합니다. 이러한 방법에서는 수신기를 만들 때 사용하는 Azure 부하 분산 장치 형식이 다릅니다. 다음 표에 차이점이 나와 있습니다.

| 부하 분산 장치 | 구현 | 사용 시기: |
| --- | --- | --- |
| **외부** |가상 컴퓨터를 호스트하는 클라우드 서비스의 **공용 가상 IP 주소**를 사용합니다. |인터넷을 포함하여 가상 네트워크의 외부에서 수신기에 액세스해야 합니다. |
| **내부** |이 수신기에 대한 개인 주소와 함께 **ILB(내부 부하 분산)**를 사용합니다. |동일한 가상 네트워크 내에서만 수신기에 액세스합니다. 하이브리드 시나리오의 사이트 간 VPN을 포함합니다. |

> [!IMPORTANT]
> 클라우드 서비스의 공용 VIP(외부 부하 분산 장치)를 사용하는 수신기의 경우 클라이언트, 수신기, 데이터베이스가 동일한 Azure 지역에 있을 때에는 송신 비용이 발생하지 않습니다. 그렇지 않고 수신기를 통해 반환된 데이터는 송신으로 간주되며 정상 데이터 전송 요율로 청구됩니다.
> 
> 

ILB는 지역 범위의 가상 네트워크에만 구성할 수 있습니다. 선호도 그룹에 대해 구성된 기존 가상 네트워크는 ILB를 사용할 수 없습니다. 자세한 내용은 [내부 부하 분산 장치](../articles/load-balancer/load-balancer-internal-overview.md)를 참조하세요.

