

## <a name="memory-preserving-updates"></a>메모리 유지 업데이트
Microsoft Azure의 업데이트 클래스는 고객이 실행 중인 가상 컴퓨터에는 어떠한 영향도 주지 않습니다. 이러한 많은 업데이트는 실행 중인 인스턴스를 방해하지 않고 업데이트할 수 있는 구성 요소 또는 서비스에 대한 업데이트입니다. 이러한 업데이트 중 일부는 가상 컴퓨터를 완전히 다시 부팅하지 않고 적용할 수 있는 호스트 운영 체제의 플랫폼 인프라 업데이트입니다.

이러한 업데이트는 원위치 실시간 마이그레이션을 가능하게 하는 기술(“메모리 유지” 업데이트)를 통해 완수됩니다. 업데이트 시 기본 호스트 운영 체제가 필요한 업데이트 및 패치를 받는 동안 가상 컴퓨터는 "일시 중지" 상태로 들어가 RAM의 메모리를 유지합니다. 가상 컴퓨터는 일시 중지 후 30초 내에 다시 시작됩니다. 다시 시작된 후 가상 컴퓨터의 시계가 자동으로 동기화됩니다.

이 메커니즘을 사용하여 모든 업데이트를 배포할 수 있는 것은 아니지만 짧은 일시 중지 시간을 지정한 경우 이 방식으로 업데이트를 배포하면 가상 컴퓨터에 미치는 영향을 크게 줄일 수 있습니다.

다중 인스턴스 업데이트(가용성 집합의 가상 컴퓨터에 대한)는 한 번에 하나의 업데이트 도메인에 적용됩니다.  

## <a name="virtual-machine-configurations"></a>가상 컴퓨터 구성
가상 컴퓨터 구성에는 다중 인스턴스 구성 및 단일 인스턴스 구성이 있습니다. 다중 인스턴스 구성에서 유사한 가상 컴퓨터는 한 개의 가용성 집합에 배치됩니다.

다중 인스턴스 구성은 물리적 컴퓨터, 전원 및 네트워크에 대한 중복성을 제공하고 응용 프로그램의 가용성을 보장하기 위해 권장됩니다. 가용성 집합의 모든 가상 컴퓨터는 응용 프로그램에 동일한 목적을 충족시켜야 합니다.

