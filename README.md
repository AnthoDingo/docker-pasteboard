## Pasteboard Docker Container

#### ENV Var

This option are optional, this is default value.

##### URL
```
ORIGIN=pasteboard.co
```

##### Time sewage
```
MAX=7
```

#### Exposition

You can expose the port 4000 on all interface like that :

```
docker run --name pastebaord \
-e ORIGIN=mydomain.tld \
-p 4000:4000 \
anthodingo/docker-pasteboard
```

Or you can bind the port 4000 only on loopback (127.0.0.1) like : (more secure)
```
docker run -d --name pastebaord \
 -e ORIGIN=mydomain.tld \
 -p 127.0.0.1:4000:4000 \
 anthodingo/docker-pasteboard
```


#### Folder

You can store data and config externaly of container :
```
docker run -d --name pasteboard \
-p 4000:4000 \
-v /srv/pasteboard/images:/pasteboard/public/storage \
anthodingo/docker-pasteboard
```


#### Nginx exemple

This exemple work in production :

```
upstream pasteboard {
   server 127.0.0.1:4000;
}

server {
    listen 80;
    server_name _;

    client_max_body_size 10M;

    location / {
        proxy_http_version 1.1;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://pasteboard/;
        proxy_redirect off;
        proxy_buffering off;
    }
}
```

You can change max image size in : client_max_body_size


#### Pasteboard auth

All auth exemple are present on source repo : https://github.com/JoelBesada/pasteboard/tree/master/auth

Exemple for hashing.js :
```javascript
exports.keyHash = function(key) {
	var hashedKey;
	var crypto = require('crypto');
	var shasum = crypto.createHash('sha1');
	shasum.update(key);
	hashedKey = shasum.digest('hex');
	return hashedKey;
};
```

##### How to install auth

Write the file on your server and push to container:
```
cat << EOF > /tmp/hashing.js
exports.keyHash = function(key) {
	var hashedKey;
	var crypto = require('crypto');
	var shasum = crypto.createHash('sha1');
	shasum.update(key);
	hashedKey = shasum.digest('hex');
	return hashedKey;
};
EOF

docker cp /tmp/hashing.js pasteboard:/pasteboard/auth/hashing.js
rm /tmp/hashing.js
```


#### Why my repo and not official ?

I included all pull request from official repo.

[Official source repo Pasteboard](https://github.com/JoelBesada/pasteboard)
[My repo Pasteboard](https://github.com/Janus-SGN/pasteboard.git)