upstream php-handler {
    server unix:/run/php/php8.3-fpm.sock;
}

server {
    listen 80;
    listen [::]:80;
    server_name _;

    server_tokens off;

    root /var/www/nextcloud;

    client_max_body_size 10G;
    client_body_timeout 300s;
    client_body_buffer_size 512k;

    fastcgi_buffers 64 4K;

    index index.php index.html /index.php$request_uri;

    add_header Referrer-Policy                   "no-referrer" always;
    add_header X-Content-Type-Options            "nosniff" always;
    add_header X-Frame-Options                   "SAMEORIGIN" always;
    add_header X-Permitted-Cross-Domain-Policies "none" always;
    add_header X-Robots-Tag                      "noindex, nofollow" always;
    add_header X-XSS-Protection                  "1; mode=block" always;
    add_header Strict-Transport-Security         "max-age=15552000; includeSubDomains" always;

    fastcgi_hide_header X-Powered-By;

    location = /robots.txt {
        allow all;
        access_log off;
        log_not_found off;
    }

    location = / {
        if ($http_user_agent ~ ^DavClnt) {
            return 302 /remote.php/webdav/$is_args$args;
        }
    }

    location /remote {
        return 301 /remote.php$request_uri;
    }

    location ^~ /.well-known {

        location = /.well-known/carddav {
            return 301 /remote.php/dav/;
        }

        location = /.well-known/caldav {
            return 301 /remote.php/dav/;
        }

        location /.well-known/acme-challenge {
            try_files $uri $uri/ =404;
        }

        location /.well-known/pki-validation {
            try_files $uri $uri/ =404;
        }

        return 301 /index.php$request_uri;
    }

    location ~ ^/(?:build|tests|config|lib|3rdparty|templates|data)(?:$|/) {
        return 404;
    }

    location ~ ^/(?:\.|autotest|occ|issue|indie|db_|console) {
        return 404;
    }

    location ~ ^/(?:composer\.(?:json|lock)|package(?:-lock)?\.json|core/shipped\.json)$ {
        return 404;
    }

    location ~ \.php(?:$|/) {

        rewrite ^/(?!index|remote|public|cron|status|ocs\/v[12]|ocs-provider\/.+|core\/ajax\/update|updater\/.+|.+\/richdocumentscode(_arm64)?\/proxy) /index.php$request_uri;

        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        set $path_info $fastcgi_path_info;

        try_files $fastcgi_script_name =404;

        include fastcgi_params;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $path_info;
        fastcgi_param HTTPS off;
        fastcgi_param modHeadersAvailable true;
        fastcgi_param front_controller_active true;

        fastcgi_pass php-handler;

        fastcgi_intercept_errors on;

        # <<< INI PALING PENTING
        fastcgi_request_buffering on;

        fastcgi_max_temp_file_size 0;
    }

    location ~ \.(?:css|js|mjs|svg|gif|png|jpg|jpeg|ico|webp|wasm|tflite|map|ogg|flac|mp4|webm)$ {
        try_files $uri /index.php$request_uri;

        expires 6M;

        add_header Cache-Control "public, max-age=15778463";

        access_log off;
    }

    location ~ \.(?:woff2?|eot|ttf|otf)$ {
        try_files $uri /index.php$request_uri;

        expires 7d;

        access_log off;
    }

    location / {
        try_files $uri $uri/ /index.php$request_uri;
    }
}