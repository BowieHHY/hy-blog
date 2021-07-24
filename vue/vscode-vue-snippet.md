### vscode  vue snippet
> vscode 快速创建单文件vue模板

> position: Code -> Preferences -> User Snippets

```json
{
	"Print to console": {
		"prefix": "!vue",
		"body": [
			"<template>",
			"<div></div>",
			"</template>",
			"<script>",
			"export default {",
			" name:'',",
			" components:{},",
			" props:{},",
			" data(){},",
			" computed:{},",
			" watch:{},",
			" created(){},",
			" mounted(){},",
			" methods:{}",
			"};",
			"</script>",
			"<style scoped></style>",
		],
		"description": "Log output to console"
	}
}
```

>  [参考link](http://kuhe.run/190403_create-vue-template-with-vscode/)

