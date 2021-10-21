# Vue + TypeScript

## 安装运行

### 安装

1. 安装node.js + npm

   直接到官网安装，安装node.js后会自动安装npm

   ```
   node -v
   npm -v
   ```

2. 安装TypeScript

   ```
   安装：npm install -g typescript
   校验：tsc -v
   ```

   ![image-20211015195312019](D:\Note\前端学习笔记\vue+typescript\pictures\image-20211015195312019.png)

tsc作用：负责将ts代码转成浏览器和nodejs识别的js代码。

### 示例

1. 编写ts代码，创建`.ts`文件

	![image-20211015205048183](D:\Note\前端学习笔记\vue+typescript\pictures\image-20211015205048183.png)

2. 编译特殊，使用命令进行编译`tsc ./xxxx.ts`，生成`.js`文件。

	*问题：新安装的vscode无法使用cmd命令*

	使用管理员身份运行vscode，使用`get-ExecutionPolicy`命令查询，如果为Restricted表示禁止终端命令的意思，运行如下命令即可修改`set-ExectionPolicy RemoteSigned`

	![image-20211015204650197](D:\Note\前端学习笔记\vue+typescript\pictures\image-20211015204650197.png)
	
3. 运行JS，将JS文件引入HTML页面执行

### 自动编译

1. 运行：tsc --init，创建tsconfig.json文件
2. 修改tsconfig.json文件，设置js文件夹："outDir":"./js/"
3. 设置vscode监视任务，之后修改项目中的ts，自动生成js

## TypeScript基础

### 变量

