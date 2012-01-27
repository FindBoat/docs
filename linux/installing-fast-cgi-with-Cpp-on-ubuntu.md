<pre>
sudo su
</pre>

<pre>
apt-get update -y
apt-get install -y chkconfig
apt-get install -y build-essential
apt-get install -y libtool
</pre>

<pre>
apt-get install -y nginx
</pre>

<pre>
vi /etc/nginx/nginx.conf
</pre>

<pre>
service nginx start
chkconfig nginx on
</pre>

<pre>
cd ~
wget http://www.fastcgi.com/dist/fcgi.tar.gz
tar -zxvf fcgi.tar.gz
rm -rf fcgi.tar.gz
cd fcgi-2.4.0
vi include/fcgio.h
</pre>

add "#include <stdio.h>" below "#include <iostream>"

<pre>
./configure
make
make install
</pre>

<pre>
cd ~
vi main.cpp
</pre>

<pre>
#include <fcgi_stdio.h>
#include <stdlib.h>

int main(int, char**)
{
	int count = 0;
	while(FCGI_Accept() >= 0)
		printf("Content-type: text/html\r\n"
			"\r\n"
			"Hello, World! "
			"Request number %d running on host <i>%s</i>\n",
			++count, getenv("SERVER_NAME"));

	return 0;
}
</pre>

link as shared library:   	

<pre>
g++ main.cpp -lfcgi
</pre>

link as static library:

<pre>
g++ main.cpp /usr/local/lib/libfcgi.a
</pre>

	