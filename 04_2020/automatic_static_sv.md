# static vs automatic variables

最近发现在UVM agent中的某个task中声明的变量在进入fork的时候，进程不能复制task中声明的变量的值，只能复制地址。因此多个线程同时使用同一个变量的时候会发生race condition。debug后发现原因是：task/function中声明的变量默认是静态变量。
之前我对static关键词的理解仅限于static定义的task/function可以在类的外部进行调用。SV中还有可配套使用的staic和automatic关键词规定变量的生命周期。

## 生命周期
 - static：无论在类内或者外声明，此variable/function/task具有静态生命周期，在build和simulation期间一直存在，但是作用域是local的。分配一个内存。
   1. 声明在module/program/interface/task/function之外的变量都是默认静态生命周期并有全局作用域。
   2. 声明在module/program/interface之内但是在task/function之外的变量是默认静态生命周期但是作用与自己的作用域。
   3. 声明在static task/function/block内的变量是默认静态生命周期。
   4. 在automatic scope中可以使用static声明变量。当每次function/task被call或者block被使用时，会读取此变量上次被修改的值。
 - automatic：此variable/function/task的生命周期局限在调用时或者作用域之内。作用域是local的。实例化分配内存。
   1. 声明在automatic function/rask/block内的变量的生命周期默认跟scope的生命周期相同。
   2. 可以在static scope中声明automatic变量。

## 默认生命周期
 - 在SV中所有task/function都有默认的静态生命周期。需要automatic的function/task的时候可以用关键词声明，而且其中的变量会在每次被call的时候重新分配内存， 并在每次结束call的时候被销毁。
 - Automatic function/task无法被hierarchical reference。
 - 因为class本身需要被实例化使用，所以在class中的method都默认auto周期。


## 关键词的位置
 - Automatic：只能用于指示生命周期，声明时在function/task关键词右侧。
 - Static：
   1. 当用于声明静态属性的时候位于function/task关键词的左侧，说明此function/task属于这个class，可以被外部调用；而不属于此class被实例化之后的某个object。其余的method都是protected。指示静态属性用的static关键词跟automatic关键词没有任何关系。然而值得注意的是静态function/task中的变量都是默认静态生命周期的。
   2. 当用于指示静态周期的时候位于function/task关键词的右侧，与automatic关键词相对应。