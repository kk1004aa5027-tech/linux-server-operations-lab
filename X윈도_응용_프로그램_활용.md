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



## 3. 노틸러스(Nautilus) 사용

### 3-1. 폴더 생성 및 사진 추가
- Client 가상머신에서 노틸러스를 실행한다.
- 원하는 위치에 새 폴더를 생성한다.
- 사진 파일을 Brasero 프로그램으로 드래그 앤 드롭한다.

### 3-2. ISO 파일 확인
- 노틸러스에서 ISO 파일을 확인한다.
- ISO 파일이 정상적으로 생성되었는지 확인한다.



## 4. 파이어 폭스(Firefox) 업데이트

### 4-1. 다운로드한 압축 파일 해제
다운로드한 Firefox 압축 파일을 해제한다.

```
cd 다운로드 /
tar xfj firefox-70.0.1.tar.bz2
```

### 4-2. Firefox 설치 경로로 이동
root 계정으로 전환한 후 Firefox 디렉터리를 /usr/local/로 이동한다.

```
su 
mv firefox  /usr/local/
chown -R root.root  /usr/local/firefox/
```

### 4-3. 파일 소유자 변경
Firefox 디렉터리와 내부 파일의 소유자를 root로 변경한다.

```
chown -R root.root /usr/local/firefox/
```

### 4-4. 심볼릭 링크 생성
Firefox 실행 파일을 /usr/local/bin/에서 실행할 수 있도록 심볼릭 링크를 생성한다.
```
cd /usr/local/bin/
ln -s /usr/local/firefox/firefox .
```

- 재부팅 후 부팅 화면에서 변경된 배경화면을 확인한다.

