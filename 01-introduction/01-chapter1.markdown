## เริ่มต้นปฐมบทแห่ง Git ##
บทนี้เป็นบทแห่งการเริ่มต้นที่จะบอกเล่าเรื่องราวที่มาที่ไปของสิ่งที่เรียกว่า version contol tools จากนั้นเราก็จะถูกดึง มาเข้าเรื่อง Git ว่าเราจะไปทำยังไงให้ Git มาอยู่ในเครื่องเราได้ ทำไงให้มันทำงานได้และเมื่อจบบทนี้เราจะได้รู้ว่า ทำไมต้อง Git นั่นสิทำไม?

## เกี่ยวกับ Version Control ##
อะไรคือ version control แล้วทำไมต้องใช้ด้วย บางคนอาจบอกว่า “ก็จำได้ว่าทำอะไรไปมีไรป่ะ” แต่สุดท้ายผ่านไปสองเดือนก็ลืมหมด version control คือระบบที่บันทึกการเปลี่ยนแปลงทั้งหมดที่เกิดขึ้นกับไฟล์หรือชุดของไฟล์ที่เรากำลัง ทำงานกับมันอยูและเมื่อมีการบันทึกการเปลี่ยนแปลง ทุกครั้งที่เกิดปัญหาแก้ไฟล์มั่วจำไม่ได้ เราก็สามารถย้อนกลับ ไปหาเวอร์ชั่นใดๆก่อนหน้าได้เสมอ โดยที่ตัวอย่างที่จะใช้ในหนังสือเล่มนี้จะเป็นไฟล์ที่ทรัพย์สินอันสำคัญเช่น source  code จะถูกควบคุมโดย version control แต่อย่างไรก็ตามสำหรับการนำไปใช้งานจริงนั้นเราสามารถประยุกต์ใช้กับ ไฟล์ประเภทไหนก็ได้
เช่นถ้าคุณเป็น graphic designer  และอยากจะเก็บเวอร์ชั่นของภาพที่เราทำไปให้หมดทุกอัน สิ่งที่ช่วยเรื่องเหล่านี้ ได้คือ Version Control System (VCS) มันคือสิ่งที่คุณ “ต้อง” ใช้เพราะมันจะช่วยให้เราถอยไปถอยมาได้ไม่ว่าจะเป็น ระดับไฟล์หรือระดับโปรเจค ถ้ามันเละมากแก้มั่วไปหมดก็สามารถถอยกลับไปได้ วื๊บๆ ดังนั้นไอ้พวกข้ออ้างไฟล์หาย ไฟล์พังเพื่อขอเลื่อนโปรเจคหรือส่งงานข้าจะใช้ไม่ได้อีกต่อไป -/\-


### Local Version Control Systems ###

ย่อหน้าที่แล้วมีคำว่า VCS  เยอะมากเอ๊ะแล้วจริงๆเขาทำด้วยอะไรกัน? คำตอบเริ่มจากทำกันบ้านๆก็ได้ไม่ต้องคิดมาก copy file ที่ต้องการไว้บ่อยๆจะแก้ก็ copy ทิ้งไว้ก่อนแล้วตั้งชื่อ directory ให้สอดคล้องกัน ทำไปเรือยๆนี่ไง VCS กราบส์ OTL ทำแบบนี้ก็ไม่ผิดแต่มันเยอะส์บางทีลืมบ้างอะไรบ้างก็บ้าบอกันได้เลยทีเดียวเพราะไม่รู้อันไหนแน่ที่ต้อง เอากลับมาใช้และเพื่อแก้ไขปัญหาเรื่องนี้โปรแกรมเมอร์ (อีกแล้วไม่ใช่ tester) ได้สร้าง VCS ที่มี database แปะมาด้วยสำหรับการเก็บประวัติการเปลี่ยนแปลงทั้งหลายทั้งปวง ดังรูป(ดู Figure 1-1).

Insert 18333fig0101.png 
Figure 1-1. Local version control diagram.

