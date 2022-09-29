# 11 노드 기반 자료 구조


---


## 11.1 연결 리스트
연결 리스트(linked list)는 배열과 마찬가지로 항목의 리스트를 표현하는 자료 구조다. 사용자가 어떤 어플리케이션에서 배열을 사용 중이라면 아마도 배열 대신 연결 리스틀 쓰고 있을 가능성이 높다.

맨 처음 설명했듯 컴퓨터에 들어 있는 메모리는 데이터 조각을 저장하는 셀들의 거대한 집합 같은 형태이다. 코드에서 배열을 생성하면 메모리 내에 연속된 빈 셀 그룹을 찾아 사용자 애플리케이션이 데이터를 저장할 수 있도록 할당한다.

![그림 11-1](image_11-1.png)

컴퓨터는 배열 내 어느 인덱스도 한 번에 갈 수 있었다.(읽기) 이와 달리 연결 리스트는 나란히 이어진 메모리 셀 묶음이 아니다. 서로 인접하지 않은 메모리 셀 묶음으로 이뤄진다. 즉 컴퓨터 메모리 전체에 걸쳐 여러 셀에 퍼져 있을 수 있다. 이렇게 서로 인접하지 않은 셀을 노드라 부른다.

그렇다면 컴퓨터는 어떻게 서로 인접하지도 않은 셀들이 같은 연결 리스트에 속하는지 알 수 있을까? 이유는 바로 각 노드가 데이터뿐만 아니라 다음 노드의 메모리 주소도 저장하기 때문이다. 

![그림 11-2](image_11-2.png)

위 예제를 보면 연결 리스트에 "a", "b", "c", "d" 4개의 데이터가 있다. 하지만 데이터를 저장하는 데 각 노드마다 메모리 셀 두 개씩, 총 8개의 셀을 사용하고 있다. 첫 번째 셀에는 실제 데이터가 들어가 있고, 두 번째 셀에는 다음 노드의 시작 메모리 주소를 뜻하는 링크가 들어 있다. 마지막 노드 링크는 연결 리스트가 끝나므로 null이다.

따라서 연결 리스트를 다루는 코드는 항상 첫 번째 노드가 메모리 어디에서 시작하는지 알아야 한다. 

연결 리스트가 배열보다 나은 점 중 하나는 프로그램에 데이터를 저장하기 위해 메모리 내에 나란히 이어진 빈 셀 묶음을 찾을 필요가 없다는 것이다.


---


## 11.2 연결 리스트 구현
루비로 Node와 LinkedList라는 두 클래스를 차례로 구현해 연결 리스트를 만들 것이다.

```Ruby
class Node

    attr_accessor :data, :next_node

    def initialize(data)

        @data = data

    end

end
```

Node 클래스에는 속성이 두 가지이다. 저장하는 값인 data와 다음 노드 링크인 next_node다. 다음과 같이 Node 클래스를 사용한다.

```Ruby
node_1 = Node.new("once")

node_2 = Node.new("upon")

node_1.next_node = node_2

node_3 = Node.new("a")

node_4.next_node = node_3

node_4 = Node.new("time")

node_3.next_node = node_4
```

위 코드로 "once"와 "upon", "a", "time"이라는 문자열을 포함하는 노드 네 개짜리 리스트를 생성했다. 

Node 클래스만 있어도 연결 리스트를 생성할 수 있지만, 어디서 시작했는지 쉽게 알려줄 방법이 필요하다. 이를 위해 LinkedList 클래스를 생성할 것이다.

```Ruby
class LinkedList

    attr_accessor :first_node

    def initialize(first_node)

        @first_node = first_node

    end

end
```

LinkedList 클래스를 사용하면 다음 코드처럼 프로그램에 앞서 생성한 연결 리스트를 알려줄 수 있다.

```Ruby
list = LinkedList.new(node_1)
```

그렇다면 일반 배열과 성능을 비교하면 어떨까? 읽기, 검색, 삽입, 삭제 과정을 분석하겠다.


---


## 11.3 읽기
일반 배열에서 O(1)만에 올바른 셀로 갈 수 있는 것과 달리 연결 리스트는 인덱스 0에 있는 링크부터 쭉 따라가야 한다. 인덱스 2(세 번째 노드)를 찾으려면 인덱스 0에 있는 인덱스 1의 링크를 따라가고, 인덱스 1에서 인덱스 2의 링크를 따라간 후 마지막으로 인덱스 2에서 값을 확인해야 한다. 

빅 오 표기법은 명시적으로 언급하지 않는 한 최악의 시나리오를 표현하므로 연결 리스트 읽기의 효율성은 O(N)이다.

