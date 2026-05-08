## 카메라

### 1. 카메라 조립

- sudo raspi-config
- 처음에 libcamera-hello로 했더니 command not found
- sudo apt update
- sudo apt install libcamera-apps -y
- 이번에는 no cameras available rpicam-hello --list-cameras로 해도 no cameras available
- available cameras 성공

### 2. 센서 조립

### 3. 사진 저장

- 리눅스에 저장된 사진을 가져오기
- Scp pi@raspberrypi:/home/pi/Desktop/*.jpg..

### 4. 최종 코드

#### 1단계: 파일 만들기

- nano motion_camera.py

#### 2단계: 코드 붙여넣기

```python
# 침입자 감지 카메라 프로젝트
# Raspberry Pi 5 + AM312 + Picamera2 버전

# 필요한 라이브러리 불러오기
from gpiozero import Button, DigitalInputDevice
from picamera2 import Picamera2
from time import sleep

# 버튼(GPIO2) 설정
button = Button(2)

# AM312 모션 센서(GPIO4) 설정
pir = DigitalInputDevice(4)

# 카메라 설정
picam2 = Picamera2()
picam2.configure(picam2.create_still_configuration())
picam2.start()

# 사진 번호
i = 0

print("모션 감지 시스템 시작")
print("움직임을 감지하면 사진을 촬영합니다")
print("버튼을 누르면 프로그램이 종료됩니다")

# 프로그램 종료 함수
def stop_program():
    print("프로그램 종료")
    picam2.stop()
    exit()

# 사진 촬영 함수
def take_photo():
    global i

    i += 1

    # 저장 파일 이름
    filename = f"/home/pi/Pictures/image_{i}.jpg"

    # 사진 촬영
    picam2.capture_file(filename)

    print(f"사진 촬영 완료: {filename}")

    # 연속 촬영 방지
    sleep(5)

# 버튼 누르면 종료
button.when_pressed = stop_program

# 계속 반복
while True:

    # 움직임 감지 시
    if pir.value:

        print("움직임 감지!")

        take_photo()

    sleep(0.1)
```

#### 3단계: 저장

- Ctrl + O
- Enter
- Ctrl + X

#### 4단계: 실행

- python3 motion_camera.py
- 동작 방식
- 움직임 감지
- 사진 촬영

#### 5단계: 종료

- 버튼 누르기
