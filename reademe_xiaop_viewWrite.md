<!DOCTYPE html>
<html lang="en">
<!-- 
	时间：2016-7-23 11:13:27
	作者：肖平
	1.功能：
		实现：	
		1.1填充
		1.2绘图
		1.3选择画笔颜色
	2.设计原理：
		主要是：
		2.1canvas的画图
		2.2javascript里面的事件设计的
		2.3核心是stroke(),for in的使用，
	3.后续发展：
		+新增❤❤
		3.1.1移动端的需要处理clientX获取的问题
		3.1.2移动端处理填充色和文字的随机变化-肖平-填充 
		3.2改变画笔大小的固定，使得设计符合美术里面的粗细结合Math.random()*20%6的lineWidth
		3.3凸显出实际画画的真实边缘消失变透明的效果Math.random()*20%6的lineWidth
		3.4width100%,height100%让canvas画板过满屏(有10%左右的误差，不知从哪儿来的)screen.width和window.width也没用
		3.5box-sizing:border-box防止盒子溢出
 -->
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	<title>1.0.1版-绘画版--肖平</title>
	<style type="text/css">
		/*3.5box-sizing:border-box*/
		*{margin: 0;padding: 0;outline-style: none;list-style: none;box-sizing:border-box;}
		html,body{width: 100%;height: 100%;}
		#curses{width: 100%;font-size: 35px;color: rgba(0,160,80,.9);background-color:black;font-family: simsun;}
		#drawxp{position: fixed;top:0;left:0;width: 100%;height: 10%;background: red;}
		#drawxp ul li{float: left;color:#fff;width: 10%;height: 11rem; text-align: center;line-height: 11rem;border: 5px solid #fff;font-weight: bold;}
		#drawxp ul li:hover{cursor: crosshair;}
		#canvas{box-shadow: 5px 5px 190px 65px rgba(0,203,123,.5) inset;
				text-shadow: 5px 5px 190px 65px hlsa(66,33%,24%,.8) inset;
				width: 100%;height: 100%;
			}
		#canvas:hover{cursor: pointer;}
		#clearRect{background-color: #fff;}
		#clearRect:hover{cursor: help !important;}
	</style>
	<script type="text/javascript" defer>
		window.onload=function(){
			/*1.0选取画笔的颜色-肖平*/
			var color1=['red','green','blue','purple','black','orange','lightpink','white','black','teal'];
			var licol=document.getElementsByTagName('li');	
			var test=document.getElementById('test');		 
			var clearRect1=document.getElementById('clearRect');			
			for(k in color1){
				licol[k].style.backgroundColor=color1[k];
				console.log(color1[k]+'\n');
			}

			/*2.0开始写作canvas的绘图-肖平*/
			/*2.1获取上下文2d，绘制stroke是关键-肖平*/
			var canvas2=document.getElementById('canvas');

			/*canvas2.width=360*3;
			canvas2.height=150*3; 
			3.4新增width100%,height100% 
			canvas2.width=screen.width;
			canvas2.height=screen.height;*/
			canvas2.width=window.innerWidth;
			canvas2.height=window.innerHeight;


			canvas2.style.border="3px solid rgba(0,133,0,.8)";
			var ctx=canvas2.getContext('2d'); 

			/*2.2.1决定画笔的颜色*/
			var colorxp;
			for(var i=0;i<licol.length;i++){
				licol[i].index=i;
				licol[i].innerHTML="";
				licol[i].onmousedown=function(){ 
					colorxp=color1[this.index];
					ctx.strokeStyle=colorxp;
					console.log('colorxp-'+this.index+'---'+colorxp);
				}
			}
			/*2.2.2核心画图代码-肖平*/
			/*2.2.3画笔绘画*/
			ctx.save();
			/*3.1.1移动端的需要处理clientX获取的问题*/
			function handleTouchEvent(event) { 
			    if (event.touches.length == 1) { 
			        switch (event.type) {
			            case "touchstart":
			            	ctx.beginPath(); 
			                /*console.log(event.touches[0].clientX + "," + event.touches[0].clientY);*/
			                break;
			            case "touchend":
			            	ctx.closePath();
			                /*console.log(event.changedTouches[0].clientX + "," + event.changeTouches[0].clientY);*/
			                break;
			            case "touchmove":
			                event.preventDefault(); 
							var event=event||window.event;
							ctx.moveTo(event.clientX,event.clientY);
							/*3.2改变画笔大小的固定，使得设计符合美术里面的粗细结合Math.random()*20%6的lineWidth*/
							var ctxLine=parseInt(Math.floor(Math.random()*20%6));
							var event=event||window.event;
							ctx.lineTo(event.changedTouches[0].clientX,event.changedTouches[0].clientY);
							ctx.lineWidth=ctxLine; 
							ctx.stroke();
			                /*console.log(event.changedTouches[0].clientX + "," + event.changedTouches[0].clientY);*/
			                break;
			        }
			    }
			}
			document.addEventListener("touchstart", handleTouchEvent, false);
			document.addEventListener("touchend", handleTouchEvent, false);
			document.addEventListener("touchmove", handleTouchEvent, false);

			canvas.onmousedown=function(event){
				ctx.beginPath();
					var event=event||window.event;
					ctx.moveTo(event.clientX,event.clientY);
					var flagTr=true;
					canvas.onmousemove=function(event){
						/*3.2改变画笔大小的固定，使得设计符合美术里面的粗细结合Math.random()*20%6的lineWidth*/
						var ctxLine=parseInt(Math.floor(Math.random()*20%6));
						canvas.onmouseup=function(){flagTr=false;}
						if(flagTr){
							var event=event||window.event;
							ctx.lineTo(event.clientX,event.clientY);
							ctx.lineWidth=ctxLine;

							/*console.log('++colorxp-'+this.index+'---'+colorxp);*/
							ctx.stroke();
						}
					}
				ctx.closePath();
			}
			/*2.3有填充色和文字的随机变化-肖平*/  
			/*3.1.2移动端的需要处理clientX获取的问题-填充 */
			test.onclick=function(){
				function handleTouchEvent(event) { 
				    if (event.touches.length == 1) { 
				        switch (event.type) {
				            case "touchstart":
				            	ctx.beginPath(); 
								var startxp=true,xpp=true;
				                /*console.log(event.touches[0].clientX + "," + event.touches[0].clientY);*/
				                break;
				            case "touchend":
				            	ctx.closePath();
				                /*console.log(event.changedTouches[0].clientX + "," + event.changeTouches[0].clientY);*/
				                break;
				            case "touchmove":
				                var event=event||window.event;
								var clientxp=event.changedTouches[0].clientX;
								var clientyp=event.changedTouches[0].clientY;
								var txtxp="我是肖平";
								var colorfillx=parseInt(Math.floor(Math.random()*1000000));  
								if(startxp){
									ctx.moveTo(clientxp,clientyp);startxp=false;
								}else{
									ctx.lineTo(clientxp,clientyp);							
								} 
								ctx.fillText(txtxp,clientxp+20,clientyp+20);
								ctx.font="300 normal 22px microsoft yahei";

								var col="#"+colorfillx;
								/*3.3凸显出实际画画的真实边缘消失变透明的效果Math.random()*20%6的lineWidth*/
								var ctxLine=parseInt(Math.floor(Math.random()*20)%6);
								ctx.lineWidth=ctxLine;
								ctx.strokeStyle=col;
								ctx.fillStyle=col;
								ctx.fill();
								ctx.stroke();
				                /*console.log(event.changedTouches[0].clientX + "," + event.changedTouches[0].clientY);*/
				                break;
				        }
				    }
				}
				document.addEventListener("touchstart", handleTouchEvent, false);
				document.addEventListener("touchend", handleTouchEvent, false);
				document.addEventListener("touchmove", handleTouchEvent, false);
			}

			var num1=-1;
			test.onmousedown=function(){
				var flagF;num1++;
				if(num1%2==0){flagF=true;}
				else if(num1%2!=0){flagF=false;};
				var startxp=true,xpp=true;
				canvas.onmousedown=function(event){
					ctx.beginPath();xpp=true;
					canvas.onmousemove=function(event){ 
						canvas.onmouseup=function(event){ 
							xpp=false;
							canvas.onmouseup=function(event){ 
								xpp=true;startxp=true;console.log('you are ok');
							}
						}
						if(xpp&&flagF){ 
							var event=event||window.event;
							var clientxp=event.clientX;
							var clientyp=event.clientY;
							var txtxp="我是肖平";
							var colorfillx=parseInt(Math.floor(Math.random()*1000000)); 
							console.log('event.clientX--'+event.clientX+'\n++\n'+'this.clientY--'+event.clientY); 
							if(startxp){
								ctx.moveTo(clientxp,clientyp);startxp=false;
							}else{
								ctx.lineTo(clientxp,clientyp);							
							} 
							ctx.fillText(txtxp,clientxp+20,clientyp+20);
							ctx.font="300 normal 22px microsoft yahei";

							var col="#"+colorfillx;
							/*3.3凸显出实际画画的真实边缘消失变透明的效果Math.random()*20%6的lineWidth*/
							var ctxLine=parseInt(Math.floor(Math.random()*20)%6);
							ctx.lineWidth=ctxLine;
							ctx.strokeStyle=col;
							ctx.fillStyle=col;
							ctx.fill();
							ctx.stroke();
						};
					};
					ctx.closePath(); 
				};
			}

			/*3.0清空画布*/
			clearRect1.onmousedown=function(){
				ctx.restore();
				ctx.beginPath();
					ctx.clearRect(0,0,canvas.width,canvas.height);
					/*console.dir(canvas.width+"---"+canvas.height);*/
				ctx.closePath();
			}
		}
	</script>
</head>
<body> 
	<!-- 3.4新增width100%,height100%   -->
	<canvas id="canvas" style="width: 100%;height:100%;"></canvas>
	<!-- 1.0选取画笔的颜色-肖平 -->
	<div id="drawxp">
		<ul>
			<li>红</li>
			<li>绿</li>
			<li>蓝</li>
			<li>紫</li>
			<li>黑</li>
			<li>橙</li>
			<li>粉</li>
			<li id="test">试试</li>
			<li id="clearRect" onclick="confirm('清空所有的内容');">清空啦</li>
			<li>肖平设计</li>
		</ul>
	</div> 
</body>
</html>