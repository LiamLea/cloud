#基础概念
###1.类比数据库
```mermaid
graph LR
A("indices(库)")-->B("types(表)")
B-->C("documents(一行记录)")
C-->D("fields(字段)")
```
###2.核心概念
```
  index         #类似数据库，多个type的集合，可以进行增删改查
  type          #类似表
  field         #字段
  document      #一条记录

  settings      #用来定义该index的相关配置（比如：备份数等）
  mappings      #用来定义该index中各字段的属性（比如有一个字段，名为name，可以在mapping中定义这个字段，类型为string等）
```