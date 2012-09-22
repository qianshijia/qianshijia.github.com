---
layout: post
title: "简单的UIView Animation"
date: 2012-09-22 12:33
comments: true
categories: Programming
---
好几天没更新了，最近一直在学iOS的Quartz2D以及Animation。今天就来分享一下我的学习成果，基于UIView的动画是最简单易学的，一般一天的学习就能作出非常漂亮的动画了。接下来正式开始今天的内容。  
先来简单介绍一下今天要实现的效果，我们要让一个小瓢虫在屏幕上转圈，并且在每次拐弯的时候把头转向要拐过去的方向。  
{% img /images/UIView_Animation/homescreen.jpg %}  
首先新建一个SingleView的工程。然后拖一个Button和一个UIImageView到MainStoryboard。然后在ViewController.h里添加一下代码。  
	
	@property (nonatomic, strong) UIImageView *bug;
	@property (nonatomic, strong) IBOutlet UIButton *startBtn;

	-(IBAction)startAnimation;
然后在MainStoryboard里把这两个属性和IBAction联接到相应的控件。  
接着下来我们开始做动画。第一步，要让小瓢虫掉个头（转180度）,方法如下：
	
	- (void)faceRight:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:1.0];//设置动画持续时间
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];//设置动画的样式
    	[UIView setAnimationDelegate:self];//设置代理，只有设置了代理下面那个方法才能在动画结束时被调用
    	[UIView setAnimationDidStopSelector:@selector(moveRight:finished:context:)];//动画结束时调用moveRight方法
    	bug.transform = CGAffineTransformMakeRotation(M_PI);//通过对象的transform属性来设置动画，在这里我们要让对象旋转180度即一个PI
    	[UIView commitAnimations];//提交动画
	}
怎么样，代码并不复杂吧。然后我们在IBAction里面添加  
	
	- (IBAction)startAnimation
	{
    	[self faceRight:nil finished:nil context:nil];
	}
运行工程，然后点击按钮测试一下效果。下一步，我们要让小虫从当前位置移动到屏幕的右边。  
	
	- (void)moveRight:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:2.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(faceDown:finished:context:)];
    	bug.center = CGPointMake(260, bugCenter.y);//改变对象的位置，通过center属性来改变
    	[UIView commitAnimations];
	}
这个方法也没什么好解释的。接下去的几个方法都是依样画葫芦：
	
	- (void)faceDown:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:1.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(moveDown:finished:context:)];
    	bug.transform = CGAffineTransformMakeRotation(-M_PI_2);
    	[UIView commitAnimations];
	}

	- (void)moveDown:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:2.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(faceLeft:finished:context:)];
    	bug.center = CGPointMake(260, 97 + 200);
    	[UIView commitAnimations];
	}

	- (void)faceLeft:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:1.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(moveLeft:finished:context:)];
    	bug.transform = CGAffineTransformMakeRotation(0);
    	[UIView commitAnimations];
	}

	- (void)moveLeft:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:2.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(faceTop:finished:context:)];
    	bug.center = CGPointMake(bugCenter.x, 297);
    	[UIView commitAnimations];
	}

	- (void)faceTop:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:1.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(moveTop:finished:context:)];
    	bug.transform = CGAffineTransformMakeRotation(M_PI_2);
    	[UIView commitAnimations];
	}

	- (void)moveTop:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
	{
    	[UIView beginAnimations:nil context:nil];
    	[UIView setAnimationDuration:2.0];
    	[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    	[UIView setAnimationDelegate:self];
    	[UIView setAnimationDidStopSelector:@selector(faceRight:finished:context:)];
    	bug.center = bugCenter;
    	[UIView commitAnimations];
	}
然后我们运行测试一下，我们会发现刚开始的掉头和移动到右边没什么问题但是后面一个转头朝下的动画就有问题。小虫子在转得的时候又会回到出发点。这是什么原因呢，我也是在这个问题上纠缠了好久，后来才发现虽然我们给小虫子设置了IBOutlet，但是我们并不能通过bug.center来得到小虫的位置（总是返回（0，0））。我觉得这可能是使用了MainStoryboard的问题（具体原因我还在搜索中），于是我就在ViewDidLoad里添加如下代码，手动给小虫子定位。
	
	- (void)viewDidLoad
	{
    	self.bug = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"bug.png"]];
    	self.bug.frame = CGRectMake(30,30, 90, 75);
    	[self.view addSubview:self.bug];
    	bugCenter = bug.center;
    	[super viewDidLoad];
	}
然后再运行一下就没有什么问题了。总结一下，UIViewAnimation很简单易用。只要记住在[UIView beginAnimations:nil context:nil]和[UIView commitAnimations]之间添加配置代码以及动画代码就行了。最后祝大家都能作出漂亮的动画效果。