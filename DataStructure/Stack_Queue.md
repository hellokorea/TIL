# 스택과 큐 자료구조
- 일반적인 컬렉션 자료구조인 스택과 큐에 대해 기재한다.

<br>

# Stack
## Stack이란?
- Stack은 자료구조 중 하나로써 LIFO 즉, 후입선출의 성질을 갖고있다.
- 가장 마지막에 있는 요소가 먼저 제거 된다.
- 스택은 최상위 요소가 가장 먼저 접근하기 때문에 추가 및 삭제의 시간 복잡도는 O(1)을 갖는다.

<br>

## 예시 그림
<img src = "https://github.com/hellokorea/project-nowmeet-node/assets/115388726/70d1c506-e6fb-469e-902c-4a4a950d221d" width="200" height="100%">

<br>

## 코드 구현
- 사실 별도의 알고리즘 구현없이 배열의 메소드를 이용하여 활용이 가능하다.
```
class Stack {
  constructor() {
    this.item = [];
  }

  push(element) {
    this.item.push(element);
  }

  pop() {
    return this.item.pop();
  }

  peek() {
    return this.item[this.item.length - 1];
  }

  isEmpty() {
    return this.item.length === 0;
  }

  size() {
    return this.item.length;
  }

  clear() {
    this.item = [];
  }
}
```

<br>

## 활용 알고리즘
- 10진수의 수를 2진수로 바꿀 때 활용할 수 있다.
```
function divideBy2(decNumber) {
  let remStack = new Stack();
  let rem;
  let binaryString = "";

  while (decNumber > 0) {
    rem = Math.floor(decNumber % 2);
    remStack.push(rem);

    decNumber = Math.floor(decNumber / 2);
  }

  while (!remStack.isEmpty()) {
    binaryString += remStack.pop().toString();
  }

  return binaryString;
}
// 1010
```


<br>

# Queue
## Queue란?
- Stack과 다르게 FIFO 즉, 선입선출 특징을 갖고있다.
- 가장 맨 앞에있는 요소가 가장 먼저 제거된다.
- 추가 및 삭제 연산에는 시간 복잡도 O(1)을 갖는다.

<br>

## 예시 그림
<img src = "https://github.com/hellokorea/project-nowmeet-node/assets/115388726/cd728943-139f-4889-b29d-54d29dbea429" width="400" height="100%">

<br>

## 코드 구현
```
class Queue {
  constructor() {
    this.items = [];
  }

  enqueue(element) {
    this.items.push(element);
  }

  dequeue() {
    return this.items.shift();
  }

  front() {
    return this.items[0];
  }

  isEmpty() {
    return this.items == 0;
  }

  size() {
    return this.items.length;
  }

  clear() {
    this.items = [];
  }
}
```

<br>

## 활용 알고리즘
- 환형 큐를 이용해 뜨거운 감자 알고리즘을 쉽게 해결할 수 있다.
```
function hotPotato(nameList, num) {
  let queue = new Queue();

  for (let i = 0; i < nameList.length; i++) {
    queue.enqueue(nameList[i]);
  }

  let elminated = "";

  while (queue.size() > 1) {
    for (let i = 0; i < num; i++) {
      queue.enqueue(queue.dequeue());
    }

    elminated = queue.dequeue();

    console.log(`${elminated} 을 퇴장시킵니다`);
  }

  return queue.dequeue();
}

const names = ["John", "Jack", "Camila", "Ingrid", "Carl"];
const winner = hotPotato(names, 7);
console.log("승자는 " + winner + "입니다.");

//
Camila 을 퇴장시킵니다
Jack 을 퇴장시킵니다
Carl 을 퇴장시킵니다
Ingrid 을 퇴장시킵니다
승자는 John입니다.
```