หนึ่งใน VCS ในตำนานที่ยังคงอยู่ค้ำฟ้ามาจนถึงปัจจุบันคือ rcs ยกตัวอย่างเช่นใน Mac OS X เองก็ยังคงมีคำสั่ง rcs ให้เราใช้งานได้หลังจากที่เราติดตั้ง Developer Tools ลงในเครื่องโดยที่เจ้า rcs นี้จะทำหน้าที่เก็บความแตกต่างของไฟล์หรือที่เรียกว่า patch sets เพื่อใช้สำหรับการสร้างไฟล์ขึ้นมาใหม่ในกรณีที่เกิด file lock หลังจากที่เราลง pacth ใหม่ลงไปในเครื่องดังนั้น rcs ก็จะเป็นตัวอย่างที่ดีของการทำ local repository แต่ของแบบนี้ก็ดีถ้าเราทำอะไรคนเดียว

### Centralized Version Control Systems ###
ในกรณีที่เราต้องการทำงานร่วมกับคนอื่นๆแล้วเราจะแบ่งปันโค้ดเทพของกันและกันได้อย่างไรนี่ไอ้ของแบบ Local Repo คงไม่เหมาะเท่าไหร่ ดังนั้นของใหม่ที่เกิดถัดมาคือ Centralized Version Control System (CVCSs) และตัวอย่างของระบบแบบนี้คือ CVS, Subversion และ Perforce นั่นเองซึ่งหัวใจหลักของการทำงานแบบนี้คือจะต้องมี server หนึ่งตัวที่รับหน้าที่เก็บของให้ทั้งหมด ทั้งไฟล์ที่เกิดการเปลี่ยนแปลงและจำนวน user ที่ check out ไฟล์จาก server และอย่างที่เรารู้ว่า Centralize Repo เป็นมาตรฐานของ VCS มานานหลายปี (ดู Figure 1-2).


Insert 18333fig0102.png 
Figure 1-2. Centralized version control diagram.

สำหรับการ setup ระบบแบบ Centralize เองก็มีข้อดีหลายอย่างมากที่ดีกว่า local VCSs ยกตัวอย่างเช่นทุกๆคนในทีมจะรู้ว่าคนอื่นๆมชในทีมที่เหลือกำลังทำอะไรอยู่ ส่วนผู้ที่ดูแลระบบก็สามารถจัดการกับสิทธิ์การทำงานของ user ทุกคนได้ซึ่งนี้ก็เป็นข้อดีที่เหนือกว่าการมานั่งกำหนดอะไรอะไรที่ local database ทีละเครื่องมากมายนัก


แต่อย่างไรก็ตามการทำงานในลักษณะนี้เองมรข้อเสียที่น่าสนใจอยู่หนึ่งอย่างและดูเหมือนว่าจะร้ายแรงมากคือในกรณีที่ server พังไปสักชั่วโมงจะทำให้คนที่ทำงานอยู่ไม่สามารถส่งอะไรเข้ามา update ได้เลยและถ้าเอาให้หนักหน่อยในกรณีที่ไม่มีการสำรองข้อมูลไว้เราจะพบกับฝันร้ายขั้นร้ายแรงคือ เราจะเสีย history ทั้งหมดของระบบไปหมดเลย (แรงดีเนอะ) ดังนั้นนี่คือด้วนมืดของ Centrlaized Repo

### Distributed Version Control Systems ###

หลังจากที่เราเห็นด้านมืดของ Centralize Rep ไปแล้วเราก็จะได้รู้จักกับสิ่งที่เรียกว่า Distributed Version Control system (DVCSs) ตัวอย่างของระบบแบบนี้คือ (Git, Mercurial, Bazaar หรือ Darcs) โดยที่ความแปลกของระบบแบบนี้คือแทนที่เครื่อง client จะทำการ แheck out เอา snapshot ล่าสุดไปไว้บนเครื่องมันจะทำสิ่งที่ยิ่งใหญ่กว่านั้นคือมันทำ full mirror ข้อมูลทั้งหมดของดปรเจคที่กำลังทำงานลงมาไว้ที่เครื่องเลยดังนั้นการทำงานแบบนี้จะแก้ปัญหาเรื่อง Server พังแล้วไม่สามารถย้อนกลับไปเอา history กลับคืนมาได้เพราะข้อมูลที่อยู่บนเครื่อง client ก็สามารถถูกส่งกลับขึ้นไปเพื่อ restore ระบบได้ทั้งหมดเหมือนกันดังนั้นการ check out ของ DVCS ก็คือการทำ Full Backup นั่นเองดังรูป (see Figure 1-3)

