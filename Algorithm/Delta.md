# Delta 
- Java 알고리즘 문제에서 사용 되는 Delta에 대해 기재한다.

<br>

## Delta란?
- N X M 좌표에서 특정 점을 기준으로 이동하고자 할 때 편리하게 사용할 수 있는 알고리즘

<br>

## Example
<img src = "https://github.com/hellokorea/OIL/assets/115388726/43b8dd05-95ff-4f06-b59c-34b4ca80b08c" width="700" height="100%">

### 표 설명
- 4 X 5 좌표가 있다고 가정한다.
- 단, 보다 직관적인 이해를 위해 (x, y) 가 아닌 (y, x)로 표기한다.
  - 만약 y 값이 - 1 된다면, 행이 바뀌는 것이기 때문에 y를 앞쪽에 배치

### 좌표 설명
- (3, 2) 좌표에서 상, 하, 좌, 우 방향으로 1칸씩 탐색한다고 가정한다.
- 이 때 4개의 좌표 결과 값을 편리하게 추출할 수 있는 것이 Delta이다.

<br>

# Code

```Java
  static char[][] map = new char[5][5];

  public static void main(String[] args) {

    char ch = 'A';

    for (int i = 0; i < 5; i++) {
      for (int j = 0; j < 5; j++) {
        map[i][j] = ch++;
      }
    }

    for (int i = 0; i < 5; i++) {
      System.out.println(Arrays.toString(map[i]));
    }

     print4(2, 3); // N
     print4(4, 4); // Y
  }

  static int[] dy4 = { -1, 1, 0, 0 };
  static int[] dx4 = { 0, 0, -1, 1 };

  static void print4(int y, int x) {
    System.out.print(map[y][x] + " : ");

    for (int d = 0; d < 4; d++) {
      int ny = y + dy4[d];
      int nx = x + dx4[d];

      if (ny < 0 || nx < 0 || ny >= 5 || nx >= 5) {
        continue;
      }
      System.out.print(map[ny][nx]);
    }

    System.out.println();
}
```

<br>

# 실행 단계
- 먼저 5 X 5 배열을 만든다. 
- 그 다음 어떤 좌표에서 상, 하, 좌, 우를 탐색할지 함수를 호출한다 print(y, x)
- d의 Index 값을 기준으로 순차적으로 탐색한다 -> 상, 하, 좌, 우

<br>

# 값 추출
<img src = "https://github.com/hellokorea/OIL/assets/115388726/5453b3bb-ba32-449a-93aa-aee5f8492f61" width="300" height="100%">

- 위 같이 5 X 5 좌표 그림이 있다고 가정해보자.
- N의 좌표 값은 -> (y : 2, x : 3)이다.
- 이 때 N의 좌표를 기준으로 사방 탐색을 하게 되면 다음과 같은 값을 얻는다.

<br>

## N -> I, S, M, O 
- 즉, (2, 3)에서 상 : (-1, 0) / 하 : (1, 0) / 좌 : (0, -1) / 우 : (0, 1) 연산이 적용 된 것이다.
- 그리고 값을 출력하기 전에 각 좌표를 찍을 때 해당 값이 유효한지 체크한다.

<br>

### 맨 좌측 및 최상단에 있을 때
- A (0, 0)에 위치한다고 가정 했을 때 (0, -1)를 이동하면 좌표 범위를 벗어난다.
- 그리고 (-1, 0)을 했을 때에도 위로 벗어난다.

<br>

### 맨 우측 및 최하단에 있을 때
- Y (4, 4)에 위치한다고 가정 했을 때 (0, 1)를 이동하면 우측 범위 좌표를 벗어난다.
- 또한 (1, 0)을 하면 하단 범위를 벗어난다.

<br>

### Y 좌표 출력
- 그럼 Y 좌표를 출력한다면 다음과 같이 값이 도출된다.

<br>

## Y -> T, X
- 이유는 위에서 설명했다.

<br>

## 정리
- 쉽게 말해 Delta는 기준이 되는 좌표지점에서 탐색을 쉽게 도와주는 알고리즘 스킬이다.
- 4방 탐색, 8방 탐색, 끝까지 탐색 등 다양한 방법을 통해 접근할 수 있다.
