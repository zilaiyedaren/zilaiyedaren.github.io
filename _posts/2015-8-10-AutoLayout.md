---
layout: post
title:  "Auto Layout 使用心得"
date:   2015-08-10
author: 自来也大人
feature-img: "img/touring.jpg"
category: ios
tag: Auto Layout
#tags: [ios, github]
---

自从Apple公司开始发布Storyboard以来，功能越来越强大，我想最终还是会取消xib这种方式，所以熟练使用Storyboard对每一个iOS开发者是必须的技能。下面我转自吕文翰_JohnLui 的博客几篇关于Storyboard的博文，感谢作者的博文！我感觉总结的很好，也转载过来留作笔记方便日后查看。不过我感觉最新鲜的第一手资料还是需要去看Apple的官方文档。

###Auto Layout 使用心得（一）

**简介**

Auto Layout 是苹果在 Xcode 5 (iOS 6) 中新引入的布局方式，旨在解决 3.5 寸和 4 寸屏幕的适配问题。屏幕适配工作在 iPhone 6 及 plus 发布以后变得更加重要，而且以往的“笨办法”的工作量大幅增加，所以很多人开始学习使用 Auto Layout 技术。

**开发环境**

本系列文章的开发环境为：

OS X 10.10.2

Xcode Version 6.2 (6C131e)

*PS：当然本文只是介绍，可以按照最新的Xcode版本进行尝试。*

**1.新建应用**

新建一个 Single View Application，命名为 AutoLayout，如下：

