1. 포털에서 **새로 만들기**로 이동한 다음 **찾아보기**를 클릭합니다. 목록에서 **가상 네트워크 게이트웨이**를 선택합니다.
2. **추가**를 클릭합니다.
3. 게이트웨이 이름을 지정합니다. 이는 게이트웨이 서브넷 이름 지정과 동일하지는 않습니다. 이는 게이트웨이 개체의 이름입니다. 
4. **가상 네트워크**에서 이 게이트웨이에 연결하려는 VNet을 선택합니다.
5. VNet을 위한 설정에서 **공용 IP 주소** 값에 대해 공용 IP 주소의 이름을 만듭니다. 이는 IP 주소를 요청하는 것이 아닙니다. IP 주소는 동적으로 할당됩니다. 대신, 이는 주소가 할당될 IP 주소 개체의 이름입니다. 
6. **VPN 유형**의 경우 선택 항목은 정책 기반 및 경로 기반입니다. 구성 시나리오에서 지원하는 VPN 게이트웨이 유형과 구성에 필요하여 사용하려는 VPN 게이트웨이 장치에서 지원하는 VPN 게이트웨이 유형을 모두 선택해야 합니다.
7. **리소스 그룹**의 경우 구성에서 다른 선택이 필요하지 않으면 **기존 선택**을 선택하고 VNet이 있는 리소스 그룹을 선택합니다.
8. **위치**의 경우 리소스 그룹과 VNet이 모두 있는 위치를 표시하는지 확인합니다.
9. **만들기**를 클릭합니다. 대시보드에 *가상 네트워크 게이트웨이 배포* 타일이 표시됩니다. 게이트웨이를 만드는 데 약간의 시간이 걸립니다. 백그라운드에서 많은 시간이 진행됩니다. 15분 이상에 대해 계획합니다. 완료 상태를 확인하기 위해 포털 페이지를 새로 고쳐야 할 수 있습니다.
10. 게이트웨이를 만든 후 포털에서 가상 네트워크를 살펴보면 게이트웨이에 할당된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 장치로 표시됩니다. 게이트웨이에 할당된 이름 및 IP 주소를 볼 수 있습니다.

<!---HONumber=AcomDC_0114_2016-->