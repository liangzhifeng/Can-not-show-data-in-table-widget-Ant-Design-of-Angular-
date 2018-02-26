# Can-not-show-data-in-table-widget-Ant-Design-of-Angular-

最近有一个Angular2的项目，使用了Ant Design of Angular，也就是NG-ZORRO。

其中有一个表格使用了Table组件，按理说这么常见的组件，应该很顺利，开始的确是的。

项目交付后，突然设计师要加一个分页功能（刚开始设计师说不需要分页）

# 没有分页时的HTML代码

    <nz-table #nzTable [nzDataSource]="data" [nzIsPagination]="false" >

# 开始入坑

分个页多简单啊，把nzIsPagination去掉就OK了，

    <nz-table #nzTable [nzDataSource]="data">
    
你以为这样就完成任务了么？ 那你也太幼稚了！！！

因为页面不展示数据了，也没有任何报错。

好吧，那就断点调试一下ts代码，可是有数据呀。

百思不得其解。

# 出坑方法一

开始将不相关代码都注释掉，问题依旧存在。

ts中通过Angular2的HTTP请求表格数据的代码如下（做了简化）：

        this.checkOverviewService.getCheckOverviewInfo().subscribe((res:any) => {
            if (res.status === 200) {
                let totalData = res.json();     
                totalData.forEach(item => {
                    let json = item.station;
                    this.data.push(json);
                });
            } else {
                this._message.error(res.data,{nzDuration: 5000});
            }
        });

先把 this.data.push(json);这一行注释掉，然后在this.checkOverviewService.getCheckOverviewInfo()这一行的上方写死一个固定值，比如

this.data.push({name:'hello'})

刷新页面，数据有了！！！ 很明显：同步数据是正常的，异步数据出问题。

再去查看官方文档：

nz-table
参数	说明	类型	默认值
nzAjaxData	远程异步数据，与nzDataSource二选一	Array	[]
nzDataSource	同步数据，与nzAjaxData二选一	Array	[]


于是修改HTML中的nzDataSource为nzAjaxData，再还原ts中的代码，果然正常了。

按理说，到这里就应该结束了。

可是我还是有点不甘心。

# 出坑方法二

我去查了一下以前的项目，发现HTML中用的就是nzDataSource，功能也是正常的！！！

这尼玛逆天了!!!


再仔细看下以下代码：
  
  totalData.forEach(item => {
                    let json = item.station;
                    this.data.push(json);
                });

绑定在页面上的this.data在不断变化。

改成下面这样试试

 const list: any[] = [];
  totalData.forEach(item => {
                    let json = item.station;
                    list.push(json);
                });
  this.data = [...list];
  
  原来是 angular 变更检测机制引起的，数组项的变更不会触发 ngOnChanges。
  
  又涨知识了！！！