![](http://cc.cocimg.com/api/uploads/20150421/1429583529219847.jpg)

点击选中 Main.storyboard，右侧内容如下：

![](http://cc.cocimg.com/api/uploads/20150421/1429583496261562.jpg)

1、2 两个按钮将会在未来的开发中产生巨大的作用，他们将拥有本系列文章的全局名称：按钮1，按钮2。请先记下他们的位置。*PS:按钮1，可以打开侧边视图，查看所有的视图层次和结构；按钮2，可以对所添加的视图进行修正。*


**2.直接上手，开始使用**

这也是我对学习新的软件编程技术的基本学习方法：有一个具体客观驱动的目标，例如做一个真正要给客户用的软件，而不是“为了学习新技术提高自己”这类伪目标。

让我们直接上手：绘制一个距离左右边都有一定距离、固定高度、垂直居中的按钮，叫“Swift on iOS”。

1.第一步，从右侧拖过来一个按钮，置于页面最中间。会有参考线出现，这一步很容易：

![](http://cc.cocimg.com/api/uploads/20150421/1429583608534842.jpg)

2.选中这个 button，将按钮背景色和前景色进行如下设置： 

![](http://cc.cocimg.com/api/uploads/20150421/1429583617320026.jpg)

3.将按钮左侧边界往左拖动直到自动吸附，留下一定的距离。右侧进行同样操作：   

![](http://cc.cocimg.com/api/uploads/20150421/1429583626492473.jpg)

4.选中这个 button，修改文字为 Swift on iOS:  

![](http://cc.cocimg.com/api/uploads/20150421/1429583653106765.jpg)

5.选中这个 button，点击 按钮2 ，选择这一项：  

![](http://cc.cocimg.com/api/uploads/20150421/1429583679197462.jpg)

**这时候 button 周围会出现一些蓝色的线条，这些就是 Auto Layout 的约束项。**

**3.节本约束已经完成，查看效果**

![](http://cc.cocimg.com/api/uploads/20150421/1429583690984789.jpg)

*PS:你会发现这个不管你是选用那个模拟器，我们所添加的按钮都会在视图的中间*

**4.分析**

选中这个 button，在右侧查看自动生成的约束项：  

![](http://cc.cocimg.com/api/uploads/20150421/1429583818140182.jpg)

只有三项，这三项的意思分别是：和父视图纵向居中对齐、右侧和父视图对齐、左侧和父视图对齐。

我们很容易就能理解这样可以定位一个按钮，但是总感觉少了点什么。**实际上这三个自动生成的约束项并不能描述一个 button 的位置，因为少了一个关键的属性：button 的高度**。以后我们会详细地讨论。

**5.核心思想**

**本质分析**

**Auto Layout 的本质是依靠 某几项约束条件 来达到对某一个元素的定位**。我们可以在某个地方只使用一个约束，以达到一个小目的，例如防止内容遮盖、防止边界溢出等。但我的最佳实践证明，如果把页面上每一个元素的位置都用 Auto Layout 进行 “严格约束” 的话，那么 Auto Layout 可以帮我们省去非常多的计算 frame 的代码。

**“严格约束” 是什么？**

简单来说，严格约束就是对某一个元素的绝对定位，让它在任一屏幕尺寸下都有着唯一的位置。这里的绝对定位不是定死的位置，而是对一个元素 完善的约束条件。*对页面上每一个元素都进行严格约束*


###Auto Layout 使用心得（二）--实现三等分

上一篇文章中，我们共同进行了 Auto Layout 的初体验，在本篇将一起尝试用 Auto Layout 实现三等分。

**Auto Layout 的本质原理**

Auto Layout 的本质是用一些约束条件对元素进行约束，从而让他们显示在我们想让他们显示的地方。

约束主要分为以下几种（欢迎补充）：

1. 相对于父 view 的约束。如：距离上边距 10，左边距 10。

2. 相对于前一个元素的约束。如：距离上一个元素 20，距离左边的元素 5 等。

3. 对齐类约束。如：跟父 view 左对齐，跟上一个元素居中对齐等。

4. 相等约束。如：跟父 view 等宽。

**三等分设计思路**

许多人刚开始接触 Auto Layout，可能会以为它只能实现上面的1、2功能，其实后面3、4两个功能才是强大、特别的地方。接下来我们将尝试设计横向三等分：

1. 第一个元素距离左边一定距离。

2. 最后一个元素距离右边一定距离。

3. 三者高度恒定，宽度相等。

4. 1和2、2和3的横向间距固定。

![](http://cc.cocimg.com/api/uploads/20150421/1429583980462358.gif)

运行结果:

![](http://cc.cocimg.com/api/uploads/20150421/1429584167101581.jpg)

*PS：其实很简单，或者刚开始不是很理解，只要把各视图之间的严格约束设定好，就很容易实现如上功能。写过网页的同学可能知道CSS也是做约束的，其他感觉思路是差不多的。*


###Auto Layout 使用心得（三）—— 自定义 cell 并使用 Auto Layout

本篇中我们将尝试自定义一个 UITableViewCell，并使用 Auto Layout 对其进行约束。

*PS:作者在此处使用了xib,其实使用Storyboard的也是可以实现的cell的这个功能*

**自定义 cell 基础**

在前面的项目中，我们采用 StoryBoard 来组织页面，StoryBoard 可以视为许多个 xib 的集合，所以我们可以得到两个信息：

1. 这个项目通过初始化主 StoryBoard 文件来展现 APP，而 UIViewController 类文件是通过 StoryBoard 文件的绑定来初始化并完成功能的。

2. 我们可以创建新的 StoryBoard 文件或者新的 xib 文件来构造 UI，然后动态地加载进页面。

**创建文件**

新建 Cocoa Touch Class：

![](http://cc.cocimg.com/api/uploads/20150421/1429588635611462.jpg)

设置和下图相同即可：

![](http://cc.cocimg.com/api/uploads/20150421/1429588751640838.jpg)

![](http://cc.cocimg.com/api/uploads/20150421/1429588760760324.jpg)

**分别选中上图中的 1、2 两处，检查 3 处是否已经自动绑定为 firstTableViewCell，如果没有绑定，请先检查选中的元素确实是 2，然后手动绑定即可。**

**完成绑定工作**

切换一页，如下图进行 Identifier 设置：

![](http://cc.cocimg.com/api/uploads/20150421/1429588767287001.jpg)

**新建 Table View Controller 页面**

新建一个 Table View Controller 页面，并把我们之前创建的 Swift on iOS 那个按钮的点击事件绑定过去，我们得到：

![](http://cc.cocimg.com/api/uploads/20150421/1429588773133441.jpg)

然后创建一个名为 firstTableViewController 的 UITableViewController 类，创建流程跟前面基本一致。不要创建 xib。然后选中 StoryBoard 中的 Table View Controller（选中之后有蓝色边框包裹），在右侧对它和 firstTableViewController 类进行绑定：

**调用自定义 cell**

修改 firstTableViewController 类中的有效代码如下：(基于Swift)

```Swift
	import UIKit
	class firstTableViewController: UITableViewController {
	    override func viewDidLoad() {
	        super.viewDidLoad()
	        var nib = UINib(nibName: "firstTableViewCell", bundle: nil)
	        self.tableView.registerNib(nib, forCellReuseIdentifier: "firstTableViewCell")
	    }
	    override func didReceiveMemoryWarning() {
	        super.didReceiveMemoryWarning()
	    }
	    // MARK: - Table view data source
	    override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
	        return 1
	    }
	    override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
	        return 10
	    }
	    override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
	        let cell = tableView.dequeueReusableCellWithIdentifier("firstTableViewCell", forIndexPath: indexPath) as firstTableViewCell
	        cell.textLabel?.text = indexPath.row.description
	        return cell
	    }
	}

```

viewDidLoad() 中添加的两行代码是载入 xib 的操作。最下面的三个 func 分别是定义：

1. self.tableView 中有多少个 section

2. 每个 section 中分别有多少个条目

3. 实例化每个条目，提供内容

![](http://cc.cocimg.com/api/uploads/20150421/1429588877895086.jpg)

**给自定义 cell 添加元素并使用 Auto Layout 约束**

首先向 Images.xcassets 中随意加入一张图片。

然后在左侧文件树中选中 firstTableViewCell.xib，从右侧组件库中拖进去一个 Image View，并且在右侧将其尺寸设置如下图右侧：

![](http://cc.cocimg.com/api/uploads/20150421/1429588891916447.jpg)

给 ImageView 添加约束：

![](http://cc.cocimg.com/api/uploads/20150421/1429588904586371.jpg)

再从右侧组件库中拖入一个 UILabel，吸附到最右侧，垂直居中，为其添加自动约束，这一步不再赘述。

**在 firstTableViewCell 类中绑定 xib 中拖进去的元素**

选中 firstTableViewCell.xib，切换到双视图，直接进行拖动绑定：

![](http://cc.cocimg.com/api/uploads/20150421/1429588911457237.jpg)

**约束 cell 的高度**

在 firstTableViewController 中添加以下方法：

    override func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
    return 50
	}

给自定义的 UILabel 添加内容

修改 firstTableViewController 中以下函数为：

    override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCellWithIdentifier("firstTableViewCell", forIndexPath: indexPath) as firstTableViewCell
    cell.firstLabel.text = indexPath.row.description
    return cell
	}

查看结果

![](http://cc.cocimg.com/api/uploads/20150421/1429588929217529.jpg)


###Auto Layout 使用心得（四）—— 22 行代码实现拖动回弹

**搭建界面**

删除首页中间的按钮，添加一个 View ，设置一种背景色便于辨认，然后对其进行绝对约束：

![](http://cc.cocimg.com/api/uploads/20150421/1429596284765699.jpg)

拖动一个 UIPanGestureRecognizer 到该 View 上：

![](http://cc.cocimg.com/api/uploads/20150421/1429596292175307.jpg)

**属性绑定**

切换到双向视图，分别右键拖动 UIPanGestureRecognizer 和该 View 的 Top Space 的 Auto Layout 属性到 ViewController 中绑定：

![](http://cc.cocimg.com/api/uploads/20150421/1429596303427243.jpg)

然后将 UIPanGestureRecognizer 右键拖动绑定：

![](http://cc.cocimg.com/api/uploads/20150421/1429596310788672.jpg)

**编写代码**

```Swift
	class ViewController: UIViewController {
     
    var middleViewTopSpaceLayoutConstant: CGFloat!
    var middleViewOriginY: CGFloat!
     
    @IBOutlet weak var middleView: UIView!
    @IBOutlet weak var middleViewTopSpaceLayout: NSLayoutConstraint!
    @IBOutlet var panGesture: UIPanGestureRecognizer!
    override func viewDidLoad() {
        super.viewDidLoad()
         
        panGesture.addTarget(self, action: Selector("pan"))
        middleViewTopSpaceLayoutConstant = middleViewTopSpaceLayout.constant
        middleViewOriginY = middleView.frame.origin.y
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
     
    func pan() {
        if panGesture.state == UIGestureRecognizerState.Ended {
            UIView.animateWithDuration(0.4, delay: 0, options: UIViewAnimationOptions.CurveEaseInOut, animations: { () -> Void in
                self.middleView.frame.origin.y = self.middleViewOriginY
                }, completion: { (success) -> Void in
                    if success {
                        self.middleViewTopSpaceLayout.constant = self.middleViewTopSpaceLayoutConstant
                    }
            })
            return
        }
        let y = panGesture.translationInView(self.view).y
        middleViewTopSpaceLayout.constant = middleViewTopSpaceLayoutConstant + y
    }
}

```

![](http://cc.cocimg.com/api/uploads/20150421/1429596320604451.gif)


###Auto Layout 使用心得（五）--根据文字、图片自动计算 UITableViewCell 高度

**搭建界面**

恢复之前删除的按钮

放置一个按钮，恢复到 firstTableViewController 的连接：

![](http://cc.cocimg.com/api/uploads/20150421/1429597756447864.jpg)

别忘了添加约束让他居中哦。

**修改 firstTableViewCell**

将 firstTableViewCell 的尺寸设置为 600 * 81，将 logo 的尺寸设置为 80 * 80。将 logo 的约束修改为如下图所示：

![](http://cc.cocimg.com/api/uploads/20150421/1429597773306705.jpg)

修改 label 的尺寸和位置，添加约束如下图：

![](http://cc.cocimg.com/api/uploads/20150421/1429597785663884.jpg)

**给 ViewController 增加 UINavigationController 嵌套**

为了便于返回。操作如下图：

![](http://cc.cocimg.com/api/uploads/20150421/1429597797398003.jpg)

![](http://cc.cocimg.com/api/uploads/20150421/1429597826747159.jpg)

**根据 label 自动计算 firstTableViewCell 高度**

选中 label，设置 lines 行数为 0，表示不限长度自动折行：

![](http://cc.cocimg.com/api/uploads/20150421/1429597853622634.jpg)

修改 label 的文字内容让其超出一行：

```Swift
import UIKit
class firstTableViewController: UITableViewController {
     
    var labelArray = Array() // 用于存储 label 文字内容
    override func viewDidLoad() {
        super.viewDidLoad()
        var nib = UINib(nibName: "firstTableViewCell", bundle: nil)
        self.tableView.registerNib(nib, forCellReuseIdentifier: "firstTableViewCell")
         
        // 循环生成 label 文字内容
        for i in 1...10 {
            var text = ""
            for j in 1...i {
                text += "Auto Layout"
            }
            labelArray.append(text)
        }
    }
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
    // MARK: - Table view data source
    override func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
        return 50
    }
    override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        return 1
    }
    override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return labelArray.count
    }
    override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("firstTableViewCell", forIndexPath: indexPath) as! firstTableViewCell
        cell.firstLabel.text = labelArray[indexPath.row]
        return cell
    }
}

```

现在到了最关键的时刻，驱动 UITableViewCell 适应 Label 内容：

1. 使用 estimatedHeightForRowAtIndexPath 替代 heightForRowAtIndexPath

estimatedHeightForRowAtIndexPath 是 iOS 7 推出的新 API。如果列表行数有一万行，那么 heightForRowAtIndexPath 就会在列表显示之前计算一万次，而 estimatedHeightForRowAtIndexPath 只会计算当前屏幕中显示着的几行，会大大提高数据量很大时候的性能。

2. 新建一个 prototypeCell 成员变量以复用，并在 viewDidLoad 中初始化

```Swift
	class firstTableViewController: UITableViewController {
     
    var labelArray = Array() // 用于存储 label 文字内容
     
    var prototypeCell: firstTableViewCell!
    override func viewDidLoad() {
        super.viewDidLoad()
        var nib = UINib(nibName: "firstTableViewCell", bundle: nil)
        self.tableView.registerNib(nib, forCellReuseIdentifier: "firstTableViewCell")
         
        // 初始化 prototypeCell 以便复用
        prototypeCell = tableView.dequeueReusableCellWithIdentifier("firstTableViewCell") as! firstTableViewCell
 ...        
}
}

```
3. 计算出高度

    override func tableView(tableView: UITableView, estimatedHeightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
	    let cell = prototypeCell
	    cell.firstLabel.text = labelArray[indexPath.row]
	    return cell.contentView.systemLayoutSizeFittingSize(UILayoutFittingCompressedSize).height + 1
	}

![](http://cc.cocimg.com/api/uploads/20150421/1429597912228362.jpg)

**特别注意：**

上面让 firstTableViewCell 根据 label 自动计算高度的过程中，有一个超级大坑：如果给左侧 UIImageView 赋的图片较大（大于 80px），将看到如下奇怪的结果：

![](http://cc.cocimg.com/api/uploads/20150421/1429597922791447.jpg)

这只是因为图片把 UITableViewCell 撑大了，并不是我们的计算没有效果。

解决方法：根据图片自动计算 firstTableViewCell 高度

首先，把图片的渲染模式改成 Aspect Fit：

![](http://cc.cocimg.com/api/uploads/20150421/1429597928764159.jpg)

给 Images.xcassets 增加三张图片，命名为 0、1、2，尺寸从小到大：

![](http://cc.cocimg.com/api/uploads/20150421/1429597934138530.jpg)

给 cellForRowAtIndexPath 增加代码：

    if indexPath.row < 3 {
    	cell.logoImageView.image = UIImage(named: indexPath.row.description)
	}


![](http://cc.cocimg.com/api/uploads/20150421/1429597952907140.jpg)

前两个 cell 看起来比较正常，第三个为什么多出了那么多空白？这就是使用 Auto Layout 限制图片宽度为 80px 的原生问题：宽度虽然限制了，高度却依然是原图的高度。解决办法也很简单：如果图片宽度大于 80px，就重绘一张 80px 宽度的图片填充进去。

新建一个 Group（虚拟文件夹），叫 Extensions，并在其内部新建 UIImage.swift 文件，内容如下：

    import UIKit
	extension UIImage {
    func resizeToSize(size: CGSize) -> UIImage {
        UIGraphicsBeginImageContext(size)
        self.drawInRect(CGRectMake(0, 0, size.width, size.height))
        let newImage = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
         
        return newImage
    }
	}


给 UIImage 类扩展了一个名为 resizeToSize 的方法，返回一个按照要求的大小重绘过的 UIImage 对象。修改 cellForRowAtIndexPath 的代码为：

```Swift
	override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCellWithIdentifier("firstTableViewCell", forIndexPath: indexPath) as! firstTableViewCell
    cell.firstLabel.text = labelArray[indexPath.row]
     
    if indexPath.row < 3 {
        var image = UIImage(named: indexPath.row.description)!
        if image.size.width > 80 {
            image = image.resizeToSize(CGSizeMake(80, image.size.height * (80 / image.size.width)))
        }
        cell.logoImageView.image = image
    }
    return cell
}

```

查看效果

![](http://cc.cocimg.com/api/uploads/20150421/1429598171687910.gif)