Insert 18333fig0103.png 
Figure 1-3. Distributed version control diagram.

นอกจากนี้แล้วระบบแบบนี้ยังถูกออกแบบให้มีความสามารถในการทำงานกับ remote repository ได้มากกว่าหนึ่งที่ด้วยนั่นหมายความว่าเราสามารถทำงานแจมกับพรรคพวกได้มากกว่าหนึงที่ในโปรเจคเดียวกัน ดังนั้นเราจะสามารถ setup workflow ได้หลายประเภทเพื่อให้รองรับการทำงานของเราและสิ่งนี้ทำไม่ได้ใน Centralize Repository นะจ๊ะย้ำ

## ตำนานของ Git ##

เฉกเช่นสิ่งมหัศจรรย์ทั้งหลายแหล่ในโลก, Git เริ่มต้นมาจากดราม่าส์เล็กๆ ณ ชุมชนหนึ่งในดาวนี้ Linux kernel เป็น open source โปรเจคอันนึงที่มีขนาดใหญ่พอสมควร เกือบทั้งชีวิตของการ maintenance kernel ของ Linux นี้ (ช่วงปี 1991–2002) การเปลี่ยนแปลงต่างๆถูกส่งไปส่งมาในรูปแบบ patch และ zip files จนกระทั่งปี 2002 Linux kernel project ก็เริ่มใช้ DVCS ที่เรียกว่า BitKeeper

ในปี 2005 ความสัมพันธ์ระหว่าง community ที่พัฒนา Linux kernel กับบริษัท commercial ที่พัฒนา BitKeeper ก็สะบั้นลง ไอ้ tool ที่เคยใช้ได้ฟรีมาตลอดก็จุ๊งไป ทำให้ community ที่พัฒนา Linux (โดยเฉพาะท่านเทพ Linus Torvalds ที่เป็นบิดาแห่ง Linux) ต้องพัฒนา tool ขึ้นมาใช้เอง โดยอิงจากประสบการณ์ที่ได้เรียนรู้ระหว่างใช้งาน BitKeeper ระบบใหม่มีเป้าหมายบางประการ ดังนี้:

*	ความเร็วส์
*	design ที่ simple
*	ต้องพัฒนาไปพร้อมๆกันที่ละหลายๆคนได้ (ประมาณ 1000 branches)
*	distributed สุดๆ
*	ใช้กับโปรเจคใหญ่ๆอย่าง Linux kernel ได้ดี (ทั้งเรื่องความเร็วส์และขนาดของข้อมูล)

ตั้งแต่ถูกคลอดมาในปี 2005, Git ก็พัฒนาและเติบโตจนเป็นของที่ใช้ง่าย แต่ก็ยังคงไว้ซึ่งข้อดีข้างต้นนี้ นั่นเร็วส์สุดยิด, เหมาะกับโปรเจคขนาดยักษ์ และพัฒนาไปพร้อมๆกันทีละหลายๆ branch อย่างน่าอัศจรรย์ (ดูได้ใน Chapter 3).

## เบสิคของ Git ##

เอาล่ะ มาดูกันว่า Git in a nutshell เป็นยังไง? ส่วนนี้เป็นส่วนสำคัญที่ท่านจะต้องดูดไป เพราะเมื่อไหร่ที่ท่านเข้าใจแก่นแท้ของ Git และเข้าใจว่ามันทำงานยังไง เมื่อนั้น การใช้ Git อย่างมีประสิทธิภาพสูงสุดก็ไม่ยากละ ขณะที่เรียนรู้ Git พยายามลืม VCSs ตัวที่ผ่านๆมาให้หมด (เช่น Subversion และพวกพ้อง) เพราะมันจะป้องกันความงงที่จะเกิดขึ้นระหว่างใช้ Git ได้ Git มองและจำข้อมูลต่างกับระบบอื่นๆค่อนข้างเยอะ แม้ว่า UI มันจะดูคล้ายๆตัวอื่นก็ตาม ทำความเข้าใจความแตกต่าง แล้วเวลาใช้จะไม่งง

### Snapshots, ไม่ใช่ความเปลี่ยนแปลง ###

