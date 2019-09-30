---
layout:     post
title:      "事件回调系统"
subtitle:   "Event Callback System"
date:       2019-09-30 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 游戏开发
    - 客户端
    - Unity
---

> **事件（Event）** 基本上说是一个用户操作，如按键、点击、鼠标移动等等，或者是一些提示信息，如系统生成的通知。应用程序需要在事件发生时响应事件。

在游戏开发过程中可能会出现这样的情况，一个用户操作可能会涉及很多事件的响应以及回调函数的执行。举个最简单的栗子，用户点击游戏界面上的一个按键，游戏中对应的文本组件将会显示“Hello World”，先放出效果图：

**按键点击前：**

![ButtonUnclick](/img/event-callback-system/ButtonUnclick.png)

**按键点击后：**

![ButtonClick](/img/event-callback-system/ButtonClick.png)



一个最基本的实现是：

```c#
private void OnButtonClick()
{
    GameObject.Find("Text").GetComponent<Text>().text = "Hello World";
}
```

但这样子的实现会导致最后代码越来越难以维护。



## 事件回调系统

> 事件回调系统本质上是一个发布-订阅（Publisher-Subscriber）模型。发布器（Publisher）是一个包含事件和委托定义的对象。订阅器（Subscriber）是一个接受事件并提供事件处理程序的对象。

在上面的栗子中，游戏按键是一个发布器，当用户点击游戏按键时，就会触发对应事件的发生，而文本组件是一个订阅器，用于监听事件的发生，当事件被触发时，文本组件就会调用相应的函数响应事件的发生。

### 具体实现

1. 定义事件类型

   ```c#
   // 事件类型
   public enum EventType
   {
       ButtonClick,
   }
   ```

   事件类型用于与具体事件的映射，当某个事件类型被触发时，事件回调系统就会调用对应的事件。

2. 定义回调函数以及事件类型与事件的映射关系

   ```c#
   // 回调委托函数
   public delegate void Callback(params object[] args);
   
   // 事件类型与事件的映射
   private static Dictionary<EventType, Callback> Events = new Dictionary<EventType, Callback>();
   ```

   回调函数使用了object[]作为参数，因此可以传递任意个数任意类型的参数作为回调函数的形参。

3. 实现绑定事件（监听事件）以及解绑事件（解除监听）的方法

   ```c#
   // 绑定事件
   public static void AddListener(EventType eventType, Callback callback)
   {
       if (!Events.ContainsKey(eventType))
       {
           Events.Add(eventType, null);
       }
       Events[eventType] += callback;
   }
   
   // 解绑事件
   public static void RemoveListener(EventType eventType, Callback callback)
   {
       if (!Events.ContainsKey(eventType))
       {
           Debug.Log(string.Format("{0}事件类型不存在", eventType));
           return;
       }
       Events[eventType] -= callback;
   }
   ```

   eventType是事件的类型，而callback是事件发生时的回调函数。

4. 实现触发事件的方法

   ```c#
   // 触发事件
   public static void Broadcast(EventType eventType, params object[] args)
   {
       if (Events != null && Events.ContainsKey(eventType) && Events[eventType] != null)
       {
           Events[eventType](args);
       }
   }
   ```

至此已经实现了事件回调系统的核心功能。

### 发布器和订阅器

* 发布器的实现

  ```c#
  public class ButtonScript : MonoBehaviour
  {
      private void Start()
      {
          GetComponent<Button>().onClick.AddListener(OnButtonClick);
      }
  
      private void OnButtonClick()
      {
          // 触发事件
          EventCallbackSystem.Broadcast(EventCallbackSystem.EventType.ButtonClick, "Hello World");
      }
  }
  ```

  在上述的栗子中，发布器是游戏按键，其触发了事件的发生。

* 订阅器的实现

  ```c#
  public class TextScript : MonoBehaviour
  {
      private void Start()
      {
          // 绑定事件类型以及对应的回调函数
          EventCallbackSystem.AddListener(EventCallbackSystem.EventType.ButtonClick, ShowText);
      }
  
      private void OnDestroy()
      {
          EventCallbackSystem.RemoveListener(EventCallbackSystem.EventType.ButtonClick, ShowText);
      }
  
      private void ShowText(params object[] args)
      {
          // 回调函数的具体实现
          GetComponent<Text>().text = args[0].ToString();
      }
  }
  ```

  在上述的栗子中，订阅器是文本组件，其实现了事件的绑定以及具体回调函数。订阅器在整个游戏的运行过程中一直处于监听的状态，当对应的事件被触发时，调用回调函数。

