---
layout: post
title: "算法和数据结构"
date: 2017-12-31 21:59:25 +0800
comments: true
categories: 
---

### 1.找到字符串中最长无重复子串
- 例如aabbc，最长无重复子串abc，长度3；abcd，长度4；

```
// 思路：
// 1.先申请一个字典，key是每一个字符，value是字符上一次出现的位置
// 2.pre：该字符上次出现的位置
// 3.cur:当前位置和上次出现位置的距离
// 4.len:最长距离
- (void)maxUniqu:(NSString *)string{
    NSMutableDictionary *nums = [NSMutableDictionary new];
    NSArray  *array = [string componentsSeparatedByString:@","];
    [array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        nums[obj] = @(-1);
    }];
    
   __block int pre = -1;
   __block int len = 0;
   __block int cur = 0;
    [array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        pre = MAX(pre, [nums[obj] intValue]);
        cur = idx - pre;
        len = MAX(len, cur);
        nums[obj] = @(idx);
        NSLog(@"\nobj:%@\npre:%d\ncur:%d\nlen:%d\nnums:%@",obj,pre,cur,len,nums);
    }];
}
```