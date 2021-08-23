---
title: "데이터베이스(DB) 14장 Indexing Hashing"
except: "데이터베이스(DB) 에서 정보를 쉽게 접근할수 있는 indexing, Hashing 대해 알아보자."
categories:
  - 데이터베이스
tags:
  - Indexing
  - Hashing
  - Bucket
  - B-Tree
  - B+Tree
toc: true
toc_sticky: true
---

# 14장 Indexing Hashing
## <span style = "color:powderblue"> ※ Indexing and Hashing

### <span style = "color:powderblue"> ※ 인덱싱 기본 개념
- 인덱싱은 원하는 자료에 빨리 접근하기 위한 매커니즘임
- Search Key는 파일을 찾아보기 위한 것
- 인덱스는 Search Key : Pointer 구조로 이루어져 있음
- 인덱스 파일은 원본보다 훨씬 작음
- 2가지 종류의 Indices 가 있음
	- 정렬 indices: 정렬한 상태
	- Hash indices: search key는 hash function으로 생긴 것
<br><br><br>

### <span style = "color:powderblue"> ※ Ordered Indices
- search key가 정렬되어있는 것
- 만약에 순차적이 아니라면 secondary index라고 함
- indexed sequential file이란 순차적으로 정렬된 파일인데, primary index로 정렬 (인덱스 순인데, 실제 파일도 그렇다)

primary index: dense index file

![image](/images/DB-14-01.png)
primary index: sparse index files
![image](/images/DB-14-02.png)
primary index: multilevel index
![image](/images/DB-14-03.png)
Secondary Index
![image](/images/DB-14-04.png)
중간에 있는 것을 bucket이라고 함 (한단계 거쳐가는 것)

<br><br><br>

### <span style = "color:powderblue"> ※ B+ tree Index file
- B+ tree는 indexed sequential file 의 대체제이다
- indexed sequential file의 단점은 파일이 커지면 성능이 떨어지며, 주기적으로 reorganization이 요구됨
- B+ tree의 장점: 자동으로 작고 지역적인 changes에도 reorganize되며, 전체적인 reorganization은 불필요함
- B+ tree의 단점: 추가 삽입과 삭제, 공간의 overhead가 있다
- B+ tree는 장점이 단점을 상쇄하고도 남음
- B+ tree는 root가 있는 tree이다. 그 특징을 정리하자면 다음과 같다
	- root로부터 leaf까지의 거리가 같다
	- 모든 root와 leaf가 아닌 노드는 n/2 이상 n 이하의 자식노드가 있다
	- leaf 노드는 (root가 아닌) (n-1)/2에서 n-1사이의 값을 갖고 있다
	- root는 최소 2개의 자식이 있다

### <span style = "color:powderblue"> ※ B+ tree 노드 구조
![image](/images/DB-14-05.png)

- Ki는 search key
- Pi는 자식들에 대한 pointer이다
- search key 노드들을 k1~ kn-1순으로 정렬

<br><br><br>

### <span style = "color:powderblue"> ※ B+ tree에서의 leaf node
![image](/images/DB-14-06.png)
- i=1~n-1일 때, Pi는 Ki에 대응하는 값을 가르키거나, bucket을 가르킨다
- Li가 leaf고, Lj가 leaf면, 그리고 i<j면 Li의 search key의 값은 Lj의 search key보다 작다
- Pn은 다음 leaf를 가르킨다

<br><br><br>

### <span style = "color:powderblue">※ Leaf Node가 아닌 노드 (in B+ Tree)
![image](/images/DB-14-07.png)
- P1의 subtree의 search key는 k1보다 작다
- Pm이 가르키는 subtree의 search key는 km-1보다 크거나 같다
- 2 <= I <= m-1에 대해, Ki-1 <= Pi < Ki
![image](/images/DB-14-08.png)

![image](/images/DB-14-09.png)

<br><br><br>


### <span style = "color:powderblue">※ B+ Tree의 쿼리문 (k를 찾을 때)
- Root Node에서 시작
	- k보다 큰 search key중 가장 작은 것을 가지고 있는 node를 찾는다
	- 그런 value가 존재하면 Ki라고 가정, Pi 혹은 Pi+1를 child node로 따라간다
	- 그렇지 않으면 k>Km-1이므로 Pm을 따라간다
- 해당 노드가 leaf node가 아니면 계속 위의 과정을 따라한다
- leaf 노드가 된다면 Ki = k인 값이 있으면 Pi를 ᄄᆞ라 record 아니면 bucket을 따라가고 없으면 k는 존재 X
<br><br><br>