고가용성을 위해 가상 컴퓨터를 구성하는 방법에 대한 자세한 내용은 [Windows 가상 컴퓨터의 가용성 관리](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [Linux 가상 컴퓨터의 가용성 관리](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

이와 반대로 단일 인스턴스 구성은 가용성 집합에 배치되지 않은 독립 실행형 가상 컴퓨터에 사용됩니다. 이러한 가상 컴퓨터는 동일한 가용성 집합 하에서 배포된 둘 이상의 가상 컴퓨터를 요구하는 SLA(서비스 수준 계약)를 충족하지 못합니다.

SLA에 대한 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)의 "클라우드 서비스, 가상 컴퓨터 및 가상 네트워크" 섹션을 참조하세요.

## <a name="multi-instance-configuration-updates"></a>다중 인스턴스 구성 업데이트
계획된 유지 관리를 수행하는 동안 Azure 플랫폼은 다중 인스턴스 구성 내에 호스팅되는 가상 컴퓨터 집합을 우선 업데이트합니다. 그러면 이러한 가상 컴퓨터가 다시 부팅되며 약 15분 동안 가동이 중지됩니다.

다중 인스턴스 구성 업데이트에서 가상 컴퓨터는 프로세스 전체에서 가용성이 유지되는 방식으로 업데이트되며, 각각의 가상 컴퓨터는 집합 내의 다른 컴퓨터와 비슷한 기능을 제공하는 것으로 간주됩니다.

기본 Azure 플랫폼에서는 가용성 집합에 포함된 각각의 가상 컴퓨터를 업데이트 도메인 및 장애 도메인에 할당합니다. 각각의 업데이트 도메인은 동일한 시간에 다시 부팅되는 가상 컴퓨터 그룹입니다. 각각의 장애 도메인은 공통된 전원 및 네트워크 스위치를 공유하는 가상 컴퓨터 그룹입니다.

업데이트 도메인 및 장애 도메인에 대한 자세한 내용은 [중복성을 위해 가용성 집합에 여러 가상 컴퓨터 구성](../articles/virtual-machines/virtual-machines-windows-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)을 참조하세요.

업데이트 도메인이 동시에 오프라인으로 전환되는 것을 방지하기 위하여, 유지 관리는 업데이트 도메인에 포함된 각각의 가상 컴퓨터를 종료하고 호스트 컴퓨터에 업데이트를 적용하고 가상 컴퓨터를 다시 시작하고 다음 업데이트 도메인으로 이동하는 방식으로 수행됩니다. 계획된 유지 관리 이벤트는 모든 업데이트 도메인이 업데이트된 후 종료됩니다.

다시 시작되는 업데이트 도메인의 순서는 계획된 유지 관리 중에 순차적으로 진행되지 않을 수 있지만 한 번에 하나의 업데이트 도메인만 다시 부팅됩니다. 현재 Azure는 다중 인스턴스 구성에서 가상 컴퓨터에 대해 계획된 유지 관리의 1주 사전 알림을 제공합니다.

가상 컴퓨터가 복원된 후 Windows 이벤트 뷰어에 표시될만한 내용의 예는 다음과 같습니다.

<!--Image reference-->
![][image2]

뷰어를 사용하여 가상 컴퓨터가 Azure 포털, Azure PowerShell, 또는 Azure CLI를 사용하여 다중 인스턴스 구성에서 구성되었는지 확인합니다. 예를 들어, 가상 컴퓨터가 다중 인스턴스 구성에 해당하는 지 확인하려면 가상 컴퓨터 찾아보기 대화 상자에 추가된 가용성 집합 열을 사용하여 가상 컴퓨터 목록을 검색할 수 있습니다. 다음 예에서 Example-VM1 가상 컴퓨터와 Example-VM2 가상 컴퓨터는 다중 인스턴스 구성에 해당합니다.

<!--Image reference-->
![][image4]

## <a name="single-instance-configuration-updates"></a>단일 인스턴스 구성 업데이트
다중 인스턴스 구성 업데이트가 완료된 후 Azure는 단일 인스턴스 구성 업데이트를 수행합니다. 이 업데이트에서도 가용성 집합에서 실행되지 않는 가상 컴퓨터가 다시 부팅됩니다.

가용성 집합에서 하나의 인스턴스만 실행하더라도 Azure 플랫폼은 이를 다중 인스턴스 구성 업데이트로 취급합니다.

단일 인스턴스 구성의 가상 컴퓨터는 가상 컴퓨터를 종료하고 호스트 컴퓨터에 업데이트를 적용한 후 가상 컴퓨터를 다시 시작하여 업데이트됩니다(약 15분의 가동 중시 시간). 이러한 업데이트는 단일 유지 관리 기간에 특정 지역의 모든 가상 컴퓨터에서 실행됩니다.

이러한 계획된 유지 관리 이벤트는 이러한 유형의 가상 컴퓨터 구성에서 응용 프로그램의 가용성에 영향을 미칩니다. Azure는 단일 인스턴스 구성에서 가상 컴퓨터에 대해 계획된 유지 관리의 1주 사전 알림을 제공합니다.

## <a name="email-notification"></a>메일 알림
단일 인스턴스 및 다중 인스턴스 가상 컴퓨터 구성인 경우에만 Azure가 향후 계획된 유지 관리에 대해 알려주기 위해 1주 전에 메일을 전송합니다. 이 메일은 구독 관리자 및 공동 관리자 메일 계정으로 전송됩니다. 이러한 유형의 메일의 예는 아래와 같습니다.

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>지역 쌍
유지 관리를 실행할 때 Azure는 단일 지역의 해당 쌍이 되는 가상 컴퓨터 인스턴스만 업데이트합니다. 예를 들어 미국 중북부에 있는 가상 컴퓨터를 업데이트할 때 Azure는 미국 중남부의 가상 컴퓨터를 동시에 업데이트하지 않습니다. 이는 별도의 다른 시간에 예약하여 지역 간의 장애 조치(failover) 또는 부하 분산을 사용하도록 설정합니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다.

현재 지역 쌍에 대한 자세한 내용은 아래 테이블을 참조하세요.

| 지역 1 | 지역 2 |
|:--- | ---:|
| 미국 중북부 |미국 중남부 |
| 미국 동부 |미국 서부 |
| 미국 동부 2 |미국 중부 |
| 북유럽 |서유럽 |
| 동남아시아 |동아시아 |
| 중국 동부 |중국 북부 |
| 일본 동부 |일본 서부 |
| 브라질 남부 |미국 중남부 |
| 오스트레일리아 남동부 |오스트레일리아 동부 |
| 인도 중부 |인도 남부 |
| 인도 서부 |인도 남부 |
| 미국 정부 아이오와 |미국 정부 버지니아 |

<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/AvailabilitySetExample.png


<!--Link references-->
[Virtual Machines 가용성 관리]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[계획된 유지 관리 및 계획되지 않은 유지 관리 이해]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md#Understand-planned-versus-unplanned-maintenance/



<!--HONumber=Nov16_HO3-->


