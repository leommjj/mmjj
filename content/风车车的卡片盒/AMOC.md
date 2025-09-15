```dataviewjs
await dv.view("模板/流动式PARA导航栏")
```
```dataviewjs
dv.table(["最近编辑"],dv.pages()
.filter(p => p.file.folder.contains(dv.current().file.folder) && p.file.name!=dv.current().file.name)
.sort(p => p.file.mtime,"desc")
.limit(8)
.map(item => {return [item.file.link]}))
```
[去过的地方](https://mmjjboke.vercel.app/)





