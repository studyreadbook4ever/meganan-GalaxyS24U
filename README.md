# greatFinalSpec
괴물같은 포트폴리오를 원해서 스스로 괴물이 되어보았다
갤럭시 S24Ultra에 탑재된 NPU를 최대로 끌어다가 뭐라도 해보자.

# 이론

컴파일러 백엔드, 이기종 컴퓨팅서 최적화는 합법성-구현가능성-수익성(그래서 이득인가?)
깔끔하게 딱 떨어지진 않는 병렬처리에서 loop carried dependence - dependence distance 좀 두는게
NPU는 본질적으로 **SIMD**된 연산들이 결합된 packet에 대해 이 한 패킷에 여러 명령이 **VLIW**형태로 되어있는것

작업마다 고정된 Latency가 있고 이 Latency를 고려한 Initial Communication이 직접 개발자가 신경써줘야 하는 부분. initiation interval 높이고 딱 딱 맞추는 interlock stall 적극활용
HVX에서 고려할거 호출-주소매핑-가시성과 동기화-수명과 사용권한 고려해가면서 marshalling

Latency 때문에 지연되는 일 최대한 논리적으로 줄여서 최적화 자체가 빛을 크게 바랠 수 있도록! 
DSP 실행-DSP 전용 출력을 주고 별개로 CPU fallback시에 DSP로 나온 값과 별개의 처리가 필요함. 작업 ID로 구분이 되어야 하고 유효한 결과 하나만 publish해야한다.
이렇게 하는 이유는 설계할 때 FREE-READY-IN_FLIGHT-COMPLETED-CONSUMED-FREE 사이클에서 IN_FLIGHT가 너무 이상하리만치 길어질때 급하게 CPU fallback을 하게 되는데 이러면 하나의 명령이 2개의 결과로써 전달이 되어버림!!

계산시에 양자화된 값의 계산 최적화 <- 이건 컴파일러가 자동으로 해주는 면이 강하다고 해도 이론은 빠삭하게 알아주자!! 그리고 saturation(오버플로우 방지하는 천장 두기) vs 비트수 늘려서 표현할건지 이 지점에 대한 최적화도 이론 공부 따로 많이 해줘야함
실행 메서드/입력버퍼/출력버퍼/매개변수 
