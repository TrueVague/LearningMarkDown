### 该Markdown根据教程时间轴进行记录。

## 1.对素材的导入

对素材的导入，首先在unity官网中获得(对于免费素材为添加，对于付费素材为购买)所需要的素材，并添加到“我的资源”中。

其次，打开Unity，打开window > Package Editor > Packages: My Assets，在其中将需要的资源下载并导入项目。

## 2.对瓦片地图素材的切割与处理

首先确定Pixels Per Unit，这确定了素材的基本大小。

接下来对单个图片的瓦片地图素材选择Sprite Mode为Multipie，并打开Sprite Editor，Slice，选择Grid By Cell Size。

然后打开Window > 2D >Tile Pallete，将整个素材加入Tile Palette中。

接下来单点其中的每个Tile就可以进行地图的绘画了。

## 3.对Sorting Layer与Order in Layer 的使用

Sorting Layer主要用于区分图与图之间较大的位置关系，Order in Layer主要用于同一 Sorting Layer 中不同贴图的位置关系，一般应用于某一具体贴图。

## 4.关于Rigidbody与Collider

在制作游戏中，引擎重力的应用需要Rigidbody的支持，引擎重力中的Rigidbody是连接物体与引擎重力的纽带。在为一个物体添加Rigidbody后才能应用重力，而物体与物体之间的碰撞则使用Collider(即碰撞体)界定。2D游戏中所使用的Rigidbody与Collider都为带有2D后缀的特殊版本，且不同的物体有各自界定的Collider。

## 5.关于移动控制

### (1)左右移动控制与贴图方向控制

#### [1]左右移动控制

在Unity的Project Settings > Input Manager中，找到Axes > Horizontal，该选项控制水平方向移动。其中，Unity一般已经定义了A/D，←/→为默认控制按键。接下来对需要进行控制的物体(一般为玩家)绑定C#代码，并编写代码。在Unity中建立空代码则会自动对代码框架进行补全。

接下来编写代码如下

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Playercontroller : MonoBehaviour
{
    public Rigidbody2D rb;//表明对哪个刚体进行控制，在Unity中选中Player的Rigidbody
    public float speed;//新建一个浮点型变量，表示移动的速度

    
    void Start()//游戏开始时执行的函数
    {
        
    }

    void FixedUpdate()//每帧执行一次的函数
    {
        Movement();
    }

    void Movement()//由作者新建的移动函数
    {
        float horizontalmove = Input.GetAxis("Horizontal");//定义一个新的变量，得到键盘上的水平输入

        if (horizontalmove != 0)//设定条件(若键盘输入不为空)执行命令
        {
            rb.velocity = new Vector2(horizontalmove * speed, rb.velocity.y);
            /*给予一个新的水平2维变量覆盖原速度*/
        }
    }
}
```

代码中定义了一个中间变量horizontalmove作为代码上下连接的桥梁。

其中，Input.GetAxis函数得到的是某一轴上的输入，其后的Horizontal则是表示水平方向。这个输入值随按键按下的时间变化在-1~1之间变化(可以为小数)，若不按键就为0，按左则为-1，按右则为1。

对于rb.velocity，其为一个指定的刚体的速度变量。这个变量为一个二维变量，由x轴变量和y轴变量共同组成。其真实格式为(rigidbody).velocity。此时代码

```C#
rb.velocity = new Vector2(horizontalmove * speed,rb.velocity.y);
```

的意图为给予指定的rigidbody一个横向速度而纵向速度不改变，同时通过这个方法给予的速度最高为变量speed的值。

#### [2]贴图方向控制

新加入的代码为：

```C#
float FaceDirection = Input.GetAxisRaw("Horizontal");//定义一个新的变量，得到键盘上的水平输入(整数值)

        if (FaceDirection != 0)//设定条件(若键盘输入不为空)执行命令
        {
            transform.localScale = new 			                                 Vector3(FaceDirection,transform.localScale.y,transform.localScale.z);
            /*使人物在其他方向上的scale不变，由键盘输入值取正负控制人物面朝方向*/
        }
```

其中，Input.GetAxisRaw函数得到的是某一轴上输入的单个值,如无输入时其值为0，输入时其值为1或-1

再使用transform.localScale函数，与中间变量连接，直接控制人物的方向。

### (2)跳跃控制

新加入的代码为：

```c#
if (Input.GetButtonDown("Jump"))//使用if语句判断跳跃键是否按下
        {
        	Player_RB.velocity = new Vector2(Player_RB.velocity.x,JumpStandardSpeed);
    		//若按下跳跃键则给予一个初始跳跃力
        }
```

其中，if的判断式中Input.GetButtonDown函数可以得到一个bool值，得到对应按键是否被按下的信息。则当跳跃键按下时，人物的刚体得到一个竖直方向的，大小为JumpStandardSpeed的初速度，在重力控制下进行跳跃。

## 6.动画效果

### (1)前置准备

想要对某个人物实现动画效果，首先就要对人物所在的Sprite绑定一个Animator Controller，其功能为整个人物运动时播放动画的控制器，控制了人物在何时，满足什么条件后，执行什么动画，从而使人物更加生动。

在新建并绑定了Animator Controller后，打开Window > Animation >Animation，选中人物的Sprite，便可以对人物添加各种动画。通过选中所有过程动画并拖拽入Animation窗口，即可实现添加动画的效果。接下来使用拖动或调整采样率的方法可以使人物的实际动作符合设想。

### (2)站立与移动的动画效果制作

当新建动画完成后，打开Windows > Animation >Animator，并选中人物所在的Sprite，则能看到人物的状态机。图中人物的默认状态为由橙箭头指向的状态，每个状态都应该绑定一个相应的动画。

现在只需完成的动画是站立与移动的动画，则我们需要两个状态，即Idle(站立)和Run(移动)。其中，Idle状态应该为任务默认的状态，则橙色箭头应调整为指向Idle，右键Idle状态，选择Set as Layer Default State。

接下来设定状态之间转换的条件，右键一个状态A，点击Make Transition，再点击另一个状态B，即可创造一个有向箭头A→B，代表由状态A变换到状态B所需的条件。在Animator窗口左侧的Parameters中，可以设定条件所使用的变量。

首先创造一个变量Running，再使用C#脚本进行对变量的调控。

添加的新代码如下：

```c#
public Animator Anim;//新建一个Animator以在代码中得到Animator中各变量的名称
if (horizontalMove != 0)//设定条件(若键盘输入不为空)执行命令
        {
            Anim.SetFloat("Running",Mathf.Abs(faceDirection));//将Running变量与faceDirection变量绑定
        }
```

其中，Setfloat函数的作用是给予某变量一个对应的值，用法为SetBool(改变变量,源变量)，而且其中的Bool可改成其他任意类型的变量。

Mathf函数则包括了各种数学函数，例中的Mathf.Abs(变量)可以得到该变量的绝对值并作为一个新变量输出。

***

