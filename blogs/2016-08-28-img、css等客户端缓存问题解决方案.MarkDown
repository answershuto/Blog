今天在开发[染陌云笔记](https://github.com/answershuto/Note.git)的时候遇到了一个缓存问题，上传头像以后读取新的头像信息，因为我在后端做的处理是上传的新的头像以“用户名＋后缀”的方式保存，这样就导致每次上传后图片改变了，实际上图片等名字并没有改变。在没有刷新浏览器等情况下，即便切换页面刷新了当前页面等数据，图片也还是没有从服务器重新获取，依旧使用之前的图片。


解决缓存的方案有两个：

##1.使用版本控制

	<script type=”text/javascript“ src="/js/test.js"+"?version:1.1.1"></script>

##2.使用随机数保证每次刷新

	<img src={this.state.userInformation.userImage+'?'+Math.random()}></img>




在我github的commit记录中可以看到相应修改[https://github.com/answershuto/Note.git](https://github.com/answershuto/Note.git),Commits on Aug 28, 2016 , [commit记录](https://github.com/answershuto/Note/commit/8b177522ae8592ecdac1a1c9fc9b568613b013b8)
