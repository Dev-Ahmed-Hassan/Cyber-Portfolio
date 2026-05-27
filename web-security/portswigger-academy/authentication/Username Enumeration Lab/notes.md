this lab is available on portswigger web academy in the authentication vulnerabilities module.

here is the link 
https://portswigger.net/web-security/learning-paths/authentication-vulnerabilities/password-based-vulnerabilities/authentication/password-based/lab-username-enumeration-via-different-responses


{SS of the lab description}
so here is the lab's description



lets start 



lets observe the structure of the site and see if anything interests us... 

our goal is to bruteforce into the site using a valid email and password..


although they have given us

words lists for usernames and passwords... 

I have included those in the bottom and we will get to those later on..

first I think there might be some way... to at least find the username. 


so right we were on noting the structure...

it is a blog site. 
{SS of the home page of the blog site}
featuring various blogs
{another SS}

each blog has some comments in the bottom.
{SS showing comments form}

the comments show names and dates  (hmm.. seems important maybe some user inputted the same username in the comments as it is in their real id)

there is also a My account page where there is a login form so... this is the place where we will be performing the enumeration...


so I was saying instead of using the words list why not first try to find the username from the comments and try it one by one.... 

I'll try 5 of those and then if not successful in finding identifying a valid username, then I will refer the provided lists.



{SS of the comment we are targetting first}
This is the username I will target first



we'll use a random pass like pass1234 to identify the behaviour of the login form


{SS showing the form with inputted values}

alright.. it gave us invalid username message
{SS showing this}

now we can at least identify if a username is valid or not, that is good. lets target another comment.

this time I am targetting a blog author 
{SS showing this}

now their name is Russell Up lets try different combinations 
{SS showing author name with blog}

now I have tried the following combinations but got the same invalid username result... lets try something else 
RussellUp
russellup
russell_up
Russell-Up
russellup
RussellUP
russell
Russell
RUSSELL

{SS showing login form with invalid}


no worries lets move to someother comment...


{SS showing new target comment}
Amber Light is new target...

lets try its combinations

AMBERLIGHT
amber-light
amberLIGHT
AmberLight
aMbErLiGhT
amber
Amber
AMBER
aMbER

no luck.... well one lesson is learnt ig... this is tiresome gathering information and planning a breakthrough part... but still it is necessary to know your target well. Alright lets take a look at words lists and figure out what we can do


USERNAMES LIST:
carlos
root
admin
test
guest
info
adm
mysql
user
administrator
oracle
ftp
pi
puppet
ansible
ec2-user
vagrant
azureuser
academico
acceso
access
accounting
accounts
acid
activestat
ad
adam
adkit
admin
administracion
administrador
administrator
administrators
admins
ads
adserver
adsl
ae
af
affiliate
affiliates
afiliados
ag
agenda
agent
ai
aix
ajax
ak
akamai
al
alabama
alaska
albuquerque
alerts
alpha
alterwind
am
amarillo
americas
an
anaheim
analyzer
announce
announcements
antivirus
ao
ap
apache
apollo
app
app01
app1
apple
application
applications
apps
appserver
aq
ar
archie
arcsight
argentina
arizona
arkansas
arlington
as
as400
asia
asterix
at
athena
atlanta
atlas
att
au
auction
austin
auth
auto
autodiscover



a lot of usernames let me try these in order of what I think are most common or expected 



Ahaaa.... found it  hte user name is oracle it finally said incorrect password
{SS showing this form}

it means that we found the username


now i didn't use any tools like hydra to automate this bruteforce so it took me a while


so now lets see the passwords file..



123456
password
12345678
qwerty
123456789
12345
1234
111111
1234567
dragon
123123
baseball
abc123
football
monkey
letmein
shadow
master
666666
qwertyuiop
123321
mustang
1234567890
michael
654321
superman
1qaz2wsx
7777777
121212
000000
qazwsx
123qwe
killer
trustno1
jordan
jennifer
zxcvbnm
asdfgh
hunter
buster
soccer
harley
batman
andrew
tigger
sunshine
iloveyou
2000
charlie
robert
thomas
hockey
ranger
daniel
starwars
klaster
112233
george
computer
michelle
jessica
pepper
1111
zxcvbn
555555
11111111
131313
freedom
777777
pass
maggie
159753
aaaaaa
ginger
princess
joshua
cheese
amanda
summer
love
ashley
nicole
chelsea
biteme
matthew
access
yankees
987654321
dallas
austin
thunder
taylor
matrix
mobilemail
mom
monitor
monitoring
montana
moon
moscow



on second thought... i should've used burpsuite for this... but i don't know how to use it until now... so lets do it by hand for now
I'm fed up by mannual typing... lets go to burpsuite... I will learn along the way...


downloading the burpsuite will take time lets try to use hydra in that while


this is the address of the post request

https://0a22002c04bf53d281126ccb006700fd.web-security-academy.net/login


so lets put that into hydra


{SS showing hydra is installed in machine}


lets first create a passwords list and then place the passwords into it 

using following commands in the desired directory

nano pass.txt
paste the text 
ctrl+O
ctrl+X


otherwise we can use echo too for inputting in the file

{SS showing the nano file}


ok so I have saved it via words-list.txt 


running the command 

hydra -l oracle -P words-list.txt 0a22002c04bf53d281126ccb006700fd.web-security-academy.net https-post-form "/login:username=^USER^&password=^PASS^:F=Incorrect password" -V -f 

I am not using the timer for now cause it is a lab and timer considerably reduces the time


yes now we are talking...
the pass found is 666666
{SS of hydra showing conformation}


now applying that in the blog site..

{SS showing inputted values}

click login


Boom... we are in

{SS showing the logged in page}


was a good learning experience and I surely understood why to use automation tools for bruteforce :( 

with this we conclude our bruteforce learning for username enumeration.

Meet you again in next lab!

Till then, Keep breaking!

