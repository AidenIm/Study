## 우분투 한영/Alt 맵핑 변경하기

터미널을 열고 (ctrl +alt + t) 아래 명령을 입력한다.

```shell
xmodmap -e 'remove mod1 = Alt_R'
xmodmap -e 'keycode 108 = Hangul'

xmodmap -e 'remove control = Control_R'
xmodmap -e 'keycode 105 = Hangul_Hanja'

xmodmap -pke > ~/.Xmodmap
```

