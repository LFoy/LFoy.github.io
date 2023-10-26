  

1. ==11866번 요세푸스 수열. N명이 ‘원’으로 앉고 K번째 사람을 계속 죽여나가는 순서 수열==
    
    ==1시간 걸렸는데 못풀었음. 지금 까지 본 것 중에 가장 어렵다.==
    
    보자마자 (나머지+pop)으로 풀어야겠다 생각했는데 어떻게 해야할지 모르겠음…
    
    답지 보니까 2가지 방식으로 풀던데 1. deque 2.나머지+pop 으로 품.
    
    리스트+나머지+pop이 틀린 방식이 아니었고 ‘원’이니까 노드로 풀어야하나? 라는 생각도 틀린게 아니었는데 왜 못풀었을까…
    
    ==아 내가 너무 잘못 생각했던게 계속 리스트의 인덱스를 이용해서 풀어야할지 리스트의 value를 이용해서 풀어야할지 감을 못잡았음. 당연히 인덱스로 풀어야했는데. 왜냐면==
    
    list는 계속 요소가 삭제 되니까 value 7 이 index 3에 있을텐데 remove()로 7을 삭제하려고 하면 말이 안되는게 len(list)를 넘어가서 % 가 적용될텐데 그럼. 절대 7이 나올수가 없음. 인덱스 값(0~len(list)-1)까지만 나오거든… 왜 이 생각을 못했을까. 천천히 생각했으면 index로 풀어야했다는걸 알수 있었을텐데…
    
      
    
    ==그리고 while문 안에 인덱스 저장하는거 num을 무한히 증가시키면 안됨. num은 계속 list의 인덱스 범위 안에 있게끔 해야함. 그래야 2를 더했을 때 인덱스 범위를 조금 넘쳐 나가면 나머지를 이용해서 그 조금 벗어난 부분만큼 앞에서부터 다시 찾을 수 있으니까==
    
      
    
    ==그리고 num += k 가 아니라 num += k - 1 이었어야함.==
    
    ==일단 3을 삭제하려면 인덱스가 2이여서 초기화가 이렇게 돼야하는 이유도 있고, 그리고 만약 3을 삭제했으면==
    
    ==list의 인덱스가 앞으로 하나씩 당겨지게 되고 2번만 이동해도 ‘삭제 전 리스트’에서 3번 이동한거와 같기때문이다. 하 이런 생각을 어떻게 하지? 진짜 어렵다… 남이 풀이 한거 못봤으면 평생 이해 못했을듯==
    
    ```
    n, k = map(int, input().split())
    
    li = [i for i in range(1, n+1)]
    yose = []
    num = 0
    
    while len(li) != 0:
        num += k - 1
        num = num % len(li) 
              
        yose.append(li.pop(num))
     
    print('<' + ', '.join(list(map(str, yose))) + '>')
    ```
    
    - ==리스트 삭제 하는법 4가지 외우기==
        
        ==i) del list[index] : 인덱스 슬라이싱을 통해서 특정 구간 삭제 가능==
        
        ==ii) list.clear() : 모든 요소 삭제되고 빈 슬라이드 됨.==
        
        ==iii) list.remove(value) : value를 찾아 첫번째 검색결과만 삭제. 검색결과가 없으면 error 리턴==
        
        ==iv) list.pop(index) : index를 넣지 않으면 맨 뒤 요소 삭제. 넣으면 인덱스로 찾아서 삭제한 뒤 그 value를 리턴함.==
        
    
    ==근데 이거 생각하는 방식이 좀 어려워서 ‘원’의 방식을 구현하려면 deque 덱(양방향 리스트(큐)) 이라는 걸 이용해서 3번째를 삭제하라고 하면 2번째까지는 덱 맨 뒤에 계속 붙이고 3번째만 pop해서 결과 리스트에 넣어주면 좀 더 깔끔했다.==
    
    _**deque 에는 심지어 ‘원’의 기능을 위해 rotate() 라는 함수까지 제공해준다! 이걸 이용해서 회전시키기 가능!**_
    
    ```
    from collections import deque
    
    n, k = map(int, input().split())
    circle = deque([x for x in range(1, n+1)])
    result = []
    
    while circle:
        circle.rotate(-(k-1))
        result.append(circle.popleft())
    ```
    
      
    