LinkedList 클래스 내 이러한 연산을 구현해 보자.

```Ruby
class LinkedList

    attr_accessor :first_node

    def initialize(first_node)
        # 첫 번째 노드가 무엇인지 컴퓨터에게 알려준다.
        @fitst_node = first_node
    end

    def read(index)
        # 리스트의 첫 번째 노드부터 시작해서 읽는다.
        current_node = @first_node
        current_index = 0

        while current_index < index do
            # 찾는 인덱스에 도달할 때까지 노드의 링크를 따라간다.
            current_node = current_node.next_node
            current_index += 1

            # 리스트의 끝에 도착했다면 찾는 값이 리스트에 없다는 뜻이므로
            # 널을 반환한다.            
            return nil unless current_node
        
        end
    
    return current_node.data

end
```

이제 다음 코드로 리스트 내 특정 인덱스를 찾을 수 있다.
```Ruby
list.read(3)
```


---


## 11.4 검색
배열과 연결 리스트는 검색 효율성이 같다.(최악의 시나리오라면 O(N) 단계가 걸린다.)

다음과 같이 검색 연산을 구현할 수 있다.

```Ruby
class LinkedList

    attr_accessor :first_node

    def initialize(first_node)
        # 첫 번째 노드가 무엇인지 컴퓨터에게 알려준다.
        @fitst_node = first_node
    end

    def read(index)
        # 리스트의 첫 번째 노드부터 시작해서 읽는다.
        current_node = @first_node
        current_index = 0

        while current_index < index do
            # 찾는 인덱스에 도달할 때까지 노드의 링크를 따라간다.
            current_node = current_node.next_node
            current_index += 1

            # 리스트의 끝에 도착했다면 찾는 값이 리스트에 없다는 뜻이므로
            # 널을 반환한다.            
            return nil unless current_node
        
        end
    
    return current_node.data

    def index_of(value)

        # 리스트의 첫 번째 노드부터 시작한다.
        current_node = @first_node
        current_index = 0

        begin

            #찾는 데이터를 찾으면 반환한다.
            if current.node.data == value
                return current_index
            end

            # 그렇지 않으면 다음 노드로 이동한다.

            current_node = current_node.next_node
            current_index += 1
        end while current_node

        #데이터를 찾지 못한 채 전체 리스트를 순회했다면 널을 반환한다.
        return nil
    
    end

end
```

다음 코드로 리스트 내 어떤 값이든 검색할 수 있다.

```Ruby
list.index_of("time")
```


---


## 11.5 삽입
연결 리스트가 배열에 비해 뚜렷한 장점을 보일 수 있는 연산이 바로 삽입이다. 일반 배열이 나머지 데이터를 한 셀씩 오른쪽으로 옮겨야 하므로 효율성이 O(N)이었지만, 연결 리스트는 단 한 단계, O(1)만 걸린다.

다음과 같은 연결 리스트가 있다면

![그림 11-3](image_11-3.png)

"yellow"를 리스트 앞에 삽입하려면 새 노드를 생성하고 노드가 "blue"를 포함하는 노드를 가리키게끔 하면 된다.

![그림 11-4](image_11-4.png)

따라서 배열과 달리 연결 리스트에는 데이터를 하나도 시프트하지 않고 리스트 앞에 데이터를 삽입할 수 있는 유연성이 있다.

만약 "pupple"을 인덱스 2("blue"와 "green" 사이)에 삽입한다면

![그림 11-6](image_11-6.png)

새 노드를 생성하고 블루와 그린 노드의 링크를 수정하면 되므로 실제 삽입은 한 단계다. 문제는 이러기 위해서는 먼저 인덱스 1에 있는 노드("blue")를 찾아야 링크를 수정할 수 있을 것이다. 따라서 연결 리스트 읽기에 그만큼 시간이 걸릴 것이다.

결국 배열과 연결 리스트의 최선과 최악의 시나리오는 정확히 정반대이다. 앞쪽에 삽입한다면 연결 리스트가 훌륭하겠지만, 끝쪽에 삽입하는 것이라면 일반 배열이 최선의 시나리오이며 연결 리스트는 최악의 경우일 것이다.

![표 11-1](table_11-1.png)

LinkedList 클래스 내 삽입 연산을 구현할 수 있다.

