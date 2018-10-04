
Flask – FastCGI
Advertisements
 Previous Page Next Page  
FastCGI is another deployment option for Flask application on web servers like nginix, lighttpd, and Cherokee.

Configuring FastCGI
First, you need to create the FastCGI server file. Let us call it yourapplication.fcgiC.

from flup.server.fcgi import WSGIServer
from yourapplication import app

if __name__ == '__main__':
   WSGIServer(app).run()
nginx and older versions of lighttpd need a socket to be explicitly passed to communicate with the FastCGI server. For that to work, you need to pass the path to the socket to the WSGIServer.

WSGIServer(application, bindAddress = '/path/to/fcgi.sock').run()
Configuring Apache
For a basic Apache deployment, your .fcgi file will appear in your application URL e.g. example.com/yourapplication.fcgi/hello/. There are few ways to configure your application so that yourapplication.fcgi does not appear in the URL.

<VirtualHost *>
   ServerName example.com
   ScriptAlias / /path/to/yourapplication.fcgi/
</VirtualHost>
Configuring lighttpd
Basic configuration of lighttpd looks like this −

fastcgi.server = ("/yourapplication.fcgi" => ((
   "socket" => "/tmp/yourapplication-fcgi.sock",
   "bin-path" => "/var/www/yourapplication/yourapplication.fcgi",
   "check-local" => "disable",
   "max-procs" => 1
)))

alias.url = (
   "/static/" => "/path/to/your/static"
)

url.rewrite-once = (
   "^(/static($|/.*))$" => "$1",
   "^(/.*)$" => "/yourapplication.fcgi$1"
)
Remember to enable the FastCGI, alias and rewrite modules. This configuration binds the application to /yourapplication.
