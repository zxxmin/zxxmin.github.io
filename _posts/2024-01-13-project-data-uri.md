---
title: 😀 아이콘을 Data URI로 변경
date: 2024-01-13 00:00:01
categories:
- 트러블슈팅
- CSS
tag:
- 트러블슈팅
- CSS
---

# 이미지 최적화
메인급 서비스들을 개발하면서 느낀 점 중 하나는 생각보다 이미지 파일들이 많이 필요하다. 주로 간단한 아이콘들이 많다.<br/>
많은 아이콘들을 간단하게 관리를 하기 위해서는 여러가지 방법이 있다. 대표적으로 이미지 스프라이트 기법을 주로 사용하는 것 같았다. 

그런데 담당님께서 아이콘들을 전부 SVG로 추출해서 URL 인코딩을 하시고 SCSS 파일에 넣으셨다.<br/>
왜 이렇게 번거러운 작업을 하시는지 궁금해서 혼자 이것저것 찾아보면서 공부를 하다가 이유를 찾았다.

아마, 이미지 최적화 때문이지 않을까 생각한다.<br/>
새로고침 시 이미지는 계속 불러와 지는데, SVG를 인코딩 하는 방식은 Data URI로 변경을 하면 브라우저에 캐싱이 되어 새로고침 시 
크기와 시간을 줄여 이미지 최적화를 할 수 있다.

SVG를 인코딩하여 Data URI를 만드는 과정을 하나씩 공부해봤다.

# 아이콘 PNG → SVG → URL 인코딩 SVG
## 1. Figma에서 SVG로 Export
![](/assets/images/troubleshooting/datauri-1.png)

**주의 사항** : 아이콘들이 **'면'** 으로 되어있어야 한다.
**이유** : SVG 장점 중 하나는 CSS로 색상, 크기 등을 쉽게 변경할 수 있다. **아이콘들의 색상을 쉽게 변경**을 하려면 `fill` 속성을 
사용하기 때문에 **'선'** 으로 되어있는 아이콘들은 `Outline stroke`, 윤곽선 만들기로 변경하고 그래도 안되는 경우는 디자이너에게 요청하였다.

![](/assets/images/troubleshooting/datauri-2.png)

## 2. 확장자 .svg로 다운받아진 파일을 메모장이나 편집 가능한 곳에서 열기 후 불필요한 내용 수정
- fill로 설정되어 있는 것들은 다 지워주면 된다.
![](/assets/images/troubleshooting/datauri-3.png)

