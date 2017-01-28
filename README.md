<p align="center">
<a href="https://github.com/TheOrchid/Platform"><img width="120" height="120"  src="https://cloud.githubusercontent.com/assets/5102591/22400392/7027abde-e5c4-11e6-8548-81e0197ee333.png">
</a>
</p>




<p align="center">
	WebSocket Server for Laravel 5 
</p>

<p align="center">



<a href="https://packagist.org/packages/orchid/socket"><img src="https://poser.pugx.org/orchid/socket/v/stable"/></a>

<a href="https://packagist.org/packages/orchid/socket"><img src="https://poser.pugx.org/orchid/socket/downloads"/></a>

<a href="https://packagist.org/packages/orchid/socket"><img src="https://poser.pugx.org/orchid/socket/license"/></a>


</p>



# Socket

Socket - full-duplex communication protocol over the TCP-connection for exchanging messages between the browser and web server in real time.

WebSocket is designed to implement in the web-browser and web-server, but it can be used for any client or server application.

Protocol WebSocket - an independent protocol based on the TCP protocol. It enables greater interaction between the browser and the web site, promoting the dissemination of interactive content and the creation of real-time games.





## Installation

1. install package

	```php
    composer require orchid/socket
	```

1. edit config/app.php

	service provider :

	```php
	Orchid\Socket\Providers\SocketServiceProvider::class
	```

1. structure

	```php
	php artisan vendor:publish
	```


## Usage

### Creature :
	
To create a new listener, you need to	
```php
php artisan make:socket MyNameClass
```

In the folder `app/HTTP/Socket/Listener` create template Web listener socket

After creating a need to establish a route which Is located `routes/socket.php`

```php
//routing is based on an Symfony Routing Component
$socket->route('/myclass', new MyClass, ['*']);
```

To launch the web-socket, use the command:
```php
php artisan socket:serve
```

### FAQ



####JavaScript
Connecting Web socket in JavaScript

```javascript
var socket = new WebSocket("ws://localhost");

socket.onopen = function() {
  alert("The connection is established.");
};

socket.onclose = function(event) {
  if (event.wasClean) {
    alert('Connection closed cleanly');
  } else {
    alert('Broken connections'); 
  }
  alert('Key: ' + event.code + ' cause: ' + event.reason);
};

socket.onmessage = function(event) {
  alert("The data " + event.data);
};

socket.onerror = function(error) {
  alert("Error " + error.message);
};


//To send data using the method socket.send(data).

//For example, the line:
socket.send("Hello");

```



####Authorization
Example of installation numbers unique socket and session laravel
```php

public function onOpen(ConnectionInterface $conn)
{
    $this->clients->attach($conn);
    
    //take user id
    $userId = $this->getUserFromSession($conn);
    
    //Create a list of users connected to the server
    array_push($this->userList, $userId);
    
    //We tell everything that happened
    echo "New connection! user_id = ({$userId})\n";
}

public function getUserFromSession($conn)
{
    // Create a new session handler for this client
    $session = (new SessionManager(App::getInstance()))->driver();
    
    // Get the cookies
    $cookies = $conn->WebSocket->request->getCookies();
    
    // Get the laravel's one
    $laravelCookie = urldecode($cookies[Config::get('session.cookie')]);
    
    // get the user session id from it
    $idSession = Crypt::decrypt($laravelCookie);
    
    // Set the session id to the session handler
    $session->setId($idSession);
    
    // Bind the session handler to the client connection
    $conn->session = $session;
    $conn->session->start();
    
    //We take the user from a session
    $userId = $conn->session->get(Auth::getName());
    return $userId;
}

```


####Nginx proxy

```

    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }

    upstream websocket {
        server you-web-site.com:5300;
    }

    server {
        listen 443;
        location / {
            proxy_pass http://websocket;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;


                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
            proxy_redirect off;
        }
    }
```


####Supervisor

```
[program:laravel-socket]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/your-path/artisan socket:serve
autostart=true
autorestart=true
user=root
numprocs=1
redirect_stderr=true
stdout_logfile=/var/www/your-path/storage/logs/socket.log
```


## License

The MIT License (MIT). Please see [License File](LICENSE) for more information.
