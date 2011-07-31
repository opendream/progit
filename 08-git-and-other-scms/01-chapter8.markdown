# Git และระบบอื่นๆ #

ทุกอย่างมันยังไม่สมบูรณ์ไปซะหมดหรอก เพราะการที่เราต้องการเปลี่ยนทุกๆ โปรเจคที่มีใช้งานอยู่ส่วนร่วมอยู่ให้เป็น Git ได้ในทันทีคงทำไม่ได้ เพราะบางครั้งเรายังยึดติดกับโปรเจคที่ใช้ VCS (Versions Control System) อื่นๆ ซึ่งหลายครั้งมันคือ Subversion คุณเองจะสามารถใช้ส่วนแรกของบทนี้สำหรับเรียนรู้เกี่ยวกับ `git svn` เป็นเครื่องมื่อที่ช่วยให้เราสามารถใช้งานร่วมกับ Subversion ได้

บางครั้ง หากต้องการที่จะแปลงโปรเจคของคุณที่มีอยู่แล้วให้มาใช้งานร่วมกับ Git ซึ่งส่วนที่ 2 ของบทนี้จะกพูดถึงการย้ายโปรเจคที่มีอยู่เดิมไปยัง Git ซึ่งก็เลี่ยงไม่ได้ที่จะต้องเริ่มต้นด้วย Subversion และสุดท้ายคือการย้ายโปรเจคด้วยสคริปต์สำหรับนำเข้าโปรเจคที่สร้างขึ้นเอง สำหรับกรณีการนำเข้าโปรเจคที่ไม่เป็นไปตามมาตรฐาน

## Git และ Subversion ##

ขณะนี้ การพัฒนาโปรเจคโอเพ่นซอร์ซส่วนใหญ่รวมไปถึงโปรเจคภายในบริษัทจำนวนมากต่างก็ใช้ Subversion เพื่อช่วยจัดการซอร์ซโค้ด โดยที่ Subversion เป็น VCS แบบโอเพ่นซอร์ซและใช้มากันมายาวนานเกือบ 10 ปีแล้ว Subversion นั้นคล้ายกับ CVS ในหลายด้าน โดยที่ CVS เองเคยเป็นพี่ใหญ่ในวงการ source-control มาก่อน Subversion

คุณลักษณะเด่นอย่างหนึ่งของ Git ก็คือ Bidirectional Bridge ไปยัง Subversion เรียกว่า git svn ซึ่งเครื่องมือนี้จะทำให้เราใช้ Git แบบไคลเอนต์ (Client) ร่วมกับเซิร์ฟเวอร์ที่เป็น Subversion ได้ ในขณะเดียวกันก็ยังใช้คุณสมบัติต่างๆ แบบโลคอล ของ Git ได้ทั้งหมด และส่งโค้ดกลับไปยังเซิร์ฟเวอร์ Subversion ได้เช่นเดียวกันกับการใช้งานไคลเอนต์ที่เป็น Subversion นั่นย่อมหมายความว่าเราสามารถสร้าง branch และ merge ภายในเครื่องของเราเองได้ ใช้งาน Staging area, rebase และ cherry-picking รวมไปถึงความสามารถอื่นๆ ในขณะที่เพื่อนๆ ร่วมโปรเจคคุณยังคงก้มหน้าก้มตาใช้งานของเก่าคร่ำคร่ากันต่อไป ซึ่งวิธีนี้เป็นวิธีการที่ดีสำหรับ(ลอง/แอบ)ใช้ Git ในสภาพแวดล้อมของหน่วยงาน และยังช่วยให้นักพัฒนาในกลุ่มทำงานได้อย่างมีประสิทธิภาพมากยิ่งขึ้น ระหว่างที่เราโน้มน้าวเพื่อที่จะได้ทรัพยากรที่พร้อมสำหรับการเปลี่ยนไปยัง Git อย่างสมบูรณ์แบบ 

### git svn ###

คำสั่งพื้นฐานใน Git สำหรับคำสั่งใช้เชื่อมการทำงานกับ Subversion ทั้งหมดจะเริ่มต้นด้วย `git svn` แล้วต่อท้ายด้วยคำสั่งอีกไม่กี่คำสั่ง ซึ่งจะได้เรียนคำสั่งที่ใช้งานโดยทั่วไปผ่านขั้นตอนการทำงานเล็กน้อย

สิ่งสำคัญอย่างหนึ่งที่ต้องจำไว้ว่าเรากำลังใช้ `git svn` เพื่อทำงานร่วมกับ Subversion ซึ่งเป็นระบบที่เก่าคร่ำคร่ากว่า Git อย่างไรก็ตามเรายังสร้าง branch และ merge ภายในเครื่องได้ ซึ่งโดยทั่วไปแล้วเป็นเรื่องดีที่จะเก็บประวัติการทำงานไว้ภายในเครื่องไปเรื่อยๆ ด้วยการ rebase ไปเรื่อยๆ และยังเป็นการเลี่ยงการเกิดผลกระทบต่อ Git repository ที่ใช้งานอยู่ด้วย

ไม่ต้องเขียนประวัติการใช้งานใหม่และโยนกลับเข้าไปอีกครั้ง **and don’t push to a parallel Git repository to collaborate with fellow Git developers at the same time** Subversion สร้างประวัติการทำงานต่อเนื่องกันไปเรื่อยๆ นั่นก็ทำให้สับสนได้ง่าย ถ้าทำงานร่วมกันเป็นทีม มีบางส่วนใช้ SVN รวมไปถึง Git ก็มั่นใจได้เลยว่าทุกคนสามารถใช้งานเซิร์ฟเวอร์ Subversion ร่วมกันได้ ทำตามเลยครับ แล้วชีวิตจะง่ายขึ้นเยอะ

### จงเตรียมพร้อม ###

สำหรับการทดลองความสามารถนี้ สิ่งที่ขาดไม่ได้ คือ SVN repository ที่สามารถเข้าไปเขียนข้อมูลได้ เพราะถ้าต้องการจะทำตามตัวอย่างด้านล่าง ก็ต้องทำให้ข้อมูลที่คัดลอกไปจาก repository เขียนเพิ่มเ่ข้าไปได้ก่อน เพื่อให้ชีวิตง่ายขึ้นก็ให้เรียกใช้เครื่องมือชื่อ `svnsync` ที่ติดมากับ Subversion ราวๆ เวอร์ชั่น 1.4 เป็นต้นมา สำหรับตัวอย่างอย่างต่อไปนี้ ได้สร้าง Subversion repository ไว้บน Google code เรียนร้อยแล้ว ซึ่งเป็นส่วนหนึ่งของโปรเจค `protobuf` ซึ่งเป็นเครื่องมือเข้ารหัสโครงสร้างข้อมูลสำหรับส่งผ่านเครือข่าย

จากนี้เป็นต้นไป สิ่งที่ต้องทำเป็นอันดับแรกคือสร้าง Subversion repository ภายในเครื่องซะก่อน:

	$ mkdir /tmp/test-svn
	$ svnadmin create /tmp/test-svn

ถัดไปก็เปิดให้ผู้ใช้ทุกคนเปลี่ยน revprops ได้ ซึ่งวิธีการที่ง่ายที่สุดคือเพิ่มสคริปต์ pre-revprop-change ซึ่ง exists 0 เสมอ:

	$ cat /tmp/test-svn/hooks/pre-revprop-change 
	#!/bin/sh
	exit 0;
	$ chmod +x /tmp/test-svn/hooks/pre-revprop-change

