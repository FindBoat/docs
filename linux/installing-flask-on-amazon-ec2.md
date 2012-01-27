
# Installing Flask on Amazon EC2

In the following guide, I used the 'Amazon Linux Basic 32-bit/64-bit' image.
And, for brevity, I started with the following commands:

<pre class="command-line">
sudo su
yum upgrade -y
</pre>

## Install required packages

To begin with, you must install <i>GCC C++ Compiler</i>, <i>OpenSSL</i>, and <i>make</i> package.

<pre class="command-line">
yum install -y gcc-c++
yum install -y libxml2-python libxml2-devel
yum install -y python-devel
easy_install uwsgi
</pre>

## Install Flask

Next, you install Flask using <i>easy_install</i>. Jinja2 and Zerkzeug will be automatically installed.

<pre class="command-line">easy_install Flask</pre>

## Install and setup web-server

You can host your node.js app directly, but for maintenability and static files handling, I chose to make it behind the web-server.
I used <i>nginx</i>, but you can choose other web-servers to your taste.

<pre class="command-line">yum install -y nginx</pre>

After installation, change configuration file for proxy setup.

<pre class="command-line">vi /etc/nginx/nginx.conf</pre>

Find the <i>location /</i> section, and change it to as follow:

<pre class="code">
location / {
    include uwsgi_params;
    uwsgi_pass 127.0.0.1:10080;
}
</pre>

Then, we can just start our web-server. And, for easier maintenance, I made it auto-starting when the machine is rebooted.

<pre class="command-line">
service nginx start
chkconfig nginx on
</pre>

## Create 'Hello, World' program

In your project folder, create your first python app.

<pre class="command-line">vi hello.py</pre>

<pre class="code">
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=10080)
</pre>

## Setup uWSGI

To configure uWSGI server, create a config file as follows:

<pre class="command-line">vi app.yaml</pre>

<pre class="code">
uwsgi:
  socket: 127.0.0.1:10080
  master: 1
  workers: 1
  chmod-socket: 666
  auto-procname: 1
  python-path: .
  pidfile: /tmp/uwsgi.pid
  daemonize: /var/log/uwsgi.log
  module: hello:app
</pre>

Here, I assume that this app.yaml file is located in the same directory as <i>hello.py</i> file.

## Start uWSGI

Using the config file, you can easily start uWSGI server:

<pre class="command-line">uwsgi --yaml app.yaml</pre>

In case, you want to run it as other user's, you can use <i>--uid</i> option additionaly.
And, because our config specify that uWSGI processes are executed as daemon, if you want to stop them all, you can run:

<pre class="command-line">kill -INT `cat /tmp/uwsgi.pid`</pre>
