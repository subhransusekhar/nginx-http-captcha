# ngx_http_captcha_module
<h1>installation method:</h1>
<br>1. $ cp ngx_http_captcha_module.c /usr/local/src/nginx-1.14.0/src/http/modules/</br>
<br>2. cd /usr/local/src/nginx-1.14.0</br>
<br>3. $ vim auto/modules</br>
<br>add the following code</br>
<pre><code>
if :; then
    nginx_module_name=ngx_http_captcha_module
    nginx_module_incs=
    nginx_module_deps=
    nginx_module_srcs=src/http/modules/ngx_http_captcha_module.c
    nginx_module_libs=
    nginx_module_link=YES

    . auto/module
fi
</code></pre>
<br>4. $ ./configure --with-debug</br>
<br>5. $ vim objs/Makefile</br>

<pre><code>
objs/src/http/modules/ngx_http_captcha_module.o:        $(CORE_DEPS) $(HTTP_DEPS) \
        src/http/modules/ngx_http_captcha_module.c
        $(CC) -c -g $(CFLAGS) $(CORE_INCS) $(HTTP_INCS) \
                -o objs/src/http/modules/ngx_http_captcha_module.o \
                src/http/modules/ngx_http_captcha_module.c
</code></pre>

<br>change to</br>
<pre><code>
objs/src/http/modules/ngx_http_captcha_module.o:        $(CORE_DEPS) $(HTTP_DEPS) \
        src/http/modules/ngx_http_captcha_module.c
        $(CC) -c -g $(CFLAGS) $(CORE_INCS) $(HTTP_INCS) \
                -I /usr/include -I /usr/local/include \
                -L /usr/lib/ -L /usr/local/lib/ -lgd -lhiredis \
                -o objs/src/http/modules/ngx_http_captcha_module.o \
                src/http/modules/ngx_http_captcha_module.c
</code></pre>
<pre><code>
-ldl -lrt -lpthread -lcrypt -lpcre -lz \
</code></pre>
<br>change to</br>
<pre><code>
-ldl -lrt -lpthread -lcrypt -lpcre -lz -lgd -lhiredis \
</code></pre>
<br>6. $ make && make install</br>

<p></p>
<h1>nginx configuration</h1>
<pre><code>
    location /captcha_img {
        captcha_redis_conf 127.0.0.1 6379;    #redis configuration, default 127.0.0.1 6379
        captcha_init;                         # verification code initialization
        captcha_width 130;                    #Captcha width, default 130 pixels
        captcha_height 30;                    #Captcha height, default 30 pixels
        captcha_length 4;                     #Verification code length, default 4, maximum length 6
        captcha_font /data/font/elephant.ttf; #font library
        captcha_expire 3600;                  #Expire time, default 3600 seconds
        captcha_output;                       #output image
    }

    location /captcha_auth {
        captcha_redis_conf 127.0.0.1 6379;    #redis configuration, default 127.0.0.1 6379
        captcha_auth;                         #verify
    }
</code></pre>

<br>restart nginx
<br>Visit http://xxx/captcha_img to display the captcha image</br>
<br>Visit http://xxx/captcha_auth?captcha_code=1234</br>
<br>captcha_code=1234 is the verification code parameter input by the user</br>