เท่านี้ก็ซิงก์โปรเจคที่สร้างขึ้นภายในเครื่องกับ repository (ซึ่งในที่นี้คือ Google code) ได้แล้วด้วยการเรียกใช้คำสั่ง `svnsync init` 

	$ svnsync init file:///tmp/test-svn http://progit-example.googlecode.com/svn/ 

วิธีการกำหนดค่าต่างๆ ด้านบนเพื่อใช้ซิงก์ข้อมูล เมื่อเสร็จเรียบร้อยแล้วก็เริ่ม clone โค้ดออกมาโดยใช้คำสั่ง

	$ svnsync sync file:///tmp/test-svn
	Committed revision 1.
	Copied properties for revision 1.
	Committed revision 2.
	Copied properties for revision 2.
	Committed revision 3.
	...

แม้ว่าคำสั่งด้านบนจะใช้เวลาไม่กี่นาที แต่หากต้องการคัดลอก repository ต้นฉบับ ไปยัง repository อื่นแทนที่จะเป็น repository ที่อยู่ในเครื่อง กระบวนการนี้จะใช้เวลาอยู่ราวๆ 1 ชั่วโมง ถึงแม้ว่าจะมีรายการ commit ไม่ถึง 100 รายการก็ตาม เพราะในขณะที่ Subversion ดึงแต่ละ revision ออกมาก ก็จะโยนเข้าไปไว้ใน repository อีกอันหนึ่งไปด้วย มันดูเป็นวิธีที่น่าตลกไปหน่อย แต่ว่ามันก็เป็นวิธีการที่ง่ายที่สุดสำหรับการทำงานแบบนี้

### เริ่มกันสักที ###

จากหัวข้อที่แล้ว เราก็มี repository ของ Subversion ที่สามารถเขียนข้อมูลลงไปได้เรียบร้อยแล้ว ถัดไปก็จะลองทำตามขั้นตอนการทำงานทั่วไปดูก่อน เริ่มต้นด้วยคำสั่ง `git svn clone` ใช้นำรายการต่างๆ จาก repository ที่เป็น Subversion เข้าสู่ repository ของ Git ต้องจำไว้ว่าสำหรับการนำข้อมูลจากเซิร์ฟเวอร์ Subversion จริงๆ ควรเปลี่ยนจาก `file:///tmp/test-svn` ให้เป็น URL ของ repository ที่ทำงานด้วยซะก่อน:

	$ git svn clone file:///tmp/test-svn -T trunk -b branches -t tags
	Initialized empty Git repository in /Users/schacon/projects/testsvnsync/svn/.git/
	r1 = b4e387bc68740b5af56c2a5faf4003ae42bd135c (trunk)
	      A    m4/acx_pthread.m4
	      A    m4/stl_hash.m4
	...
	r75 = d1957f3b307922124eec6314e15bcda59e3d9610 (trunk)
	Found possible branch point: file:///tmp/test-svn/trunk => \
	    file:///tmp/test-svn /branches/my-calc-branch, 75
	Found branch parent: (my-calc-branch) d1957f3b307922124eec6314e15bcda59e3d9610
	Following parent with do_switch
	Successfully followed parent
	r76 = 8624824ecc0badd73f40ea2f01fce51894189b01 (my-calc-branch)
	Checked out HEAD:
	 file:///tmp/test-svn/branches/my-calc-branch r76

คำสั่งด้านบนจะคล้ายกันการทำงานของคำสั่ง `git svn init` ต่อด้วย `git svn fetch` ไปยัง URL ที่ระบุโคยจะใช้เวลาทำงานสักครู่ แต้ด้วยโปรเจคที่สร้างขึ้นมีรายการ commit ไปเพียงแค่ 75 รายการ และขนาดโดยรวมของโค้ดไม่ใหญ่มาก ดังนั้นไม่น่าจะใช้เวลาไม่กี่นาทีเท่านั้น แต่อย่างไรก็ตาม Git จะ check out ออกมาครั้งละรายการ และ commit เข้าไปยัง repository ของ Git ดังนั้นสำหรับโปรเจคที่มีการายการ commit หลายร้อยหรือหลายพันรายการ จะใช้เวลาหลายชั่วโมงหรือหลายวันกว่าจะเสร็จ

คำสั่ง `-T trunk -b branches -t tags` เป็นส่วนที่บอก Git ว่า repository ของ Subversion นี้เป็นไปตามโครงสร้างการแตก branch และการกำหนด tag แบบทั่วไป แต่ในกรณีที่ตั้งชื่อ trunk, branch หรือ tag ที่ต่างออกไป ก็แค่เปลี่ยนค่าใน option ได้ เนื่องจากมันค่นอข้างจะเป็นเรื่องที่เจอกันได้เรื่อง เพราะฉะนั้นคำสั่งด้านบนจึงสามารถแทนที่ด้วย `-s` เป็นส่วนที่หมายถึง repository นี้ใช้เลย์เอาท์มาตรฐานของ subversion และกำหนดค่าให้กับ option ต่างๆ เช่นเดียวกับคำสั่งด้านบน จะได้คำสั่ง:

	$ git svn clone file:///tmp/test-svn -s

เมื่อถึงขั้นตอนนี้จะได้ repository ของ Git ที่นำข้อมูล branch และ tag เข้ามาเก็บไว้เรียบร้อยแล้ว:

	$ git branch -a
	* master
	  my-calc-branch
	  tags/2.0.2
	  tags/release-2.0.1
	  tags/release-2.0.2
	  tags/release-2.0.2rc1
	  trunk

เรื่องหนึ่งที่สำคัญคือเครื่องมือนี้จะกำหนด namesapce สำหรับอ้างถึงต่างกันออกไป เมื่อดึงข้อมูลออกมาจาก repository ของ Git จะได้ข้อมูลของ branch ทั้งหมดของ repository นั้นออกมาเป็นไว้ที่ repository ภายในเครื่อง ซึ่งเหมือนกับ `origin/[branch]` โดยที่ namespace จะถูกกำหนดด้วยชื่อของกลุ่มที่เราสนใจอยู่ (namespaced by the name of the remote) ซึ่ง `git svn` จะทึกทักเอาว่าเราต้องไม่มีกลุ่มข้อมูลที่เราสนใจอยู่หลายกลุ่มและนอกจากนั้นยังจะคิดไปเราบันทึกตำแหน่งที่ใช้อ้างถึง (references to points) ทั้งหมดไว้บนเซิร์ฟเวอร์แบบที่ไม่ได้กำหนด namespace ซึ่งถ้าใช้คำสั่ง `show-ref` ของ Git ก็จะเห็น ตำแหน่งทั้งหมดที่ใช้อ้างถึงได้:

	$ git show-ref
	1cbd4904d9982f386d87f88fce1c24ad7c0f0471 refs/heads/master
	aee1ecc26318164f355a883f5d99cff0c852d3c4 refs/remotes/my-calc-branch
	03d09b0e2aad427e34a6d50ff147128e76c0e0f5 refs/remotes/tags/2.0.2
	50d02cc0adc9da4319eeba0900430ba219b9c376 refs/remotes/tags/release-2.0.1
	4caaa711a50c77879a91b8b90380060f672745cb refs/remotes/tags/release-2.0.2
	1c4cb508144c513ff1214c3488abe66dcb92916f refs/remotes/tags/release-2.0.2rc1
	1cbd4904d9982f386d87f88fce1c24ad7c0f0471 refs/remotes/trunk

