### 예약어를 지정해서 편하게 코딩하자
안드로이드 스튜디오는 라이브 템플릿이라는게 있는데 vscode에는 없음.. 
그런데 스니펫이라고 비슷한 역할을 하는 것이 있다. 

코드조각: 사용자 코드 조각 구성
Snippets: Configure User Snippets 

여기에 들어가서 언어 설정하고 작성하면 됨.   

Dart.json파일에 다음과 같이 작성하였다.
```json
{
    "Freezed": {
        "prefix": "dataclass",
        "body": [
            "import 'package:freezed_annotation/freezed_annotation.dart';",
            "",
            "part '${1:filename}.freezed.dart';", 
            "part '${1:filename}.g.dart';",      
            "",
            "@freezed",
            "class ${2:ClassName} with _$${2} {",  
            "  const factory ${2}({",
            "",
            "  }) = _${2};",
            "",
            "  factory ${2}.fromJson(Map<String, Object?> json) => _$${2}FromJson(json);",
            "}"
        ],
        "description": "Freezed class template with camelCase parts"
    },

    "Sealed Freezed": {
        "prefix": "sealed",
        "body": [
            "import 'package:freezed_annotation/freezed_annotation.dart';",
            "part '${1:filename}.freezed.dart';", 
            "",
            "@freezed",
            "sealed class ${2:ClassName}<T> with _$${2}<T> {",
            "   const factory ${2}.success(T data) = Success;",
            "   const factory ${2}.error(String e) = Error;",
            "}"
        ],
        "description": "Freezed class template with camelCase parts"
    },

    "Test Code": {
        "prefix": "testcode",
        "body": [
            "import 'package:test/test.dart';",
            "",
            "void main() {",
            "   group('explainText', () {",
            "       setUp(() {});",
            "",
            "       test('test1', () async {});",
            "   });",
            "}"
        ],
        "description": "test code sample"
    },

    
}
```  

좀 한땀한땀 작성해야하긴한데.. 암튼 prefix 부분이 일종의 예약어 지정 같은 부분. body에 코드 작성.   
