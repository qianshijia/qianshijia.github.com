---
layout: post
title: "CoreData学习笔记"
date: 2012-09-13 20:22
comments: true
categories: Programming
---
最近一段时间一直忙着投简历，主要方向当然是iOS的开发。在看了不下30家的招聘公司的要求之后，发现很多都要求应聘者了解或者掌握CoreData。所以今天就来学习下CoreData基础。我会用一个简单的project来介绍CoreData的基础知识。废话不多说，现在开始。  
CoreData是为开发者提供的一种简单稳定的数据存储和读取的接口。相比起sqlite 3，它能使代码更简洁，运行效率更高。我们今天做的这个项目很简单，就是有这么两张表，第一章表叫StudentInfo，包含了学生姓名，年龄，性别，以及住址。第二章表叫AddressDetails，包含了街道名，城市名，省，和邮编。应该有人已经能看出来，这张表就是对应着第一张表里的住址。所以这两张表是一对一的关系。好了，如果我们用数据库的话，第一步肯定就是建表啦，然后还要敲一大堆SQL代码。我最烦SQL代码，因为很容易打错，尤其是语句很长的时候。CoreData就很好的帮我们解决了这个问题。请看：  
第一步还是新建工程，为了方便我们这里直接选择Master-Detail Applications。项目名称就叫StudentManagementSystem吧， Class Prefix输入SMS，然后device选iPhone。  
{% img /images/learning_coredata/newproj.jpg %}  
注意下面的方框要勾选前面三个：Use Storyboards, Use Core Data, Use Automatic Reference Counting。  
{% img /images/learning_coredata/projdetail.jpg %}  
接下来我们要先对工程做一些修改。首先删除以下4个文件，直接选择move to trash：

* SMSMasterViewController.h
* SMSMasterViewController.m
* SMSDetailViewController.h
* SMSDetailViewController.m

然后新建一个文件叫SMSMasterViewController，subclass选择UITableViewController，注意下面两个选项都不要勾选，然后确定。  
{% img /images/learning_coredata/masterview.jpg %}  
在SMSMasterViewController.h里添加一个属性：
	
	@property (nanotomic,strong) NSManagedObjectContext *managedObjectContext;
并且在.m里synthesize这个属性。接下去到MainStoryboard里选中DetailViewController并按Delete键删除。  
好了现在打开SMSAppDelegate.h文件我们先来熟悉一些概念。可以看到上面有三个属性，类名分别是：

+ NSManagedObjectContext
+ NSManagedObjectModel
+ NSPersistentStoreCoordinator

简单介绍下这三个类是什么：

+ *NSManagedObjectContext*：相当于一张便签纸，把我们从数据库获取的所有objects记录在上面。我们编程的时候和这个类的对象打的交道最多，因为我们必须通过它来进行数据的插入，删除和修改等操作。
+ *NSManagedObjectModel*：顾名思义，它代表了数据库里存储的数据的结构。它定义了存储数据包含了哪些属性，以及与其他数据之间的关系。xCode提供了一种很方便的可视化操作来设置这些结构。
+ *NSPersistentStoreCoordinator*：它负责建立与数据库的联接。

我们下面就来看看xCode提供给我们的可视化编辑器，用来方便快捷的编辑我们想要的NSManagedObjectModel。在左边的工程目录里找到StudentManagementSystem.xcdatamodeld，点击它，并且在左下角的Editor Style里选到右边的那个选项。  
{% img /images/learning_coredata/editor.jpg %}  
我们先点选可视化编辑框里的那个已经存在的Entity，然后把它删掉。接下去我们一步步来添加一个Entity。点击Add Entity，然后命名为StudentInfo。  
{% img /images/learning_coredata/addentity.jpg %}  
我们可以看到它包含了attributes和relationship两栏。接下来我们给它添加attributes，在图形化界面中选中这个Entity并且点击Add Attribute不放，在弹出菜单里选择Add Attribute。  
{% img /images/learning_coredata/addattr.jpg %}
然后在图形界面里选中我们刚创建的Attribute，在右边的编辑区域里把Name改成name，把Attribute Type改成string。接着用同样的方法创建age和gender，注意age的type我们用Integer32。
{% img /images/learning_coredata/nameattr.jpg %}  
属性添加完毕后，接着依样画葫芦把另一个Entity，AddressDetails也创建好。完成后屏幕上应该是这样：  
{% img /images/learning_coredata/attrdone.jpg %}  
开头我们就说了，这两个entity（表）之间是一对一的关系，接下来我们就来创建这种关系。选中StudentInfo，点击Add Attribute不放，在弹出菜单里选择Add Relationship。把这个relationship改名为address，并且Destination选择AddressDetails。然后，因为苹果建议我们每当创建一个relationship的同时创建一个反向的relationship。选中AddressDetails添加Relationship改名为studentinfo，destination为StudentInfo，然后Inverse选择我们刚创建的address。完成后界面就是这样的：  
{% img /images/learning_coredata/reladone.jpg %}  
下面一步，我们从测试中来学习CoreData的工作机制。首先我们添加几条数据，在AppDelegate里的application:didFinishLaunchingWithOptions方法里添加如下代码：  
	
	NSManagedObjectContext *context = self.managedObjectContext;
    //创建NSManagedObject对象指向StudentInfo这个Entity，并设置Value。
    NSManagedObject *studentInfo = [NSEntityDescription insertNewObjectForEntityForName:@"StudentInfo" inManagedObjectContext:context];
    [studentInfo setValue:@"Eric" forKey:@"name"];
    [studentInfo setValue:[NSNumber numberWithInt:25] forKey:@"age"];
    [studentInfo setValue:@"male" forKey:@"gender"];
    
    //创建NSManagedObject对象指向AddressDetails这个Entity，并设置Value。
    NSManagedObject *addressDetails = [NSEntityDescription insertNewObjectForEntityForName:@"AddressDetails" inManagedObjectContext:context];
    [addressDetails setValue:@"507/909 Swanston St." forKey:@"street"];
    [addressDetails setValue:@"Melbourne" forKey:@"city"];
    [addressDetails setValue:@"Victoria" forKey:@"state"];
    [addressDetails setValue:[NSNumber numberWithInt:3053] forKey:@"zip"];
    
    //添加两个对象之间的关系
    [studentInfo setValue:addressDetails forKey:@"address"];
    [addressDetails setValue:studentInfo forKey:@"studentinfo"];
    
    //保存所创建的对象
    NSError *error;
    if(![context save:&error])
    {
        NSLog(@"Couldn't save: %@", [error localizedDescription]);
    }