2. 10815번 숫자 카드 비교하기
    
    8분 걸림
    
    if nums[i] in cards:
    
    는 누구나 생각했을 것이고 여기서 문제는 시간이기 때문에
    
    cards를 리스트가 아닌 딕셔너리로 정의하면 ‘in’이 O(1)로 줄어들어서 좋음 리스트는 O(n)임;;
    
    다른 사람들은 바이너리 써치를 직접 구현하셔서 O(logn)으로 O(n) 보다 더 줄이셨네. 그래도 O(1)이 짜세욘
    
    ```
    n =  int(input())
    cards = {key for key in list(map(int, input().split()))}
    m = int(input())
    nums = list(map(int, input().split()))
    
    for i in range(len(nums)):
        if nums[i] in cards:
            nums[i] = 1
        else:
            nums[i] = 0
    
    print(*nums)
    ```
    
      
    
3. 1676번 N!의 0 개수 세기
    3분 걸림. 쉬웠다.
    
    숫자를 스트링으로 바꾸고 비교하면 돼서 쉬움.
    
    ```
    n = int(input())
    result = 1
    cnt = 0
    
    for i in range(1,n+1):
        result *= i
    
    for num in reversed(str(result)):
        if num == '0':
            cnt += 1
        else:
            break
    
    print(cnt)
    ```
    
    다른 사람들 풀이보니까 진짜 특이하게 많이 풀었는데 엄청 수학적으로 생각한 사람도 있고 그러네;;
    
      
     ^c8ef3b
