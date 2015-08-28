Description
===========

Installs the OS's package for nginx, and configures it as a Reverse Proxy/
SSL terminator (e.g. in front of haproxy). Certificate content is read from
an encrypted data bag.  

See the *Usage* section below for more information.

Requirements
============

Cookbooks
---------
* nginx [http://community.opscode.com/cookbooks/nginx](http://community.opscode.com/cookbooks/nginx)

Attributes
==========
The following attributes are defined in this cookbook:

* `node[:nginx][:cert_items]` - the name/id of the item to read from an encrypted data bag, containing the .crt and .key
* `node[:nginx][:listen]` - non-ssl port on which nginx listens. If `ssl_only` is true, all connections to this port will be redirected to the `ssl_listen` port. (default is 80)
* `node[:nginx][:ssldir]` - directory where ssl certs are stored. This is a subdirectory of `node[:nginx][:dir]`.
* `node[:nginx][:ssl_protocols]` - the SSL protocols to use (default is "SSLv3 TLSv1")
* `node[:nginx][:ssl_ciphers]` - SSL ciphers to use (default is "ALL:!aNULL:!ADH:!eNULL:!LOW:!MEDIUM:!EXP:RC4+RSA:+HIGH")
* `node[:nginx][:ssl_prefer_server_ciphers]` - whether or not to prefer server ciphers. (default is 'on')
* `node[:nginx][:use_epoll]` - whether or not to use epoll for events (default is true)
* `node[:nginx][:multi_accept]` - accept as many connections as possible after nginx gets notification about a new connection (default is 'on')
* `node[:nginx][:ssl_only]` - if true (the default), all connections to the default `listen` port will be redirected to the `ssl_list` port.

The upstream module is used to proxy connections to another server (e.g an app server or haproxy)
* `node[:nginx][:upstream][:name]` - The name used for the group of upstream servers.
* `node[:nginx][:upstream][:servers]` - an array of hashes used to specify upstream servers. This should be specified in a role as follows:
    
    node[:nginx][:upstream][:servers] = [
        { 
            :address      => "127.0.0.1:8000",
            :max_fails    => "3",
            :fail_timeout => "0",
            :weight       => "1",
            :down         => false
        }
    ]

Additionally, this cookbook will read attribute values set in the Opscode `nginx` cookbook. See 
`attributes/default.rb` for more information.

Usage
=====
Uses the Opscode cookbook to install the package for nginx (not the source), 
then reconfigures it as an SSL Terminator, node[:servers].

The `servers` array items should contain the following information, where `cert` is an array of strings
where each element is a line from the .crt file. Likewise, `key` is an array of strings where each
element is line from the .key file.

  { 
   "ssl_listen" => "ssl",
   "crt_path" => "/a/b/c.pem",
   "key_path" => "/i/j/k.pem",
   "host" => "localhost:8080"
  }

License and Author
==================

Author:: Brad Montgomery (<bmontgomery@coroutine.com>)

Copyright:: 2012, Coroutine LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
