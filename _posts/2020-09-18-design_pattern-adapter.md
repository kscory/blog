---
layout: post
comments: true
title:  "[Design Pattern] Adapter 패턴"
date:   2020-09-18
author: Cory
categories: Design_Pattern
permalink : /dev/design-pattern/adapter
tags: design pattern
---

__Adapter Pattern__ 이란 클래스의 인터페이스를 사용자가 기대하는 다른 인터페이스로 변환하는 패턴으로, 호환성이 없는 인터페이스 때문에 함께 동작할 수 없는 클래스들이 함께 작동하도록 해줍니다. (by [wikipedia](https://ko.wikipedia.org/wiki/%EC%96%B4%EB%8C%91%ED%84%B0_%ED%8C%A8%ED%84%B4)), Adapter 의 다른의미로 __Wrapper__ 라는 말을 사용하는데 다들 한번쯤은 들어보지 않았을까 싶습니다.

어댑터 패턴은 __클래스 어댑터__, __객체 어댑터__ 두 가지 방식으로 사용할 수 있습니다. 차이는 클래스 어댑터는 상속을 사용하고 객체 어댑터는 합성을 사용한다는 점입니다. 아래 다이어 그램을 확인해보면 결국 Adapter 가 `operation` 을 사용할 때 `speicificOperation` 함수를 호출하는데 이것이 내부의 객체로 오는지 상속을 통해서 오는지의 차이가 있을 뿐입니다.

![](https://lh3.googleusercontent.com/pw/ACtC-3eZmLlzDNA8f2izSaCVPqEYHFDjnT6hneJtA_EH0nzAb7-2nnbtMSMosJTsCx0n__cDEKbVno00aRrPktxnuzE_Oao5tXMQDfkUQQ367hehJLhDhzGTYJ5w306Pnc7GFVS9kH_fnJ5-7_X7UaDt6aM2=w640-h240-no?authuser=0)
<div style="text-align: center;">출처: https://en.wikipedia.org/wiki/Adapter_pattern</div><br>

구성 요소에는 다음과 같은 요소가 있습니다.

* __Target__`: Client 가 직접적으로 사용하ㅇ려고 하는 인터페이스를 정의한다. (Adaptee 가 지원하길 바라는 인터페이스를 의미한다.)
* __Adaptee__: Adapter 에서 사용하고자 하는 인터페이스를 정의하고 있다.
* __Adapter__: Target 인터페이스를 상속받아서 구현하는 클래스로 이 때 Adaptee 의 함수를 사용하게 된다.
* __Client__: Target 인터페이스를 사용한다.

간단하게 이를 클래스로 구현해보면 다음과 같습니다.

```java
// Target
interface Target {
    void operation();
}

// Adaptee
interface Adaptee {
    void specificOperation();
}

// 클래스 어댑터
class ClassAdapter implements Target, Adaptee {

    public ClassAdapter() {
    }

    @Override
    public void operation() {
        // 상속받은 specificOperation 을 호출
        this.specificOperation();
    }

    @Override
    public void specificOperation() {
        System.out.println("this is class adapter");
    }
}

// 객체 어댑터
class ObjectAdapter implements Target {

    Adaptee adaptee;

    public ObjectAdapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void operation() {
        // 객체의 specificOperation 을 호출
        adaptee.specificOperation();
    }
}

class SampleAdaptee implements Adaptee {
    @Override
    public void specificOperation() {
        System.out.println("this is class adapter");
    }
}

// Client
class Client {

    Target targetClassAdapter = new ClassAdapter();
    Target targetObjectAdapter = new ObjectAdapter(new SampleAdaptee());
    
    void execute() {
        // 클래스 adapter
        targetClassAdapter.operation();
        
        // 객체 어댑터
        targetObjectAdapter.operation();
    }
}
```

### 사용하는 이유

결국 어댑터 패턴이라는 것은 모든 것을 하나의 인터페이스로 추상화하기 힘들기 때문에 사용됩니다. 즉, 기존 라이브러리가 더 이상 실제 서비스와 맞지 않아 다시 만들어야 하는데, 이 라이브러리를 다른 부서에서도 사용하고 있다고 생각해 봅시다. 그럼 이 라이브러리를 고치게 되면, 다른 부서에 영향을 끼치게 될 것입니다. 그럴 경우 Adapter 패턴을 이용하면 기존 라이브러리를 건들지 않으면서 새로운 기능을 추가시킬 수 있을 것입니다.

특히 만약 정책이 자주 바뀌는 경우, 계속해서 라이브러리를 바꿔야 할까요? 즉, 기존의 것을 최대한 건들지 않고 구현할 수 있어야 합니다. 그렇기 때문에 오래된(?) 프레임워크 혹은 많은 사람들이 개발해야 하는 프로그램에는 xxxAdapter 라는 이름을 이용해서 기존에 만들었던 코드를 고치지 않고 어댑터를 사용하는 예시를 흔하게 확인해볼 수 있습니다.

### 장점과 단점

그럼 Adapter 패턴의 장단점을 알아보겠습니다.

장점
1. Adapter 패턴의 가장 큰 장점을 기존 코드를 변경하지 않아도 된다는 점입니다.
2. 기존 코드를 변경하지 않기 때문에 클래스 재활용성을 증가시킬 수 있습니다.

단점
1. 구성요소를 위해 클래스를 증가시켜야 하기 때문에 복잡도가 증가할 수 있습니다.
2. 클래스 Adapter 의 경우 상속을 사용하기 때문에 유연하지 못합니다.
3. 반명 객체 Adapter 의 경우는 대부분의 코드를 다시 작성해야 하기 때문에 효율적이지 못합니다.

### 실제 사용 예제

제가 가장 먼저 생각났던 어댑터는 안드로이드 리사이클러뷰 어댑터였습니다. 그래서 이를 만들면서 어댑터 패턴이 사용되는 방식에 대해서 설명드리겠습니다.

먼저 구성요소에 대해서 간단하게 설명드리면

* __Target__`: RecyclerView.Adapter
* __Adaptee__: items (mutableListOf<T>())
* __Adapter__: BaseAdapter
* __Client__: RecyclerView

여기서 Client 와 Target 은 안드로이드 프레임워크에 이미 정의되어 있습니다. 그래서 저희는 사실상 사용하고 싶은 Adapter 와 Adaptee 를 정의하면 됩니다. 여기서는 객체 어댑터 방식으로 사용하게 됩니다. (ViewHolder 는 생략하겠습니다.)

여기서 가장 이해하기 쉬운 부분이 `getItemCount()` 함수입니다. 즉, getItemCount 를 호출하면 내부에 있는 items 가 대신해서 size 를 호출해 줍니다.

즉, recyclerview 에서는 count 를 통해 view 를 그리게 되는데 어댑터에서는 나름 items 라는 adaptee 에게 함수 호출을 위임하게 됩니다.

```java
abstract class BaseAdapter<B : ViewDataBinding, T : Any>(
    @LayoutRes private val layoutResId: Int,
    private val bindingVariableId: Int
) : RecyclerView.Adapter<BaseViewHolder<B, T>>() {

    protected var items = mutableListOf<T>()

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) =
        BaseViewHolder<B, T>(
            layoutResId, parent, bindingVariableId
        )

    // items 에게 호출을 위임하고 있다.
    override fun getItemCount(): Int = items.size

    fun getItem(position: Int) = items[position]

    fun updateItems(data: MutableList<T>) {
        items = data
        notifyDataSetChanged()
    }

    override fun onBindViewHolder(holder: BaseViewHolder<B, T>, position: Int) {
        holder.bind(getItem(position))
    }
}
```

이렇게 해서 Adapter 패턴에 대해서 알아봤습니다. 

예전에 안드로이드 프로그래밍을 공부했을 때, 가장 많이 사용한 클래스가 Adapter 클래스였습니다. 그 때에는 그냥 연결하면 뷰와 데이터가 연결된다. 라고만 생각하고 넘어갔었는데, 왜 Adapter 로 적용되었는지 생각해보니 재밌기도 하면서 신기하기도 합니다.
