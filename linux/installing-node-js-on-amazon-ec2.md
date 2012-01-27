# Installing node.js on Amazon EC2

In the following guide, I used the 'Amazon Linux Basic 32-bit/64-bit' image.
And, for brevity, I started with the following command:

<pre class="command-line">
sudo su
yum upgrade -y
</pre>

## Install required packages

To begin with, you must install <i>GCC C++ Compiler</i>, <i>OpenSSL</i>, and <i>make</i> package.

<pre class="command-line">
yum install -y gcc-c++
yum install -y openssl-devel
yum install -y make
</pre>

## Install and setup web-server

You can host your node.js app directly, but for maintenability and static files handling, I chose to make it behind the web-server.
I used <i>nginx</i>, but you can choose other web-servers to your taste.

<pre class="command-line">yum install -y nginx</pre>

After installation, change configuration file for proxy setup.

<pre class="command-line">vi /etc/nginx/nginx.conf</pre>

Find the <i>location /</i> section, and change it to as follow:

<pre class="code">
location / { 
    proxy_pass     http://127.0.0.1:1337/;
}
</pre>

Then, we can just start our web-server. And, for easier maintenance, I made it auto-starting when the machine is rebooted.

<pre class="command-line">
service nginx start
chkconfig nginx on
</pre>

## Install node.js

Next, you must download the node.js file.

<pre class="command-line">
wget http://nodejs.org/dist/node-latest.tar.gz
tar -zxvf node-latest.tar.gz
rm -rf node-latest.tar.gz
</pre>

Then, move to the unzipped folder, and start building the software.

<pre class="command-line">
cd node-v0.6.7
./configure --prefix=/usr
make
make install
</pre>

<i>Typically, build process takes time.</i> And, by specifying <i>--prefix</i> option, node.js binary('<i>node</i>') will be located in <i>PATH</i> automatically.

## Write 'Hello, World' program

For your first app, let's write the simplest example, 'Hello, World':

<pre class="command-line">
vi hello.js
</pre>

<pre class="code">
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(1337, "127.0.0.1");
console.log('Server running at http://127.0.0.1:1337/');
</pre>

Now, everything is ready. Let it begin!

<pre class="command-line">
node hello.js
</pre>

## Extra steps

For easier package management for node.js, you can use [<i>npm</i>](http://npmjs.org/). To install:

<pre class="command-line">
curl http://npmjs.org/install.sh | sh
</pre>

And, you can find the list of node.js packages(modules) at [here](https://github.com/joyent/node/wiki/modules).