ซึ่ง repository ของ Git จะเห็นในลักษณะ:

	$ git show-ref
	83e38c7a0af325a9722f2fdc56b10188806d83a1 refs/heads/master
	3e15e38c198baac84223acfc6224bb8b99ff2281 refs/remotes/gitserver/master
	0a30dd3b0c795b80212ae723640d4e5d48cabdff refs/remotes/origin/master
	25812380387fdd55f916652be4881c6f11600d6f refs/remotes/origin/testing

เรามี 2 เซิร์ฟเวอร์ด้วยกัน เซิร์ฟเวอร์แรกชื่อ `gitserver` ที่มี `master` เป็น branch และอีกเซิร์ฟเวอร์หนึ่งชื่อ `origin` มี `master` และ `testing` เป็น branch แยกกันอยู่ภายใน

ต้องจำไว้อย่างหนึ่งว่าวิธีการในตัวอย่างของการอ้างไปยังกลุ่มข้อมูลที่เราสนใจอาศัยข้อมูลที่ได้จาก `git svn` ซึ่ง tag ที่เพิ่มเข้ามาเพื่อทำหน้าที่ branch ไม่ใช่ tag ของ Git จริงๆ โดยที่ Subversion จะนำเข้าข้อมูลโดยตั้งชื่อ tag ภายใต้ branch

### กลับสู่ Subversion ###

ถึงตอนนี้ก็มี repository ที่พร้อมใช้งานแล้ว ตอนนี้ก็สามารถแก้ไขหรือเปลี่ยนแปลงข้อมูลตามที่ต้องการในโปรเจคและ commit กลับไปยัง upstream ซึ่งทั้งหมดใช้ Git ในลักษณะของ SVN Client ซึ่งแน่นอนว่าการทำงานทั้งหมดที่พูดถึงยังไม่ปรากฎบนเซิร์ฟเวอร์ Subversion มีอยู่เพียงแค่ภายในเครื่องเท่านั้น:

	$ git commit -am 'Adding git-svn instructions to the README'
	[master 97031e5] Adding git-svn instructions to the README
	 1 files changed, 1 insertions(+), 1 deletions(-)

ถัดไปก็ต้องโยนข้อมูลการเปลี่ยนกลับไปยัง upstream สังเกตว่าวิธีการต่อไปนี้จะเป็นการเปลี่ยนแปลงวิธีที่เคยใช้งาน Subversion เพราะใน repository ของ Git สามารถ commit โค้ดได้ภายในเครื่องแบบออฟไลน์ จากนั้นจะส่งข้อมูลทั้งหมดกลับไปยังเซอิร์เวอร์ Subversion กลับไปในครั้งเดียว โดยการโยนข้อมูลกลับไปยังเซิร์ฟเวอร์ Subversion ก็ทำได้โดยใช้คำสั่ง `git svn dcommit`:

	$ git svn dcommit
	Committing to file:///tmp/test-svn/trunk ...
	       M      README.txt
	Committed r79
	       M      README.txt
	r79 = 938b1a547c2cc92033b74d32030e86468294a5c8 (trunk)
	No changes between current HEAD and refs/remotes/trunk
	Resetting to the latest refs/remotes/trunk

คำสั่งด้านบนคือการส่งข้อมูลกลับไปยัง Subversion และนำไปวางไว้เป็นเวอร์ชั่นล่าสุดของ repository (HEAD) พร้อมกับรายการ commit ต่างๆ กลับไปพร้อมกัน จนไปถึงปรับปรุงค่าสำหรับบ่งชี้ (unique identifier) บน Git ภายในเครื่องใหม่ด้วย ขึ้นตอนนี้สำคัญมากเพราะนั้นหมายถึงค่า SHA-1 สำหรับแต่ละการ commit จะเปลี่ยนไป สาเหตุส่วนหนึ่งก็เป็นเพราะการทำงานบน Git ในลักษณะของไคลเอนท์กับโปรเจคมีเซิร์ฟเวอร์เป็น Subversion ไม่ใช่ความคิดที่ดีเท่าไหร่นัก ถ้าดูรายการ commit ล่าสุด จะเห็นว่า `git-svn-id` ได้ถูกเพิ่มเข้ามาแล้ว:

	$ git log -1
	commit 938b1a547c2cc92033b74d32030e86468294a5c8
	Author: schacon <schacon@4c93b258-373f-11de-be05-5f7a86268029>
	Date:   Sat May 2 22:06:44 2009 +0000

	    Adding git-svn instructions to the README

	    git-svn-id: file:///tmp/test-svn/trunk@79 4c93b258-373f-11de-be05-5f7a86268029

สังเกตว่าค่า checksum ของ SHA จะเริ่มต้นด้วย `97031e5` เป็นลำดับแรก เมื่อ commit ไปแล้ว ตอนนี้ก็กลายเป็น `938b1a5` และในกรณีที่ต้องการ push ไปทั้งเซิร์ฟเวอร์ Git และเซิร์ฟเวอร์ Subversion เราต้อง push (`dcommit`) ไปยังเซิร์ฟเวอร์ Subversion ก่อนเป็นลำดับแรก เพราะนั่นจะเป็นการเปลี่ยนข้อมูลที่ commits ของเรา

### การดึงข้อมูลเปลี่ยนแปลงใหม่ ###

ในกรณีที่กำลังทำงานร่วมกันกับเพื่อนหลายคน ซึ่งในบางครั้งเพื่อนในกลุ่มเรา push ข้อมูลใหม่ขึ้นมา และอีกคนหนึ่งก็พยายามที่จะ push ข้อมูลของเขาขึ้นมาเพื่อแก้ conflict ที่เกิดขึ้น (conflict ทีเกิดขึ้นคือข้อมูลที่ต่างกันระหว่างคนแรกและคนที่สอง) การเปลี่ยนแปลงของคนที่ 2 นั้นจะถูกปฏิเสธ (reject) จนกว่าจะ merge ข้อมูลในส่วนที่เกิด conflict ซะก่อน ซึ่งใน `git svn` จะหน้าตาเป็นแบบนี้:

	$ git svn dcommit
	Committing to file:///tmp/test-svn/trunk ...
	Merge conflict during commit: Your file or directory 'README.txt' is probably \
	out-of-date: resource out of date; try updating at /Users/schacon/libexec/git-\
	core/git-svn line 482

เพื่อแก้ไขปัญหาในลักษณะนี้ ทำได้โดยการใช้คำสั่ง `git svn rebase` ซึ่งจะดึงข้อมูลการเปลี่ยนแปลงทั้งหมดบนเซิร์ฟเวอร์ที่เราไม่มีข้อมูลอยู่และ rebase งานของเราให้เป็นเวอร์ชั่นล่าสุดล่าสุดเช่นเดียวกันกับบนเซิร์ฟเวอร์:

	$ git svn rebase
	       M      README.txt
	r80 = ff829ab914e8775c7c025d741beb3d523ee30bc4 (trunk)
	First, rewinding head to replay your work on top of it...
	Applying: first user change

