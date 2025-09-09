
단말 Host Checker란, 사용자의 컴퓨터(단말)가 조직의 보안 정책을 준수하는지 확인하는 소프트웨어 에이전트입니다. 주로 [SSL VPN](https://www.google.com/search?num=10&newwindow=1&sca_esv=d2b59b7238d5f6ec&cs=0&sxsrf=AE3TifPup6QGMqTtwzfCAhjVAHZcdwHuIQ%3A1757400992743&q=SSL+VPN&sa=X&ved=2ahUKEwjo5YLvjMuPAxW6cfUHHS-RFFkQxccNegQIBBAB&mstk=AUtExfBsE2WWCC6UtYfubqjg0lNwFT8_bRX9qq5_l4pF8hWnkbZsvj3-DKOmF-H3UtgmJ0jaRExRVch9ewe7UlE2KRJRGFdFxCn645uQ8EWfF-sRrJH6L0pGlQeYADPeIrNh8ckFIJiaSqmhQGGKQiE39ak0vVn6csu0SYk01jqTEBbEWhiOcorX6d56tIkISK7tPCIutQZ1uQnH8pm6ak5QzChJgoG210PtKy5q7Mr6B51SgvDNkNSnHpspxSbY4w37RHNZyNiGbOnIOdUr17dHpYiZ&csui=3)과 같은 네트워크 접속 시, 백신 소프트웨어의 버전, 운영체제(OS) 정보, 하드디스크 암호화 상태, 패치 적용 여부 등을 점검하여 네트워크 접속을 허용하거나 거부하는 역할을 합니다. 

Host Checker의 주요 기능 

- **보안 상태 확인**: 설치된 백신 소프트웨어의 업데이트 여부, 스캔 이력, 안티스파이웨어 설치 여부 등을 확인합니다.
- **운영체제 정보**: 단말의 OS 버전과 패치 적용 상태를 점검합니다.
- **하드웨어 보안**: 하드디스크 암호화 설정 여부를 확인할 수 있습니다.
- **정책 기반 검증**: 미리 정의된 보안 정책이나 사용자가 직접 설정한 커스텀 규칙에 따라 단말의 보안 상태를 검증합니다.

Host Checker가 사용되는 이유

- **보안 강화**:
    
    허가되지 않거나 보안이 취약한 단말의 네트워크 접속을 차단하여 조직의 내부 자산을 보호합니다. 
    
- **정책 준수**:
    
    직원들이 반드시 지켜야 하는 보안 정책(예: 최신 백신 설치, OS 업데이트)을 강제하여 보안 수준을 유지합니다. 
    

간단히 말해, 단말 Host Checker는 조직의 네트워크에 접속하려는 컴퓨터가 '안전한' 상태인지 확인하는 보안 지킴이 역할을 한다고 볼 수 있습니다. 

**사용자 사용 환경(보안 적합성) 제어**

마지막으로 살펴볼 것은 사용자의 사용 환경(보안 적합성) 제어입니다. SSL VPN에 접속하는 사용자의 PC 환경을 확인하는 것은 아주 중요합니다. SSL VPN에 접속하는 사용자의 PC는 여태 내부 네트워크에 소속되었던 단말이 아닌 외부에서 공인인터넷과 같은 환경에 노출되어 있던, 보안 위험 요소를 다수 내재할 가능성이 있는 단말입니다. 만일 PC가 랜섬웨어 혹은 바이러스에 감염되어 있거나 매우 취약한 운영체제 등을 사용하고 있다면 SSL VPN 접속 시 심각한 보안 위협을 맞닥뜨릴 수 있죠. VPN을 타고 내부 리소스에 접근하는 순간, 바이러스가 퍼지는 것은 순식간일 것입니다. 그렇기에 SSL VPN은 접속하고자 하는 사용자의 PC 사용 환경(보안 적합성)을 면밀히 검토하고 허용되는 기준에 부합하는 사용자만이 접근이 가능하도록 합니다. 그리고 이를 정책 설정을 통해 실현하지요. 

SSL VPN 정책 설정을 위해 사용자의 사용 환경(보안 적합성), 컴퓨터에 많이 확인하는 사항은 사용자 컴퓨터의 **OS 버전**과 패치 현황, **백신 설치 여부**, 스파이웨어 감염 여부 등입니다. 그리고 VPN이 세운 기준, 다시 말해 일정 버전 이상의 OS 버전이나 특정 백신 설치, 스파이웨어 미검출 등에 부합하는 사용자만이 VPN을 접근할 수 있도록 허용합니다. 만약 사용 환경이 보안 기준을 충족하지 않는다면 접근을 거부하거나 필요한 백신 등을 설치할 수 있는 사이트로 Redirect(이하 리다이렉트)하기도 하죠.

![[./img/Host_Checker_Overview.png]]

출처 : https://docs.pulsesecure.net/WebHelp/PCS/9.1R8/AG/Content/PCS/PCS_AdminGuide/Host_Checker_Overview.htm

위 그림은 Pulse Secure(Ivanti) SSL VPN의 사용자 환경 점검 기능인 Host Checker에서 정책(Policy)을 통해 확인할 수 있는 요소 일부를 표로 표현한 것입니다. 자세히 보면 안티바이러스, 방화벽, OS 버전 등을 확인하고 정책에 반영할 수 있는 것을 볼 수 있죠. 또 F5 Networks 社의 APM 정책 설정 과정에서도 사용자의 사용 환경(보안 적합성)을 검증하는 정책을 설정하는 것을 아래 그림을 통해 볼 수 있습니다.

![[./img/f5networks_apm_policy_user_env_hostchecker.png]]

출처 : https://loadbalancing.se/