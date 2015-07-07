<!--Meta
category:OpenQA
title:OpenQA测试使用流程
DO NOT Delete Meta Above -->
#Openqa测试使用流程

<h2 id="one">一、Openqa 环境安装</h2>
安装openqa的deb包：    
1.下载openqa.deb安装包，deb包存放路径：够快云库-产品部－测试组－自动化测试－Openqa。安装方式：用dpkg -i 安装时可能会出现依赖安装不上的问题，用apt-get install -f 修复即可，在文件管理器双击安装不会出现这个问题，建议双击安装。   

 2.初始化第一次运行环境:  
在终端运行openqa-init（不要加sudo）

<h2 id="two">二、运行openqa</h2>
１.启动webui:
在终端输入：sudo openqa-webui （需要加sudo）

２.启动worker:
在终端打开另一个窗口输入：sudo openqa-worker（需要加sudo） 

３.下载iso:
下载要测试的iso到~/OpenQA/iso中
    
４.同步测试代码和needles:     
1)	在和github通信之前需要先在本地生成SSH key并将SSH公钥添加到cr.deepin.io，方法如下：   
a.	在家目录执行ssh-keygen -t rsa -C "your_email@youremail.com"（邮件地址换成个人的真实邮件地址）;    
b.	将～/.ssh目录下的/id_rsa.pub内容全部拷贝添加到cr.deepin.io个人账户的SSH public keys中;    
2)	在终端输入：cd ~/OpenQA/deepin      
3)	将下面的yujingmei(两处)改成你的gerrit用户名（使用bash或者zsh）：   
git clone ssh://yujingmei@cr.deepin.io:29418/deepin-testcases . && scp -p -P 29418    yujingmei@cr.deepin.io:hooks/commit-msg .git/hooks/    

５．启动openqa-test开始测试:    
openqa-test  + 要设置的环境变量参数params      
说明：多个环境变量之间使用逗号隔开，环境变量参数params 格式：    
ISO=iso名字,INSTLANG=zh_CN,NEEDLEMAKER=1,......

６．从浏览器输入http://localhost/对web界面的测试过程和测试结果进行访问:  
 
![alt text](/home/mm/桌面/1.png "photo1")  
第一次访问此页面时需点击上图右上角的[login]进行登录,方便日后的其他操作,继续点击该页面的[TEST RESULT OVERVIEW]查看测试结果:

![alt text](/home/mm/桌面/2.png "photo2")    

点击[TEST RESULT OVERVIEW]后会出现正在执行的任务:   
 
![alt text](/home/mm/桌面/3.png "photo3")       

点击上图中间黄色的具体任务名称后,会出现下图,在下图中可看到当前虚拟机的运行窗口,以及运行到了哪一个用例.      

![alt text](/home/mm/桌面/4.png "photo4")       


<h2 id="three">三、测试用例结构说明</h2>
测试用例代码存放在：~/OpenQA/deepin (/var/lib/openqa/tests/deepin) 目录下，目录结构为：    

![alt text](/home/mm/桌面/5.png "photo5")    

对以上文件结构的说明：     
1.main.pm文件：测试程序的入口     

2.needles目录：needles目录由多个needle组成，一个needle由一个json文件和一个png图片组成（必须同名），png图片是一个预期图，json文件包含了对这个预期图的区域描述，还有对自身描述的tag。注意：needle文件名 != needle的名字。      
eg:  inst-user-setting-up_2014-2.json        
{      
  "tags": [      
    "ENV-INSTLANG-en",  (筛选条件：想要匹配这个needle，环境变量中INSTLANG 的值必须是 en)    
    "ENV-ISOVER-2014.2",      
    "inst-user-setting-default"　(inst-user-setting-default 是needle的真实名字)       
  ],       
  "area": [       
    {     
      "type": "match",      
      "height": 768,      
      "width": 1024,     
      "xpos": 0,      
      "ypos": 0         
    }           
  ]}         
tests目录：测试程序存放目录，使用perl编写，API参考：https://github.com/os-autoinst/openQA/blob/master/docs/WritingTests.asciidoc#api         

<h2 id="four">四、Needle的制作</h2>
编写自动化测试用例的第一步是制作needle，制作needle的方法如下：        
１．在终端运行openqa-test启动测试:        
    需要多加一个参数NEEDLEMAKER=1 ，例如：openqa-test ISO=deepin-desktop-amd64.iso,INSTLANG=zh_CN,NEEDLEMAKER=1        

２．【save screen】对话框:          
    openqa-test测试完成后不会立即结束，通常会在当前电脑屏幕左上角出现一个小小的【save screen】的对话框，一定不要关闭此对话框（这一点很重要）:        
    ![alt text](/home/mm/桌面/6.png "photo6")      
    