ตอนนี้งานทั้งหมดที่มีอยู่ภายในเครื่องก็เช่นเดียวกันกับไฟล์เวอร์ชั่นล่าสุดที่อยู่บนเซิร์ฟเวอร์แล้ว ดังนั้นก็ปิดงานด้วยคำสั่ง `dcommit`:

	$ git svn dcommit
	Committing to file:///tmp/test-svn/trunk ...
	       M      README.txt
	Committed r81
	       M      README.txt
	r81 = 456cbe6337abe49154db70106d1836bc1332deed (trunk)
	No changes between current HEAD and refs/remotes/trunk
	Resetting to the latest refs/remotes/trunk

จำไว้ให้ว่าไม่เหมือน Git ที่จะบังคับให้ merge งานกับ upstream ก่อนที่จะ push กลับเข้าไป ซึ่ง `git svn` ช่วยเราได้ในกรณีที่เกิด conflict ถ้ามีใครสักคนโยนข้อมูลการเปลี่ยนแปลงของไฟล์สักไฟล์ และต่อมาเราก็โยนข้อมูลการเปลี่ยนแปลงของไฟล์อีกไฟล์หนึ่งกลับไป `dcommit` ก็ยังทำงานได้ไม่มีปัญหา:

	$ git svn dcommit
	Committing to file:///tmp/test-svn/trunk ...
	       M      configure.ac
	Committed r84
	       M      autogen.sh
	r83 = 8aa54a74d452f82eee10076ab2584c1fc424853b (trunk)
	       M      configure.ac
	r84 = cdbac939211ccb18aa744e581e46563af5d962d0 (trunk)
	W: d2f23b80f67aaaa1f6f5aaef48fce3263ac71a92 and refs/remotes/trunk differ, \
	  using rebase:
	:100755 100755 efa5a59965fbbb5b2b0a12890f1b351bb5493c18 \
	  015e4c98c482f0fa71e4d5434338014530b37fa6 M   autogen.sh
	First, rewinding head to replay your work on top of it...
	Nothing to do.

อีกอย่างหนึ่งที่ต้องจำคือ ด้วยเหตุที่ว่าผลลัพธ์คือสถานะของโปรเจคที่ไม่ได้เกิดขึ้นเช่นเดียวกันกับเครื่องคอมพิวเตอร์ทุกเครื่องเมื่อเรา push ไปแล้ว ถ้าการเปลี่ยนแปลงดังกล่าวขัดแย้งกันแต่ไม่เกิด conflict ปัญหาที่แก้ยากก็จะตามมาภายหลัง ซึ่งนี่จะต่างจากเซิร์ฟเวอร์ที่เป็น Git เพราะ Git เราสามารถทดสอบสถานะต่างๆ บนไคลเอนต์ได้อย่างเต็มที่ก่อนที่จะโยนข้อมูลกลับไปยังเซิร์ฟเวอร์ ในทางตรงกันข้ามสำหรับ SVN แล้ว เราไม่สามารถมั่นใจได้เลยว่าสถานะก่อนและหลังการ commit จะเหมือนกันทุกอย่าง

เราสามารถใช้คำสั่งเพื่อดึงรายการเปลี่ยนแปลงจากเซิร์ฟเวอร์ Subversion ถึงแม้จะยังไม่ต้องการจะ commit ก็ตาม โดยใช้คำสั่ง `git svn fetch` เพื่อดึงเอาข้อมูลใหม่บนเซิร์ฟเวอร์ลงมา แต่สำหรับ `git svn rebase` จะไม่มีดึงข้อมูลใหม่ลงมาหรืออัพเดตข้อมูลของรายการ commit ที่อยู่ภายในเครื่องเลย:

	$ git svn rebase
	       M      generate_descriptor_proto.sh
	r82 = bd16df9173e424c6f52c337ab6efa7f7643282f1 (trunk)
	First, rewinding head to replay your work on top of it...
	Fast-forwarded master to refs/remotes/trunk.

เมื่อสั่ง `git svn rebase` ให้ทำงานในแต่ละครั้งต้องแน่ใจว่าโค้ดของเราเป็นโค้ดเวอร์ชั่นล่าสุดเสมอ แต่ถ้ามีการเปลี่ยนแปลงเกิดขึ้นภายในเครื่อง ก็ให้ commit การเปลี่ยนแปลงนั้นไปก่อนชั่วคราวหรือ stash ส่วนนั้นออกไปก่อนที่จะใช้คำสั่ง `git svn rebase` เพราะไม่เช่นนั้นแล้วคำสั่งนี้ก็จะหยุดทำงานเมื่อเห็นว่าเกิดข้อผิดพลาดของการ merge เกิดขึ้นอยู่ดี:

### ปัญหาการสร้าง Branch ของ Git ###

พอเราเริ่มคุ้นเคยกับขั้นตอนการทำงานของ Git แล้ว เรื่องถัดไปก็น่าจะลองทำ branch เป็นเรื่องต่อไป แก้ไขข้อมูลที่แตก branch ออกไป และ merge เข้าหากัน ในกรณีที่ push ข้อมูลเข้าไปยังเซิร์ฟเวอร์ที่เป็น Subversion ผ่าน git svn ก็น่าจะเคยอยาก rebase งานให้อยู่บน branch เดียวกันไปแทนที่จะ merge ข้อมูลใน branch เข้าหากัน ด้วยเหตุผลที่ให้ความสำคัญกับการ rebase นั่นก็เพราะว่า Subversion จะเก็บประวัติการทำงานต่อกันไปเรื่อยๆ และยังไม่ได้เกี่ยวข้องเรื่อง merge เหมือนกับที่ Git ทำ ดังนั้น git svn ก็เลยทำตัวในลักษณะเดียวกันกับต้นฉบับ เมื่อมีการแปลง snapshots ให้กลายเป็นรายการ commit ของ Subversion

สมมติว่าประวัติการทำงานของเราหน้าตาเป็นแบบนี้ คือ เราสร้าง branch ชื่อ `experiment` และ commit ไป 2 รายการ จากนั้นก็ merge กลับไปยัง `master` เมื่อสั่ง `dcommit` ก็จะได้ผลลัพธ์ดังนี้:

	$ git svn dcommit
	Committing to file:///tmp/test-svn/trunk ...
	       M      CHANGES.txt
	Committed r85
	       M      CHANGES.txt
	r85 = 4bfebeec434d156c36f2bcd18f4e3d97dc3269a2 (trunk)
	No changes between current HEAD and refs/remotes/trunk
	Resetting to the latest refs/remotes/trunk
	COPYING.txt: locally modified
	INSTALL.txt: locally modified
	       M      COPYING.txt
	       M      INSTALL.txt
	Committed r86
	       M      INSTALL.txt
	       M      COPYING.txt
	r86 = 2647f6b86ccfcaad4ec58c520e369ec81f7c283c (trunk)
	No changes between current HEAD and refs/remotes/trunk
	Resetting to the latest refs/remotes/trunk