```Ruby
class LinkedList

    attr_accessor :first_node

    # 중복 코드는 생략

    def insert_at_index(index, value)
        current_node = @first_node
        current_index = 0

        # 먼저 삽입하려는 새 노드의 바로 앞 인덱스를 찾는다.
        while current_index < index do
            current_node = current_node.next_node
            current_index +=1
        end

        # 새 노드를 생성한다
        new_node = Node.new(value)

        # 새 노드를 가리키도록 앞 노드의 링크를 수정한다.
        current_node.next_node = new_node

    end

end
```


---


## 11.6 삭제
효율성 면에서 삭제는 삽입과 매우 유사하다. 연결 리스트 앞에서 노드를 삭제하려면 한 단계면 된다. 연결 리스트의 first_node가 두 번째 노드를 가리키게 하면 된다.

가령 "once", "upon", "a", "time" 값을 포함하는 연결 리스트 예제였다면, "once"를 삭제하고 싶으면 "upon"에서 시작하도록 바꾸면 된다

```Ruby
list.first_node = node_2
```

이와 달리 배열은 첫 번째 원소를 삭제하면 나머지 데이터를 모두 한 셀씩 왼쪽으로 시프트해야 하므로 효율성이 O(N)이다.

![표 11-2](table_11-2.png)

앞서 본 연결 리스트에서 인덱스 2("pupple")에 있는 값을 삭제할 것이다. 컴퓨터는 인덱스 1에서 "green" 노드를 가리키도록 링크를 변경한다.

![그림 11-10](image_11-10.png)

아래는 LinkedList 클래스 내 삭제 연산을 구현한 것이다.

```Ruby
class LinkedList

    attr_accessor :first_node

    # 중복 코드는 생략한다.

    def delete_at_index(index)
        current_node = @first_node
        current_index = 0

        # 먼저 삭제하려는 노드 바로 앞 인덱스를 찾아 current_node에 할당한다.
        while current_node < index - 1 do
            current_node = current_node.next_node
            current_index += 1
        end

        # 삭제하려는 노드 바로 뒤 노드를 찾는다.
        node_after_deleted_node = current_node.next_node.next_node

        # current_node 링크가 node_after_deleted_node를 가리키도록 수정한다.
        # 이렇게 하면 삭제하려던 노드가 리스트에서 제외된다.
        current_node.next_node = node_after_deleted_node

    end

end
```

연결 리스트와 배열을 분석해서 비교하면 다음과 같다.

![표 11-10](table_11-10.png)


---


## 11.7 연결 리스트 다뤄보기
연결 리스트가 빛을 발하는 경우는 한 리스트를 검사해서 많은 원소를 삭제할 때다. 예를 들어 이메일 주소 리스트를 샅샅이 검토해서 유효하지 않은 형식의 이메일을 모두 삭제하는 어플리케이션을 만든다고 하자. 이 알고리즘은 한 번에 하나씩 모든 이메일 주소를 검사하고, 정규식(어떤 유형의 데이터를 식별하는 특수한 패턴)을 사용해 이메일 주소가 유효한지 확인할 것이다.(유효하지 않으면 리스트에서 삭제한다.)

배열이든 연결 리스트든 전체 리스트를 검사해야 하므로 일단 N단계가 걸린다. 하지만 삭제할 때 둘은 차이를 보이게 된다.

배열은 이메일 주소를 삭제를 할 때마다 나머지 데이터를 왼쪽으로 시프트해야 하므로 O(N) 단계가 소요된다. 모든 시프트는 다음 이메일 주소를 검사하기도 전에 일어난다. 따라서 삭제하면서 유효하지 않은 이메일 개수 * N 단계가 추가로 걸리게 된다.

반면 연결 리스트는 삭제가 필요하면 그저 그 노드의 링크가 적절한 노드를 가리키도록 바꾸면 되므로 삭제에 단 한 단계가 걸린다.(O(1))


---


## 11.8 이중 연결 리스트
연결 리스트를 흥미롭게 응용하는 한 가지 사례가 큐의 내부 자료 구조로 사용하는 것이다. 큐는 데이터를 끝에만 삽입할 수 있고, 맨 앞에만 삭제할 수 있는 항목의 리스트였다. 하지만 연결 리스트 역시 큐처럼 끝에만 삽입하고 맨 앞만 삭제하는 제약을 걸면 큐의 기반으로 사용할 수 있다.

그렇다면 배열 대신 연결 리스트로 큐를 구성하면 어떤 장점이 있을까? 데이터 삽입은 효율성(1)인 배열이 더 낫기 때문에 삽입 측면에서는 연결 리스트가 오히려 불리하다.

하지만 큐에서 데이터 삭제 측면을 보면 앞에서부터 데이터를 삭제하는 데 O(N)이 걸리는 배열과 달리 O(1)인 연결 리스트가 더 빠르다.

