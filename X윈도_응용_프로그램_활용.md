## 1. 배경화면 바꾸기

### 1-1. 툴 다운받기

```
su -c 'dnf -y install gnome-tweak-tool'
```

### 1-2. 툴 실행하기

```
gnome-tweaks
```

-테마와 인터페이스 배경 등 변경가능

## 2. GRUB 부팅 화면 배경화면 변경

### 2-1. 원하는 이미지 파일 이동

```
cd 다운로드/
```
- 다운로드한 이미지를 GRUB 설정 디렉터리로 이동한다.
- 
```
su -c 'mv wall.png   /boot/grub2'
```

### 2-2. GRUB 설정 파일 수정
```
su -c 'gedit /etc/default/grub'
```

### GRUB 설정 파일 수정

```
#GRUB_TERNUBAK OUTPUT="console"
GRUB_BACKGROUND= "/boot/grub2/wall.png"

```
-GRUB_TERMINAL_OUTPUT 항목을 주석 처리한다.
-GRUB_BACKGROUND 항목을 추가하여 배경 이미지 경로를 지정한다.




### 2-3. 설정 적용
```
su -c 'grub2-mkconfig -o /boot/grub2/grub.cfg'
```

- GRUB 설정 파일을 다시 생성한다.
- 재부팅 후 부팅 화면에서 변경된 배경화면을 확인한다.

