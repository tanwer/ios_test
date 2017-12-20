# ios_test
在线安装ios包的跳转链接~

使用nginx搭建https服务器

首先确保机器上安装了openssl和openssl-devel

#yum install openssl
#yum install openssl-devel
然后就是自己颁发证书给自己

在特定目录下生成证书

cd /usr/local/nginx/conf

openssl genrsa -des3 -out server.key 1024

生成服务器端的私钥server.key

openssl req -new -key server.key -out server.csr

生成的csr文件交给CA签名后形成服务端自己的证书，输入证书信息，国家、地区、公司、邮箱等

openssl rsa -in server.key  -out server_nopwd.key

去除key文件口令的命令,生成一个无密码保护的key

openssl x509 -req -days 365 -in server.csr -signkey server_nopwd.key -out server.crt

至此证书已经生成完毕，下面就是配置nginx


vim /usr/local/nginx/conf/vhost/ios.conf

# HTTPS server  
server {

	listen       443;
	
	server_name  120.132.9.213;
	
	ssl on;
	
	ssl_certificate      /usr/local/nginx/conf/server.crt;
	
	ssl_certificate_key  /usr/local/nginx/conf/server_nopwd.key;
	
	#autoindex on;  
	#autoindex_exact_size off;   
	
	location /ios {
		alias  /data/xmob/client/ios_app;
		index index.html index.htm;
	}
	location /ipa {
		alias /data/xmob/client/ios_app;
		add_header Content-Dispositoin "attachment";
	}
}
然后重启nginx即可。 service nginx reload

ps： 如果出现“[emerg] 10464#0: unknown directive "ssl" in /usr/local/nginx-0.6.32/conf/nginx.conf:74”则说明没有将ssl模块编译进nginx，在configure的时候加上“--with-http_ssl_module”即可^^

至此已经完成了https服务器搭建



由于ios9 不信任自己颁发的证书，所以通过github个人主页做为跳板。。。
