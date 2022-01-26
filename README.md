# ngx_http_captcha_module
<h2>Dev Env (Ubuntu):</h2>
<br>sudo apt install libperl-dev libgeoip-dev libgd-dev libhiredis-dev unzip gcc make libpcre3-dev zlib1g-dev redis</br>
<pre><code>
wget https://nginx.org/download/nginx-1.18.0.tar.gz
tar -xvf nginx-1.18.0.tar.gz
mv nginx-1.18.0 /usr/local/src/
cd /usr/local/src/nginx-1.18.0
</code></pre>
<h2>Build the Module from source:</h2>
<pre><code>
git clone https://github.com/subhransusekhar/nginx-http-captcha.git
./configure --add-module=nginx-http-captcha
</code></pre>

<br>vi objs/Makefile</br>
<br>search for</br>
<pre><code>
-ldl -lrt -lpthread -lcrypt -lpcre -lz \
</code></pre>
<br>replace with</br>
<pre><code>
-ldl -lrt -lpthread -lcrypt -lpcre -lz -lgd -lhiredis \
</code></pre>
<br>make && make install</br>

<p></p>
<h1>nginx configuration</h1>
<pre><code>
    location /captcha_img/ {
        #captcha_redis_conf 127.0.0.1 6379;                             #redis configuration, default 127.0.0.1 6379
        captcha_init;                                                  # verification code initialization
        captcha_width 130;                                             #Captcha width, default 130 pixels
        captcha_height 30;                                             #Captcha height, default 30 pixels
        captcha_length 4;                                              #Verification code length, default 4, maximum length 6
        captcha_font /usr/share/fonts/truetype/dejavu/DejaVuSans.ttf;  #font library
        captcha_expire 3600;                                           #Expire time, default 3600 seconds
        captcha_output;                                                #output image
    }

    location /captcha_auth/ {
        #captcha_redis_conf 127.0.0.1 6379;    #redis configuration, default 127.0.0.1 6379
        captcha_auth;                         #verify
    }
</code></pre>

<br>validate the configuration file</br>
<code>
/usr/local/nginx/sbin/nginx -t
</code>
<code>
/usr/local/nginx/sbin/nginx -s reload
</code>
<br>Visit http://xxx/captcha_img/ to display the captcha image</br>
<br>Visit http://xxx/captcha_auth/?captcha_code=1234</br>
<br>captcha_code=1234 is the verification code parameter input by the user</br>
