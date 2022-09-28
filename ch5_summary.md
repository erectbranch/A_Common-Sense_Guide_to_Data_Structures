## 5.1 선택 정렬
또 다른 정렬 알고리즘인 선택 정렬(selection sort)를 알아보자. 선택 정렬은 다음과 같은 단계를 따른다.

1. 배열의 각 셀을 왼쪽부터 오른쪽 방향으로 확인하며 어떤 값이 최솟값인지 결정한다. 한 셀씩 이동하면서 현재까지 가장 작은 값을 변수로 저장한다.(실제로는 최솟값의 인덱스까지 저장할 수 있다.) 변수에 들어 있는 값보다 작은 값이 있는 셀을 만나면 변수가 새 인덱스를 가리킨다.

![그림 5-1](image_5-1.png)

2. 이제 최솟값이 어느 인덱스에 들어 있는지 알았으므로 그 인덱스 값과 패스스루를 처음 시작했을 때의 값과 비교한다. 패스스루를 시작했을 때 인덱스는 첫 패스스루에서는 인덱스 0일 것이고, 두 번째 패스스루에서는 인덱스 1일 것이다.

![그림 5-2](image_5-2.png)

3. 데이터가 모두 정렬될 때까지 1,2단계를 반복한다.


---


## 5.2 선택 정렬 실제로 해보기(생략)


---


## 5.3 선택 정렬 구현
다음은 자바스크립트로 구현한 선택 정렬이다.

```Javascript
function selectionSort(array){

    // 선택 정렬의 각 패스스루에 해당하는 바깥 루프.
    for(var i = 0; i < array.length; i++){

        // 현재까지의 최솟값이 든 인덱스를 저장한다.
        var lowestNumberIndex = i;

        // 인덱스 i에 어떤 값이 들어 있든 i를 할당한다.
        // 실제 값이 아닌 '인덱스'임에 유의
        // 첫 번째 패스스루에서는 0, 두 번째에서는 1일 것이다.
        // i+1부터 시작한다. 
        for(var j = i + 1; j < array.length; j++){

            // 현재 찾은 값이 가장 작은 수가 든 인덱스에 저장된 값보다도 작다면
            // 그 인덱스를 lowestNumberIndex로 대체한다.
            if(array[j] < array[lowestNumberIndex]){
                lowestNumberIndex = j;
            }
        }

        // 위 루프가 끝나면 정렬되지 않은 상태에서 최솟값의 인덱스를 아는 상태다
        // 최솟값이 이미 올바른 위치(i)에 있는지 확인.
        // 올바른 위치가 아니라면 최솟값과 array[i]를 교환한다.
        if(lowestNumberIndex != i){
            var temp = array[i];
            array[i] = array[lowestNumberIndex];
            array[lowestNumberIndex] = temp;
        }
    }
    
    return array;
}

```


---


## 5.4 선택 정렬의 효율성
일반적으로 원소 N개가 있을 때 (N - 1) + (N - 2) + (N - 3)... + 1 번의 비교이다.

이와 달리 교환은 한 패스스루 당 최대 한 번 일어난다. 각 패스스루마다 최솟값이 이미 올바른 위치에 있는지 여부에 따라 교환을 안 하거나 교환을 한 번 하기 떄문이다. 배열이 역순으로 정렬된 최악의 시나리오에서는 버블 정렬과 달리 비교할 떄마다 빠짐없이 교환해야 한다. 

다음 표는 버블 정렬과 선택 정렬을 나란히 비교한 것이다.

![표 5-2](table_5-2.png)

표를 보면 선택 정렬은 버블 정렬보다 단계 수가 반 정도 작다. 즉 선택 정렬이 대체로 두 배 더 빠르다.


---


## 5.5 상수 무시하기
하지만 빅 오 표기법에서는 선택 정렬과 버블 정렬을 정확히 같은 방식으로 설명한다. 둘은 똑같이 O($N^2$)이다.

사실 선택 정렬의 효율성을 따지자면 O($N^2$/2) 정도일 것이다. 하지만 빅 오 표기법은 다음과 같은 규칙을 갖기 때문에 선택 정렬과 버블 정렬을 똑같이 표기한다.

> 빅 오 표기법은 상수를 무시한다.


---


## 5.6 빅 오의 역할
빅 오는 버블 정렬과 선택 정렬 간에 차이를 두지 않지만, 알고리즘의 장기적인 증가율을 분류하는 훌륭한 방법이기에 여전히 매우 중요하다.


---


## 5.7 실제 예제
배열을 입력으로 받아서 두 원소마다 하나 걸러 하나를 뽑아 배열을 생성하는 루비 애플리케이션을 작성한다고 하자.

```Ruby
def every_other(array)
    new_array = []

    array.each_with_index do |element, index|
        new_array << element if index.even?
    end

    return new_array

end
```

위 구현은 입력받은 배열의 각 원소를 순회하며 그 원소의 인덱스가 짝수인 경우에만 새 배열에 원소를 추가한다.

배열의 모든 원소를 하나씩 살펴보므로 배열 룩업(lookup)은 N번이다. 반면 새 배열에는 두 원소마다 하나씩 원소를 삽입하므로 N/2 번의 삽입만 수행한다. 따라서 N + N/2로 O(1.5N)이지만 빅 오 표기법은 상수를 무시하므로 단순히 O(N)이다.

위 알고리즘 대신 배열의 각 원소를 순회하며 인덱스가 짝수인지 확인하는 대신 처음부터 배열의 짝수 인덱스만 룩업할 수 있다.

```Ruby
def every_other(array)
    new_array = []
    index = 0

    while index < array.length
        new_array << array[index]
        index += 2
    end

    return new_array

end
```

두 번째 구현은 각 원소를 하나씩 확인하는 대신 while 루프를 이용해 건너뛰며 읽는다. 따라서 원소가 N개일 때 N/2 번의 룩업과 N/2 번의 새로운 배열로의 삽입을 수행한다. 따라서 이 알고리즘도 O(N)이다.

하지만 역시 두 번째 알고리즘이 훨씬 빠르다. 


---