หลักๆเลย Git ต่างกับ VCS อื่นๆ (Subversion และเพื่อนๆ) ตรงที่วิธีที่ Git มองข้อมูลที่มันเก็บ โดยคอนเซปแล้ว ระบบอื่นๆจะเก็บข้อมูลในรูปของ listของความเปลี่ยนแปลง ระบบเหล่านี้ (CVS, Subversion, Perforce, Bazaar ฯลฯ) คิดว่าข้อมูลที่มันเก็บคือ set ของ files และความเปลี่ยนแปลงที่เกิดขึ้นกับแต่ละ file ในเวลาที่ดำเนินไป ดังเช่นตัวอย่างในรูป Figure 1-4.

Insert 18333fig0104.png 
Figure 1-4. Other systems tend to store data as changes to a base version of each file.

Git ไม่ได้มองหรือจำข้อมูลที่มันเก็บอย่างนั้น ในทางกลับกัน Git มองข้อมูลมันเหมือนกับเป็น set ของ snapshots ของ filesystem ขนาดจิ๋ว ทุกๆครั้งที่คุณ commit หรือ save project ใน Git มันจะถ่ายรูปว่า files ของเราหน้าตาเป็นไง ณ บัดนั้น และเก็บ reference ไปยัง snapshot (รูปถ่าย) นั้น และเพื่อให้มีประสิทธิภาพ ถ้า file ไม่ถูกแก้ไข Git จะไม่จำ file นั้นๆซ้ำ แค่เก็บ link ไปยัง file เก่าที่เหมือนกันเป๊ะๆ ที่มันเคยจำไว้แล้วเฉยๆ Git มองข้อมูลท่มันเก็บเหมือนดังภาพ Figure 1-5. 

Insert 18333fig0105.png 
Figure 1-5. Git stores data as snapshots of the project over time.

นี่ความความแตกต่างที่สำคัญระหว่าง Git และ VCSs อื่นๆเกือบทั่วโลก มันทำให้ Git ต้องคิดใหม่ ทำใหม่เกือบทุกๆอย่าง ขณะที่ระบบอื่นๆแค่ copy มาจากรุ่นก่อนๆ มันทำให้ Git เหมือนเป็น filesystem ขนาดจิ๋วที่มากับ tools อันทรงพลังที่สร้างขึ้นมาครอบมันมากกว่าที่จะเป็นแค่ VCS ธรรมดา เด๋วเราค่อยมาโชว์ของดีที่ได้จากการมองข้อมูลในลักษณะนี้ในหัวข้อ branching ใน Chapter 3

### Nearly Every Operation Is Local ###

Most operations in Git only need local files and resources to operate — generally no information is needed from another computer on your network.  If you’re used to a CVCS where most operations have that network latency overhead, this aspect of Git will make you think that the gods of speed have blessed Git with unworldly powers. Because you have the entire history of the project right there on your local disk, most operations seem almost instantaneous.

For example, to browse the history of the project, Git doesn’t need to go out to the server to get the history and display it for you—it simply reads it directly from your local database. This means you see the project history almost instantly. If you want to see the changes introduced between the current version of a file and the file a month ago, Git can look up the file a month ago and do a local difference calculation, instead of having to either ask a remote server to do it or pull an older version of the file from the remote server to do it locally.

This also means that there is very little you can’t do if you’re offline or off VPN. If you get on an airplane or a train and want to do a little work, you can commit happily until you get to a network connection to upload. If you go home and can’t get your VPN client working properly, you can still work. In many other systems, doing so is either impossible or painful. In Perforce, for example, you can’t do much when you aren’t connected to the server; and in Subversion and CVS, you can edit files, but you can’t commit changes to your database (because your database is offline). This may not seem like a huge deal, but you may be surprised what a big difference it can make.

### Git Has Integrity ###

Everything in Git is check-summed before it is stored and is then referred to by that checksum. This means it’s impossible to change the contents of any file or directory without Git knowing about it. This functionality is built into Git at the lowest levels and is integral to its philosophy. You can’t lose information in transit or get file corruption without Git being able to detect it.

The mechanism that Git uses for this checksumming is called a SHA-1 hash. This is a 40-character string composed of hexadecimal characters (0–9 and a–f) and calculated based on the contents of a file or directory structure in Git. A SHA-1 hash looks something like this:

	24b9da6552252987aa493b52f8696cd6d3b00373

