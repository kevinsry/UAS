
**Kevin Surya Diansyah 1202190028**


### - Duplicate 2 containers then start
![image](https://user-images.githubusercontent.com/83237598/148384091-aeba656e-bcee-412a-b976-3ea31a1b4b67.png)
### - Change IP address ubuntu_php.7.4_2 to 10.0.3.111/24 & ubuntu_php.7.4_3 to 10.0.3.121
![image](https://user-images.githubusercontent.com/83237598/148384692-67b8128e-f77f-4f23-a89e-03aadcf0cefb.png)
### - Go to lxc_php7_2.dev hosts directory and change server name
![image](https://user-images.githubusercontent.com/83237598/148385021-bf901cb8-bf1d-4254-875d-82ce7309e7e9.png)
![image](https://user-images.githubusercontent.com/83237598/148385181-5aaa0ae0-027b-49ff-bfe5-326eebab5b67.png)
### - restart nginx
![image](https://user-images.githubusercontent.com/83237598/148385402-60bf1d82-360e-447a-8e31-600c4c605366.png)
### - Do the same step for ubuntu_php7.4_3 (10.0.3.121), debian_php5.6_2 (10.0.3.112), and debian_php5.6_3 (10.0.3.122)
### - Go to directory hosts on vm and add server name
![image](https://user-images.githubusercontent.com/83237598/148385721-35de26a6-ed04-4555-81fc-81c37bde4d6e.png)
### - Go to nano /etc/nginx/sites-available/vm.local
![10](https://user-images.githubusercontent.com/92965284/148387606-2f93d33d-1cff-474a-ab5d-1f9567c3b020.jpg)
### - Run the JMeter without Load balancing
   - JMeter with 50 person
     
     ![TanpaLoad(50) 2](https://user-images.githubusercontent.com/92965284/148388453-033fe9c9-d87f-44ec-98f8-86f0e58158eb.png)
     
     ![TanpaLoad(50) 1](https://user-images.githubusercontent.com/92965284/148388502-3a035f1b-76c4-4221-affa-36f0cdca50a2.png)

     ![TanpaLoad(50)](https://user-images.githubusercontent.com/92965284/148388542-19c2265e-2f30-43e3-bdfa-b1e75cb19530.png)

   - JMeter with 100 person
     
     ![TanpaLoad(100) 2](https://user-images.githubusercontent.com/92965284/148388633-3dcf4420-b463-411d-b5fb-35881f8cd75c.jpg)
  
     ![TanpaLoad(100) 1](https://user-images.githubusercontent.com/92965284/148388665-da1730bf-f81e-4512-b75a-ad57a329826e.jpg)
     
     ![TanpaLoad(100)](https://user-images.githubusercontent.com/92965284/148388686-056bae48-f804-4178-8e7b-f6eb8c416737.jpg)
     
   - JMeter with 150 person
   
     ![TanpaLoad(150) 2](https://user-images.githubusercontent.com/92965284/148388735-0bcb1534-e900-48b4-b768-21beb99b6308.jpg)

     ![TanpaLoad(150) 1](https://user-images.githubusercontent.com/92965284/148388759-22bcecb6-3d5f-4ae9-a692-a7be61ec8608.jpg)
     
     ![TanpaLoad(150)](https://user-images.githubusercontent.com/92965284/148388772-c21d82d8-4f1f-4a8c-922b-26cf9b417915.jpg)

     
### - After that, go to nano /etc/nginx/sites-available/vm.local then add Load balancing and Change the proxy_pass
![image](https://user-images.githubusercontent.com/83237598/148386361-40df0f27-2966-4b2b-9804-818ddc487845.png)

![image](https://user-images.githubusercontent.com/83237598/148386483-c4f1dafb-5e93-42a4-b79b-adb59d4211a8.png)

### - Back to JMeter
   - JMeter with 50 person
     
     ![Load(50) 2](https://user-images.githubusercontent.com/92965284/148389307-7c18fc7c-410a-48b5-a139-b69856c588aa.png)

     ![Load(50) 1](https://user-images.githubusercontent.com/92965284/148389332-6647848a-6793-44a1-9398-499d2ac841da.png)
     
     ![Load(50)](https://user-images.githubusercontent.com/92965284/148389358-4a76842d-363e-44ef-b6d3-980b96129d17.png)

   - JMeter with 100 person
    
     ![Load(100) 2](https://user-images.githubusercontent.com/92965284/148389392-b963c7da-1894-4213-84d5-e76833747bc9.jpg)
     
     ![Load(100) 1](https://user-images.githubusercontent.com/92965284/148389431-33c1d2fa-def8-410e-ad55-18bb7df052a2.jpg)
     
     ![Load(100)](https://user-images.githubusercontent.com/92965284/148389486-a1e81d89-de43-44eb-b221-4fadc9ffc685.jpg)
     
   - JMeter with 150 person
     
     ![Load(150) 2](https://user-images.githubusercontent.com/92965284/148389581-cf099874-9214-43a9-a0ad-41bcb2c62cdc.jpg)

     ![Load(150) 1](https://user-images.githubusercontent.com/92965284/148389612-b4598bdb-cd72-4b15-b820-7fc543d4b370.jpg)
     
     ![Load(150)](https://user-images.githubusercontent.com/92965284/148389677-186036c1-cd95-4473-bfe3-04aa60a75f26.jpg)