เมื่อ `dcommit` ทำงานบน branch พร้อมกับ merge รายการต่างๆ เรียบร้อย เว้นแต่ว่าถ้าลองดูในรายการประวัติของโปรเจคที่เป็น Git ของเรา จะพบว่ามันไม่มีการเขียนข้อมูลรายการ commit ลงไปใน `experiment` แต่ข้อมูลการเปลี่ยนแปลงทั้งหมดไปปรากฎบน SVN ในลักษณะของการ merge commit แทน

หากคนอื่น clone โปรเจคนี้ออกไป สิ่งที่เขาจะเห็นคือการ merge commit ที่ถูกรวมเข้าด้วยกันแล้วแทน โดยจะไม่เห็นรายละเอียดว่ามีข้อมูลใดบ้างที่ commit เช่น ที่มาของรายการนี้ หรือ วันเวลาที่ commit

### การสร้าง Branch ใน Subversion ###

การสร้าง branch ใน Subversion นั้นไม่ได้เหมือนกับการทำ branch ใน Git นัก แต่ถ้าทำเป็นลืมๆ หรือข้ามการทำ branch ไปได้ก็จะเป็นเรื่องดี แต่อย่างไรก็ตามเราก็ยังคงสามารถสร้าง branch และ commit เข้าไปใน Subversion ด้วยการใช้งาน git svn อยู่ดี

#### การสร้าง Branch ใหม่ใน SVN ####

สำหรับการสร้าง branch ใน Subversion ก็ทำได้ด้วยการใช้คำสั่ง `git svn branch [branchname]`:

	$ git svn branch opera
	Copying file:///tmp/test-svn/trunk at r87 to file:///tmp/test-svn/branches/opera...
	Found possible branch point: file:///tmp/test-svn/trunk => \
	  file:///tmp/test-svn/branches/opera, 87
	Found branch parent: (opera) 1f6bfe471083cbca06ac8d4176f7ad4de0d62e5f
	Following parent with do_switch
	Successfully followed parent
	r89 = 9b6fe0b90c5c9adf9165f700897518dbc54a7cbf (opera)

คำสั่งนี้จะเหมือนกับคำสั่ง `svn copy trunk branches/opera` ใน Subversion รวมไปถึงการทำงานบนเซิร์ฟเวอร์ Subversion แต่เรื่องสำคัญเรื่องหนึ่งคือเราไม่ได้ check out จาก branch นั้น เพราะเมื่อเรา commit กลับไปตอนนี้ รายการ commit ทั้งหมดจะไปอยู่ใน `trunk` บนเซิร์ฟเวอร์ ไม่ใช่ `opera`

### เปลี่ยน Active Branch ###

Git คิดถึงว่า เมื่อใช้ dcommits แล้ว จะไปอยู่ที่ branch ไหนด้วยการเข้าไปดูข้อมูลในประวัติการทำงานของ branch ใดๆ ที่อยู่ใน Subversion ของคุณ ซึ่งก็น่าจะมีอยู่เพียงรายการเดียวและน่าจะเป็นรายการสุดท้ายจาก `git-svn-id` ในประวัติการทำงานของ branch ปัจจุบัน

ซึ่งถ้าต้องการทำพร้อมกันหลาย branch ก็ทำได้ด้วยการกำหนดให้ branch ทั้งหมดภายในเครื่องที่ต้องการทำงานพร้อมกันนั้น `dcommit` ไปยัง branch บน Subversion ด้วยการเริ่มต้นด้วยการนำข้อมูลการ commit ของ branch ใน Subversion ที่ต้องการทั้งหมดก่อน เช่น ถ้าต้องการ branch ชื่อ `opera` ที่เราทำงานแยกออกมาได้ ก็ทำได้ด้วยใช้คำสั่ง:

	$ git branch opera remotes/opera

ขณะนี้ หากเราต้องการ merge ข้อมูลของ branch ชื่อ `opera` กลับไปยัง `trunk` (`master` branch) ก็ทำได้ด้วยคำสั่ง `git merge` เท่านั้น แต่ว่าต้องใส่คำอธิบายการ commit (commit message) เข้าไปด้วย (ระบุด้วยตัวเลือก `-m`) หรือไม่อย่างนั้นแล้วข้อความนั้นจะเป็น "Merge branch opera" แทนที่จะเป็นข้อความที่อธิบายได้ดีกว่านั้น

ถึงแม้ว่าเราจะใช้ `git merge` และการ merge ก็ดูท่าจะง่ายมากเมื่อเทียบกับ Subversion (นั่นเพราะ Git จะตรวจสอบความเข้ากันได้สำหรับการ merge ให้อัตโนมัติ) แต่นี่ไม่ใช่สำหรับการ merge ของ Git เราต้อง push ข้อมูลเหล่านี้กลับไปยังเซิร์ฟเวอร์ Subversion นั่นทำให้ไม่สามารถควบคุมการ commit ที่แย่งออกไปหลายโปรเจคได้ ดังนั้น หลังจากส่งข้อมูลกลับไปแล้ว ก็จะเหมือนกับว่า commit นั้นโดนบีบอัดเข้าไปกับงานที่หมดของ branch อื่นๆ ภายใต้ commit เดียว และเมื่อใดที่เกิดการควบรวม branch หนึ่งไปยังอีก branch หนึ่งแล้ว มันต้องใช้พลังเยอะมากทีเดียวถ้าต้องการที่จะกลับไปทำงานต่อที่ branch แรกนั้น แต่สำหรับ Git แล้ว มันเด็กมาก คำสั่ง `dcommit` นั้นใช้สำหรับลบข้อมูลใดๆ ก็ตามที่บอกว่า branch ใดที่ถูก merge เข้ามา ดังนั้นการคำนวณเพื่อหาข้อมูลก่อนหน้าที่ merge มานั้นผิดพลาดไป ซึ่งคำสั่ง dcommit จะทำให้ผลลัพธ์ได้ที่จาก `git merge` เหมือนกับผลลัพธ์ที่ได้จาก `git merge --squash` โชคร้ายหน่อยที่ไมมีวิธีการอื่นเลยเพื่อหลีกเลี่ยงสถานะการณ์นี้เนื่องจาก Subversion ไม่สามารถเก็บข้อมูลได้ ดังนั้นเราก็คงต้องทำงานด้วยวิธีพิกลพิการ อันเนื่องมาจากข้อจำกัดหลายด้านของระบบซึ่งมันจะเป็นอยู่แบบนี้ต่อไปตราบใดที่เรายังคงใช้งานเซิร์ฟเวอร์ที่เป็น Subversion เช่นนี้การหลีกเลี่ยง issue นี้ก็คือ เราต้องลบ local branch (ในกรณีนี้คือ `opear`) หลังจากเรา mege มันเข้าไปยัง remositor แล้ว

### คำสั่งต่างๆ ของ Subversion ###

`git svn` เป็นชุดเครื่องมือที่เตรียมคำสั่งต่างๆ สำหรับอำนวยความสะดวกสำหรับการย้ายมายัง Git ด้วยการเตรียมฟังก์ชันการใช้งานต่างๆ คล้ายกันกับที่มีใน Subversion ซึ่งบางคำสั่งก็เตรียมไว้เช่นเดียวกันกับที่ Subversion ใช้

#### รูปแบบการเก็บประวัติของ SVN ####

