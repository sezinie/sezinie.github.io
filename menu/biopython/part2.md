

#  Ipython notebook server & R studio server setting on Biolinux 8 virtual machine
발표자 : 박세진 

## 1. biolinux virtual machine 설치 
* biolinux8은 ubuntu 14.04LTS를 기반으로 한 리눅스 배포판입니다. ubuntu 14.04LTS에서 생기는 동일한 이슈가 발생할 수 있습니다. 그런 경우, 우분투 12.04LTS를 기반으로 한 biolinux7(Released November 2012- http://nebc.nerc.ac.uk/tools/bio-linux-7 )을 사용해보세요.


virtualbox( https://www.virtualbox.org/wiki/Downloads )를 자신의 os에 맞게 설치한 후 
http://environmentalomics.org/bio-linux-download/ 에서 
http://nebc.nerc.ac.uk/downloads/bio-linux-8-latest.ova 를 다운받습니다.(약 4기가)
1. 해당 파일을 더블클릭하면 virtualbox가 열리면서 설정창과 함께 불러옵니다. 
(ubuntu 64bit, virtualbox 모든 네트워크 카드의 맥주소 초기화)
2. 또는 import로 불러올 수 있습니다. 

가상머신은 사용자 manager 패스워드 manager 로 생성됩니다. (passwd 명령어로 수정)

자판이 제대로 인식되는지 ~을 타이핑해봅니다. 
안나온다면 키보드 input source에 korean을 추가합니다.
시작 버튼에서 text entry 
- korean 추가, 원하는 단축키 설정. 

sudo apt-get update

## 2. ipython notebook 설치

sudo apt-get install python-matbplotlib # 이미 설치되어있음. 
sudo apt-get install python-tornado
sudo apt-get install ipython
sudo apt-get install ipython-notebook

여기까지 설치하시면 원하는 위치에서 
ipython notebook --pylab=inline 
으로 ipython notebook 실행할 수 있습니다. 
정지 할때는 ctrl+c

참고 url.
http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-python-ipython-notebook/?fb=ko-kr  

* ipython profile위치를 확인해봅시다. 
cd ~/.config/ipython/
ls
/profile_default 디렉토리만 있는 것을 알 수 있습니다. 
* notebook server 생성.
ipython profile create nbserver
ls
/profile_nbserver 디렉토리가 생성되었습니다. 

biolinux 8에서는 ipython nbserver profile이 다음 위치에 생성되지만, 
~/.config/ipython/profile_nbserver/

각자 환경마다 생성되는 위치가 다를 수 있습니다.

*On Linux (OpenSUSE):*
~/.config/ipython/profile_nbserver/  

*On Linux (Ubuntu):*
~/.ipython/profile_nbserver/

*On Windows:*
\users\azureuser\.ipython\profile_nbserver


* ipython의 profile_nbserver 경로로 이동. 
cd ~/.config/ipython/profile_nbserver

* 인증서 생성.
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
* 아래와 같은 명령을 수행시 비밀번호를 입력하게되며, 이후 생성되는 key를 복사해놓는다. (sha1:으로 시작)
python -c "import IPython;print IPython.lib.passwd()"

* 현재 경로에 위치한 ipython_notebook_config.py을 .old로 백업해놓고 수정.  
cp ipython_notebook_config.py ipython_notebook_config.py.old

* ipython_notebook_config.py 설정파일 수정. 
gedit ipython_notebook_config.py

* 해당 라인을 #을 지우고 옵션을 바꾼다. # 지울때 c.앞에 첫 칸에 공란이 남지 않도록 주의.
* password는 앞에서 복사해놓은 키 사용. mycert.pem이 해당 경로에 생성되어 있는지 확인.
* userid 는 가상머신 default로 사용시 manager 또는 자신이 생성한 id사용.  
c.IPKernelApp.pylab = 'inline'
c.NotebookApp.certfile = u'/home/userid/.config/ipython/profile_nbserver/mycert.pem'
c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... ' 
c.NotebookApp.ip = '*'
c.NotebookApp.port = 9999
c.NotebookApp.open_browser = False

* ipython notebook 을 nbserver에 설정한 profile대로 실행. cmd 닫아도 background로 계속 실행. 
nohup ipython notebook --profile=nbserver 1 > /dev/null 2>&1

* process중지하려면 
ps
kill psid

* 이후 서버에서 직접 실행시 
https://localhost:9999
* 외부에서 접속시 (서버 관리자에게 9999포트 개방 요청 해야할 수도 있습니다.)
https://ip:9999

## 3. R studio server 설치
sudo apt-get install gdebi-core
sudo apt-get install libapparmor1
wget http://download2.rstudio.org/rstudio-server-0.98.994-amd64.deb
sudo gdebi rstudio-server-0.98.994-amd64.deb

* 이후 서버에서 직접 실행시 
https://localhost:8787
* 외부에서 접속시 (서버 관리자에게 8787포트 개방 요청 해야할 수도 있습니다.)
https://ip:8787