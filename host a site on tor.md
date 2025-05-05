
## What is TOR

tor stands for the onion router it is very privacy focused and allow to anonymously surf the web or dark web its hide the ip addresses and encrypt the internet traffic with multiple series of nodes so in between no one know that what is the source node and what is the destination node


## how to host a simple website on tor

so to host a website on tor you need to host a website on your local machine you can use diifrent webserver to host a website like nginx ,apache2 etc..

## here i mention step to host a simple website on local machine

first you need to download the webserver where you host a website in my case i use a nginx and i am  using kali linux as a os  so if you use other os like windows so the command is diiffrent

the command to install nginx in your local machine:

	sudo apt update
	sudo apt install nginx -y

move  your website folder  under /var/www/html

	cp /your/website/folder/path /var/www/html

start the server

	sudo systemctl start nginx

verify everything works fine or not

	sudo systemctl status nginx



option 2:
	hit the localhost on your browser

		http://localhost or  http://localhost:80 or http://127.0.0.1:80


![[Pasted image 20250505161236.png]]


you can see the home page of the website 

okk,

now to host to tor network you have to install a tor in your local machine and to access the website you need to install tor-launcher

to install tor:

	sudo apt install tor -y
	
then you need to start the tor,

	sudo systemctl start tor

to start the tor on boot

	sudo systemctl enable tor

to verify tor is started or not 

	sudo systemctl status tor

now, to access the tor website you need to tor-browser here is the command to install tor browser

	sudo apt install torbrowser-launcher -y

to run tor browser

	torbrowser-launcher

okk you successfully setup a website and install a tor in your local machine

you can not resolve the tor website through normal dns tor use diffrent networking right its use .onion for every domain

to make that happend you need to edit the configuration of tor 

first edit a torrc file 

	nano /etc/tor/torrc

in torrc file add the below lines

	HiddenServiceDir /var/lib/tor/hidden_service/
	HiddenServicePort 80 127.0.0.1:80

![[Pasted image 20250505145627.png]]

now restart the tor using this command

	sudo systemctl restart tor


to get the .onion address you have have to obtain from this path

	sudo cat /var/lib/tor/my_custom_hidden_service/hostname

![[Pasted image 20250505153801.png]]


now put this on a tor borwser and it will look like this

![[Pasted image 20250505154112.png]]

now if you want to add a custom prefix in your address you need to find a address for that for that you can use a vanity v3 for find the address here below i mention a steps to find a your custom address and host on that address

first clone the given repo

	git clone https://github.com/cathugger/mkp224o

go in this folder 

	cd mkp224o

![[Pasted image 20250505154606.png]]

first run the autogen.sh

	./autogen.sh

this will make a configure file after that run this file

	./configure.sh

after this compile the whole folder using make

	make

now to find a custom address you can run command like this

	./mkp224o abc

in my case 

	./mk224 anurag

![[Pasted image 20250505154930.png]]


in this folder /mk224o you will find all the above as an folder 

![[Pasted image 20250505155042.png]]

this folder contain three things

--> hostname
--> public key
--> secret key

![[Pasted image 20250505155231.png]]


now go to the /var/lib/tor folder

	cd /ver/lib/tor

make a directory called my_service

	mkdir my_service

![[Pasted image 20250505155513.png]]

go inside that directory

	cd my_service

copy the public and secret key from the where you find the address 

`cp ~/Downloads/tor_miner/mkp224o/abcgebba74a54fc7dpy3v52dgsak7hjsqzft2maq65diod4p26ctauyd.onion .


![[Pasted image 20250505155718.png]]

give the permissionas

	sudo chown -R debian-tor:debian-tor /var/lib/tor/my_service/
	sudo chmod 700 /var/lib/tor/my_service/
	sudo chmod 600 /var/lib/tor/my_service/hs_ed25519_public_key
	sudo chmod 600 /var/lib/tor/my_service/hs_ed25519_secret_key

change the path in /etc/tor/torrc 

![[Pasted image 20250505160148.png]]

now restart the nginx and tor

	sudo systemctl restart nginx
	suso systemctl restart tor

put a hostname in your browser

![[Pasted image 20250505160737.png]]

![[Pasted image 20250505160818.png]]


for any  Troubleshooting you can verify if this below command works find then all good

	tor -f /etc/tor/torrc