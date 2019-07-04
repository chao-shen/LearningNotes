# 两个重叠view事件顺序

## 在activity布局结构如下
![image](https://img-blog.csdnimg.cn/20190704074318638.png "")

其中两个ClickOrderView都是继承view，由布局结构可看，ClickOrderView2在ClickOrderView上面

## 场景1-最下层的ClickOrderView和上层的ClickOrderView2都没有onclickListener事件，日志如下：
![image](https://img-blog.csdnimg.cn/20190704075001836.png "")

其中红框是up事件的结果

## 场景2-最下层的ClickOrderView有onclickListener事件、上层的ClickOrderView2没有onclickListener事件，日志如下：
![image](https://img-blog.csdnimg.cn/20190704075513212.png "")

其中红框是up事件的结果

## 场景3-最下层的ClickOrderView没有onclickListener事件、上层的ClickOrderView2有onclickListener事件，日志如下：
![image](https://img-blog.csdnimg.cn/20190704075729776.png "")

其中红框是up事件的结果

## 场景4-最下层的ClickOrderView和上层的ClickOrderView2都有onclickListener事件，日志如下：
![image](https://img-blog.csdnimg.cn/20190704080006254.png "")

其中红框是up事件的结果，据观察和场景3结果一致