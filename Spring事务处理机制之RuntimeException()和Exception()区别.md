**RuntimeException()和Exception()区别：**

1.继承自RuntimeException或error的是非检查型异常，而继承自exception的则是检查型异常（当然，runtimeexception本身也是exception的子类）。
2.对非检查型类异常可以不用捕获，而检查型异常则必须用try语句块进行处理或者把异常交给上级方法处理总之就是必须写代码处理它。所以必须在service捕获异常，然后再次抛出，这样事务方才起效。

**Spring事务默认只在发生未被捕获的RuntimeException()时才进行回滚。**

Spring通过SpringAOP进行声明式事务管理：

　　SpringAOP异常捕获的原理：被拦截的方法需显式抛出异常，并不能经任何处理，这样aop代理才能捕获到方法的异常，才能进行回滚，

默认情况下SpringAOP只捕获RuntimeException的异常，因此不是RuntimeException或其子类的异常不能够捕获，默认情况下不进行回滚，

但可以通过配置来捕获特定的异常并回滚 。

因此：

**方法1：**在service层不使用try......catch或者在catch中最后加上throw new RuntimeException()，这样程序异常时aop才可以捕获异常并进行回滚。

最终在service上层（如controller层、action层、view层）要继续捕获这个异常并处理。

**方法2：**在service层方法上进行配置，改变默认规则，方法如下 ：

（1） 让checked例外也回滚：在整个方法前加上 @Transactional(rollbackFor=Exception.class)

 

（2）让unchecked例外不回滚： @Transactional(notRollbackFor=RunTimeException.class)

 

（3）不需要事务管理的(只查询的)方法：@Transactional(propagation=Propagation.*NOT_SUPPORTED*)

 

 在整个方法运行前就不会开启事务还可以加上：@Transactional(propagation=Propagation.*NOT_SUPPORTED*,readOnly=true)，这样就做成一个只读事务，可以提高效率。

 