# Flutter StatefulWidget 생명주기
플러터가 statefulWidget을 만들 때, State객체를 만드는데 이 곳은 위젯안에서 '변하는 상태'가 유지되는 곳이다.
- 위젯이 사용하는 데이터는 변경될 수 있다
- 하지만 위젯이 빌드 될 때 데이터를 동시에 읽을 순 없다
- 따라서 모든 state는 build() 가 호출되기 전까지 설정 되어야 한다  

## StatefulWidget과 State 는 엄연히 분리되어있다. 왜?
State는 오래 유지되지만 위젯은 구성이 변경 될때마다 폐기하고 다시 빌드된다. 플러터에서는 위젯 재생성에 대한 코스트가 낮기 때문에 state와 widget을 분리하는 것은 다양한 이점을 가져온다. 
- 성능 최적화 (Widget은 UI 구조를 정의, State는 데이터와 로직을 관리)
- 데이터 지속성 (state가 유지되므로 중요한 데이터 혹은 복잡한 계산 결과를 보존할 수 있음)
- 효율적인 UI 업데이트
- 애니메이션 가능

## 1. createState()
- 플러터가 StatefulWidget을 빌드하도록 지시하면 즉시 [createState()]가 호출. 
- 반드시 존재해야 한다
- statefulWidget을 생성하면 자동으로 같이 코드를 만들어줌
```dart
  @override
  State<MainScreen> createState() => _MainScreenState();
``` 

## 2.State 객체가 마운트됨
- createState가 state클래스를 생성하면 buildContext는 state에 할당.
- BuildContext는 위젯이 배치된 위젯 트리의 위치를 단순화 한 것.
- 모든 위젯은 bool형식의 this.mounted 속성을 가지고 있음. 
- buildContext가 할당되면 true를 리턴. 
- 위젯이 unmounted상태일때 setState를 호출하면 error가 발생.

## 3. initState()
- 위젯이 생성될 때 단 한 번만 호출됨
- 항상 `super.initState()`를 먼저 호출해야 함
- initState에서 실행되면 좋은 것들
  - 생성된 위젯 인스턴스의 BuildContext에 의존적인 것들의 데이터 초기화
  - 동일 위젯트리내에 부모위젯에 의존하는 속성 초기화
  - Stream 구독, 알림변경, 또는 위젯의 데이터를 변경할 수 있는 다른 객체 핸들링.

## 4. didChangeDependencies()
- `initState()` 직후와 위젯의 의존성이 변경될 때마다 호출 (업데이트되는 위젯을 상속한 경우라든지)
- `context`를 사용하여 의존성에 접근 가능
- 상속한 위젯이 업데이트 될때 네트워크 호출이 필요한 경우 유용
- InheritedWidget을 사용할 때 특히 중요. 예를 들어, Theme.of(context) 또는 MediaQuery.of(context)를 사용할 때 이 메서드가 호출됨

## 5. build()
- 위젯의 UI를 구성
- 상태가 변경될 때마다 호출 (걍 자주 호출됨)
- 필수이자 오버라이드 대상 -> 반드시 위젯 리턴
- 상태를 변경하거나 비동기 작업을 시작하는 등의 작업은 여기서 하면 안됨

## 6. didUpdateWidget(Widget oldWidget)
- 부모 위젯이 재구성되어 이 위젯을 업데이트할 때 호출
- 이전 위젯과 새 위젯을 비교하여 필요한 업데이트를 수행
- `oldWidget` 매개변수를 통해 이전 위젯에 접근 가능
- 플러터는 오래동안 유지 되는 state를 다시 사용하기 때문에 존재함

## 7. setState()
- State의 메서드이며, 엄밀히 따지면 생명주기 메서드 아님. 하지만 상태 관리에 중요한 역할을 한다.
- 상태를 변경하고 UI를 업데이트하기 위해 호출
- '데이터가 변경되었음’을 프레임워크에 알리는데 사용
- build context의 위젯을 다시 빌드
- setState() 내에서 실행되는 코드는 동기적이어야 한다. 
- 비동기 작업의 결과로 상태를 업데이트할 때는 주의가 필요.

## 8. deactivate()
- tree에서 State가 제거 될때 호출(같은 프레임 내에서 다시 삽입 가능)
- State객체가 tree의 한 지점에서 다른 지점으로 이동 할 수 있기 때문에 존재하지만 거의 사용 x

## 9. dispose()
- 위젯이 영구적으로 제거될 때 호출
- 리소스 해제, 구독 취소 등의 정리 작업에 사용
- 메모리 누수 방지를 위해 중요 

## 10. State 객체가 언마운트됨
- unmount된 state 객체는 결코 다시 mount되지 않으며, setState()가 호출되면 에러가 발생  


## 11. reassemble()
- hot reload될 때 호출.
- 개발중에 디버깅 목적으로 사용  


## 생명주기 시간순 정리
```
1. createState()
   |
2. mounted == true
   |
3. initState()
   |
4. didChangeDependencies()
   |
5. build()
   |
   |   <--- 위젯 활성 상태 --->
   |
   |   setState() 호출 시:
   |       |
   |       build()
   |
   |   의존성 변경 시:
   |       |
   |       didChangeDependencies()
   |       |
   |       build()
   |
   |   부모 위젯 재구성 시:
   |       |
   |       didUpdateWidget()
   |       |
   |       build()
   |
   |   개발 중 Hot Reload 시:
   |       |
   |       reassemble()
   |       |
   |       didUpdateWidget() (필요시)
   |       |
   |       build()
   |
6. deactivate() (위젯이 트리에서 제거될 때, 다시 삽입될 수 있음)
   |
   (다시 삽입되면 didChangeDependencies()로 돌아감)
   |
7. dispose()
   |
8. mounted == false
```