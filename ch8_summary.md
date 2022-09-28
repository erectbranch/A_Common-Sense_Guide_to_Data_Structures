# 8. 스택과 큐로 간결한 코드 생성
스택과 큐라는 새로운 자료 구조를 알아볼 것이다. 사실 두 자료 구조도 완전히 새롭다고는 할 수 없다. 제약을 갖는 배열일 뿐이다. 하지만 그 제약 덕에 두 자료 구조가 매우 간결해진다.

스택과 큐는 임시 데이터를 처리할 수 있는 간결한 도구다. 예로 식당에서 음식을 주문한다면 손님이 주문한 내역은 식사를 준비해서 내놓을 때까지 중요하고 그 이후로는 버려진다. 또한 식당은 주문이 들어온 순서대로 식사를 만들어야 한다. 스택과 큐를 사용하면 데이터를 순서대로 처리할 수 있으며 필요 없으면 버릴 수 있다.


---


## 8.1 스택
스택(stack)이 데이터를 저장하는 방법은 배열과 같다. 즉, 단순히 원소들의 리스트다. 다만 한 가지, 스텍에는 다음과 같은 세 가지 제약이 있다.

* 데이터는 스택의 끝에만 삽입할 수 있다.

* 데이터는 스택의 끝에서만 읽을 수 있다.

* 데이터는 스택의 끝에서만 삭제할 수 있다.

접시 더미를 스택처럼 생각할 수 있다. 가장 위에 있는 접시를 제외하고는 다른 접시의 윗면을 볼 수 없다. 가장 위를 제외하고는 접시를 추가할 수도, 제거할 수도 없다. 실제로 스택의 끝을 top, 스택의 시작을 bottom이라고 부른다.

스택에 새 값을 삽입하는 것을 스택에 푸시(push)한다고 말한다. 

스택 연산을 묘사하는 데 쓰이는 두 문자어가 "Last In, First Out"을 뜻하는 LIFO이다. 늦게 들어온 항목이 결국 스택에서 팝될 첫 번째 항목이라는 의미이다.


---


## 8.2 스택 다뤄보기
오래 사용할 데이터를 저장할 때는 보통 스택을 사용하지 않지만, 임시 데이터를 사용해야 하는 다양한 알고리즘에서 스택은 유용한 도구이다.

자바스크립트 린터(linter), 프로그래머가 작성한 자바스크립트 코드를 검사해서 각 줄이 문법적으로 올바른지 확인하는 프로그램의 시작 부분을 만들어 보자. 매우 다양한 측면에서 문법을 검사해야 하기 때문에 만들기 상당히 복잡하지만, 예제에서는 린터의 한 측면인 여는 괄호와 닫는 괄호에 초첨을 맞출 것이다.

괄호에서 문법 오류는 세 가지 상황에서 발생한다.

1. 여는 괄호는 있는데 닫는 괄호는 없다
```
(var x = 2;
```

2. 여는 괄호가 없는데 닫는 괄호가 있다.
```
var x = 2;)
```

3. 여는 괄호와 닫는 괄호의 종류가 다르다.
```
(var x = [1, 2, 3)];
```

이럴 때 스택을 사용해 알고리즘을 훌륭하게 구현할 수 있다. 다음과 같은 과정을 따른다.

빈 스택을 준비해서 다음과 같은 규칙에 따라 각 문자를 왼쪽부터 오른쪽 방향으로 읽는다.

1. 괄호(소괄호나 중괄호, 대괄호)가 아닌 문자는 모두 무시한다.

2. 여는 괄호가 나오면 스택에 푸시한다. 이 괄호가 닫히기를 기다릴 것이다.

3. 닫는 괄호가 나오면 top 스택을 확인한다. 그리고 다음과 같이 분석한다
* 스택 top에 괄호가 없다.: 오류 2번 문제
* 스택 top에 괄호는 있지만 종류가 다르다.: 오류 3번 문제
* 스택 top에 괄호가 있으며 종류도 같다.: 성공적으로 닫았다. 이 괄호는 더 이상 기록할 필요가 없으므로 top 원소를 pop한다.

4. 줄 끝에 도달했는데 여전히 스택에 남아있는 괄호가 있다면 여는 괄호에 대응하는 닫는 괄호가 없다는 뜻이다.: 오류 1번 문제