ถ้าใช้ Subversion มาก่อน และได้ลองเปิดดูประวัติการทำงานแบบ SVN output style ซึ่งตอนนี้ถ้าต้องการดูประวิตการทำงานรูบแบบเดียวกันนั้นก็ให้ใช้คำสั่ง `git svn log`:

	$ git svn log
	------------------------------------------------------------------------
	r87 | schacon | 2009-05-02 16:07:37 -0700 (Sat, 02 May 2009) | 2 lines

	autogen change

	------------------------------------------------------------------------
	r86 | schacon | 2009-05-02 16:00:21 -0700 (Sat, 02 May 2009) | 2 lines

	Merge branch 'experiment'

	------------------------------------------------------------------------
	r85 | schacon | 2009-05-02 16:00:09 -0700 (Sat, 02 May 2009) | 2 lines
	
	updated the changelog

You should know two important things about `git svn log`. First, it works offline, unlike the real `svn log` command, which asks the Subversion server for the data. Second, it only shows you commits that have been committed up to the Subversion server. Local Git commits that you haven’t dcommited don’t show up; neither do commits that people have made to the Subversion server in the meantime. It’s more like the last known state of the commits on the Subversion server.
สำหรับคำสัง `git svn log` นี้มี 2 เรื่องสำคัญที่ต้องรู้ไว้ คือ เรื่องแรก คำสั่งนี้จะทำงานแบบออฟไลน์ ซึ่งไม่เหมือนกับคำสั่ง `svn log` จริงๆ ที่จะไปถามเซิร์ฟเวอร์ Subversion เพื่อขอข้อมูล และเรื่องที่สองคือ คำสั่งนี้จะแสดงเฉพาะรายการทำงานที่ commit ไปยังเซิร์ฟเวอร์ Subversion แต่ประวัติการ commit ด้วย Git ซึ่งยังไม่ได้ใช้คำสั่ง dcommited จะไม่ได้ได้เอามาแสดง

#### หมายเหตุใน SVN ####

`git svn log` จำลองการทำงานหลายอย่างของ `svn log` ให้ทำงานได้แบบออฟไลน์ ซึ่งถ้าหากต้องการผลลัพธ์เช่นเดียวกันกับ `svn annotate` ให้ใช้คำสั่ง `git svn blame [FILE]` โดยผลลัพธ์ที่ได้ออกมาคือ:

	$ git svn blame README.txt 
	 2   temporal Protocol Buffers - Google's data interchange format
	 2   temporal Copyright 2008 Google Inc.
	 2   temporal http://code.google.com/apis/protocolbuffers/
	 2   temporal 
	22   temporal C++ Installation - Unix
	22   temporal =======================
	 2   temporal 
	79    schacon Committing in git-svn.
	78    schacon 
	 2   temporal To build and install the C++ Protocol Buffer runtime and the Protocol
	 2   temporal Buffer compiler (protoc) execute the following:
	 2   temporal 

เหมือนกับที่ผ่านมา ผลัพธ์ด้านบนจะไม่แสดงข้อมูลการ commit ที่ทำด้วย Git ไปแล้วในเครื่องหรือส่งไปยังเซิร์ฟเวอร์ Subversion ระหว่างนั้นแล้ว

#### ข้อมูลเซิร์ฟเวอร์ SVN ####

You can also get the same sort of information that `svn info` gives you by running `git svn info`:

	$ git svn info
	Path: .
	URL: https://schacon-test.googlecode.com/svn/trunk
	Repository Root: https://schacon-test.googlecode.com/svn
	Repository UUID: 4c93b258-373f-11de-be05-5f7a86268029
	Revision: 87
	Node Kind: directory
	Schedule: normal
	Last Changed Author: schacon
	Last Changed Rev: 87
	Last Changed Date: 2009-05-02 16:07:37 -0700 (Sat, 02 May 2009)

This is like `blame` and `log` in that it runs offline and is up to date only as of the last time you communicated with the Subversion server.

#### Ignoring What Subversion Ignores ####

If you clone a Subversion repository that has `svn:ignore` properties set anywhere, you’ll likely want to set corresponding `.gitignore` files so you don’t accidentally commit files that you shouldn’t. `git svn` has two commands to help with this issue. The first is `git svn create-ignore`, which automatically creates corresponding `.gitignore` files for you so your next commit can include them.

The second command is `git svn show-ignore`, which prints to stdout the lines you need to put in a `.gitignore` file so you can redirect the output into your project exclude file:

	$ git svn show-ignore > .git/info/exclude

That way, you don’t litter the project with `.gitignore` files. This is a good option if you’re the only Git user on a Subversion team, and your teammates don’t want `.gitignore` files in the project.

### Git-Svn Summary ###

The `git svn` tools are useful if you’re stuck with a Subversion server for now or are otherwise in a development environment that necessitates running a Subversion server. You should consider it crippled Git, however, or you’ll hit issues in translation that may confuse you and your collaborators. To stay out of trouble, try to follow these guidelines:

* Keep a linear Git history that doesn’t contain merge commits made by `git merge`. Rebase any work you do outside of your mainline branch back onto it; don’t merge it in.
* Don’t set up and collaborate on a separate Git server. Possibly have one to speed up clones for new developers, but don’t push anything to it that doesn’t have a `git-svn-id` entry. You may even want to add a `pre-receive` hook that checks each commit message for a `git-svn-id` and rejects pushes that contain commits without it.

If you follow those guidelines, working with a Subversion server can be more bearable. However, if it’s possible to move to a real Git server, doing so can gain your team a lot more.

## Migrating to Git ##

If you have an existing codebase in another VCS but you’ve decided to start using Git, you must migrate your project one way or another. This section goes over some importers that are included with Git for common systems and then demonstrates how to develop your own custom importer.

### Importing ###

You’ll learn how to import data from two of the bigger professionally used SCM systems — Subversion and Perforce — both because they make up the majority of users I hear of who are currently switching, and because high-quality tools for both systems are distributed with Git.

### Subversion ###

If you read the previous section about using `git svn`, you can easily use those instructions to `git svn clone` a repository; then, stop using the Subversion server, push to a new Git server, and start using that. If you want the history, you can accomplish that as quickly as you can pull the data out of the Subversion server (which may take a while).

However, the import isn’t perfect; and because it will take so long, you may as well do it right. The first problem is the author information. In Subversion, each person committing has a user on the system who is recorded in the commit information. The examples in the previous section show `schacon` in some places, such as the `blame` output and the `git svn log`. If you want to map this to better Git author data, you need a mapping from the Subversion users to the Git authors. Create a file called `users.txt` that has this mapping in a format like this:

	schacon = Scott Chacon <schacon@geemail.com>
	selse = Someo Nelse <selse@geemail.com>

To get a list of the author names that SVN uses, you can run this:

	$ svn log --xml | grep author | sort -u | perl -pe 's/.>(.?)<./$1 = /'

That gives you the log output in XML format — you can look for the authors, create a unique list, and then strip out the XML. (Obviously this only works on a machine with `grep`, `sort`, and `perl` installed.) Then, redirect that output into your users.txt file so you can add the equivalent Git user data next to each entry.

You can provide this file to `git svn` to help it map the author data more accurately. You can also tell `git svn` not to include the metadata that Subversion normally imports, by passing `--no-metadata` to the `clone` or `init` command. This makes your `import` command look like this:

	$ git-svn clone http://my-project.googlecode.com/svn/ \
	      --authors-file=users.txt --no-metadata -s my_project

