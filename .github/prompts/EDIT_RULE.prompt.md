---
mode: agent
---
# Flyer UI 开发规范

## UTS规范
- 表示为空有两种方式: | null 和 ?:
```
type obj = {
	id : number,
	name : string,
	age : number | null, // 可以设置为空
	sex ?: boolean // 可以设置为空
}
```
- 需要注意的是，使用 ?: 时，属性不可以传值，如：title?: string = null 是错误的，编译器会将?:识别为undefined，抛出异常Type 'null' is not assignable to type 'string | undefined'，只可使用title?: string 或 title: string = ''。
- UTS中，编译为安卓和ios环境时，不存在undefined，只有null，代码中需避免显示使用undefined。
- 文件使用import导入时，导出函数和变量，不可与当前文件中的同名变量命名相同，避免命名冲突。
- 非空判断时，需明确使用null进行判断。
```
// 正确使用
if(config.title != null) {
    state.title = config.title
}
// 错误使用
state.title = config?.title
```

## CSS规范
- 仅支持类名选择器，伪类选择器等不支持。
- `line-height` 只支持 `<text>|<button>|<textarea>`。
- `white-space` 只支持 `<text>|<button>`。
- `font-size|text-align|color` 只支持 `<text>|<button>|<input>|<textarea>`。
- `text-align` 只支持 `<text>|<button>|<input>|<textarea>`。