You will see these hash values all over the place in Git because it uses them so much. In fact, Git stores everything not by file name but in the Git database addressable by the hash value of its contents.

### Git Generally Only Adds Data ###

When you do actions in Git, nearly all of them only add data to the Git database. It is very difficult to get the system to do anything that is not undoable or to make it erase data in any way. As in any VCS, you can lose or mess up changes you haven’t committed yet; but after you commit a snapshot into Git, it is very difficult to lose, especially if you regularly push your database to another repository.

This makes using Git a joy because we know we can experiment without the danger of severely screwing things up. For a more in-depth look at how Git stores its data and how you can recover data that seems lost, see “Under the Covers” in Chapter 9.

### The Three States ###

Now, pay attention. This is the main thing to remember about Git if you want the rest of your learning process to go smoothly. Git has three main states that your files can reside in: committed, modified, and staged. Committed means that the data is safely stored in your local database. Modified means that you have changed the file but have not committed it to your database yet. Staged means that you have marked a modified file in its current version to go into your next commit snapshot.

This leads us to the three main sections of a Git project: the Git directory, the working directory, and the staging area.

Insert 18333fig0106.png 
Figure 1-6. Working directory, staging area, and git directory.

The Git directory is where Git stores the metadata and object database for your project. This is the most important part of Git, and it is what is copied when you clone a repository from another computer.

The working directory is a single checkout of one version of the project. These files are pulled out of the compressed database in the Git directory and placed on disk for you to use or modify.

The staging area is a simple file, generally contained in your Git directory, that stores information about what will go into your next commit. It’s sometimes referred to as the index, but it’s becoming standard to refer to it as the staging area.

The basic Git workflow goes something like this:

1.	You modify files in your working directory.
2.	You stage the files, adding snapshots of them to your staging area.
3.	You do a commit, which takes the files as they are in the staging area and stores that snapshot permanently to your Git directory.

If a particular version of a file is in the git directory, it’s considered committed. If it’s modified but has been added to the staging area, it is staged. And if it was changed since it was checked out but has not been staged, it is modified. In Chapter 2, you’ll learn more about these states and how you can either take advantage of them or skip the staged part entirely.

## Installing Git ##

Let’s get into using some Git. First things first—you have to install it. You can get it a number of ways; the two major ones are to install it from source or to install an existing package for your platform.

### Installing from Source ###

If you can, it’s generally useful to install Git from source, because you’ll get the most recent version. Each version of Git tends to include useful UI enhancements, so getting the latest version is often the best route if you feel comfortable compiling software from source. It is also the case that many Linux distributions contain very old packages; so unless you’re on a very up-to-date distro or are using backports, installing from source may be the best bet.

To install Git, you need to have the following libraries that Git depends on: curl, zlib, openssl, expat, and libiconv. For example, if you’re on a system that has yum (such as Fedora) or apt-get (such as a Debian based system), you can use one of these commands to install all of the dependencies:

	$ yum install curl-devel expat-devel gettext-devel \
	  openssl-devel zlib-devel

	$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
	  libz-dev libssl-dev
	
When you have all the necessary dependencies, you can go ahead and grab the latest snapshot from the Git web site:

	http://git-scm.com/download
	
Then, compile and install:

	$ tar -zxf git-1.7.2.2.tar.gz
	$ cd git-1.7.2.2
	$ make prefix=/usr/local all
	$ sudo make prefix=/usr/local install

After this is done, you can also get Git via Git itself for updates:

	$ git clone git://git.kernel.org/pub/scm/git/git.git
	
### Installing on Linux ###

If you want to install Git on Linux via a binary installer, you can generally do so through the basic package-management tool that comes with your distribution. If you’re on Fedora, you can use yum:

	$ yum install git-core

Or if you’re on a Debian-based distribution like Ubuntu, try apt-get:

	$ apt-get install git-core

### Installing on Mac ###

There are two easy ways to install Git on a Mac. The easiest is to use the graphical Git installer, which you can download from the Google Code page (see Figure 1-7):

	http://code.google.com/p/git-osx-installer

Insert 18333fig0107.png 
Figure 1-7. Git OS X installer.