Now you should have a nicer Subversion import in your `my_project` directory. Instead of commits that look like this

	commit 37efa680e8473b615de980fa935944215428a35a
	Author: schacon <schacon@4c93b258-373f-11de-be05-5f7a86268029>
	Date:   Sun May 3 00:12:22 2009 +0000

	    fixed install - go to trunk

	    git-svn-id: https://my-project.googlecode.com/svn/trunk@94 4c93b258-373f-11de-
	    be05-5f7a86268029
they look like this:

	commit 03a8785f44c8ea5cdb0e8834b7c8e6c469be2ff2
	Author: Scott Chacon <schacon@geemail.com>
	Date:   Sun May 3 00:12:22 2009 +0000

	    fixed install - go to trunk

Not only does the Author field look a lot better, but the `git-svn-id` is no longer there, either.

You need to do a bit of `post-import` cleanup. For one thing, you should clean up the weird references that `git svn` set up. First you’ll move the tags so they’re actual tags rather than strange remote branches, and then you’ll move the rest of the branches so they’re local.

To move the tags to be proper Git tags, run

	$ cp -Rf .git/refs/remotes/tags/* .git/refs/tags/
	$ rm -Rf .git/refs/remotes/tags

This takes the references that were remote branches that started with `tag/` and makes them real (lightweight) tags.

Next, move the rest of the references under `refs/remotes` to be local branches:

	$ cp -Rf .git/refs/remotes/* .git/refs/heads/
	$ rm -Rf .git/refs/remotes

Now all the old branches are real Git branches and all the old tags are real Git tags. The last thing to do is add your new Git server as a remote and push to it. Here is an example of adding your server as a remote:

	$ git remote add origin git@my-git-server:myrepository.git

Because you want all your branches and tags to go up, you can now run this:

	$ git push origin --all

All your branches and tags should be on your new Git server in a nice, clean import.

### Perforce ###

The next system you’ll look at importing from is Perforce. A Perforce importer is also distributed with Git, but only in the `contrib` section of the source code — it isn’t available by default like `git svn`. To run it, you must get the Git source code, which you can download from git.kernel.org:

	$ git clone git://git.kernel.org/pub/scm/git/git.git
	$ cd git/contrib/fast-import

In this `fast-import` directory, you should find an executable Python script named `git-p4`. You must have Python and the `p4` tool installed on your machine for this import to work. For example, you’ll import the Jam project from the Perforce Public Depot. To set up your client, you must export the P4PORT environment variable to point to the Perforce depot:

	$ export P4PORT=public.perforce.com:1666

Run the `git-p4 clone` command to import the Jam project from the Perforce server, supplying the depot and project path and the path into which you want to import the project:

	$ git-p4 clone //public/jam/src@all /opt/p4import
	Importing from //public/jam/src@all into /opt/p4import
	Reinitialized existing Git repository in /opt/p4import/.git/
	Import destination: refs/remotes/p4/master
	Importing revision 4409 (100%)

If you go to the `/opt/p4import` directory and run `git log`, you can see your imported work:

	$ git log -2
	commit 1fd4ec126171790efd2db83548b85b1bbbc07dc2
	Author: Perforce staff <support@perforce.com>
	Date:   Thu Aug 19 10:18:45 2004 -0800

	    Drop 'rc3' moniker of jam-2.5.  Folded rc2 and rc3 RELNOTES into
	    the main part of the document.  Built new tar/zip balls.

	    Only 16 months later.

	    [git-p4: depot-paths = "//public/jam/src/": change = 4409]

	commit ca8870db541a23ed867f38847eda65bf4363371d
	Author: Richard Geiger <rmg@perforce.com>
	Date:   Tue Apr 22 20:51:34 2003 -0800

	    Update derived jamgram.c

	    [git-p4: depot-paths = "//public/jam/src/": change = 3108]

You can see the `git-p4` identifier in each commit. It’s fine to keep that identifier there, in case you need to reference the Perforce change number later. However, if you’d like to remove the identifier, now is the time to do so — before you start doing work on the new repository. You can use `git filter-branch` to remove the identifier strings en masse:

	$ git filter-branch --msg-filter '
	        sed -e "/^\[git-p4:/d"
	'
	Rewrite 1fd4ec126171790efd2db83548b85b1bbbc07dc2 (123/123)
	Ref 'refs/heads/master' was rewritten

If you run `git log`, you can see that all the SHA-1 checksums for the commits have changed, but the `git-p4` strings are no longer in the commit messages:

	$ git log -2
	commit 10a16d60cffca14d454a15c6164378f4082bc5b0
	Author: Perforce staff <support@perforce.com>
	Date:   Thu Aug 19 10:18:45 2004 -0800

	    Drop 'rc3' moniker of jam-2.5.  Folded rc2 and rc3 RELNOTES into
	    the main part of the document.  Built new tar/zip balls.

	    Only 16 months later.

	commit 2b6c6db311dd76c34c66ec1c40a49405e6b527b2
	Author: Richard Geiger <rmg@perforce.com>
	Date:   Tue Apr 22 20:51:34 2003 -0800

	    Update derived jamgram.c

Your import is ready to push up to your new Git server.

### A Custom Importer ###

If your system isn’t Subversion or Perforce, you should look for an importer online — quality importers are available for CVS, Clear Case, Visual Source Safe, even a directory of archives. If none of these tools works for you, you have a rarer tool, or you otherwise need a more custom importing process, you should use `git fast-import`. This command reads simple instructions from stdin to write specific Git data. It’s much easier to create Git objects this way than to run the raw Git commands or try to write the raw objects (see Chapter 9 for more information). This way, you can write an import script that reads the necessary information out of the system you’re importing from and prints straightforward instructions to stdout. You can then run this program and pipe its output through `git fast-import`.

To quickly demonstrate, you’ll write a simple importer. Suppose you work in current, you back up your project by occasionally copying the directory into a time-stamped `back_YYYY_MM_DD` backup directory, and you want to import this into Git. Your directory structure looks like this:

	$ ls /opt/import_from
	back_2009_01_02
	back_2009_01_04
	back_2009_01_14
	back_2009_02_03
	current

In order to import a Git directory, you need to review how Git stores its data. As you may remember, Git is fundamentally a linked list of commit objects that point to a snapshot of content. All you have to do is tell `fast-import` what the content snapshots are, what commit data points to them, and the order they go in. Your strategy will be to go through the snapshots one at a time and create commits with the contents of each directory, linking each commit back to the previous one.

As you did in the "An Example Git Enforced Policy" section of Chapter 7, we’ll write this in Ruby, because it’s what I generally work with and it tends to be easy to read. You can write this example pretty easily in anything you’re familiar with — it just needs to print the appropriate information to stdout. And, if you are running on Windows, this means you'll need to take special care to not introduce carriage returns at the end your lines — git fast-import is very particular about just wanting line feeds (LF) not the carriage return line feeds (CRLF) that Windows uses.

To begin, you’ll change into the target directory and identify every subdirectory, each of which is a snapshot that you want to import as a commit. You’ll change into each subdirectory and print the commands necessary to export it. Your basic main loop looks like this:

	last_mark = nil

	# loop through the directories
	Dir.chdir(ARGV[0]) do
	  Dir.glob("*").each do |dir|
	    next if File.file?(dir)

	    # move into the target directory
	    Dir.chdir(dir) do 
	      last_mark = print_export(dir, last_mark)
	    end
	  end
	end

You run `print_export` inside each directory, which takes the manifest and mark of the previous snapshot and returns the manifest and mark of this one; that way, you can link them properly. "Mark" is the `fast-import` term for an identifier you give to a commit; as you create commits, you give each one a mark that you can use to link to it from other commits. So, the first thing to do in your `print_export` method is generate a mark from the directory name:

	mark = convert_dir_to_mark(dir)

You’ll do this by creating an array of directories and using the index value as the mark, because a mark must be an integer. Your method looks like this:

	$marks = []
	def convert_dir_to_mark(dir)
	  if !$marks.include?(dir)
	    $marks << dir
	  end
	  ($marks.index(dir) + 1).to_s
	end

Now that you have an integer representation of your commit, you need a date for the commit metadata. Because the date is expressed in the name of the directory, you’ll parse it out. The next line in your `print_export` file is

	date = convert_dir_to_date(dir)

where `convert_dir_to_date` is defined as

	def convert_dir_to_date(dir)
	  if dir == 'current'
	    return Time.now().to_i
	  else
	    dir = dir.gsub('back_', '')
	    (year, month, day) = dir.split('_')
	    return Time.local(year, month, day).to_i
	  end
	end

That returns an integer value for the date of each directory. The last piece of meta-information you need for each commit is the committer data, which you hardcode in a global variable:

	$author = 'Scott Chacon <schacon@example.com>'

Now you’re ready to begin printing out the commit data for your importer. The initial information states that you’re defining a commit object and what branch it’s on, followed by the mark you’ve generated, the committer information and commit message, and then the previous commit, if any. The code looks like this:

	# print the import information
	puts 'commit refs/heads/master'
	puts 'mark :' + mark
	puts "committer #{$author} #{date} -0700"
	export_data('imported from ' + dir)
	puts 'from :' + last_mark if last_mark

You hardcode the time zone (-0700) because doing so is easy. If you’re importing from another system, you must specify the time zone as an offset. 
The commit message must be expressed in a special format:

	data (size)\n(contents)

The format consists of the word data, the size of the data to be read, a newline, and finally the data. Because you need to use the same format to specify the file contents later, you create a helper method, `export_data`:

	def export_data(string)
	  print "data #{string.size}\n#{string}"
	end

All that’s left is to specify the file contents for each snapshot. This is easy, because you have each one in a directory — you can print out the `deleteall` command followed by the contents of each file in the directory. Git will then record each snapshot appropriately:

	puts 'deleteall'
	Dir.glob("**/*").each do |file|
	  next if !File.file?(file)
	  inline_data(file)
	end