따라서 서로 중요한 연산에서 하나는 O(1)이고 하나는 O(N)이기 때문에 크게 뭘 써도 상관이 없어 보인다. 하지만 이중 연결 리스트라는 특수한 연결 리스트 변형을 사용하면 큐에서의 데이터 삽입과 삭제를 모두 O(1)로 할 수 있다.

이중 연결 리스트는 각 노드에 2개의 링크가 있다는 점만 제외하면 연결 리스트와 비슷하다. 한 링크는 다음 노드를 가리키고, 다른 한 링크는 앞 노드를 가리킨다. 그뿐만 아니라 이중 연결 리스트는 처음과 마지막 노드를 모두 기록한다.

![그림 11-11](image_11-11.png)

코드로 살펴보면 이중 연결 리스트의 핵심부는 다음과 같다.
```Ruby
class Node

    attr_accessor :data, :next_node, :previous_node

    def initialize(data)
        @data = data
    end

end

class DoublyLinkedList

    attr_accessor :first_node, :last_node

    def initialize(first_node=nil, last_node=nil)
        @first_node = first_node
        @last_node = last_node
    end

end
```

이중 연결 리스트는 항상 첫 노드와 마지막 노드를 모두 알고 있으므로, O(1)으로 접근할 수 있다. 유사하게 다음과 같은 방식으로 한 단계 만에 이중 연결 리스트 끝에 데이터를 삽입할 수 있다.

![그림 11-12](image_11-12.png)

* 새 노드("Sue")를 생성해서 이 노드의 previous_node가 last_node("Greg")를 가리키도록 한다. 

* 이어서 last_node("Greg")의 next_node가 새 노드("Sue")를 가리키도록 바꾼다.

* 끝으로 새 노드("Sue")를 이 연결 리스트의 last_node로 선언한다.

```Ruby
class DoublyLinkedList

    attr_accessor :first_node :last_node

    def initialize(first_node=nil, last_node=nil)

        @first_node = first_node
        @last_node = last_node

    end

    def insert_at_end(value)

        new_node = Node.new(value)

        # 연결 리스트에 아직 원소가 없을 때(new_node가 처음이 됨)
        if !@first_node
            @first_node = new_node
            @last_node = new_node
        
        else
            # 새 노드의 앞이 원래 last 노드를 가리키게 한다.
            new_node.previous_node = @last_node

            # 기존 마지막 노드의 뒤에 새 노드를 잇는다.
            @last_node.next_node = new_node

            # 마지막 노드의 자리를 새 노드가 대체한다.
            @last_node = new_node
        end
    
    end


end
```

이중 연결 리스트는 리스트 앞과 끝 모두 바로 접근할 수 있으므로 양 끝에 O(1)으로 데이터를 삽입할 수 있을 뿐 아니라, O(1)으로 양 끝 데이터를 삭제할 수 있다. 따라서 큐를 위한 완벽한 내부 자료 구조이다.

다음은 이중 연결 리스트를 기반으로 한 완벽한 큐 예제이다.

```Ruby
class Node

    attr_accessor :data, :next_node, :previous_node

    def initialize(data)
        @data = data
    end

end



class DoublyLinkedList

    attr_accessor :first_node, :last_node

    def initialize(first_node=nil, last_node=nil)
        @first_node = first_node
        @last_node = last_node
    end


    # 마지막에 삽입
    def insert_at_end(value)

        new_node = Node.new(value)

        # 연결 리스트에 아직 원소가 없을 때(new_node가 처음이 됨)
        if !@first_node
            @first_node = new_node
            @last_node = new_node
        
        else
            # 새 노드의 앞이 원래 last 노드를 가리키게 한다.
            new_node.previous_node = @last_node

            # 기존 마지막 노드의 뒤에 새 노드를 잇는다.
            @last_node.next_node = new_node

            # 마지막 노드의 자리를 새 노드가 대체한다.
            @last_node = new_node
        end
    
    end


    # 맨 앞을 제거
    def remove_from_frond
    
        removed_node = @first_node
        @first_node = @first_node.next_node

        return removed_node
    
    end

end



# 이중 연결 리스트를 쓰는 큐 클래스
class Queue

    attr_accessor :queue


    def initialize
        
        # 새 노드
        @queue = DoublyLinkedList.new

    end


    def enque(value)

        # 새 값을 맨 뒤에 삽입
        @quere.insert_at_end(value)

    end

    
    def deque

        removed_node = @queue.remove_from_front

        return remover_node.data

    end


    def tail

        return @queue.last_node.data

    end


end
```


---