위 알고리즘을 루비로 구현해 보겠다. 루비의 배열에는 배열 끝에 원소를 추가하고 배열 끝에서 원소를 제거할 수 있는 push와 pop 메서드가 기본적으로 내장되어 있다. 배열에 이 두 메서드만 사용해도 효과적으로 배열을 스택으로 사용할 수 있다.

```Ruby
class Linter

    attr_reader :error

    def initialize

        # 간단한 배열을 스택으로 사용한다.
        @stack = []

    end

    def lint(text)
    
        # 텍스트 내 각 문자를 읽는 루프를 시작한다.
        text.each_char.with_index do |char, index|

            if opening_brace?(char)

            # 문자가 여는 괄호면 스택에 push한다.
                @stack.push(char)

            elsif closing_brace?(char)

                if closes_most_recent_opening_brace?(char)

            # 닫는 괄호 문자가 가장 최근에 나온 여는 괄호를 닫았다면
            # 스택에서 해당 여는 괄호를 팝한다.
                    @stack.pop

                else 
            
            # 닫는 괄호 문자가 가장 최근에 나온 여는 괄호를 닫지 않았다면
                    @error = "Incorrect closing brace: #{char} at index #{index}"

                    return

                end
            end
        end

        if @stack.any?

    # 줄 끝에 도달했는데 스택이 비어있지 않다면,
    # 여는 괄호 대응하는 닫는 괄호가 나오지 않은 것이다.
            @error = "# {@stack.last} does not have a closing brace"

        end
    end


    private
    def opening_brace?(char)
        ["(", "[", "{"].include?(char)
    end

    def closing_brace?(char)
        [")", "]", "}"].include?(char)
    end

    def opening_brace_of(char)
        {")" => "(", "]" => "(", "}" => "{"}[char]
    end

    def most_recent_opening_brace
        @stack.last
    end

    def closes_most_recent_opening_brace?(char)
        opening_brace_of(char) == most_recent_opening_brace
    end
end

```


---


## 8.3 큐
큐(queue) 역시 간결하게 임시 데이터를 다루며 제약이 있는 배열이라는 점이 스택과 비슷하다. 하지만 데이터를 처리하는 순서가 다르다.

극장에 줄 서 있는 사람들을 큐처럼 생각할 수 있다. 줄 맨 앞에 있는 사람이 그 줄을 떠나 가장 먼저 극장에 들어간다. 첫 번째로 추가된 항목이 가장 먼저 제거된다. 그래서 큐를 "First In, First Out"의 약자인 "FIFO"로 표현한다.

큐도 마찬가지로 세 가지 제약을 포함한다.
* 데이터는 큐의 끝에만 삽입할 수 있다. (스택과 동일)
* 데이터는 큐의 앞에서만 읽을 수 있다. (스택과 정반대)
* 데이터는 큐의 앞에서만 삭제될 수 있따. (스택과 정반대)


---


## 8.4 큐 다뤄보기
출력 잡(job)부터 웹 애플리케이션의 백그라운드 워커에 이르기까지 많은 애플레케이션에서 흔하게 큐를 사용한다.

프틴터가 네트워크상에 있는 여러 컴퓨터에서 출력 잡을 받아들일 수 있도록 간단한 인터페이스를 프로그래밍한다고 하자. 루비 배열에는 배열 끝에 데이터를 추가하는 push 메서드와 배열 앞에서 데이터를 삭제하는 shift 메서드가 있으므로 두 메서드를 활용하면 된다.

```Ruby
class PrintManager

    def initialize
        @queue = []
    end

    def queue_print_job(document)
        #문서를 요청받으면 push
        @queue.push(document)
    end

    def run
        while @queue.any?
        # run을 하면
        # 루비의 시프트 메서드는 배열의 첫 번째 원소를 삭제하고 반환한다.
        print(@queue.shift)
        end
    end

    private

    def print(document)
    
    # 실제 프린터를 실행시키는 코드다.
    # 데모용으로 터미널에 출력한다.
    puts document
    
    end

end

```

위 클래스를 다음처럼 활용한다.

```Ruby
print_manager = Printmanager.new

print_manager.queue_print_job("First Document")

print_manager.queue_print_job("Second Document")

print_manager.queue_print_job("Third Document")

print_manager.run

```

---