## 3. SVG 코드들을 encoded
- [URL-encoder for SVG](https://yoksel.github.io/url-encoder/) 사이트 애용.

## 4. Insert SVG에 &lt;svg&gt;&lt;/svg&gt;를 먼저 작성 후 메모장에 &lt;svg&gt; 태그를 제외하고 복사 + 붙여넣기
- Preview에서 잘 변환 되었는지 확인을 꼼꼼히 해준다.
  ![](/assets/images/troubleshooting/datauri-4.png)

## 5. Take encoded 에 있는 코드 복사하기

## 6. SCSS 변수를 지정해서 사용할 클래스명을 key 값으로, encoded 된 코드들은 value 값으로 지정
```scss
$icons : (
	"ico-file" : "%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M26.165 23.6C26.2033 23.6001 26.2407 23.5885 26.2726 23.5667C26.3045 23.5449 26.3294 23.5139 26.3443 23.4776C26.3591 23.4412 26.3632 23.4012 26.356 23.3625C26.3488 23.3238 26.3307 23.2882 26.3039 23.26L22.4949 19.274C22.4679 19.2457 22.4332 19.2264 22.3954 19.2184C22.3576 19.2105 22.3184 19.2143 22.2827 19.2293C22.247 19.2444 22.2165 19.2701 22.195 19.303C22.1735 19.336 22.1621 19.3748 22.1621 19.4145V23C22.1621 23.1592 22.2235 23.3118 22.3329 23.4243C22.4422 23.5368 22.5905 23.6 22.7451 23.6H26.165Z' /%3E%3Cpath d='M21.239 24.55C21.0395 24.3474 20.8809 24.1061 20.7725 23.8401C20.6641 23.574 20.608 23.2885 20.6074 23V18.4H17.1094C16.2855 18.4025 15.496 18.7405 14.9134 19.3401C14.3308 19.9396 14.0024 20.7521 14 21.6V32.8C14 33.6487 14.3276 34.4626 14.9107 35.0627C15.4938 35.6629 16.2847 36 17.1094 36H24.1055C24.9301 36 25.721 35.6629 26.3042 35.0627C26.8873 34.4626 27.2149 33.6487 27.2149 32.8V25.2H22.7451C22.4648 25.1996 22.1873 25.1419 21.9288 25.0304C21.6702 24.9188 21.4358 24.7555 21.239 24.55ZM29.7412 17.2H33.1611C33.1994 17.2001 33.2368 17.1885 33.2687 17.1667C33.3006 17.1449 33.3255 17.1139 33.3404 17.0776C33.3552 17.0412 33.3593 17.0012 33.3521 16.9625C33.345 16.9238 33.3268 16.8882 33.3 16.86L29.491 12.874C29.464 12.8457 29.4293 12.8264 29.3915 12.8184C29.3537 12.8105 29.3145 12.8143 29.2788 12.8293C29.2431 12.8444 29.2126 12.8701 29.1911 12.903C29.1697 12.936 29.1582 12.9748 29.1582 13.0145V16.6C29.1582 16.7592 29.2197 16.9118 29.329 17.0243C29.4383 17.1368 29.5866 17.2 29.7412 17.2Z' /%3E%3Cpath d='M29.7412 18.8C29.1756 18.7955 28.6344 18.5623 28.2344 18.1507C27.8345 17.7391 27.6079 17.1821 27.6035 16.6V12H22.3565C21.5836 12.0009 20.8427 12.3173 20.2962 12.8797C19.7497 13.4421 19.4423 14.2046 19.4414 15V16.8H21.4878C21.749 16.8011 22.0074 16.8552 22.2482 16.9594C22.489 17.0636 22.7074 17.2157 22.8909 17.407L28.1943 22.957C28.5643 23.3433 28.7709 23.8645 28.7691 24.407V31.2H31.3465C32.9259 31.2 34.2105 29.854 34.2105 28.2V18.8H29.7412Z' /%3E%3C/svg%3E"
)
```

변환 끝

# HOW? : 사용 방법
사용하기 전에 어떻게 아이콘이 보여진다는건지 원리를 알고 싶었다.

## 문자 조합 함수
```scss
@function str-replace($string, $search, $replace: '') {
    $index: str-index($string, $search);

    @if $index {
        @return str-slice($string, 1, $index - 1)+$replace+str-replace(str-slice($string, $index + str-length($search)), $search, $replace);
    }

    @return $string;
}
```

### str-replace ( $string, $search, $replace )
- **용도** : 문자 값의 일부를 다른 문자 값으로 대체하는 SCSS 내장 함수
- **매개변수** : 함수에 변경할 문자, 찾을 문자, 변경할 문자
![](/assets/images/troubleshooting/datauri-5.png)

### str-index ( $string, $substring )
![](/assets/images/troubleshooting/datauri-6.png)
- **용도** : $string 내에서 $substring의 첫 번째 인덱스를 반환. 포함하지 않으면 null 반환
![](/assets/images/troubleshooting/datauri-7.png)

### str-slice ( $string, $start-at, $end-at )
![](/assets/images/troubleshooting/datauri-8.png)
- **용도** : $string에서 $start-at 인덱스에서 시작하여 $end-at 인덱스에서 끝나는 (양쪽 모두 포함) 부분 반환
![](/assets/images/troubleshooting/datauri-9.png)

### str-length ( $string )
![](/assets/images/troubleshooting/datauri-10.png)
- **용도** : $string에 있는 문자의 수를 반환
![](/assets/images/troubleshooting/datauri-11.png)

## 아이콘 Data URI 조합 함수
```scss
@function pathIcon($icon : map-get($icons,"ico-search"), $color : $white, $size : 24) {
    $result: "data:image/svg+xml,%3Csvg version='1.1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' viewBox='0 0 " + $size + " " + $size + "' xml:space='preserve'%3E";
    $result: $result + "%3Cg fill='"+str-replace(''+ $color, '#', '%23')+"'%3E";
    $result: $result + $icon;
    $result: $result + "%3C/g%3E%3C/svg%3E";
    @return $result;
}
```

### map-get ($map, $key, $keys… )
![](/assets/images/troubleshooting/datauri-12.png)
- **용도** : $map 안에 있는 $key의 value 값을 반환, 없으면 null 반환
![](/assets/images/troubleshooting/datauri-13.png)

### $result: $result + "%3Cg fill='"+str-replace(''+ $color, '#', '%23')+"'%3E";
- SVG 코드에서 fill=”#000000” 을 URL 인코딩 시키면 fill='%23000000’ 이런식으로 HTML 태그의 ‘<’, ’>’, ’#’ 같은 문자가 URL 인코딩 방식으로 변환된다.
```bash
'<' === '%3c'
'>' === '%3E'
'#' === '%23'
```

#### str-replace(''+ $color, '#', '%23')
- 이부분은 ‘#’을 ‘%23’으로 바꾸기 위한 작업.

$color → #000000 으로 가정하면,
1.  ‘’ + $color  : 이 표현식은 SCSS에서 ‘#000000’을 문자열로 취급하기 위해 빈문자열 ‘’ 을 더한 것.
2. str-replace('#000000', '#', '%23')
   $string → ‘#000000’
   $search → ‘#’
   $replace → ‘%23’
3. str-index(’#000000’, ‘#’)
   $index → 1
4. str-slice(’#000000’, 1, 0) → 빈문자열 ‘ ’
5. str-slice(’#000000’, 1 + str-length(‘#’))
   str-length(‘#’)) → 1
   str-slice(’#000000’, 2) → 000000
6. str-replce(’000000’, ‘#’, ‘%23’)
   $string → ‘000000’
   $search → ‘#’
   $replace → ‘%23’
7. str-index(’000000’, ‘#’)
   $index → null
8. @if $index → 조건이 false 일 때 재귀 호출이 종료
9. str-slice($string, 1, $index - 1) → ‘ ‘
   $replace → %23
   str-replace(str-slice($string, $index + str-length($search)), $search, $replace) → 000000
   ’’+%23+000000
10. @return → ‘%23000000’

```scss
$icoHasEl: '.btn, .chip, .input';

[class*="ico-"]:not(#{$icoHasEl}) {
    display: inline-flex;
    vertical-align: middle;
    flex: 0 0 auto;
    width: $iconM;
    height: $iconM;
    background-repeat: no-repeat;
    background-position: center;
    background-size: cover;
    font-size:0;
    &.sm {
        width: $iconSm;
        height: $iconSm;
    }
    &.lg {
        width: $iconLg;
        height: $iconLg;
    }
    @each $cls, $icon in $icons {
        &.#{$cls} {
            background-image: url(pathIcon(map-get($icons,#{$cls}), $grayColor5));
        }
        &.spot.#{$cls} {
            background-image: url(pathIcon(map-get($icons,#{$cls}), $spot)) !important;
        }
        &.spot2.#{$cls} {
            background-image: url(pathIcon(map-get($icons,#{$cls}), $spot2)) !important;
        }
        &.spot3.#{$cls} {
            background-image: url(pathIcon(map-get($icons,#{$cls}), $spot3)) !important;
        }
        &.white.#{$cls} {
            background-image: url(pathIcon(map-get($icons,#{$cls}), $white)) !important;
        }
        &.black.#{$cls} {
            background-image: url(pathIcon(map-get($icons,#{$cls}), $black)) !important;
        }
    }
}
```

# WHY? : 왜 이렇게 사용하는지
- 이미지가 많다면 이미지의 용량을 줄이고 유지보수를 위해서는 다양한 방법이 있다. 
  대표적으로는 이미지 스트라이프 기법도 있는데, 왜 굳이 이렇게 사용하는지 궁금해서 몇가지 생각해보고 찾아보았다.

## SVG 코드 장단점
### 장점
1. **파일 크기 감소**<br/>
   SVG 코드는 텍스트 형식이므로 일반적으로 크기가 작아 페이지 로딩 속도가 빨라질 수 있음.
2. **확장성**<br/>
   SVG는 백터 형식. 해상도에 상관없이 이미지사 선명하게 유지. 반응형에 유리.
3. **스타일**<br/>
   CSS를 사용하여 SVG의 색상, 크기 등을 쉽게 변경 가능
4. **적은 HTTP 요청**<br/>
   이미지를 직접 코드에 포함시킴으로써 추가적인 HTTP 요청을 줄일 수 있어 성능 향상
5. **DOM 내에서 조작 용이**<br/>
   SVG는 DOM의 일부로 취급. JS를 통해 동적으로 수정하거나 애니메이션 적용 용이

### 단점
1. **HTML이나 CSS 파일 복잡**
2. **데이터 보안 이슈**<br/>
   외부에서 제공받은 SVG 코드는 보안문제가 생길 수 있음

## 이미지 파일 장단점
### 장점
1. **일반적인 사용**<br/>
   대부분 개발자가 사용할 수 있어서 급할때는 변경이 가능
2. **복잡한 그래픽 표현**<br/>
3. 이미지 파일이 별도로 관리되므로 **명확한 파일 관리**

### 단점
1. **해상도 제약**<br/>
   비트맵 형식인 PNG, JPG는 고해상도로 확대 시 깨질 수 있음.
2. **파일 크기 증가**<br/>
   고해상도 이미지는 파일 크기가 커져 페이지 로딩 속도를 저하
3. **반응형 제약**

# 결론
- **SVG 코드** → 주로 아이콘이나 간단한 그래픽을 사용할 때 유리. 반응형 디자인이나 스타일 변경이 필요한 경우 적합
- **이미지 파일** → 복잡한 그래픽이나 사진과 같이 정교한 이미지 표현이 필요할 때 유용