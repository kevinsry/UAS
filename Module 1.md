**Kevin Surya Diansyah 1202190028**

Practical question.

![1 1](https://user-images.githubusercontent.com/89094789/152289708-ab37ab25-c3e4-4ed6-a0e6-a3d5e47bc007.PNG)


From the question, we make changes on "soal latihan praktikum" according to "soal praktikum". Link for Practical Question [Disini.](https://github.com/aldonesia/Sistem-Administrasi-Server-2021/blob/master/modul-1/soal_praktikum.md)

### 1. Change container name from ubuntu php_5.6 to ubuntu_landing and config IP with new condition
___

- rename ubuntu_php5.6 to ubuntu_landing

    - stop ubuntu from previous condition

        ```bash
        sudo lxc-stop -n ubuntu_php5.6
        ```
    - change container name then start container

        ```bash
        sudo lxc-copy -R -n ubuntu_php5.6 -N ubuntu_landing
        sudo lxc-start -n ubuntu_landing
        ```
    - check list container
        ```bash
        sudo lxc-ls -f
        ```
        ![1](https://user-images.githubusercontent.com/89094789/152289921-9c43afa2-0f6e-4441-b7bf-01bcb4546049.PNG)

- setting ip 10.0.3.103

     - attach container and open network interfaces

        ```bash
        sudo lxc-attach -n ubuntu_landing
        nano /etc/network/interfaces
        ```
        ![attack container](https://user-images.githubusercontent.com/89094789/152291172-9cfc7873-2155-44d4-b7a9-abb392e1f4e1.PNG)

        
    - restart network

        ```bash
        systemctl restart networking.service
        ifconfig
        ```
        ![restart](https://user-images.githubusercontent.com/89094789/152291394-c92b40e4-23fe-499f-898d-8830bb0c5f56.PNG)

        
    - exit from ubuntu_lanidng and do the next step

        ```bash
        exit
        ```

### 2. Create Debian 9 LXC Container and the name is debian_php5.6 then config IP with new condition
___

- create container

     - create container debian 9 and start container

        ```bash
        sudo lxc-create -n debian_php5.6 -t download -- --dist debian --release stretch --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org
        sudo lxc-start -n debian_php5.6
        ```
    - check container list
        ```bash
        sudo lxc-ls -f
        ```
        ![list](https://user-images.githubusercontent.com/89094789/152292099-b2d73145-2c1d-4a3f-af4e-8d81afee2dd3.PNG)


- install nginx and nginx-extras 

    - install nginx and nginx-extras inside the container
        ```bash
        sudo apt install nginx nginx-extras
        ```

- setting ip 10.0.3.102

    - attach to container and install net-tools curl then open network interfaces

        ```bash
        sudo lxc-attach -n debian_php5.6
        apt install nano net-tools curl
        nano /etc/network/interfaces
        ```
        ![set ip](https://user-images.githubusercontent.com/89094789/152292428-7191b8a0-0603-40be-a110-41f5b25d6b5b.PNG)

    - restart network and check ip

        ```bash
        systemctl restart networking.service
        ifconfig
        ```
        ![restart 2](https://user-images.githubusercontent.com/89094789/152292414-216e0b44-0829-49a9-839f-08648d3e68cf.PNG)


### 3. setting nginx debian_php5.6 with domain http://lxc_php5.dev and create LXC description inside
___

- setting nginx

    - open sites available directory and create empty file with name lxc_php5.6.dev

        ```bash
        cd /etc/nginx/sites-available
        touch lxc_php5.6.dev
        nano lxc_php5.6.dev 
        ```
        ![3 1](https://user-images.githubusercontent.com/89094789/152292925-615679da-0e2b-4944-a6d2-6c480404f72f.PNG)
    - open sites enabled directory, create symbolic link file container and do testing then open directory host file

        ```bash
        cd ../sites-enabled
   	    ln -s /etc/nginx/sites-available/lxc_php5.6.dev .
   	    nginx -t
   	    nginx -s reload
   	    nano /etc/hosts
        ```
        ![3 2](https://user-images.githubusercontent.com/89094789/152292993-cb2ca958-3370-49e3-90de-c5d58bf048cc.PNG)

    - open html directory, create lxc_php5.6 directory dan copy code from nginx-debian.html to index.html then open index html file

        ```bash
        cd /var/www/html
        mkdir lxc_php5.6
        cp index.nginx-debian.html lxc_php5.6/index.html
        nano index.html
        ```
       ![3 3](https://user-images.githubusercontent.com/89094789/152293033-1633b7b5-132c-4af7-a683-e510b9dcab21.PNG)

    - check connectivity to URL

        ```bash
        curl -i http://lxc_php5.dev
        ```
    - exit from debian_php5.6 and do the next step

        ```bash
        exit
        ```

### 4. setting nginx ubuntu_landing with domain http://lxc_landing.dev and create LXC description inside

- setting nginx

    - attach to container then open sites available directory

        ```bash
        sudo lxc-attach -n ubuntu_landing
        cd /etc/nginx/sites-available
        ```
    - rename file name and change the server_name and the directory

        ```bash
        mv lxc_php5.6.dev lxc_landing.dev
        nano lxc_landing.dev
        ```
        ![4 1](https://user-images.githubusercontent.com/89094789/152294190-0d80ec8e-af9c-43e1-9627-b679ae27ba80.PNG)

    - open sites enable directory, create symbolic link file container and do testing then open directory host file

        ```bash
        cd ../sites-enabled
   	    ln -s /etc/nginx/sites-available/lxc_landing.dev .
   	    nginx -t
   	    nginx -s reload
   	    nano /etc/hosts
        ```
        ![4 2](https://user-images.githubusercontent.com/89094789/152294217-a2b17310-f8bc-48fb-b8f7-e58aff27c6a9.PNG)

    - open var html directory and rename file lxc_php5.6 to lxc_landing then open index html

        ```bash
        cd /var/www/html
        mv lxc_php5.6 lxc_landing
        nano index.html
        ```
       ![4 3](https://user-images.githubusercontent.com/89094789/152294252-d5ae51fd-5214-43ae-8906-94bfb78d6eaf.PNG)

    - check connectivity to URL

        ```bash
        curl -i http://lxc_php5.dev
        ```
    - exit from ubuntu_landing and do next step

        ```bash
        exit
        ```
### 5. setting ubuntu landing to autostart when open the vm
- setting config container

    - setting config auto start with lxc.start.auto = 1

        ```bash
        echo "lxc.start.auto = 1" >> /var/lib/lxc/ubuntu_landing/config
        ```
    - check container list
        ```bash
        sudo lxc-ls -f
        ```
       ![5](https://user-images.githubusercontent.com/89094789/152293941-07b11faa-958a-4f12-a383-294b4346665d.PNG)

    - stop the container
        ```bash
        sudo lxc-stop -n ubuntu_landing
        ```
### 6. setting nginx on vm according to the question
- setting nginx 

    - setting nginx hosts

        ```bash
        sudo nano /etc/hosts
        ```
        ![6 1](https://user-images.githubusercontent.com/89094789/152293718-ded0d983-b885-478d-9006-7f66dba50547.PNG)

     - open sites available directory and open vm.local

        ```bash
        cd /etc/nginx/sites-available
        sudo nano vm.local
        ```
        ![6 2](https://user-images.githubusercontent.com/89094789/152293698-565839b3-a79d-4aa7-a856-b852714499a5.PNG)

    - do testing then check connectivity to URL

        ```bash
        sudo nginx -t
        sudo nginx -s reload
        curl -i http://vm.local
        ```
### 7. testing on web browser
- testing web result 
    - access http://vm.local

      ![7 1](https://user-images.githubusercontent.com/89094789/152293413-c9a4a598-8bc5-48b9-b281-b61a5a9e1c10.PNG)

      
    - access http://vm.local/blog
    
      ![7 2](https://user-images.githubusercontent.com/89094789/152293439-cd21798e-f8a1-4dbe-841e-b9d56716c08f.PNG)

      
    - access http://vm.local/app
    
      ![7 3](https://user-images.githubusercontent.com/89094789/152293471-fdd3a436-5cca-4316-9267-00bd6abba022.PNG)

      
### 8. Analysis result
- Answer
    
   -Ubuntu 16.04 xenial will reach end of standart support and cant be used for php 5.6
   - a proxy server is a server application that acts as an intermediary between a client requesting a resource and the server providing that resource.
    From the case, vm.local as proxy server and web landing page.