３．远程连接至虚拟机进行相关操作:        
在终端运行vncviewer　localhost:5991连接至测试中的虚拟机进行需要制作needle的测试操作(上图出现的TightVNC:QEMU窗口表示已远程连接至虚拟机,同时在该窗口进行了打开控制中心的操作)，对每个需要制作needle的操作点击一次步骤2中【save screen】按钮,当所有需要截图的操作都执行完【save screen】后，关闭【save screen】这个对话框。       
说明：        
①、vnc端口一般是5991，可用sudo netstat -plent查看 qemu-system-x 进程端口，例如某一次的查看结果如下，其中可以看出该进程占用的端口是5991：       
$ sudo netstat -plent        
tcp  0  0   0.0.0.0:5991  0.0.0.0:*   LISTEN   11953/qemu-system-x      
②、vnc连接命令：         
vncviewer localhost:5991         

４．访问web页面制作needle文件:        
    在web界面制作needle文件又分了以下步骤:           
1)打开具体任务的maker这个步骤(方法是进入该任务后点击该任务左侧的maker):         
    ![alt text](/home/mm/桌面/7.png "photo7")         
    
2)接着点击maker的needles editor:        
    ![alt text](/home/mm/桌面/8.png "photo8")
    
5.在needles editor中编辑needle区域和needle的名称：     
 ![alt text](/home/mm/桌面/9.png "photo9")     
 
1)在图片中用鼠标拖拽的方式选择需要制作needle的区域,如上图中所示的控制中心用户区域,此时需要注意查看json文件中的type值需要为match;      
2)在红色区域的Tags输入框中输入tag的名称,建议此名称采用以下格式来写:模块名-功能-功能细节,例如: dcc-main-user(dcc是dde-control-center的缩写),输入完后点击右侧的[add];     
3)在红色区域的Name输入框中输入needle的名称,needle名称可以和tag名称不一样,但是强烈建议保持一致,例如也输入dcc-main-user;       
4)在Action中点击红色区域的保存按钮,至此一个needle制作完成了,会在后台needles文件夹中生成一对json和png文件:        
dcc-main-user.json            
dcc-main-user.png        


<h2 id="five">五、编写perl脚本与环境参数</h2>

编写自动化测试用例的第二步是编写perl脚本,在perl脚本中需要使用到之前制作的needle,具体步骤如下:      
1.进入tests目录,新建需要测试的模块名称文件夹,如mkdir Controlcenter;        
2.进入步骤1中新建的Controlcenter目录,新建pm文件,如新建DccUser.pm, DccUser.pm具体内容如下,其中蓝色部分为needle的真实名称(也就是对应的json文件中的tag名称):       

    use base "basetest";    
    use strict;     
    use testapi;       

    sub adduser {       
        # close networktip         
        sleep 3;          
        if (check_screen "dcc-networktips-check"){          
            mouse_set 977, 21;          
            mouse_click;             
    }        

        # show controlcenter        
        mouse_set 1023, 767;        
    
        # click userbtn on the mainpage of controlcenter       
        assert_screen "dcc-main-area", 10;
        mouse_set 50, 50;
        assert_and_click "dcc-main-user";
        sleep 2;

        # click useradd btn
        assert_screen "dcc-useradd-btn", 10;
        assert_and_click "dcc-useradd-btn";
        sleep 2;
   
        # input userinfo to dcctext
        my $username = get_var("ADDUNAME");
        my $passwd = get_var("ADDUPWD");
        mouse_set 880, 198;
        mouse_click;
        type_string $username;
        sleep 1;
        send_key "tab";
        type_string $passwd;
        sleep 1;
        send_key "tab";
        type_string $passwd;
        assert_and_click "dcc-useradd-ok";

        # authentication of admin
        sleep 5;
        mouse_set 405, 379;
        mouse_click;
        type_string "deepin";
        sleep 1;
        send_key "tab";
        send_key "tab";
        send_key "tab";
        send_key "ret";
    }

    sub deluser {
        # wait for useradded page    
        mouse_set 1023, 767;      
        assert_screen "dcc-useradded-area", 10;      
        mouse_set 50, 50;  
          
   
        # click the delbtn 
        assert_and_click "dcc-userdel-btn";
        sleep 2;

        # click the delbtn2
        assert_and_click "dcc-userdel-btn2";
        sleep 2;

        # click deluser ok

        assert_and_click "dcc-userdel-ok";
    }

    sub run {
        adduser;
        sleep 8;
        deluser;
    
        # exit dcc-user 
        sleep 3;
        assert_and_click "dcc-backtohome-btn";
        mouse_set 50, 50;
        mouse_click; 
    }

    sub test_flags {   
        #  without anything - rollback to 'lastgood' snapshot if failed
        # 'fatal' - whole test suite is in danger if this fails
        # 'milestone' - after this test succeeds, update 'lastgood'
        # 'important' - if this fails, set the overall state to 'fail'
        return { important => 1 };     
    }    
1;        




