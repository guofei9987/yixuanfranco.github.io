---
layout: post
title: 最小七牛使用法则
categories:
- Geek
---

2015 年 11 月 10 日, 我用 TermRecord 录制了一个视频, 从微信传给大妈审核, 然而大妈拿着手机看不到.  大妈结论: 可见没有一个任性发布标准 URL 的渠道多忧桑...→\_→

梗已经接到, 于是开始折腾:  

* [7牛作为图床][1]
* 面向中国用户, 使用 GitHub 作为图床时而会炸. 所以建议我们使用七牛.
* [大妈的教程][2]
* 以上是我收集到的梗.

## 分析
1. 先把七牛的命令什么的搞定.
2. 再传视频.
3. 生成链接, 发给大妈, 问题就解决了.

## 开始!

1.  [qrsync][3]
不想用 7 牛的 用户界面, 于是先把命令行解决吧.  
    \* 这个链接里, 包括 **工具** + **配置方法**

* 下载好文件夹. 进入文件夹, `touch conf.json`
	* `vi conf.json` 敲 i 开始编辑文件.
	* 按照教程说, 把下面的东西 粘贴进去, 改成自己的:

版本一:

	'{
	    "access\_key": "Please apply your access key here",
	    "secret\_key": "Dont send your secret key to anyone",
	    "bucket": "Bucket name on qiniu resource storage",
	    "sync\_dir": "Local directory to upload",
	    "async\_ops": "fop1;fop2;fopN",
	    "debug\_level": 1
	}


[版本2:][4]

	'{
	    "src": "/home/your/sync\_dir",
	    "dest": "qiniu:access\_key=<AccessKey>&secret\_key=<SecretKey>&bucket=<Bucket>&key\_prefix=<KeyPrefix>&threshold=<Threshold>",
	    "debug\_level":  1
	}



## 先说一下我踩到的梗:

 * 我一开始 不知道 Bucket 是什么, 所以按照教程说的 , 我随便写的. 结果上传不上.
   * 于是才发现, Bucket 是有规定的, 是自己建立的 空间 名.(详见: 下方 Bucket 解析.)
 * 然后大妈给的我 版本二 的 conf.json 文件, 在复制 access key时, 鬼使神差的 把key 复制到 \<\> 中了.
   * 记住, 这个必须删掉. 所以你复制好了之后应该是 access\_key=..... 而不是 access\_key=\<....\>
   * 大妈云: 类似 <key> [key] {key} 都叫模板占位符, 标明这里要配置的,但是,不包含 \<\> [] {} 什么的标签,那是为了区分正常字串用的,,,   
 * 类似 key\_profix 等等各种附加功能配置,从来都是可选, 没有特殊场景是不用的.
 * 七牛一般账户只能上传富文本, 只有如果上传不上去, 付费吧...

**解析一下上面的 conf.json 是什么意思.**

 * acccess\_key 和 secret\_key, 都是你自己的七牛用户里特有的.
	* 账号→ 密匙 → 就可以看到这两个 Key, 复制粘贴到文件里就可以了.
	* **小白提示**: 粘贴的时候不要去掉 " ", 要粘贴到 " "中间.


 * bucket:
	* 在七牛左上角, 新建 一个空间.
	* bucket 就是你的空间名称.
	* 比如:
		* 我的空间叫 yixuanfranco
		* 那么, 我的配置应该是 "bucket":"yixuanfranco"

 * sync\_dir: 是你的本地文件夹的地址.
	* 可以在本地建立一个文件夹, 然后把他的绝对地址粘贴上.
	* **小白提示**: pwd 可以知道你 文件夹 的绝对地址.

* **版本一 conf.json**:其他的东西 可以暂时不用管它. 保留就好.
*  **版本二 conf.json**: 把 &key\_prefix=<KeyPrefix>&threshold=<Threshold> 先删掉, 需要的时候再加. 跑通了再说.

所以, 这个文件的逻辑已经非常清楚了:  

* 检测 access key + secret key, 确定你的身份.
* 确定你要上传到的空间.
* 把本地 库的内容 上传到七牛的 空间中.


**Checklist**:  

* 下载了 qrsync.
* 新建+配置了 conf.json.
* 在你本地的文件夹里 随便放一点文件.

做完上述所有内容后, 我们就可以开始上传东西了:  
`./qrsync conf.json`
运行命令, 即可上传你的文件了!  

* 要在 qrsync 前加 ./ , 不然可能运行不了.
* 我一开始就没运行成功.

## 到哪里去看我生成的 URL 呢？
1. 先查看一下自己 **空间** 的设置.
	* 进入到自己的空间里.
	* 点击空间设置.
	* 点击域名设置, 就可以看到自己的域名了.

2. 怎么查看文件?
	* 你可能会发现, 打开自己的域名, 里面什么都没有. 是个 404 界面.
	* 你需要 域名/文件名 打开自己上传的文件.
		* 比如, 我刚刚说, 在本地的文件夹里, 随便放一些东西.
		* 我们假设你放了 yixuan.md 好了.
		* 那么,查看这个文件的方式为: 域名/yixuan.md.

## 补充

* 生成目录的命令:
  * `python gen4idx.py ./ footer-7niu.html NULL`

^\_^ 希望你一切都搞定了.  

* 如果没搞定的话, 可以 发**邮件**至 liyixuan5402@gmail.com 联系我.
* 或者, 加我的 **facebook**: liyixuan5402@hotmail.com


## PPS. Nov.26, 2016 10:22

> 因为 qrsync 不能用了, 于是昨天折腾了会, 把新的 qshell 折腾明白了. 下面的补充算是给我自己看的. qshell 至今没搞明白到底怎么增量上传, 一上传就传全部也是炸了.  whatever, 写在这里给自己备份一下. 
 1. 本地快照:
 `./qshell dircache /Users/mac/Desktop/TheSummerofMQT/Qiniu ao.md `
2. 同步七牛:
\`./qshell qupload 10 conf.json
\`\`\`

[1]:	https://github.com/OpenMindClub/2.OMOOC.py/issues/70
[2]:	http://blog.zhgdg.org/2013-08/usage7niu/
[3]:	http://docs.qiniu.com/tools/v6/qrsync.html
[4]:	http://developer.qiniu.com/docs/v6/tools/qrsync.html