然后紧接着我们来测试一下是否能获取到刚才添加的这些值。接着刚才的代码往下写：
	
	NSFetchRequest *fetechRequest = [[NSFetchRequest alloc] init];
    NSEntityDescription *entity = [NSEntityDescription entityForName:@"StudentInfo" inManagedObjectContext:context];
    [fetechRequest setEntity:entity];
    NSArray *fetechedResults = [context executeFetchRequest:fetechRequest error:&error];
    for(NSManagedObject *stuInfo in fetechedResults)
    {
        NSLog(@"Name:%@", [stuInfo valueForKey:@"name"]);
        NSLog(@"age:%@", [stuInfo valueForKey:@"age"]);
        NSManagedObject *address = [stuInfo valueForKey:@"address"];
        NSLog(@"City:%@", [address valueForKey:@"city"]);
        NSLog(@"Zip:%@", [address valueForKey:@"zip"]);
    }
阅读上面的代码我们可以发现，我们不仅能获取到StudentInfo这个Entity里的值还能通过我们刚才创建的relationship，获取AddressDetails这个Entity，非常的方便。接下来我们运行一下看看结果，注意这时候模拟器上不会有任何的显示，我们要看Log里的输出。OK，结果正确。  
{% img /images/learning_coredata/result1.jpg %}  
到目前为止，我们用的都是NSManagedObject的对象来描述我们的Entity，其实还有一个更好的办法就是为每个Entity创建Model。xCode同样提供了非常方便的方法来达到这个目的。回到StudentManagementSystem.xcdatamodeld，选中StudentInfo这个Entity然后在顶上的菜单栏里选择File->New File->Core Data->NSManagedObject subclass。然后确定，xCode就为我们创建好了一个Model。重复同样的步骤为AddressInfo也创建Model。  
{% img /images/learning_coredata/simodel.jpg %}   
接下来找到StudentInfo.h我们发现里面有个property还是NSManagedObject对象，这是因为我们在创建StudentInfo这个Model的时候AddressDetails这个Model还不存在，所以系统不知道它的类型，只能把它归为NSManagedObject的对象。很简单，我们重新创建一个StudentInfo的Model并且覆盖原来的就可以了。  
接下来我们把刚才上面贴的两段代码改成下面的：  
	
	NSManagedObjectContext *context = self.managedObjectContext;
    //创建NSManagedObject对象指向StudentInfo这个Entity，并设置Value。
    StudentInfo *studentInfo = [NSEntityDescription insertNewObjectForEntityForName:@"StudentInfo" inManagedObjectContext:context];
    studentInfo.name = @"Eric";
    studentInfo.age = [NSNumber numberWithInt:25];
    studentInfo.gender = @"male";
    
    //创建NSManagedObject对象指向AddressDetails这个Entity，并设置Value。
    AddressDetails *addressDetails = [NSEntityDescription insertNewObjectForEntityForName:@"AddressDetails" inManagedObjectContext:context];
    addressDetails.street = @"507/909 Swanston St.";
    addressDetails.city = @"Melbourne";
    addressDetails.state = @"Victoria";
    addressDetails.zip = [NSNumber numberWithInt:3053];
    
    //添加两个对象之间的关系
    studentInfo.address = addressDetails;
    addressDetails.studentinfo = studentInfo;
    
    //保存所创建的对象
    NSError *error;
    if(![context save:&error])
    {
        NSLog(@"Couldn't save: %@", [error localizedDescription]);
    }
    
    NSFetchRequest *fetechRequest = [[NSFetchRequest alloc] init];
    NSEntityDescription *entity = [NSEntityDescription entityForName:@"StudentInfo" inManagedObjectContext:context];
    [fetechRequest setEntity:entity];
    NSArray *fetechedResults = [context executeFetchRequest:fetechRequest error:&error];
    for(StudentInfo *stuInfo in fetechedResults)
    {
        NSLog(@"Name:%@", stuInfo.name);
        NSLog(@"age:%@", stuInfo.age);
        AddressDetails *address = studentInfo.address;
        NSLog(@"City:%@", address.city);
        NSLog(@"Zip:%@", address.zip);
    }
接着运行下看看结果：  
{% img /images/learning_coredata/result2.jpg %}  
OK，两条输出，没有任何问题。接下来大家可以自己添加一个TableView的输出把数据库里的数据逐条显示在TableView里，这里我就不做了。今天的学习到此结束。

