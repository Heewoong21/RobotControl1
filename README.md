# humanoidMotionControl_python

### 오늘 수업은 이전에 완성한 aiAPP의 Detection 결과에 따라

## 로봇이 움직이도록 하는 과정이다.

### 1. 로봇 제어를 위해서 어떤 방법이 있는가?

유 / 무선 통신을 이용한 제어

유선 통신의 기준이 되는 UART 통신에 대해서 알아 보자

파일 doc/26. UART통신알아보기_2025.pptx을 참고 하세요   


### 2. 프로토콜이란?

모든 기기간에는 통신규약(Protocol)이 있다.

휴머노이드 로봇의 모션제어 프로토콜을 알아 보자

모션을 제어 하기 위한 프로토콜은 15byte 프로토콜로 되어 있다. 

![2025 로봇 AI_사진_20250512_1](https://github.com/user-attachments/assets/da18d07c-93b0-4561-8472-4c98a6fc9513)
실제 소스 코드에서 구현된 것은 다음과 같다. 
```
import serial

def execute_motion(port, motion_id, parent=None):
    # 모션 제어 기본 패킷 생성
    packet_buff = [0xff, 0xff, 0x4c, 0x53,  # 헤더
                   0x00, 0x00, 0x00, 0x00,  # Destination ADD, Source ADD
                   0x30, 0x0c, 0x03,        # 0x30 실행 명령어 0x0c 모션실행 0x03 파라메타 길이
                   motion_id, 0x00, 0x64,   # 모션 ID, 모션 반복, 모션 속도 지정
                   0x00]                    # 체크섬

    # 체크섬 계산
    checksum = 0
    for i in range(6, 14):
        checksum += packet_buff[i]
    packet_buff[14] = checksum

    # 시리얼 포트 열기
    try:
        ser = serial.Serial(port, 115200, timeout=1)
        if ser.is_open:
            # 패킷 전송
            ser.write(packet_buff)
        else:
            raise serial.SerialException(f"Failed to open serial port {port}.")
    except serial.SerialException as e:
        # 경고 메시지 박스 표시
        if parent:
            print(f"Serial Port Error", str(e))
        else:
            print(f"Serial Port Error: {e}")
    finally:
        if 'ser' in locals() and ser.is_open:
            ser.close()

```

각각의 모션 번호가 의미 하는 동작은 다음을 참고 하세요 
파일 doc/27. 휴머노이드모션테이블.pdf를 참고 하세요 


### 3. pySerial 이란?

파이썬에서 외부기기(휴머노이드)를 제어 하기 위한 방법

코드를 실행 하는 방법은 다음과 같다. 


  3.1. git clone 하거나 다운로드 받아 압축 풀고 새폴더에서
  
  3.2. code로 열기

  3.3. 가상환경과 연결하기

  3.4. requirements.txt로 패키지를 설치 한다. 
```
pip install -r requirements.txt
```

USB 동글을 연결하고 com3가 활성화된 상태에서 선택된

동글이 연결 되었는데 추가 com port가 안보이는 경우

아래 댓글에 있는 cp-2104 드라이버를 설치 해야 한다.

장치 관리자 - port에 아래와 같이 보여야 한다.

![2025 로봇 AI_사진_20250512_6](https://github.com/user-attachments/assets/663a30f4-5ece-41ac-89a8-1b29f62188d3)


최종 완성된 app

![2025 로봇 AI_사진_20250512_7](https://github.com/user-attachments/assets/de8ccb84-4085-4649-89de-193e3ac9ea1d)

![2025 로봇 AI_사진_20250512_8](https://github.com/user-attachments/assets/2d85fa9c-8743-4103-ac84-4da6952fd3ad)

## 다음 시간에는

4. aiAPP에 통합 하기?

사물 인식을 한 결과에 따라서 로봇이 어떤 동작을 취할 수 있게 한다. 

모듈 2개만 옮겨서 만들면 될것 같음 
![image](https://github.com/user-attachments/assets/da7666cd-67d3-4e29-b256-30047ece76b8)

