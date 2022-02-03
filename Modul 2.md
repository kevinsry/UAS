**Kevin Surya Diansyah 1202190028**

## Practical Question
https://github.com/aldonesia/Sistem-Administrasi-Server-2021/blob/master/modul-2/soal_praktikum.md
## 1.
### Destroy lxc_landing ver 18 and install ubuntu landing ver 20 (focal)
```bash
sudo lxc-destroy ubuntu_landing
```
<img width="594" alt="2" src="https://user-images.githubusercontent.com/83237598/144440310-03467ad1-af56-42c7-bfbe-32875efe8913.png">

```bash
sudo lxc-create -n ubuntu_landing -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
```
### Config IP ubuntu landing (10.0.3.103)
```bash
sudo lxc-start -n ubuntu_landing
sudo lxc-attach  -n ubuntu_landing
nano /etc/netplan/10-lxc.yaml
netplan apply
```
<img width="547" alt="3 (2)" src="https://user-images.githubusercontent.com/83237598/144442747-31dad820-a909-4bf3-9812-f161b21d81e2.png">


![image](https://user-images.githubusercontent.com/83237598/144441576-6e805588-8591-4c79-8e5d-a00a27dcecbb.png)

<img width="467" alt="5" src="https://user-images.githubusercontent.com/83237598/144442904-d01defea-6634-41bc-b7c7-04ad4cbed890.png">

### Set auto start at Ubuntu_landing
<img width="580" alt="6" src="https://user-images.githubusercontent.com/83237598/144443943-9660f07e-3089-4f1a-89b4-fb5b8d219599.png">

### Instal SSH
<img width="475" alt="7" src="https://user-images.githubusercontent.com/83237598/144444483-0c9c4a7f-bdb9-4eb1-914c-95e1c2d81ec4.png">

### Set PermitRootLogin yes & RSAAuthentication yes
![image](https://user-images.githubusercontent.com/83237598/144445223-e7f114aa-2b2a-4a59-a42b-ed1b5c703dce.png)

### Check ssh, it's already run or not
<img width="604" alt="8" src="https://user-images.githubusercontent.com/83237598/144445384-89f4b0bb-cf88-47c5-83ff-4db09a8f11af.png">

## 2.
### Same step as ubuntu landing
### Destroy ubuntu_php7.4 ver 18 and install ubuntu_php7.4 ver 20 (focal)
<img width="559" alt="no 2" src="https://user-images.githubusercontent.com/83237598/144446528-993ea3bc-26cb-4fc7-ab54-cd5e9bd817ce.png">

## 3.
## vm.local/
### Install Laravel with ansible & go to ansible directory and create new folder

![image](https://user-images.githubusercontent.com/83237598/144457459-47704029-43ec-4a9b-ad7c-1ef6e9dc1868.png)
  
### create host
  
![image](https://user-images.githubusercontent.com/83237598/144457532-1794e9ba-4c92-4517-b749-7ea1a6eebaaf.png)

  ```markdown
  [landing]
  ubuntu_landing ansible_host=lxc_landing.dev ansible_ssh_user=root ansible_become_pass=sepasi
  
  ```

 ### create directory to run php folder
          	
![image](https://user-images.githubusercontent.com/83237598/144458602-c788dac3-b757-4bc5-ad26-b23fc83e2f88.png)
    

  ```
  ---
  - hosts: all
    become : yes
    tasks:
      - name: install nginx nginx extras
        apt:
         pkg:
           - nginx
           - nginx-extras
         state: latest
      - name: start nginx
        service:
         name: nginx
         state: started
      - name: menginstall tools
        apt:
         pkg:
           - curl
           - software-properties-common
           - unzip
         state: latest
      - name: "Repo PHP 7.4"
        apt_repository:
          repo="ppa:ondrej/php"
      - name: "Updating the repo"
        apt: update_cache=yes
      - name: Installation PHP 7.4
        apt: name=php7.4 state=present
      - name: install php untuk laravel
        apt:
         pkg:
            - php7.4-fpm
            - php7.4-mysql
            - php7.4-mbstring
            - php7.4-xml
            - php7.4-bcmath
            - php7.4-json
            - php7.4-zip
            - php7.4-common
         state: present
  ```

  

  ### create folder installcomposer.yml

  ![image](https://user-images.githubusercontent.com/83237598/144459972-36237625-d760-4ccc-af8f-94925f15a39a.png)


    


  ```
  ---
   -hosts: all
    become : yes
    tasks:
     - name: Download and install Composer
       shell: curl -sS https://getcomposer.org/installer | php
       args:
        chdir: /usr/src/
        creates: /usr/local/bin/composer
        warn: false
     - name: Add Composer to global path
       copy:
        dest: /usr/local/bin/composer
        group: root
        mode: '0755'
        owner: root
        src: /usr/src/composer.phar
        remote_src: yes
     - name: Composer create project
       become_user: root
       composer:
        command: create-project
        arguments: laravel/laravel landing 
        working_dir: /var/www/html
        prefer_dist: yes
       environment:
          COMPOSER_NO_INTERACTION: "1"
     - name: mengkopi file .env.example jadi .env
       copy:
        dest: /var/www/html/landing/.env.example
        src: /var/www/html/landing/.env
        remote_src: yes
     - name: mengganti konfigurasi .env
       lineinfile:
        path: /var/www/html/landing/.env
        regexp: "{{ item.regexp }}"
        line: "{{ item.line }}"
        backrefs: yes
       loop:
        - { regexp: '^(.*)DB_HOST(.*)$', line: 'DB_HOST=10.0.3.200' }
        - { regexp: '^(.*)DB_DATABASE(.*)$', line: 'DB_DATABASE=landing' }
        - { regexp: '^(.*)DB_USERNAME(.*)$', line: 'DB_USERNAME=admin' }
        - { regexp: '^(.*)DB_PASSWORD(.*)$', line: 'DB_PASSWORD= ' }
        - { regexp: '^(.*)APP_URL(.*)$', line: 'APP_URL=http://vm.local' }
        - { regexp: '^(.*)APP_NAME=(.*)$', line: 'APP_NAME=landing' }
     - name: Composer install ke landing
       composer:
         command: install
         working_dir: /var/www/html/landing
       environment:
         COMPOSER_NO_INTERACTION: "1"
     - name: generate php artisan
       args:
        chdir: /var/www/html/landing
       shell: php artisan key:generate
     - name: mengganti permission storage
       file:
        path: /var/www/html/landing/storage
        mode: 0777
        recurse: yes
  
  ```

  

  

  ### Install
  
  <img src= "https://github.com/acid99/Sistem-Administrasi-Server/blob/main/assets/laprak2/no%203/2021-11-30_6.png?raw=true">


  ### Create lxc_landing.dev

  ![image](https://user-images.githubusercontent.com/83237598/144527710-15956ccd-c610-4ed9-b1ec-15dd670013be.png)



    ```
    server {
            listen 80;
    
            root /var/www/html/landing/public;
            index index.php index.html index.htm;
            server_name lxc_landing.dev;
    
            error_log /var/log/nginx/landing_error.log;
            access_log /var/log/nginx/landing_access.log;
    
            client_max_body_size 100M;
            location / {
                    try_files $uri $uri/ /index.php$args;
            }
            location ~\.php$ {
                    include snippets/fastcgi-php.conf;
                    fastcgi_pass unix:run/php/php7.4-fpm.sock;
                    fastcgi_param SCRIPTFILENAME $document_root$fastcgi_script_name;
            }
    }
    ```

    

  ### Create file config.yml

  ![image](https://user-images.githubusercontent.com/83237598/144459347-de209817-9aee-41eb-ac22-49f25a31c225.png)

    

    ```
    ---
    - hosts: all
      become : yes
      vars:
        domain: 'lxc_landing.dev'
      tasks:
       - name: stop apache2
         service:
          name: apache2
          state: stopped
          enabled: no
       - name: Write {{ domain }} to /etc/hosts
         lineinfile:
          dest: /etc/hosts
          regexp: '.*{{ domain }}$'
          line: "127.0.0.1 {{ domain }}"
          state: present
       - name: ensure nginx is at the latest version
         apt: name=nginx state=latest
       - name: start nginx
         service:
          name: nginx
          state: started
       - name: copy the nginx config file 
         copy:
          src: ~/ansible/laravel/lxc_landing.dev
          dest: /etc/nginx/sites-available/lxc_landing.dev
       - name: Symlink lxc_landing.dev
         command: ln -sfn /etc/nginx/sites-available/lxc_landing.dev /etc/nginx/sites-enabled/lxc_landing.dev
         args:
          warn: false
       - name: restart nginx
         service:
          name: nginx
          state: restarted
       - name: restart php7
         service:
          name: php7.4-fpm
          state: restarted
       - name: curl web
         command: curl -i http://lxc_landing.dev
         args:
          warn: false
    ```

    

  ### Install

![image](https://user-images.githubusercontent.com/83237598/144459478-9b735d1a-927f-4bb6-a7ec-8198fb2d2ae6.png)


  ### Open vm.local/

  ![image](https://user-images.githubusercontent.com/83237598/144459187-adbc0d6a-c6b3-403d-8279-9c887f9e5277.png)

## 4.      
## vm.local/blog

![2](https://user-images.githubusercontent.com/92965284/144470961-254b14ee-81bb-4cea-a019-a9223aa5b297.png)

  ```
     nano hosts
  ```
 ### buat deploy-wordpress
![3](https://user-images.githubusercontent.com/92965284/144471270-2928ee9b-742a-4013-8bea-d9d0078f754e.png)

  ```
     nano deploy-wordpress.yml
  ```
### buat direktori 
![4](https://user-images.githubusercontent.com/92965284/144471837-b25865c4-71e4-4a9d-a68e-4bbb1c2728bb.png)

  ```
    mkdir -p roles/wordpress/tasks
    mkdir -p roles/wordpress/handlers
    mkdir -p roles/wordpress/templates
  ```
 ### open main.yml pada tasks
![5](https://user-images.githubusercontent.com/92965284/144472419-5ce0c433-ab96-40cc-9e18-d9df0f273936.png)

  ```
    ---
    - name: delete apt chache
     become: yes
     become_user: root
     become_method: su
     command: rm -vf /var/lib/apt/lists/*
   - name: install requirement
     become: yes
     become_user: root
     become_method: su
     apt: name={{ item }} state=latest update_cache=true
     with_items:
      - nginx
      - nginx-extras
      - curl
      - wget
      - php7.4
      - php7.4-fpm
      - php7.4-curl
      - php7.4-xml
      - php7.4-gd
      - php7.4-opcache
      - php7.4-mbstring
      - php7.4-zip
      - php7.4-json
      - php7.4-cli
      - php7.4-mysqlnd
      - php7.4-xmlrpc
      - php7.4-curl
   - name: wget wordpress
     shell: wget -c http://wordpress.org/latest.tar.gz
   - name: tar latest.tar.gz
     shell: tar -xvzf latest.tar.gz
   - name: copy folder wordpress
     shell: cp -R wordpress /var/www/html/blog
   - name: chmod
     become: yes
     become_user: root
     become_method: su
     command: chmod 775 -R /var/www/html/blog/
   - name: copy .wp-config.conf
     copy:
      src=~/ansible/wordpress/wp.conf
      dest=/var/www/html/blog/wp-config.php
   - name: copy wordpress.conf
     copy:
      src=~/ansible/wordpress/wordpress.conf
      dest=/etc/nginx/sites-available/{{ domain }}
     vars:
      servername: '{{ domain }}'
   - name: Symlink wordpress.conf
     command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
   
   - name: restart nginx
     become: yes
     become_user: root
     become_method: su
     action: service name=nginx state=restarted
   - name: Write {{ domain }} to /etc/hosts
     lineinfile:
      dest: /etc/hosts
      regexp: '.*{{ domain }}$'
      line: "127.0.0.1 {{ domain }}"
      state: present
   - name: enable module php mbstring
     command: phpenmod mbstring
   - name: restart php
     become: yes
     become_user: root
     become_method: su
     action: service name=php7.4-fpm state=restarted
   - name: restart nginx
     become: yes
     become_user: root
     become_method: su
     action: service name=nginx state=restarted
  ```
  
 ### open wp.conf pada handlers
![6](https://user-images.githubusercontent.com/92965284/144473317-175f7890-0168-43c5-9621-297d3b24a901.png)

  ```
    <?php
      /**
       * The base configuration for WordPress
       *
       * The wp-config.php creation script uses this file during the installation.
       * You don't have to use the web site, you can copy this file to "wp-config.php"
       * and fill in the values.
       *
       * This file contains the following configurations:
       *
       * * MySQL settings
       * * Secret keys
       * * Database table prefix
       * * ABSPATH
       *
       * @link https://wordpress.org/support/article/editing-wp-config-php/
       *
       * @package WordPress
       */
      define( 'WP_HOME', 'http://vm.local/blog' );
      define( 'WP_SITEURL', 'http://vm.local/blog' );
      // ** MySQL settings - You can get this info from your web host ** //
      /** The name of the database for WordPress */
      define( 'DB_NAME', 'blog' );
      /** MySQL database username */
      define( 'DB_USER', 'admin' );
      /** MySQL database password */
      define( 'DB_PASSWORD', 'SysAdminSas0102' );
      /** MySQL hostname */
      define( 'DB_HOST', '10.0.3.200:3306' );
      /** Database charset to use in creating database tables. */
      define( 'DB_CHARSET', 'utf8' );
      /** The database collate type. Don't change this if in doubt. */
      define( 'DB_COLLATE', '' );
      /**#@+
       * Authentication unique keys and salts.
       *
       * Change these to different unique phrases! You can generate these using
       * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
       *
       * You can change these at any point in time to invalidate all existing cookies.
       * This will force all users to have to log in again.
       *
       * @since 2.6.0
       */
      define( 'AUTH_KEY',         'put your unique phrase here' );
      define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
      define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
      define( 'NONCE_KEY',        'put your unique phrase here' );
      define( 'AUTH_SALT',        'put your unique phrase here' );
      define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
      define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
      define( 'NONCE_SALT',       'put your unique phrase here' );
      /**#@-*/
      /**
       * WordPress database table prefix.
       *
       * You can have multiple installations in one database if you give each
       * a unique prefix. Only numbers, letters, and underscores please!
       */
      $table_prefix = 'wp_';
      /**
       * For developers: WordPress debugging mode.
       *
       * Change this to true to enable the display of notices during development.
       * It is strongly recommended that plugin and theme developers use WP_DEBUG
       * in their development environments.
       *
       * For information on other constants that can be used for debugging,
       * visit the documentation.
       *
       * @link https://wordpress.org/support/article/debugging-in-wordpress/
       */
      define( 'WP_DEBUG', false );
      /* Add any custom values between this line and the "stop editing" line. */
      /* That's all, stop editing! Happy publishing. */
      /** Absolute path to the WordPress directory. */
      if ( ! defined( 'ABSPATH' ) ) {
              define( 'ABSPATH', __DIR__ . '/' );
      }
      /** Sets up WordPress vars and included files. */
      require_once ABSPATH . 'wp-settings.php';
  ```
  
  ### open wordpress.conf pada templates
  ![7](https://user-images.githubusercontent.com/92965284/144474031-dff63b9e-3456-4c59-bd93-5d9876bf0fc3.png)
  
  ```
    server {
       listen 80;
       listen [::]:80;
       # Log files for Debugging
       access_log /var/log/nginx/wordpress-access.log;
       error_log /var/log/nginx/wordpress-error.log;
       # Webroot Directory for Laravel project
       root /var/www/html/blog;
       index index.php index.html index.htm;
       # Your  Name
       server_name lxc_php7.dev;
       location / {
               try_files $uri $uri/ /index.php?$query_string;
       }
       # PHP-FPM Configuration Nginx
       location ~ \.php$ {
               try_files $uri =404;
               fastcgi_split_path_info ^(.+\.php)(/.+)$;
               fastcgi_pass unix:/run/php/php7.4-fpm.sock;
               fastcgi_index index.php;
               fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
               include fastcgi_params;
       }
    }
  ```
  ### progress installation ansible
  ![12](https://user-images.githubusercontent.com/92965284/144529352-dce32931-1535-4d8d-b88e-c1cc35cc8b1d.png)

  ### cek browser dengan vm.local/blog
  ![9](https://user-images.githubusercontent.com/92965284/144474874-94f0331f-097f-461a-b452-f62266985fd6.png)
  
  ### login wordpress
  ![10](https://user-images.githubusercontent.com/92965284/144476639-8f3b052f-6401-4dff-93f2-347169a158f3.png)
  
  ### hasil
  ![11](https://user-images.githubusercontent.com/92965284/144477024-4a64c0bd-fd53-47d7-963d-da3e83f12b1f.png)

## Soal Tambahan Praktikum 2
1. rubah konfigurasi php7.4 yang semula menggunakan socket menjadi menggunakan port ( 127.0.0.1:9001 ) => kalo bisa edit di ansible

### Wordpress
### Sama seperti laravel, ganti konfigurasi file menjadi wordpress.conf kemudian rubah seperti gambar dibawah
![image](https://user-images.githubusercontent.com/83237598/144585916-685d1667-d49d-41f7-90c7-6bb4fd1e0eff.png)

### mengganti php sock pada main.yml
![image](https://user-images.githubusercontent.com/83237598/144586767-e2ae94d6-e0bd-4193-b421-c56823f6f74c.png)

### Landing
### konfigurasi file landing.conf dan rubah seperti gambar dibawah
![image](https://user-images.githubusercontent.com/83237598/144586924-a6aa37f9-2cc8-4f6f-a4f0-d2e7d919dea4.png)

### mengganti php sock
![image](https://user-images.githubusercontent.com/83237598/144586971-118244d8-795c-4f42-9b63-e2fef3da79a5.png)

### kemudian jalankan
![image](https://user-images.githubusercontent.com/83237598/144587021-d12f8f34-1043-42df-9baa-c5766e658a05.png)
