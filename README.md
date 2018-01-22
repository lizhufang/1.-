//console.log('你好');

/*
var http=require('http');

//获取网址内容
http.get('http://www.itsource.cn/',function(res){   //回调函数
	//console.log(res);
	var html='';
	
	
	//拿数据,需要绑定data事件
	res.on('data',function(txt){
		console.log('------------------');
		console.log(txt.toString());
		
		
		console.log(txt.length);
	//	html+=txt;
	});
	res.on('end',function(){
		console.log(html);
	});
});

*/

/*
 * 文件系统
 * 	1.引入文件系统模块
 * 	2.使用fs文件系统模块的方法读取文件
 * 		fs.readFile()  //异步方式 
 * 		fs.readfileSync()//同步方式
 */

//var fs=require('fs');//引入模块

//异步--复杂语句，另开一个线程，，，，回调
/*
console.log(2222);
fs.readFile('./data.txt',function(err,txt){
	console.log(txt.toString());  //转中文，，toString（）
	
});
console.log(3333);

//同步
/*
console.log('tongj');
var data=fs.readFileSync('./data.txt');
console.log('tongj',data.toString());
console.log(2222);
*/

/*
 * 写文件
 * 	1.引入fs模块
 * 	2.找到写的内容
 * 	3.开始写入
 * 		fs.writeFile()  异步
 * 		fs.writeFileSync()  同步
 * 
 */
/*  异步
var fs=require('fs');
var data='今天发生aaa';

fs.writeFile('./11.txt',data,function(err){
	if(err){
		throw err;
	}else{
		console.log('文件写入成功');
	}
});

*/

//异步
/*
var fs=require('fs');

fs.unlink('./11.txt',function(err){
	if(err){
		throw err;
	}else{
		console.log('文件删除成功');
	}
});
*/

/*
 * 以流的方式读取
 * 		fs.createReadStream()
 */

/*
var fs=require('fs');
var str1=fs.createReadStream('./data.txt');
//监听data事件，有事件时会触发
str1.on('data',function(txt){
	console.log(txt);
});
//读完触发该事件
str1.on('end',function(){
	console.log('文件读取结束');
})

str1.on('error',function(){
	console.log('文件读取失败');
});

 * 
 */

/*
var fs=require('fs');

var str2=fs.createWriteStream('./1111.txt');//创建

str2.write('测试');//写

str2.end();// 结束

//绑定 finish事件 end
str2.on('finish',function(){
	console.log('以流的方式写入成功');
});
*/

/*
 * 管道，，
 

var fs=require('fs');

var s1=fs.createReadStream('./data.txt');
var s2=fs.createWriteStream('./data2.txt');
s1.pipe(s2);
*/



/*
 * 网络爬虫
 * 
 * 需求：下载itsouce网页上的所有图片
 * 	1.使用http模块发请求，获取响应的数据
 * 	2.分析html数据，提取图片地址
 * 	3.提取图片地址，再次发出请求，获取图片文件保存到硬盘中
 * 
 */

var http=require('http');
var path=require('path');
var fs=require('fs');

http.get('http://www.itsource.cn/',function(res){
	var content='';
	//通过响应对象，得到html数据
	res.on('data',function(str){
		//console.log(str);
		content+=str;
	});
	
	res.on('end',function(){
		//提取图片地址
		//html代码存放在content中
		//console.log(content);//正则表达式,,,img,,,QQ号，6到12即可
		var reg=/src="(.*?\.JPG)"/img;//.表示匹配字符，？表示出现多少次，.字符表示结束
		var data;
		while(data=reg.exec(content)){
			//console.log(data[1]);
			//url='http://www.itsource.cn/'+data[1];
			getImage(data[1]);
		}
		//console.log(content.match(reg));		
	})
	
});

function getImage(url){
	//先获取文件名
	var obj=path.parse(url);
	var fn=obj.base;
	var str1=fs.createWriteStream('./file/'+fn)
	
	//拼接无根目录的图片
	if(obj.root.length==0){
		url='/'+url;
	}
	
	url='http://www.itsource.cn/'+url;
	http.get(url,function(res){
		
		res.pipe(str1);//直接利用管道流进硬盘
		console.log(fn+'读取完毕');
	});
}
