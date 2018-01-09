# 继承和组合的使用时机

- 继承是一个大概念中的细分种类，比如有一个鸡类，那么公鸡和母鸡可以继承此鸡类。在这里鸡是大概念，而公鸡母鸡细分种类，毕竟无论公母，它们都是鸡。
- 组合是一个大概念的部件component的关系，比如汽车中有引擎、冷气机、CD机、方向盘等等，这些部件组成了一台车，故是组合关系，毕竟它们任一个部件都不能单独作为一台车。而车有型号A123、B456等，它们独立成车，所以就是继承关系了。


### 继承

![image](https://github.com/xcw0754/coder-skills/blob/master/pics/inherit_relation.png)

```
class chicken{
	//鸡的基类
};
class rooster: public chicken {
	//公鸡，继承鸡
};
class hen: public chicken {
	//母鸡，继承鸡
};
```


### 组合

![image](https://github.com/xcw0754/coder-skills/blob/master/pics/component_relation.png)

```
class CDMachine{
	//CD机
};
class AirMachine{
	//空气机
};
class Engine{
	//引擎
};

class Car{	// 不用继承，用组合
private:
	CDMachine a;
    AirMachine b;
    Engine c;
}


```