### <span style = "color:powderblue">※ B+ Tree 삽입
- classification은 다른 object들을 class 에 들어가도록 돕는다
- decision tree로 표현 가능하다
![image](/images/DB-14-10.png)
쉽게 설명하면 다음과 같다. 실제 들어갈수 있는 개수를 초과하면, n/2+1을 위로 올리고 분리한다고 보면 된다. adams를 넣은 다음 그림이 그 예로 볼 수 있는데, Adams-brandt-cali-crick 4개는 초과이므로 cali가 위로 올라가게 된다. cali가 올라가게 되면서 crick과 cali는 왼쪽과 분리하게 된다. 마찬가지로 위에 칸도 가득차면 똑같이 분리가 된다. 만약에 모든 노드들이 꽉 차면? 새로 leaf 노드 층을 만들어서 이사한다!
<br><br><br>


### <span style = "color:powderblue">※ B Tree Index File

![image](/images/DB-14-11.png)
- B-Tree Index File은 B+Tree와 같지만 차이점이 있는데, 바로 중간의 노드들도 자신이 가르키는 포인터가 존재한다는 것이다. 쉽게 말해 b+Tree는 모든 리프노드들만이 실제 value를 가르키는 pointer이 있지만, B Tree는 중간에 있는 노드들은 leaf노드로 복제가 되지 않고 실제 값을 pointing 가능하다.

![image](/images/DB-14-12.png)



<br><br><br>


### <span style = "color:powderblue">※ 그렇다면 B Tree와 B+ Tree중에 무엇을 이용해야 하는가?

- B Tree 장점:
	- B+ tree보다 노드 개수 적음
	- leaf 노드 찾기 전에 원하는 결과 찾을 수 있음
- B Tree 단점:
	- leaf노드까지 가야 찾는 경우가 대다수
	- non leaf node가 많아서 fan out 이 줄어듦. 따라서 깊이가 더 늘어남
	- insertion deletion이 더 복잡함
	- 구현 어려움

따라서 B+Tree가 더 낫다.

<br><br><br>
<br><br><br>


# <span style = "color:Yellow">**※ 예상 질문**
## <span style = "color:Orange"> **Q1 . Indexing 2가지 설명** </span>

- Indexing은 원하는 data를 빠르게 찾는 방법을 의미한다. 여기서 Search key는 value에게 가는 pointer과 pair이 되어 있는데, 이를 어떻게 indexing하냐에 따라 방식이 달라진다. 처번째 방식은 Ordered Indices로 search key는 sorted order 즉 정렬된 순서로 존재하는 것이고 hash indices는 해시 함수를 통해 분산되어 있음을 의미한다. Ordered indices는 primary index로 순차적으로 정렬되어 있거나, 실제 값들은 순차적으로 정렬되어 있지는 않은 secondary index로 존재하고, 때로는 이들이 여러 층의 multilevel 형태로 존재하기도 한다.

## <span style = "color:Orange"> **Q2 . B+ Tree 구조와 Search, Insertion, Deletion을 설명하시오** </span>

- B+ Tree는 Key와 Pointer이 정해진 개수이하, 그 절반 이상의 형태로 존재한다. 무조건 Root는 2개 이상의 자식 노드가 있으며, 리프 노드가 아닌 노드는 자식노드로 향하는 포인터가, 모든 비어있지 않은 리프노드는 value로 향하는 pointer이 존재한다는 점이 있다. Search를 하기 위해서는 자신이 찾고자 하는 값보다는 크되, 그중 가장 작은 key를 찾아야 한다는 매커니즘을 반복적으로 실행하면, leaf node level에 도달하여 해당 key의 존재 여부를 찾을 수 있다는 점이 있다. Insertion은 한 대상을 입력할 때, 만약에 해당 노드를 수용하지 못할 정도로 자리가 찼다면, 그중 중간의 한 노드를 (n/2+1) 위로 올려 부모 노드로 만들고 아래 두 노드 덩어리들을 분리하게 된다. 만약에 중간에 노드도 자리가 없다면 상위 부모로 다시 나누는 형식을 반복하게 된다. Deletion의 경우 복잡한 방식을 이용하는데, 만일 leaf 노드이면서 internal node를 지칭하면, 만일 삭제시에 leaf 노드가 있는 node의 개수가 존재하면 그 노드를(가장 오른쪽중 작은 것)을 부모 노드로 옮기어 계속하되, 해당 노드가 없다면(단일노드 삭제) 왼쪽에서 해당 노드를 분리하여 빌려오게 된다.

## <span style = "color:Orange"> **Q3 . B Tree와 B+tree의 차이점을 설명하고 왜 B+Tree가 더 좋은지 설명하시오** </span>

- B Tree는 B+ Tree와 달리 모든 value로 향하는 pointer이 리프 노드에만 있는 것이 아닌, 도중에 non leaf node에도 존재할 수 있어 node수가 전체적으로 작다는 특징이 있으며, search 시에 리프 노드까지 가지 않을 수 도 있다는 점이 있다. 그러나, 리프 노드까지 가야 하는 점이 대부분이고, non leaf node가 많아서 fan out이 줄어들어 깊이가 더 늘어나며, insertion deletion, 구현이 모두 복잡하고 어렵다는 단점이 있어 B+Tree가 더 효율적이라고 할 수 있다.