---
layout: post
title: "Custom Core Data Migrations"
date: 2015-07-17 17:58:42 +0800
comments: true
categories: 
---
目前App数据库版本已经有两个了，当前版本的数据库只是初始版本的简单扩展，增加了一个表(Entity);大版本升级需要修改一个表的定义，增加一个表，并添加表之间的关联(Relationship),还要考虑未来扩展的需要；  

先补充各种术语，从目前的代码中找到数据库处理相关的代码，了解使用到的类和方法；翻了几篇对Core Data的简述，知道需要做数据迁移，从初始版到当前版本使用的是轻量级迁移(Lightweight Migration)，通过设置一个字典实现:  
```c
NSError *error = nil;
NSURL *storeURL = <#The URL of a persistent store#>;
NSPersistentStoreCoordinator *psc = <#The coordinator#>;
NSDictionary *options = [NSDictionary dictionaryWithObjectsAndKeys:
    [NSNumber numberWithBool:YES], NSMigratePersistentStoresAutomaticallyOption,
    [NSNumber numberWithBool:YES], NSInferMappingModelAutomaticallyOption, nil];
 
BOOL success = [psc addPersistentStoreWithType:<#Store type#>
                    configuration:<#Configuration or nil#> URL:storeURL
                    options:options error:&error];
if (!success) {
    // Handle the error.
}
```  
考虑到未来扩展的需要，参考[Custom Core Data Migrations]和[自定义 Core Data 迁移]希望代码的实现可以方便日后扩展,v1 to v2, v2 to v3...结果运行不正常，在处理v2 to v3时死循环了，确认示例代码运行正常，开始排查：

首先，排除参考代码实现上的遗漏，确认没有。  
第二步，将示例代码中的数据库移过来进行测试，运行正常---看来与数据库本身有关。  
第三步，已经确认问题原因在于从v2至v3迁移    

```c
//See if we can find a matching destination model
NSManagedObjectModel *model = nil;
NSMappingModel *mapping = nil;
NSString *modelPath = nil;
for (modelPath in modelPaths) {
    model = [[NSManagedObjectModel alloc] initWithContentsOfURL:[NSURL fileURLWithPath:modelPath]];
    mapping = [NSMappingModel mappingModelFromBundles:@[[NSBundle mainBundle]]
                                           forSourceModel:sourceModel
                                         destinationModel:model];
    //If we found a mapping model then proceed
    if (mapping) {
        break;
    }
}
```  
先怀疑mapping model创建有问题，仅有v1,v2两个版本时，创建v1至v2 mapping model，可正确迁移；增加v3，创建v2至v3 mapping model，跟踪发现，迁移到v2后，总能获取到sourceModel至v2 model的mapping model;是否因为modelPaths中顺序问题导致？排查，否定；   

删除v1至v2 mapping model，确认仅留下v2-v3 mapping model，可正确完成迁移；综合对前面情况的分析，只留下v2-v3 mapping model可实现v1-v3迁移，测试确认,问题已经初步解决；这样的办法不能解决后续扩展问题，还得继续钻...  

从已得到的解决办法看，是否可以认为是因为v1-v2,v2-v3都可以由Core Data自己推导出mapping model导致呢？是否可以不手动创建mapping model，直接使用inferredMappingModelForSourceModel:destinationModel:error:获取mapping model呢？  
测试确认，可行，可实现v1/v2至v3迁移;差不多了吧？还不够,如果使用手动创建mapping model怎么办...

既然问题是v2-v3迁移时反复获取至v2的mapping model，那么完成v1-v2迁移后将往v2迁移的mapping排除掉步就解决了吗？ 
第一种尝试，modelPaths中去掉v2 path，不行;  
第二种尝试，比较entityVersionHashesByName,可行!  

至此，问题基本解决，希望对新手有帮助，如有问题或有更好的建议，请各位email:wrennywang@qq.com，万分感激!

[自定义 Core Data 迁移]:(http://www.objccn.io/issue-4-7/) 
[Custom Core Data Migrations]:(http://www.objc.io/issues/4-core-data/core-data-migration/)   

