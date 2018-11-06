---
layout: post
title:  "C#学习笔记（3）"
date:   2015-11-12 22:00:00
tags: C#
---

## 接口: 

接口不能实例化。  
接口就是让子类来实现的。  
接口可以实现“多继承”（多实现），一个类只能继承自一个父类，但是可以实现多个接口。  
接口解决了不同类型之间的多态问题，比如鱼与船不是同一类型，但是都能在水里“游泳”，只是方式不一样，要对“游泳”实现多态，就只能考虑接口。  
定义一个接口，建议：一定要以大写I开头。  
接口里面只能包含【方法】。  
方法、属性、索引器、事件  →  “方法”。  
接口中的所有成员，都不能显示的 写任何访问修饰符。  
默认是public的访问修饰符。  
接口中的成员，子类必须实现。  
当一个类同时继承父类，并且实现了多个接口的时候，必须将继承类，写在第一个。  
显示实现接口没有访问修饰符，默认是私有的。  
显示实现接口时，在方法名称前加了“接口名”，形如：  接口名.方法名。


---

## 异常处理:

当try块中某行代码发生异常后，从该行代码开始后面的代码都不会继续执行了，程序直接跳转到catch块中进行执行。  
如果希望代码无论如何都要被执行，则一定要将代码放在finally块中。  
1.当catch有无法捕获到的异常时，程序崩溃，但在程序崩溃前会执行finally中的代码，而finally块后的代码则由于程序崩溃了无法执。  
2.如果在catch块中又引发了异常，则finally块中的代码也会在继续引发异常之前执行，但是finally块后的代码则不会执行。  
3.当catch块中有Return语句时，finally块中的代码会在return 之前执行，但是finally块后的代码不会执行。

### catch块的几种写法：  
第一种：  这种写法可以捕获try块中的所有异常
<pre><code>         catch
            {
                Console.WriteLine("发生异常了！");
            }
</code></pre>
第二种：   这种写法可以捕获try块中的所有异常
<pre><code>            catch (Exception ex)
           {
              Console.WriteLine("发生异常了！");
              Console.WriteLine(ex.Message);
              Console.WriteLine(ex.Source);
              Console.WriteLine(ex.StackTrace);
           }
            finally
            {
                Console.WriteLine("finally中的代码@！！");
            }
           Console.ReadKey();
</code></pre>		   
第三种：对不同的异常，使用不同的方式来处理（使用多个不同的catch块来捕获异常）
<pre><code>             catch (NullReferenceException e)
            {
                //空指针异常
                Console.WriteLine("空指针异常：{0}", e.Message);
            }
            catch (DivideByZeroException e)
            {
                //除数为0的异常
                Console.WriteLine("除数为0，详细信息：{0}", e.StackTrace);
            }
            catch (ArgumentException e)
            {
                //参数异常
               Console.WriteLine("参数异常,详细信息：{0}", e.StackTrace);
            }
            catch (Exception e)
            {
               //捕获其余所有的异常
               Console.WriteLine(e.StackTrace);
            }
</code></pre>


---

## 可变参数： 

1.如果方法有多个参数，可变参数必须作为最后一个参数  
2.可变参数可以传递参数也可以不传递参数，如果不传递参数，则args数组为一个长度为0的数组。  
3.可变参数可以直接传递一个数组进来。

---

## out参数：
out参数在使用之前必须在方法里面为out参数赋值。  
out参数无法获取传递进来的变量中的值，只能为传递进来的变量赋值。  
out参数在方法执行完毕之前，必须赋值。

 


---