Note:	Because many systems think of their revisions as changes from one commit to another, fast-import can also take commands with each commit to specify which files have been added, removed, or modified and what the new contents are. You could calculate the differences between snapshots and provide only this data, but doing so is more complex — you may as well give Git all the data and let it figure it out. If this is better suited to your data, check the `fast-import` man page for details about how to provide your data in this manner.

The format for listing the new file contents or specifying a modified file with the new contents is as follows:

	M 644 inline path/to/file
	data (size)
	(file contents)

Here, 644 is the mode (if you have executable files, you need to detect and specify 755 instead), and inline says you’ll list the contents immediately after this line. Your `inline_data` method looks like this:

	def inline_data(file, code = 'M', mode = '644')
	  content = File.read(file)
	  puts "#{code} #{mode} inline #{file}"
	  export_data(content)
	end

You reuse the `export_data` method you defined earlier, because it’s the same as the way you specified your commit message data. 

The last thing you need to do is to return the current mark so it can be passed to the next iteration:

	return mark

NOTE: If you are running on Windows you'll need to make sure that you add one extra step. As metioned before, Windows uses CRLF for new line characters while git fast-import expects only LF. To get around this problem and make git fast-import happy, you need to tell ruby to use LF instead of CRLF:

	$stdout.binmode

That’s it. If you run this script, you’ll get content that looks something like this:

	$ ruby import.rb /opt/import_from 
	commit refs/heads/master
	mark :1
	committer Scott Chacon <schacon@geemail.com> 1230883200 -0700
	data 29
	imported from back_2009_01_02deleteall
	M 644 inline file.rb
	data 12
	version two
	commit refs/heads/master
	mark :2
	committer Scott Chacon <schacon@geemail.com> 1231056000 -0700
	data 29
	imported from back_2009_01_04from :1
	deleteall
	M 644 inline file.rb
	data 14
	version three
	M 644 inline new.rb
	data 16
	new version one
	(...)

To run the importer, pipe this output through `git fast-import` while in the Git directory you want to import into. You can create a new directory and then run `git init` in it for a starting point, and then run your script:

	$ git init
	Initialized empty Git repository in /opt/import_to/.git/
	$ ruby import.rb /opt/import_from | git fast-import
	git-fast-import statistics:
	---------------------------------------------------------------------
	Alloc'd objects:       5000
	Total objects:           18 (         1 duplicates                  )
	      blobs  :            7 (         1 duplicates          0 deltas)
	      trees  :            6 (         0 duplicates          1 deltas)
	      commits:            5 (         0 duplicates          0 deltas)
	      tags   :            0 (         0 duplicates          0 deltas)
	Total branches:           1 (         1 loads     )
	      marks:           1024 (         5 unique    )
	      atoms:              3
	Memory total:          2255 KiB
	       pools:          2098 KiB
	     objects:           156 KiB
	---------------------------------------------------------------------
	pack_report: getpagesize()            =       4096
	pack_report: core.packedGitWindowSize =   33554432
	pack_report: core.packedGitLimit      =  268435456
	pack_report: pack_used_ctr            =          9
	pack_report: pack_mmap_calls          =          5
	pack_report: pack_open_windows        =          1 /          1
	pack_report: pack_mapped              =       1356 /       1356
	---------------------------------------------------------------------

As you can see, when it completes successfully, it gives you a bunch of statistics about what it accomplished. In this case, you imported 18 objects total for 5 commits into 1 branch. Now, you can run `git log` to see your new history:

	$ git log -2
	commit 10bfe7d22ce15ee25b60a824c8982157ca593d41
	Author: Scott Chacon <schacon@example.com>
	Date:   Sun May 3 12:57:39 2009 -0700

	    imported from current

	commit 7e519590de754d079dd73b44d695a42c9d2df452
	Author: Scott Chacon <schacon@example.com>
	Date:   Tue Feb 3 01:00:00 2009 -0700

	    imported from back_2009_02_03

There you go — a nice, clean Git repository. It’s important to note that nothing is checked out — you don’t have any files in your working directory at first. To get them, you must reset your branch to where `master` is now:

	$ ls
	$ git reset --hard master
	HEAD is now at 10bfe7d imported from current
	$ ls
	file.rb  lib

You can do a lot more with the `fast-import` tool — handle different modes, binary data, multiple branches and merging, tags, progress indicators, and more. A number of examples of more complex scenarios are available in the `contrib/fast-import` directory of the Git source code; one of the better ones is the `git-p4` script I just covered.

## Summary ##

You should feel comfortable using Git with Subversion or importing nearly any existing repository into a new Git one without losing data. The next chapter will cover the raw internals of Git so you can craft every single byte, if need be.