The other major way is to install Git via MacPorts (`http://www.macports.org`). If you have MacPorts installed, install Git via

	$ sudo port install git-core +svn +doc +bash_completion +gitweb

You don’t have to add all the extras, but you’ll probably want to include +svn in case you ever have to use Git with Subversion repositories (see Chapter 8).

### Installing on Windows ###

Installing Git on Windows is very easy. The msysGit project has one of the easier installation procedures. Simply download the installer exe file from the Google Code page, and run it:

	http://code.google.com/p/msysgit

After it’s installed, you have both a command-line version (including an SSH client that will come in handy later) and the standard GUI.

## First-Time Git Setup ##

Now that you have Git on your system, you’ll want to do a few things to customize your Git environment. You should have to do these things only once; they’ll stick around between upgrades. You can also change them at any time by running through the commands again.

Git comes with a tool called git config that lets you get and set configuration variables that control all aspects of how Git looks and operates. These variables can be stored in three different places:

*	`/etc/gitconfig` file: Contains values for every user on the system and all their repositories. If you pass the option` --system` to `git config`, it reads and writes from this file specifically. 
*	`~/.gitconfig` file: Specific to your user. You can make Git read and write to this file specifically by passing the `--global` option. 
*	config file in the git directory (that is, `.git/config`) of whatever repository you’re currently using: Specific to that single repository. Each level overrides values in the previous level, so values in `.git/config` trump those in `/etc/gitconfig`.

On Windows systems, Git looks for the `.gitconfig` file in the `$HOME` directory (`C:\Documents and Settings\$USER` for most people). It also still looks for /etc/gitconfig, although it’s relative to the MSys root, which is wherever you decide to install Git on your Windows system when you run the installer.

### Your Identity ###

The first thing you should do when you install Git is to set your user name and e-mail address. This is important because every Git commit uses this information, and it’s immutably baked into the commits you pass around:

	$ git config --global user.name "John Doe"
	$ git config --global user.email johndoe@example.com

Again, you need to do this only once if you pass the `--global` option, because then Git will always use that information for anything you do on that system. If you want to override this with a different name or e-mail address for specific projects, you can run the command without the `--global` option when you’re in that project.

### Your Editor ###

Now that your identity is set up, you can configure the default text editor that will be used when Git needs you to type in a message. By default, Git uses your system’s default editor, which is generally Vi or Vim. If you want to use a different text editor, such as Emacs, you can do the following:

	$ git config --global core.editor emacs
	
### Your Diff Tool ###

Another useful option you may want to configure is the default diff tool to use to resolve merge conflicts. Say you want to use vimdiff:

	$ git config --global merge.tool vimdiff

Git accepts kdiff3, tkdiff, meld, xxdiff, emerge, vimdiff, gvimdiff, ecmerge, and opendiff as valid merge tools. You can also set up a custom tool; see Chapter 7 for more information about doing that.

### Checking Your Settings ###

If you want to check your settings, you can use the `git config --list` command to list all the settings Git can find at that point:

	$ git config --list
	user.name=Scott Chacon
	user.email=schacon@gmail.com
	color.status=auto
	color.branch=auto
	color.interactive=auto
	color.diff=auto
	...

You may see keys more than once, because Git reads the same key from different files (`/etc/gitconfig` and `~/.gitconfig`, for example). In this case, Git uses the last value for each unique key it sees.

You can also check what Git thinks a specific key’s value is by typing `git config {key}`:

	$ git config user.name
	Scott Chacon

## Getting Help ##

If you ever need help while using Git, there are three ways to get the manual page (manpage) help for any of the Git commands:

	$ git help <verb>
	$ git <verb> --help
	$ man git-<verb>

For example, you can get the manpage help for the config command by running

	$ git help config

These commands are nice because you can access them anywhere, even offline.
If the manpages and this book aren’t enough and you need in-person help, you can try the `#git` or `#github` channel on the Freenode IRC server (irc.freenode.net). These channels are regularly filled with hundreds of people who are all very knowledgeable about Git and are often willing to help.

## Summary ##

You should have a basic understanding of what Git is and how it’s different from the CVCS you may have been using. You should also now have a working version of Git on your system that’s set up with your personal identity. It’s now time to learn some Git basics.
