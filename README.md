# JsRpc
## js逆向之远程调用(rpc)免去抠代码


## 目录结构
    -- main.go (服务器的主代码)
    -- JsEnv.js (客户端-网页上的js环境)
    
    
  
    先粘贴JsEnv环境到网站控制台
    
    再注入ws 控制台粘贴
    var demo = new Hlclient("wss://域名:12443/ws?group=test&name=test")
    
    注册一个方法 第一个参数hello为方法名，第二个参数为函数，resolve里面的值是想要的值，param是可传参参数，可以忽略
    
    demo.regAction("hello", function (resolve,param) {
	    var c="好烦呐"+param
        resolve(c);
    })



### 基本介绍
    在网站的控制台新建一个WebScoket客户端链接到服务器通信，调用服务器的接口 服务器会发送信息给客户端 客户端执行完js代码后把获得想要的内容发回给服务器 服务器接收到后再显示出来 <br>
    
### 实现
    本方法可以https证书且支持wss  

    在https的网站想要新建WebSocket连接如果是连接到普通的ws可能会报安全错误，所以需要更换为wss。但是wss搭建可能有点费时和力 你们看着玩吧
    
    1.无https证书者。直接编译main.go 我试了自己的win7电脑，发现谷歌浏览器可以在https的网站连接ws而不一定要wss,所以在win7及以下的系统可能可以直接开服务用 默认端口12080
    
    2.有https证书者。修改main.go文件 把r.Run()注释掉，把r.RunTls注释取消掉 并且参数设置证书的路径 直接输入名字就是当前路径 默认端口：12443
    
    另外的题外话，有域名没证书不会搞的 或者有域名有公网(非固定IP的)都可以搞成的，自己研究研究
    
### 食用方法
   ![image](https://user-images.githubusercontent.com/41224971/134774461-1b502f9f-f58d-4fd8-9a8e-9ac402ef9b60.png)
   
    打开编译好的go文件，开启服务
    有3个接口:
        /list是查看当前连接的ws服务
        /ws是浏览器注入ws连接的接口
        /go是获取数据的接口 (数据格式json: {"group":"hhh","hello":"好困啊yes","name":"baidu","status":"200"} )
        
        
    说明：接口用?group和name来区分，我也不知道我为啥要抄两个名字来区分
    wss://域名.cn:12443/ws?group={}&name={}" //注入ws的例子 group和name都可以随便
    https://域名.cn:12443/go?group={}&name={}?action={}&param={} //group和name填写上面注入时候的，action是注册的方法名,param是可选的参数
    
    步骤一：粘贴js环境
   ![image](https://user-images.githubusercontent.com/41224971/134774597-5c8c845b-072e-40d1-bdf7-24e89f78b22e.png)
    步骤二：注入ws和方法
    ![image](https://user-images.githubusercontent.com/41224971/134774859-a4594f23-b828-4538-8b89-9d96813f7d1e.png)
    步骤三：访问接口就能获得数据
    ![image](https://user-images.githubusercontent.com/41224971/134775037-167724d4-ae94-4fcf-88c4-d881621b712c.png)
    {"group":"hhh","hello":"好困啊yes","name":"baidu","status":"200"} 其中 hello是会变的 是action名字。 用代码访问的时候要注意这个名字


### 食用案例-爱锭网15题
    本题解是把它ajax获取数据那一个函数都复制下来，然后控制台调用这样子~
    ![image](https://user-images.githubusercontent.com/41224971/134793093-bac742e9-2f66-4fe4-b98b-7769d7379350.png)
    1.f12查看请求，跟进去 找到ajax那块，可以看到call函数就是主要的ajax发包 输入页数就可以，那我们复制这个函数里面的代码备用
    
    ![image](https://user-images.githubusercontent.com/41224971/134793125-1b262f08-56b1-4f8d-b5d5-bfe7a1925252.png)

    2.先在控制台粘贴我的js环境，再注入一个rpc链接 注册一个call方法，名字自定义 第二个参数粘贴上面call的代码，小小修改一下
       先定义num=param 这样就传参进来了，再定义一个变量来保存获取到的数据，resolve(变量) 就是发送。完了就注入好了，可以把f12关掉了
       
    ![image](https://user-images.githubusercontent.com/41224971/134793215-8f45a95b-a234-4023-aa5f-39d748faded4.png)

    3.调用接口就完事了，param就是传参页数 
    
    控制台可以关，但是注入的网页不要关哦
    


    