3.send_key 接口特殊键对应名称如下：        
alt     
backspace      
ctrl       
delete      
down      
end       
equal        
esc       
home        
insert      
left       
meta         
minus         
pgdn        
pgup        
ret          
right          
shift         
spc         
sysrq        
tab          
up         
win           
发送组合键 ctrl+alt+t 例子：send_key "ctrl-alt-t";  （记得是用连接符 “-”不是 “+”）      
   
4.具体功能测试的pm文件完成后,需要编辑main.pm文件,加载各个功能的pm文件,例如main.pm内容如下(真实测试过程中main.pm文件是需要一直更新且内容是越来越多的),其中蓝色区域表示加载DccUser.pm文件进行测试:

    use strict;
    use testapi;
    use autotest;

    use Gtk2;
    use threads;
    use threads::shared;

    sub loadtest($) {
        my ($test) = @_;
        autotest::loadtest(get_var("CASEDIR") . "/tests/$test");
    }


    sub loadInstTests(){
        #loadBootTests();

        # choose language
        loadtest "Installation/LanguageMenu.pm";

        # user setting
        loadtest "Installation/UserSetting.pm";

        # partition setting
        loadtest "Installation/Partitioning.pm";

        # installing
        loadtest "Installation/StartInstallation.pm";

        # waiting for the installation to the end, and restart
        loadtest "Installation/Finish.pm";
    }

    sub loadLoginTests{

        # login
        loadtest "Login/Login.pm";

    }

    sub loadDesktopTests(){

        # guid
        loadtest "Desktop/WelcomeGuid.pm";

    }

    sub loadDockTests{
        loadtest "Dock/DockSwitch.pm";
    }

    sub loadLauncherTests{
        loadtest "Launcher/StartUp.pm";
    }

    sub loadDeepinMusicPlayerTests{

        loadtest "DeepinMusicPlayer/DMusicStartUp.pm";

        loadtest "DeepinMusicPlayer/DMusicMain.pm";

        loadtest "DeepinMusicPlayer/DMusicExit.pm";
    }


    sub loadDccUserTests{
        loadtest "Controlcenter/DccUser.pm";
    }


    sub loadDmovieTests{
        loadtest "DeepinMovie/StartCloseDmovie.pm";

    }

    sub loadDccDisplayTests{
        loadtest "Controlcenter/DccDisplay.pm";

    }


    sub loadNeedleMaker{
        loadtest "NeedleMaker/Maker.pm"

    }


    # install
    loadInstTests;

    # login
    loadLoginTests;

    # close system notifications
    #closeSysNotifications;

    # entry desktop
    loadDesktopTests;

    if (get_var("DOCK")){
        loadDockTests;
    }

    if (get_var("LAUNCHER")){
        loadLauncherTests;
    }

    if (get_var("DEEPINMUSICPLAYER")){
        loadDeepinMusicPlayerTests;
    }

    if (get_var("DCCDISPLAY")){
        loadDccDisplayTests;
    }

    if (get_var("DCCUSER")){
        loadDccUserTests;
    }

    if (get_var("DMOVIE")){

        loadDmovieTests;
    }

    if (get_var("NEEDLEMAKER")){
        loadNeedleMaker;
    }

    sleep 15;
    1;
    
5.填写环境参数:      
在写新测试用例时，如果有新增环境参数，或有依赖环境参数时（如在启用DCCUSER时，还会依赖ADDUNAME参数等），记得在variable 文件和 VarManual 文件夹中添加说明。以便于OpenQA产品测试的设置。    
1) Variable文件:       
罗列出环境参数的名称,以单词首字母的先后顺序排列,例如现有的variable文件内容如下(此文件持续更新中):       
ADDUNAME     
ADDUPWD      
DCCUSER    
DCCDISPLAY     
DEEPINMUSICEPLAYER     
DMOVIE     
DOCK      
INSTLANG      
LAUNCHER     
NEEDLEMAKER      
USERNAME      
USERPWD     
2) VarManual 文件夹:     
每个Variable文件中的环境参数都有一个文件,文件名称以环境参数命名,例如ADDUNAME文件内容如下:      
说明：       
控制中心添加用户时设置的用户名称        
默认：无      
使用方法：      
直接设置：ADDUNAME=yourusername 即可     

<h2 id="six">六、在本地先进行测试与调试</h2>
在本地验证新编写的自动化测试代码能否顺利进行:         
在终端执行openqa-test时加上各个功能的参数,例如控制中心用户测试的DCCUSER(注意一定要和pm文件中一样保持大小写):        
openqa-test  DCCUSER=1 ,其他参数....        
其他参数说明:       
中文语言下必须带上INSTLANG=zh_CN,否则安装器默认安装的是英文        


<h2 id="seven">七、本地调试通过后才能提交到cr.deepin.io</h2>
cr.deepin.io上现有openqa的测试用例存放的项目名称是deepin-testcases,平时在提交时需要先在本地测试通过后才能提交, 提交到cr.deepin.io的方法如下：      
git  status     
git  add  -A        
git  commit  -m  "此处需要自行编写提说明"     
git  review  master  -t  "所有执行openqa-test时需要附带的参数"        

