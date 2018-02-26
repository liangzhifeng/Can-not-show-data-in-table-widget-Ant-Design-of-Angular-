# Can-not-show-data-in-table-widget-Ant-Design-of-Angular-

最近有一个Angular2的项目，使用了Ant Design of Angular，也就是NG-ZORRO。

其中有一个表格使用了Table组件，按理说这么常见的组件，应该很顺利，开始的确是的。

项目交付后，突然设计师要加一个分页功能（刚开始设计师说不需要分页）

# 没有分页时的代码

    <nz-table #nzTable [nzDataSource]="data" [nzIsPagination]="false" >