4. 2563번 도화지에 색종이 붙이고 넓이 산출하기
    
    ==**하 미치겠다… 이거 너무 쉬웠는데 계속 답이 너무 크게 나오길래 뭔가 했더니 2차원 배열 선언이 잘못됐었음.**==
    
    30분 안에 풀었을 것 같은데.
    
    2차원 배열 선언하고 색종이 크기 100만큼 도화지의 배열에 1 넣어주고 마지막에 sum 해주면 끝이었는데 이걸 1시간 넘게 못풀었네 아오…
    
    ```
    paper = [[0] * 100] * 100
    
    위처럼 2차원 선언하면 내부 리스트가
    전부 같은 객체(레퍼런스)을 가짐.
    그래서 paper[0][0] = 1 을 해주면
    paper[0][0] = 1
    paper[1][0] = 1
    paper[2][0] = 1
    ...
    paper[100][0] = 1 
    이 됐던것;;; 그래서 sum 치면 커진거임;;
    
    paper = [[0 for _ in range(100)] for _ in range(100)]
    ```
    
    - ==2차원 배열 선언하는 법==
        
        > ==무조건 컴프리헨션 이용해야함!==
        
    
    ```
    n = int(input())
    paper = [[0 for _ in range(100)] for _ in range(100)]
    result = 0
    
    for _ in range(n):
        x, y = map(int, input().split())
    
        for row in range(10):
            for col in range(10):
                paper[x+row][y+col] = 1
    
    for row in paper:
        result += sum(row)
        
    print(result)
    ```
    
    하 쉬웠는데 진짜 아쉽다… 그래도 2차원 배열 초기화 하는법 확실히 배웠다고 생각하자.
    
    다른 사람 풀이 보면 100개의 좌표를 not in 시켜서 없으면 좌표 추가 하는 식으로해서 len(좌표 리스트) 해서 구하네 굿~
    
    - zip 으로도 푼 사람 있어서 zip 공부함.
    
    [https://www.daleseo.com/python-zip/](https://www.daleseo.com/python-zip/)
    
      
    
5. 1475번 숫자 세트 구하기 (6,9는 같다고 봄)
    
    17분 걸림. 나름 쉬웠음.
    
    각자 숫자가 몇개씩 있는지 파악한 뒤에
    
    max(list) 이용하면 되겠다고 생각했고
    
    6,9 는 별도로 생각해주기로 하니까 술술 풀림.
    
    ```
    import math
    
    room = input()
    nums = [0] * 10
    cnt = 0
    
    for i in room:
        nums[int(i)] += 1
    
    cnt = math.ceil((nums[6] + nums[9]) / 2)
    nums[6] = cnt
    nums[9] = cnt
    
    print(int(max(nums)))
    ```
    
    6이랑 9를 너무 하드코딩한거 같아서 좀 별로였는데 다른 사람 풀이 보니까 6이랑 9를 균형있게 count 해나간다는 컨셉이 참 이쁘네
    
    ```
    for n in room:
        if n == '6' or n == '9':
            if num[6] <= num[9]:
                num[6] += 1
            else:
                num[9] += 1
        else:
            num[int(n)] += 1
    print(max(num))
    ```
    
    특정 X가 리스트 또는 문자열 안에 몇개 있는지 셀 때 Counter 이용하는거 까먹었군;;
    
    딕셔너리 반환해주는데 {X : 몇개}
    
    ```
    from collections import Counter
    
    C = Counter(number)
    # 6이나 9는 2개에 한세트씩 필요
    # 나머지 숫자들은 1개에 한세트씩 필요
    C['6'] = ( C['6'] + C['9'] ) // 2 + ( C['6'] + C['9'] ) % 2
    del C['9']
    
    answer = max([n for n in C.values()])
    ```
    
      
    
6. 11723번 집합 명령어 구현하기
    
    44분 걸림
    
    쉬웠는데 메모리 조건때문에 메모리 줄이느라 너무 오래걸림.
    
    all이랑 clear 명령어는 x가 없어서 unpaking이 한줄로 처리가 불가능해서 여기서 쪼금 시간 씀
    
    ==**그리고 set 초기화할때 컴프리헨션 안되니까**==
    
    ==**그리고 {} 이건 딕셔너리지 세트가 아님 바보야**==
    
    ==**set(list) 이렇게 set 안에 넣어야지!**==
    
    ==**그리고 문자열 넣을때**==
    
    ==**set(”hello”) 하면 h e l o 가 저장됨**==
    
    ==**S.add(”hello”) 이건 hello 저장됨. 이거 좀 어렵더라.**==
    
    ```
    from sys import stdin
    
    m = int(input())
    S = set()
    
    result = ]
    
    for _ in range(m):
        command = stdin.readline().rstrip().split()
        if len(command) == 2:
            x = int(command[1])
    
        if command[0] == 'add':
            S.add(x)    
        elif command[0] == 'remove':
            if x in S:
                S.remove(x)
        elif command[0] == 'check':
            if x in S:
                print(1)
            else:
                print(0)
        elif command[0] == 'toggle':
            if x in S:
                S.remove(x)
            else:
                S.add(x)
        elif command[0] == 'all':
            \#S = set([i for i in range(1,21)])
    '''
    다른 사람 풀이보니까 set로 선언해도되네.
    메모리 초과 났던 
    '''
            for i in range(1,21):
                S.add(i)
        elif command[0] == 'empty':
            S.clear()
        else:
            pass
    ```
    
    많은 사람들이 집합이 아닌 바이너리?로 풀었음.
    
    s = 0 이 초기화이고
    
    s = 1 이면 1이 있다
    
    s = 000001 이건 6이 있다;; 이런식으로 ㄷㄷ;;
    
    s = 111111111111111 이 all 임
    
    근데 이거 표현을
    
    s = 200000000000 - 1 이렇게 수식 써서 11111111111로 표현한거임 ㄷㄷ 미친;; 왜이렇게까지 어렵게;;
    
      
    
7. 1476번 지구, 태양, 달의 해를 받아 년도로 나타내기
    
    48분 걸림.
    
    보자마자 나머지 이용하는건 알았는데 수식 구하는게 너무 시간이 많이 걸림 그렇게 어렵진 않았는데 천천히 하면 됨.
    
    지구,태양 달의 해를 받아 년도를 나타내는 역수 방식은 어렵기 때문에
    
    년도를 1부터 계속 +1 해주면서 지구,태양,달의 해로 나타내고 입력받은 거랑 같으면 년도를 리턴해주는 식으로 품.
    
    ```
    e, s, m = map(int, input().split())
    r, r1, r2 = 0, 0, 0
    i = 0
    
    while (e, s, m) != (r, r1, r2):      
        i += 1
        if i % 15 == 0:
            r = 15
        else:
            r = i % 15
    
        if i % 28 == 0:
            r1 = 28
        else:
            r1 = i % 28
    
        if i % 19 == 0:
            r2 = 19
        else:
            r2 = i % 19
    
    print(i)
    ```
    
    하 이거 if, else 안쓰고 이렇게 i(년도)를 while 끝에서 더해주면 깔끔해지네…
    
    i = 0 이면(0년 지난게 아니라 1년 지난거임) 출력하면 1이 나와서 1년 지난거고
    
    i = 16 일때 출력하면 2가 나와서 2년 지난거고
    
    여기서 중요한건 출력할때 i를 체크하는거였음.
    
    I = 16인데 r이 2 나오니까 16년 지났는데 r이 1이여야하는데 2라고 잘못 나왔다고 생각해서 깔끔하게 못짰네;
    
      
    
    ```
    while (e, s, m) != (r, r1, r2):      
        
    		r = i % 15 + 1
        r1 = i % 28 + 1
    		r2 = i % 19 + 1
    		
    		i += 1
    print(i)
    		
    ```
    
      
    
    다른 사람 풀이보면 좀 직관 적임
    
    ```
    while True:
        if ne == E and ns == S and nm == M:
            break
        ne += 1
        ns += 1
        nm += 1
        ans += 1
        if ne > 15:
            ne = 1
        if ns > 28:
            ns = 1
        if nm > 19:
            nm = 1
    \#r, r1, r2 를 계속 1~최대 까지만 반복하는 중
    ```
    
      
    
8. 1789번 서로 다른 n개의 자연수의 합이 S일 때 n 찾기
    
    5분 걸림.
    
    문제 처음 봤을 땐 막막했는데 S=10일때, S=12일때 해보니까 바로 규칙 찾음.
    
    무조건 1+2+3+4+…+n 이 가장 최대의 n을 구할 수 있더라고.
    
    ```
    s = int(input())
    num = 0
    
    for n in range(1, s+1):
        num += n
        if num > s:
            print(n-1)
            break
        elif num == s:
            print(n)
            break
    ```
    

  

1. 1094번 막대 자르기
    
    35분 걸림.
    
    감을 잡고 나니까 어렵지 않았는데 감 잡기 까지 좀 걸림.
    
    그러니까 이걸 딕셔너리로 풀지, 리스트로 풀지, 2차원리스트로 풀지 고민했음.
    
    처음엔 인덱스 슬라이싱으로 리스트를 막대로 보고 리스트를 쪼개는건가 했는데 그러면 막대를 버리고 이런 개념이 어려워지더라고. 그래서 bars[64] 로 시작해서 remove, append 하면 되겠다고 생각했음.
    
    ```
    x = int(input())
    bars = [64]
    
    while sum(bars) != x:    
        if sum(bars) > x:
            minBar = min(bars)
            bars.remove(minBar)
            bars.append(minBar/2)
            
            if sum(bars) < x:
                bars.append(minBar/2)
     
    print(len(bars))
    ```
    
    뭐냐 다른 사람 풀이보니까 왜 나처럼 푼 사람이 한명도 없지?;;
    
    다 이상하게 풀었어. 64 32 16 8 4 2 1 선언해서 이걸로 어떻게 조합해서 x를 만들면 그 숫자만큼 break하게 해놨네;;
    
    내 풀이가 가장 예쁨.
    
      
    
2. 17478번 재귀함수 설명하는 프로그램 만들기
    
    40분 걸렸는데.
    
    쉬웠음. 오타 찾아 내느라 20분 쓴듯;;
    
    재밌는 문제 였음. 재귀에 대해서 다시 생각하게함ㅋㅋ
    
    그리고 recursion 임 스펠링 틀렸네;;
    
    ```
    n = int(input())
    cnt = 0
    
    def reculsion(n, cnt):
        if n == 0:
            print('____'*cnt + '\"재귀함수가 뭔가요?\"')
            print('____'*cnt + '\"재귀함수는 자기 자신을 호출하는 함수라네\"')
            print('____'*cnt + '라고 답변하였지.')
        else:
            print('____'*cnt + '\"재귀함수가 뭔가요?\"')
            print('____'*cnt + '\"잘 들어보게. 옛날옛날 한 산 꼭대기에 이세상 모든 지식을 통달한 선인이 있었어.')
            print('____'*cnt + '마을 사람들은 모두 그 선인에게 수많은 질문을 했고, 모두 지혜롭게 대답해 주었지.')
            print('____'*cnt + '그의 답은 대부분 옳았다고 하네. 그런데 어느 날, 그 선인에게 한 선비가 찾아와서 물었어.\"')
            cnt += 1
            reculsion(n-1, cnt)
            cnt -= 1
            print('____'*cnt + '라고 답변하였지.')
            
    
    print('어느 한 컴퓨터공학과 학생이 유명한 교수님을 찾아가 물었다.')
    reculsion(n, cnt)
    ```
    
    근데 이게 내가 좀 실수한게
    
    재귀함수가 뭔가요? 이거를 if else 전에 재귀함수 들어서자마자 질문 했어야함. 그래야 이 문제의 의도가 맞아…
    
    다른 사람 풀이보고 아차싶었음…
    
      
    
    하 그래도 6일차 때 10문제 풀었다 ㅎ_ㅎ
    
    내일 실버4로 갈 수 있겠군!