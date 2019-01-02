# script attribute

외부 스크립트 파일을 다운로드 하면서 생기는 스크립트 로딩 지연으로 인한 병목 현상을 근본적으로 방지하기 위해 HTML5 부터 script 태그에 async와 defer attrubute가 추가되었습니다.

```html
<script async src = "extern.js"></script>
<script defer src = "extern.js"></script>
```

![출처: Peter Reverloo](https://github.com/leafcis/TIL/blob/master/Src/script.jpg?raw=true)

~~~markdown
async
웹페이지 파싱과 외부 스크립트 파일의 다운로드가 동시에 진행됩니다. 스크립트는 다운로드 완료 직후 실행되며, IE9 이하 버전은 지원하지 않습니다.
~~~

~~~markdown
defer
웹페이지 파싱과 외부 스크립트 파일의 다운로드가 동시에 진행되며 스크립트는 웹페이지 파싱 완료 직후 실행됩니다. IE9 이하 버전에서 정상적으로 동작하지 않을 수도 있습니다.
~~~

async와 defer attribute의 차이는 웹페이지 파싱과 외부 스크립트 파일의 다운로드가 동시에 진행된다는 면에서는 동일하나, 스크립트의 실행시점이 다릅니다.

| async                                                        | defer                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 웹 페이지 파싱과 외부 스크립트 파일의 다운로드가 동시에 진행 됨 | 웹 페이지 파싱과 외부 스크립트 파일의 다운로드가 동시에 진행 됨 |
| 스크립트 다운로드 완료 직후 실행                             | 웹페이지 파싱 완료 직후 실행                                 |

