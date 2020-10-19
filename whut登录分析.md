连接WHUT-WLAN，查看WLAN信息

```cmd
ipconfig /all
无线局域网适配器 WLAN:
   连接特定的 DNS 后缀 . . . . . . . :
   描述. . . . . . . . . . . . . . . : Intel(R) Wireless-AC 9560 160MHz
   物理地址. . . . . . . . . . . . . : *-*-*
   DHCP 已启用 . . . . . . . . . . . : 是
   自动配置已启用. . . . . . . . . . : 是
   本地链接 IPv6 地址. . . . . . . . : fe80::****:****:****:7686%3(首选)
   IPv4 地址 . . . . . . . . . . . . : 10.*.*.33(首选)
   子网掩码  . . . . . . . . . . . . : 255.255.0.0
   获得租约的时间  . . . . . . . . . : 2020年10月15日 14:49:30
   租约过期的时间  . . . . . . . . . : 2020年10月15日 19:33:21
   默认网关. . . . . . . . . . . . . : 10.120.255.254
   DHCP 服务器 . . . . . . . . . . . : 172.30.16.139
   DHCPv6 IAID . . . . . . . . . . . : 5***1
   DHCPv6 客户端 DUID  . . . . . . . : 00-*-29
   DNS 服务器  . . . . . . . . . . . : 202.114.49.206
                                       202.114.49.196
   TCPIP 上的 NetBIOS  . . . . . . . : 已启用
```

浏览器输入：http://172.30.16.34/srun_portal_pc.php?ac_id=&

<img src="C:\Users\Remrinrin\OneDrive - whut.edu.cn\工作记录\Untitled.assets\whut01.png" style="zoom: 80%;" />

访问外网重新定向：

* google浏览器会自动访问http://www.gstatic.com/generate_204（？暂时不清楚这是什么），然后跳转：http://172.30.16.34/srun_portal_pc.php?ac_id=1&cmd=login&switchip=172.30.14.104&mac=58:a0:23:79:c2:64&ip=10.120.113.33&essid=WHUT%2DWLAN&apname=JB-JH-ITL-1710-C&apgroup=WHUT-WLAN-Dual&url=http%3A%2F%2Fwww%2Egstatic%2Ecom%2Fgenerate%5F204
* ![](C:\Users\Remrinrin\OneDrive - whut.edu.cn\工作记录\Untitled.assets\whut02.png)

```html
<form name="form2" action="" method="post" onsubmit="return check1(this)">
	<input type="hidden" name="action" value="login">
	<input type="hidden" name="ac_id" value="64">
	<input type="hidden" name="user_ip" value="10.120.113.33">
	<input type="hidden" name="nas_ip" value="">
	<input type="hidden" name="user_mac" value="58:a0:23:79:c2:64">
	<input type="hidden" name="url" value="http://www.gstatic.com/generate_204">
	<!-- 省略非必要html元素 -->
	<input type="text" name="username"  id="loginname" maxlength="16" tabindex="1" value="252653" 
           class="textinput requset user-input">  <!-- 用户名 -->
    <input type="password" name="password" maxlength="16" tabindex="2" id="password" value="" 
           class="textinput requset"> 	 <!-- 密码 -->
    <input type="submit" style="background-image:url('./images/ss_login.png');width:120px;height:42px;float: left;border: 1px solid #78b6e8;" 
           alt="登录" value="" id="action_login">
</form>

srun_portal_pc.php?ac_id=1&cmd=login&switchip=172.30.14.104&mac=58:a0:23:79:c2:64&ip=10.120.113.33&essid=WHUT%2DWLAN&apname=JB-JH-ITL-1710-C&apgroup=WHUT-WLAN-Dual&url=http%3A%2F%2Fwww%2Egstatic%2Ecom%2Fgenerate%5F204
```



```javascript
//弹窗认证
function check1(frm) 
{
	//省略无关内容
	//var save_me = (frm.save_me.checked) ? 1 : 0;
	var d = "action=login&username="+encodeURIComponent(frm.username.value)+
			"&password={B}"+e+
			"&ac_id="+$("input[name='ac_id']").val()+
			"&user_ip="+$("input[name='user_ip']").val()+
			"&nas_ip="+$("input[name='nas_ip']").val()+
			"&user_mac="+$("input[name='user_mac']").val()+
			"&save_me="+save_me+
			"&ajax=1";
	//alert(d);
	//这里要用AJAX同步提交POST
 	$.ajax({type: "post",
			url: "/include/auth_action.php", 
			data: d,
			async : false,
			success: function(res) {
		res1 = res;
	}
	}); 
    	var p = /^login_ok,/;
	if(p.test(res1))//认证成功，弹出小窗口
	{
		var arr = res1.split(",");
		if(arr[1] != "")//写入用于双栈认证的COOKIE
		{
			setCookie("double_stack_login", arr[1]);
		}
		if(arr[2]!="")//写入用户名密码COOKIE
		{
			setCookie("login", arr[2]);
		}
		window.open("srun_portal_pc_succeed.php", "","width=450,height=350,left=0,top=0,resizable=1");//弹出小窗口
		setTimeout("redirect()", 2000); //重定向到输入的网址
	}
	else
	{
		alert(res1); //提示错误信息
	}
	return false;
}
```