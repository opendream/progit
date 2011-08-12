# ปรับแต่ง Gitb #

ที่ผ่านมาผมเน้นพื้นฐานการทำงานของ Git รวมถึงการใช้งานมัน และได้นำเสนอเครื่องมือหลายตัวที่ช่วยให้คุณสามารถใช้งาน Git ได้ง่าย และมีประสิทธิภาพมากขึ้น สำหรับบทนี้ผมจะพูดถึงเรื่องวิธีการปรับแต่ง Git ให้ทำงานตามสมัยนิยม โดยเน้นที่การปรับแต่งสำคัญๆ เป็นหลัก ซึ่งทำให้เราสามารถทำให้ Git ทำงานได้ในแบบที่เราอยากได้ บริษัทอยากได้ หรือแบบที่กลุ่มของเราอยากได้

## ตั้งค่าให้ Git ##

เราจะได้เห็นในบทที่ 1 ว่าเราสามารถปรับแต่งค่าของ Git ได้ด้วยคำสั่ง git config อย่างแรกที่เรากำหนดได้คือ ชื่อของเรา และ e-mail ของเรา

	$ git config --global user.name "John Doe"
	$ git config --global user.email johndoe@example.com

หลังจากนี้เราจะได้เรียนรู้วิธีการปรับแต่ง Git เพื่อให้ตรงกับความต้องการของเรามากที่สุด

ในบทแรกเราได้เราได้เรียนรู้การปรับแต่ง Git เบื้องต้นไปแล้วแต่ผมจะสรุปมันให้ฟังอีกที เราสามารถกำหนดลักษณะการทำงานของ Git ได้โดยการแก้ไขไฟล์สำหรับตั้งค่าเบื้องต้น โดยไฟล์แรกที่ Git จะไปอ่านคือไฟล์ `/etc/gitconfig` ซึ่งเก็บค่าเบื้องต้นสำหรับผู้ใช้ทุกคนบนเครื่องและสำหรับทุก Repositories ถ้าเราใส่คำว่า `--system` ตอนที่เรียก `git config` มันจะไปอ่านและเขียนที่ไฟล์นี้

ไฟล์ต่อไปที่ Git เข้าไปดูคือ `~/.gitconfig` ซึ่งใช้สำหรับปรับแต่งของผู้ใช้แต่ละคนบนเครื่อง เราสามารถแก้ไขไฟล์นี้โดยการใส่คำว่า `--global` เข้าไป

สุดท้าย Git จะไปดูไฟล์ตั้งค่าในแต่ละ Git directory (`.git/config`) ที่เรากำลังใช้งานอยู่ ค่าเหล่านี้จะมีผลเฉพาะใน reposiroty ที่ไฟล์นั้นอยู่เท่านั้น 

ค่าที่ถูกตั้งอยู่ใกล้กับ repository จะทั้บค่าที่อยู่ไกลออกไปเช่น การตั้งค่าที่ `.git/config` จะทับค่าที่อยู่ใน `/etc/gitconfig` เป็นต้น เราสามารถเข้าไปแก้ไขค่าในไฟล์ได้โดยตรง แต่โดยปกติแล้วเราสามารถตั้งค่าต่างๆ ได้ผ่านคำสั่ง `git config` อยู่แล้ว


### พื้นฐานการปรับแต่งบนเครื่องตัวเอง ###

การปรับแต่งใน Git มีอยู่สองประเภทคือ ปรับแต่งบนเครื่องตัวเอง (cliend side) และ ปรับแต่งที่ฝั่งเซิฟเวอร์ (server side) โดยส่วนมาแล้วจะเน้นการปรับแต่งบนเครื่องตัวเองมากกว่า เพราะมักจะเอาไว้ใช้เองไม่มีผลกับคนอื่น อย่างไรก็ตามค่าที่สามารถปรับแต่งได้มีอยู่มหาศาล แต่ที่จะพูดในบทนี้จะเป็นค่าที่ใช้กันบ่อยๆ หรือค่าที่จำเป็นสำหรับ workflow เป็นหลัก แม้ว่าในหลายๆ ค่าจะมีประโยชน์ในกรณีที่เราใช้ตัวล่าสุดแต่คงไม่ได้เอามาใส่ในเล่มนี้ ถ้าต้องการรู้ว่า Git ของคุณมีอะไรให้ปรับแต่งได้บ้าง ให้ลองสั่ง

	$ git config --help

คู่มือการใช้งานที่ได้จาก `git config` จะแสดงรายการของค่าสำหรับปรับแต่พร้อมกับรายละเอียดอีกเล็กน้อย

#### core.editor ####

ถ้าไม่ได้ตั้งค่าอะไร Git จะเปิด text editor ที่กำหนดเป็นค่าบริยายในเครื่อง หรือไม่ก็ใช้ Vi เป็นตัวแก้ไขข้อความที่จะ commit หรือ tag ถ้าต้องการจะแก้ไขไปใช้ text editor ตัวอื่นให้ใช้คำสั่ง `core.editor` ในการตั้งค่า

	$ git config --global core.editor emacs

หลังจากนี้ ไม่ว่า shell ของเราจะจะตั้งค่าปริยายสำหรับแก้ไขไฟล์เป็นอะไร โปรแกรม Git จะไปเปิด Emacs สำหรับแก้ไขข้อความเสมอ 

#### commit.template ####

เราเรากำหนดค่านี้ไปที่ไฟล์บนเครื่อง Git จะใช้ไฟล์นี้เป็นข้อความตั้งต้นสำหรับการ commit ตัวอย่างเช่น เราสร้างไฟล์ `$HOME/.gitmessage.txt` ที่ออกมาหน้าตาแบบนี้

	subject line

	what happened

	[ticket: X]

วิธีการบอก Git ให้รู้ว่าเราต้องการให้ข้อความในไฟล์นี้มาแสดงใน text editor ตอนที่สั่ง `git commit` ให้สั่ง `commit.template` ดังนี้

	$ git config --global commit.template $HOME/.gitmessage.txt
	$ git commit

แล้วโปรแกรม text editor จะเปิดข้อความดังนี้ ทุกครั้งที่เราสั่ง commit

	subject line

	what happened

	[ticket: X]
	# Please enter the commit message for your changes. Lines starting
	# with '#' will be ignored, and an empty message aborts the commit.
	# On branch master
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	# modified:   lib/test.rb
	#
	~
	~
	".git/COMMIT_EDITMSG" 14L, 297C

ต่อไปถ้าเราต้องการกำหนดนโยบายสำหรับข้อความที่จะ commit ให้เราใส่ไฟล์ต้นแบบไว้ในเครื่องแล้วกำหนดให้ Git ใช้เป็นค่าบริยาย จะช่วยให้การเปลี่ยนแปลงรูปแบบข้อความ commit ในทีมได้ง่ายขึ้น

#### core.pager ####

คำสั่ง core.pager ใช้สำหรับบอก Git ให้แสดงผลเป็นหน้าๆ สำหรับข้อมูลยาวๆ เช่น `log` และ `diff` เป็นต้น เราสามารถตั้งค่าเป็น `more` หรืออื่นๆ ตามที่ชอบ (ค่าบริยายคือ `less`) หรือเราจะปิดการแสดงผลเป็นหน้าไปเลยก็ได้ โดยการกำหนดค่าว่างให้มัน

	$ git config --global core.pager ''

ถ้าเราสั่งคำสั่งข้างต้น ต่อไป Git จะแสดงข้อมูลทั้งหมดแบบไม่มีการแบ่งหน้า โดยไม่สนใจว่าข้อมูลจะยาวแค่ไหน

#### user.signingkey ####

ถ้าเราสร้าง signed annotated tag ขึ้นมา (อย่างที่คุยไว้ในบทที่ 2) การกำหนด GPG signing key สามารถทำได้เหมือนการกำหนดค่าอื่นๆ ตามนี้

	$ git config --global user.signingkey <gpg-key-id>

ตอนนี้ เราสามารถใส่ tags โดยไม่ต้องมีกำหนด key ทุกครั้งที่เราใส่ tag ด้วยคำสั่ง `git tag`

	$ git tag -s <tag-name>

#### core.excludesfile ####

เราสามารถกำหนดรูปแบบของไฟล์ไว้ใน `.gitignore` เพื่อให้ Git ไม่สนใจไฟล์เหล่านั้นตอนที่เราสั่ง `git add` หรือตอนที่ตรวจสอบสถานะของโครงการ อย่างที่เคยคุยไว้ในบทที่สอง อย่างไรก็ตามถ้าเราต้องการให้มีไฟล์นอกโครงการของเรา ทำหน้าที่เก็บค่าเหล่านี้ หรือค่าอื่นๆ เราสามารถใช้คำสั่ง `core.excludesfile` ได้ โดยชี้คำสั่งนี้ไปที่ path ของไฟล์ที่มีเนื้อหาแบบเดียวกับ `.gitignore` ไฟล์

#### help.autocorrect ####

คำสั่งนี้ใช้ได้เฉพาะใน Git 1.6.1 หรือเวอร์ชั่นที่สูงกว่า ถ้าเราพิมพ์คำสั่งผิดโปรแกรม Git 1.6 จะแสดงข้อความประมาณนี้

	$ git com
	git: 'com' is not a git-command. See 'git --help'.

	Did you mean this?
	     commit

ถ้าเรากำหนด `help.autocorrect` เป็น 1 โปรแกรม Git จะแก้ไขข้อความแล้วสั่งให้โปรแกรมทำงานทันที ในกรณีที่ระบบเดาออกมาได้แค่ 1 รูปแบบ

### ใส่สีให้ Git ###

Git สามารถแสดงสีบน terminal ได้ ซึ่งจะช่วยให้เราดูค่าต่างๆ ได้ง่ายและเร็วขึ้น โดยคำสั่งสำหรับกำหนดค่าสีมีดังนี้

#### color.ui ####

Git จะพยายามใส่สีให้เราในทุกๆ ค่าที่ออกมาถ้าเราต้องการ เราสามารถกำหนดไปในรายละเอียดเกี่ยวกับสีและรูปแบบที่จะออกมาได้ แต่ก่อนอื่นต้องเราต้องเปิดให้ Git รู้ว่าเราต้องการให้มีสีก่อน โดยใช้คำสั่ง `color.ui` ดังนี้

	$ git config --global color.ui true

เมื่อค่าถูกกำหนด โปรแกรม Git จะใส่สีให้กับทุกค่าที่ออกมาใน terminal ทันที คำสั่งนี้ถูกใส่เข้ามาใน Git version 1.5.5 ถ้าเราใช้เวอร์ชั่นก่อนหน้านี้ เราจะต้องกำหนดค่าสีทีละค่าด้วยตัวเอง

You'll rarely want `color.ui = always`. In most scenarios, if you want color codes in your redirected output, you can instead pass a `--color` flag to the Git command to force it to use color codes. The `color.ui = true` setting is almost always what you'll want to use.

#### `color.*` ####

ถ้าเราต้องการกำหนดลึกลงไปว่าคำสั่งใดบ้างที่ต้องการให้มีสี และมีสีอย่างไร เราสามารถกำหนดสีโดยใช้ verb-specific coloring setting ได้ โดยสามารถใช้ `true` `false` หรือ `always` กับคำสั่งเหล่านี้

	color.branch
	color.diff
	color.interactive
	color.status

ถ้ายังไม่สะใจ แต่ละคำสั่งสามารถกำหนดย่อยลงไปได้อีก โดยเราสามารถกำหนดสีให้กับแต่ละส่วนที่ออกมา เช่น ถ้าเราต้องการกำหนดให้ meta information ที่อยู่ใน diff มีสีตัวอักษรเป็นสีฟ้า มีสีพื้นหลังเป็นสีดำ และเป็นตัวหนา เราสามารถสั่งได้ดังนี้

	$ git config --global color.diff.meta "blue black bold"

เราสามารถกำหนดสีด้วยคำว่า normal black red green yellow blue magenta cyan หรือ white ถ้าเราต้องการกำหนดค่าอย่าง ตัวหนา แบบในตัวอย่างก่อนหน้านี้ เราสามารถใช้คำว่า bold dim ul blink และ reverse ได้

ดูเพิ่มเติมได้ใน `git config` manpage สำหรับค่าอื่นๆ ถ้าต้องการจะทำ

### ทำการ Merge และ Diff จากโปรแกรมภายนอก ###

แม้ว่า Git จะมีระบบ diff (ที่เราใช้กันในบทก่อนๆ) เอาไว้แล้วก็ตาม แต่ถ้าเราต้องการใช้โปรแกรมอื่นในการตรวจสอบ diff ก็สามารถทำได้ เช่นเดียวกันเราสามารถใช้เครื่องมือที่มี GUI สำหรับการ merge และแก้ไข conflict แทนการใช้ merge ที่มีมากับ Git ก็ได้ ผมจะยกตัวอย่างการตั้งค่าเพื่อใช้ Perforce Visual Merge Tools (P4Merge) สำหรับ diffs และ merge เพราะมันมีหน้าตาที่ดูดี ที่สำคัญคือมันฟรี

ถ้าต้องการทดลอง (P4Merge รองรับระบบปฏิบัติการหลักๆ) ก็สามารถทำตามได้เลย ผมจะใช้ path และ names โดยอ้างอิงจาก Mac และ Linux ส่วนใครที่ใช้ Windows ก็ให้เปลี่ยน `/usr/local/bin` ไปตาม path ของระบบที่ใช้อยู่นะครับ

ขั้นแรกให้ Download P4Merge จากที่นี่

	http://www.perforce.com/perforce/downloads/component.html

เริ่มต้น เราจะกำหนดให้ external wrapper script ไปยังคำสั่งที่ต้องการ (ตัวอย่างจะเป็น path ของ Mac แต่ถ้าเป็นระบบอื่นก็ให้ลองหาคำสั่ง p4merge ในเครื่องดู) สร้าง merge wrapper ในชื่อ extMerge ให้มันไปเรียกคำสั่งที่เราต้องการดังนี้

	$ cat /usr/local/bin/extMerge
	#!/bin/sh
	/Applications/p4merge.app/Contents/MacOS/p4merge $*

สำหรับตัว diff wrapper เราจะได้รับ arguments ทั้งหมด 7 ตัว โดยเราจะส่ง 2 ตัวต่อไปยัง diff script ทั้งนี้ค่าปริยาย Git จะส่ง argument ไปยังโปรแกรม diff เป็นดังนี้

	path old-file old-hex old-mode new-file new-hex new-mode

เนื่องจากเราต้องการแค่ argument `old-file` และ `new-file` ดังนั้นเราจะให้ wrapper ส่งต่อไปเฉพาะที่เราต้องการดังนี้ 

	$ cat /usr/local/bin/extDiff 
	#!/bin/sh
	[ $# -eq 7 ] && /usr/local/bin/extMerge "$2" "$5"

จากนั้นเราต้องเปลี่ยนให้ไฟล์ wrapper ทั้งสองตัวของเราเป็นไฟล์ที่สามารถ execute ได้

	$ sudo chmod +x /usr/local/bin/extMerge 
	$ sudo chmod +x /usr/local/bin/extDiff

เมื่อทุกอย่างพร้อม เราจะสามารถกำหนดให้ Git ไปใช้ mearge resolution และ diff tools ของเราได้แล้ว เริ่มต้นเราบอก Git ก่อนว่าเราใช้ `merge.too` ตัวไหน จากนั้นก็บอกวิธีการเรียกใช้โดย `mergetool.extMerge.cmd` จากนั้นก็บอก Git ว่าเมื่อโปรแกรมทำงานเสร็จแล้วให้สนใจความสำเร็จหรือล้มเหลวของการ merge หรือไม่ โดยใช้คำสั่ง `mergetool.trustExitCode` เมื่อทำ merge เสร็จแล้วก็มากำหนดค่าให้ diff บ้างโดยใช้คำสั่ง `diff.external` ดังนี้

	$ git config --global merge.tool extMerge
	$ git config --global mergetool.extMerge.cmd \
	    'extMerge "$BASE" "$LOCAL" "$REMOTE" "$MERGED"'
	$ git config --global mergetool.trustExitCode false
	$ git config --global diff.external extDiff

หรือจะใช้วิธีแก้ไขไฟล์ `~/.gitconfig` แล้วเพิ่มบรรทัดนี้ลงไป

	[merge]
	  tool = extMerge
	[mergetool "extMerge"]
	  cmd = extMerge "$BASE" "$LOCAL" "$REMOTE" "$MERGED"
	  trustExitCode = false
	[diff]
	  external = extDiff

หลังจากกำหนดค่าเสร็จแล้ว ถ้าเราลองสั่ง diff ตามนี้
	
	$ git diff 32d1776b1^ 32d1776b1

แทนที่จะได้ผลลัพทธ์เป็น diff จาก command line โปรแกรม Git จะไปเปิดโปรแกรม P4Merge ขึ้นมาแทนซึ่งได้ผลดังรูป 7-1

Insert 18333fig0701.png 
รูป 7-1. P4Merge.

ถ้าเราลอง merge สอง branches แล้วพบว่าเกิด conflic ขึ้น เราสามารถทดลองสั่ง `git mergetool` ดู โปรแกรม Git จะสั่ง P4Merge ให้ทำงานเพื่อให้เราแก้ไข conflic ผ่าน GUI

การทำ wrapper มีข้อดีตรงที่เราสามารถปรับเปลี่ยนเครื่องมือในการ diff และ merge ได้ง่ายขึ้น ตัวอย่างเช่น เมื่อต้องการเปลี่ยนเครื่องมือ `extDiff` และ `extMerge` ไปใช้ KDiff3 สิ่งที่เราต้องทำก็แค่แก้ไขไฟล์ `extMerge` เท่านั้น

	$ cat /usr/local/bin/extMerge
	#!/bin/sh	
	/Applications/kdiff3.app/Contents/MacOS/kdiff3 $*

เท่านี้ Git ก็จะไปใช้โปรแกรม KDiff3 ในการแสดงผล diff และ merge conflict แทน

Git ได้เตรียมวิธีง่ายๆ ให้เราสามารถเปลี่ยนไปใช้โปรแกรม merge-resolution บางตัวได้โดยไม่ต้องตั้งคำสั่งสำหรับเรียกใช้เลย เราสามารถตั้ง merge ให้ไปเรียกใช้ kDiff3 opendiff tkdiff meld xxdiff emerge vimdiff หรือ gvimdiff ได้ในบรรทัดเดียว และในกรณีที่เราไม่ต้องการใช้ kDiff3 ในการทำ diff แต่ต้องการใช้สำหรับการ merge-resolution เราสามารถสั่งได้ตามนี้

	$ git config --global merge.tool kdiff3

ถ้าเราใช้คำสั่งข้างต้นแทนการสร้างไฟล์ `extMerge` และ `extDiff` โปรแกรม Git จะใช้โปรแกรม kDiff3 สำหรับการ merge-resolution และใช้โปรแกรม diff ที่มาพร้อมกับ Git สำหรับการทำ diff

### การจัดการตัวอักษรและช่องว่าง ###

เป็นเรื่องที่น่ารำคัญมากๆ เมื่อเราไล่ดูการแก้ไข code แล้วปรากฏว่าแทนที่เราจะเห็นเฉพาะส่วนเปลี่นแปลงที่มีผลกับโปรแกรม เรากลับเห็นจุดบรรทัดที่เปลี่ยนแปลงแค่ช่องว่างหลังบรรทัด โดยเฉพาะตอนที่เราไม่ได้ทำงานคนเดียว หรือทำงานข้ามระบบปฏิบัติการ มันมีโอกาสมากที่จะทำผิดพลาดเพราะ text editor ไม่ได้แสดงให้เราเห็นว่ามีช่องว่างอยู่ท้ายบรรทัดนั้นๆ หรือบางทีอาจจะแก้รหัส end-of-line ไปตามแต่ละระบบปฏิบัติการ โปรแกรม Git มีค่าที่เราตั้งได้หลายตัวเพื่อจัดการปัญหานี้ 

#### core.autocrlf ####

ถ้าคุณทำงานบนระบบ Windows หรือทำงานบนระบบอื่นๆ แต่ทำงานร่วมกับคนที่เขียนโปรแกรมบน Windows คุณน่าจะเคยเจอปัญหา ระหัส end-of-line มาบ้าง นั่นเป็นเพราะ Windows ใช้ทั้ง carriage-return และใช้ linefeed สำหรับขึ้นบรรทัดใหม่ในไฟล์ แต่ใน Mac และ Linux ใช้เฉพาะ linefeed เท่านั้น ดูเหมือนจะเป็นเรื่องเล็กน้อยนะครับ แต่มันน่ารำคาญสุดๆ

Git สามารถจัดการมันได้ ด้วยการเปลี่ยน carriage-return เป็น linefeed ให้อัตโนมัติตอนที่เราสั่ง commit และเปลี่ยนกลับให้เมื่อ checks out กลับมา เราสามารถเปิดใช้ความสามารถนี้ด้วยคำสั่ง `core.autocrlf` ถ้าอยู่บนระบบ Windows กำหนดให้ค่าเป็น `true` โปรแกรม Git จะแปลง linefeed เป็น carriage-return ให้ตอนที่เรา check out 


	$ git config --global core.autocrlf true

แต่ถ้าเราอยู่บน Linux หรือ Mac ที่ต้องการใช้ linefeed สำหรับจบบรรทัด เราไม่ต้องการให้ Git มา convert ให้แล้ว แต่อย่างไรก็ตามถ้าเราทำงานกับ Windows เราอาจจะบังเอิญไปเอาไฟล์ที่จบด้วย carriage-return มาใส่ในโครงการของเราเข้า วิธีการแก้ไขก็คือเราต้องบอกให้ Git เปลี่ยน carriage-return ไปเป็น linefeed ให้ตอนที่ commit แต่ไม่ต้องเปลี่ยนตอนที่ checkout ด้วยคำสั่ง `core.autocrlf`

	$ git config --global core.autocrlf input

พอทำแบบนี้เราจะได้ carriage-return ลงท้ายบรรทัดสำหรับ Windows และได้ linefeed ลงท้ายสำหรับ Mac Linux และใน repository ครับ

แต่ถ้าเราเป็นนักพัฒนาบน Windows และโปรเจคของเราเป็น Windows เท่านั้น เราสามารถปิดความสามารถนี้ทิ้งไปได้เลย โดยใช้คำสั่ง

	$ git config --global core.autocrlf false

#### core.whitespace ####

Git มาพร้อมกับความสามารถในการจัดการปัญหาช่องว่างทั้ง 4 ประการ โดยสองประการแรกจะถูกเปิดโดยอัตโนมัติ แต่จะปิดไปก็ได้ ส่วนอีกสองอันถ้าต้องการเปิดก็สามารถทำได้เช่นกัน

สองคำสั่งแรกที่เปิดโดยอัตโนมัติคือ `trailing-space` กับหลับจัดการปัญหาช่องว่างหลังบรรทัด และอีกคำสั่งคือ `space-before-tab` สำหรับจากการช่องว่างก่อน tab ในตอนต้นของบรรทัด

อีกสองคำสั่งที่ปิดเอาไว้แต่สามารถเปิดได้ คือ `indent-with-non-tab` สำหรับจัดการการใช้ช่องว่างขนาดเท่ากับแปดตัวอักษรหรือมากกว่า แทนที่จะใช้ tab และคำสั่งสุดท้ายคือ `cr-at-eol` ใช้สำหรับบอก Git ว่าไม่ต้องไปยุ่งกับ carriage-return ที่ท้ายบรรทัด 

เราสามารถบอก Git ให้ปิดหรือเปิดความสามารถเหล่านี้ได้ โดยการกำหนดค่าให้กับ `core.whitespace` โดยใส่คำสั่งที่ต้องการให้เปิด แล้วคั่นด้วย comma ถ้าตัวไหนที่ไม่ได้ใส่ไว้ Git จะเข้าใจว่าให้ปิดความสามารถนั่นๆ ไป หรืออีกทางหนึ่งคือให้ใส่เครื่องหมายลบ `-` เอาไว้หน้าคำสั่งนั้นๆ ตัวอย่างเช่นถ้าตอ้งการเปิดความสามารถทั้งหมด ยกเว้น `cr-at-eol` ให้สั่งตามนี้

	$ git config --global core.whitespace \
	    trailing-space,space-before-tab,indent-with-non-tab

Git จะช่วยจัดการปัญหาเหล่านี้ให้ตอนที่เราสั่ง `git diff` และจะใส่สีไว้ให้เห็นชัดๆ เพื่อเราจะได้แก้ไขก่อนที่จะ commit เข้าไป นอกจากนี้มันยังช่วยจัดการเรื่อง apple patches ตอนสั่ง `git apply` ด้วย เมื่อเราสั่ง apply patches เราสามารถให้ Git ช่วยเตื่อนได้ถ้าพบปัญหาเรื่องช่องว่าง เช่น

	$ git apply --whitespace=warn <patch>

หรือเราสามารถบอกให้ Git แก้ข้อมูลให้เลย ก่อนที่จะทำการ patch ก็ได้

	$ git apply --whitespace=fix <patch>

ความสามารถนี้สามารถนำปประยุกต์ใช้กับ git rebase ได้เช่นเดียวกัน ตัวอย่างเช่น ถ้าเราเผลอ commit ช่องว่างที่ไม่เหมาะสมเข้าไป จึงไม่อยาก push ขึ้นไปที่ server เราสามารถสั่ง `rebase` ด้วยคำสั่ง `--whitespace=fix` แล้ว Git จะไปจัดการกับปัญหาช่องว่างให้เราเอง


### การปรับแต่งบนเครื่อง Server ###

Git บนฝั่ง Server ไม่ค่อยมีอะไรให้ทำเท่าไหรครับ แต่มีหลายอย่างที่รู้ไว้ใช่ว่าใส่บ่าแบกหามครับ

#### receive.fsckObjects ####

ตามปกติ Git จะไม่มีระบบตรวจสอบความเรียบร้อยของแต่ละไฟล์ระหว่างที่ push ขึ้นมาบน Server ถึงแม้ว่า Git จะสามารถตรวจสอบด้วยวิธี SHA-1 checksum ได้ แต่มันทำไม่ได้ทำในทุกๆ การ push เพราะว่ามันจะทำให้เสียเวลามากๆ ถ้าทุกครับที่ push เราต้องตรวจสอบมันทุกไฟล์ ยิ่งถ้า repository หรือ push มีไฟล์จำนวนมาก แต่ถ้าเราต้องการให้ Git มันตรวจสอบทุกๆ ไฟล์ในทุกๆ ครั้งที่ push เราก็สามารถบีบคอให้ Git มันทำให้ได้ ผ่านการกำหนด `receive.fsckObjects` ให้เป็น true

	$ git config --system receive.fsckObjects true

ที่นี่ Git จะทำการตรวจสอบทุกๆ ไฟล์ ก่อนที่จะรับเข้าไป repository เพื่อให้แน่ใจว่าความผิดพลาดที่ client จะไม่สงผลกระทบต่อข้อมูลของเรา

#### receive.denyNonFastForwards ####

ถ้าเรา rebase commits ที่เรา push ขึ้นไปแล้ว จากนั้นก็ push มันขึ้นไปอีกที หรือไม่เราก็พยายาม push commit ที่ไม่มีจุดเชื่อมต่อกับ commit บน server ตามปกติทั้งสองกรณี Git จะไม่อนุญาติให้เราทำ ซึ่งนี้เป็นสิ่งที่ดี แต่ในบางกรณีถ้าคุณเข้าใจระบบเป็นอย่างดี และแน่ในว่าต้องการทำอย่างนั้นจริงๆ เราก็สามารถใช้ `-f` เพื่อบีบคือให้ Git ยอม push ให้เรา

เพื่อไม่ให้ใครมา force-update ได้ เราสามารถสั่งห้ามเด็จขาดโดยกำหนดให้ `receive.denyNonFastForwards` มีค่าเป็น true

	$ git config --system receive.denyNonFastForwards true

อีกวิีธีที่สามารถทำได้บนฝั่ง server คือการใช้ receive hooks ซึ่งจะได้ดูกันต่อไป สำหรับวิธีนั้นจะค่อนข้างซับซ้อนเหมือนการทำ non-fast-forwards ที่เจาะจงกลุ่มผู้ใช้

#### receive.denyDeletes ####

ผู้ใช้บางคนใช้วิธีขี้โกง เมื่อเห็นว่าเรามีข้อกำหนด `denyNonFastForwards` ก็จะใช้วิธีลบ branch ทิ้ง แล้วก็ push ขึ้นมาอีกทีพร้อมจุดเชื่อมต่อใหม่ สำหรับ Git เวอร์ชั่นใหม่ (สูงกว่าเวอร์ชั่น 1.6.1) เราสามารถตั้งค่า `receive.denyDeletes` เป็น true ได้

	$ git config --system receive.denyDeletes true

วิธีการนี้จะทำให้ Git ไม่ยอมให้ลบ branch หรือ tag ผ่านการ push ที่ผั่ง client ถ้าต้องการลบจริงๆ จะต้องลบที่ฝั่ง server ด้วยตัวเอง จริงๆ มีวิธีจัดการกับปัญหานี้อีกแบบด้วย ACLs ่ซึ่งเราจะอธิบายกันตอนท้ายๆ บทนี้

## Git Attributes ##

Git สามารถใช้ความสามารถนี้ได้เฉพาะกับ directory หรือ กับไฟล์ เท่านั้น เราเรียกการกำหนด path-specific ว่า Git attributes ซึ่งสามาถกำหนดไว้ในไฟล์ `.gitattributes` ที่อยู่ใน repository ของเรา (ปกติแล้วจะใส่ไว้ใน root ของ project) หรือใส่ไว้ในไฟล์ `.git/info/attributes` ถ้าไม่ต้องการให้ไฟล์ attributes ถูกส่งขึ้นไปบน server ด้วย

การใช้ attributes จะทำให้เราเหมือนว่าสามารถกำหนดวิธีในการ merge สำหรับไฟล์แต่ละแบบได้ หรือสำหรับแต่ละ directory ใน project ของเราได้ เราสามารถสอน Git ได้ว่าไฟล์แต่ละแบบจะต้อง diff กันอย่างไร หรือจะต้องนำไฟล์นั้นๆ ไปทำอะไรก่อนแล้วค่อยนำค่ามา diff สำหรับในส่วนนี้เราจะใช้ตัวอย่างเพื่อแสดงให้เห็นวิธีการใช้งานคำสั่งบางส่วนของ attributes

### Binary Files ###

เทคนิคเท่ๆ ที่ได้จากการใช้งาน Git attribute คือเราสามารถบอก Git ได้ว่าไฟล์ไหนคือ binary (ในหลายกรณีระบบอื่นๆ จะไม่สามารถแยกได้) และสามารถบอกได้ด้วยว่าจะจัดการกับไฟล์รูปแบบนั้นได้อย่างไร ตัวอย่างเช่น text file ที่คอมพิวเตอร์เป็นคนสร้างขึ้นมา ถึง diff ออกมาก็อ่านหรือแก้ไขไม่ได้ ไฟล์พวกนี้ก็ควรถูกจัดการแบบไฟล์ binary ในขณะที่ไฟล์ binary บางไฟล์เราสามารถบอกความแตกต่างของแต่ละไฟล์ได้ เราก็ควรจะบอกให้ Git ช่วยจัดการให้

#### ระบุไฟล์ประเภท Binary Files ####

ไฟล์บางไฟล์อาจจะดูเหมือน text files แต่จริงๆ แล้วมันถูกสร้างขึ้นมาเพื่อให้จัดการมันแบบข้อมูล ิbinary ตัวอย่างเช่น project ที่ถูกสร้างด้วย XCode บน Mac จะมีไฟล์ที่ชื่อ `.pbxproj` ซึ่งด้านในเก็บข้อมูลแบบ JSON (plain text javascript data format) ซึ่งคนที่สร้างคือตัว IDE เอง สำหรับกำหนดค่า setting ในโปรแกรม ในทางเทคนิคแล้วนี่ถือว่าเป็น text file เพราะข้อมูลถูกเก็บด้วย ASCII และสามารถอ่านได้ แต่เราไม่จัดการไฟล์นี้แบบ text file เรามองไฟล์นี้เป็นเหมือน database ขนาดเล็ก เราไม่สามารถ merge ไฟล์นี้ได้ถ้ามีคนสองคนแก้มัน และการใช้ diff ก็ไม่ได้ช่วยอะไรมากนัก หมายวามว่าไฟล์นี้ควรถูกจัดการด้วยคอมพิวเตอร์ หรือเรียกว่าเราควรจัดการไฟล์นี้แบบ binary file

การบอก Git ให้จัดการไฟล์ `pbxproj` แบบไฟล์ที่มีข้อมูลเป็น binary สามารถทำได้โด้ยการเพิ่มบรรทัดนี้ลงใน `.gitattributes`

	*.pbxproj -crlf -diff

หลังจากนี้ Git จะไม่พยายามแก้ปัญหา carriage-return ให้เรา หรือไม่พยายามดูความแตกต่างในไฟล์ เมื่อเราสั่ง git diff แต่ใน Git เวอร์ชัน 1.6 เราสามารถใช้คำสั่งสั้นๆ แทนการใช้ `-crlf -diff` ได้ ด้วยคำสั่งนี้

	*.pbxproj binary

#### Diffing Binary Files ####

ใน Git เวอร์ชัน 1.6 เราสามารถใช้ Git attributes ในการจัดการเปรียบเทียบ binary ไฟล์ได้ วิธีการคือเราต้องบอก Git ว่าจะแปลง binary data ให้เป็น text แบบไหน ก่อนที่จะใช้เปรียบเทียบโดยใช้ diff

เพราะว่านี้เรื่องที่เท่มากๆ แต่คนทั่วไปกลับไม่ค่อยรู้จักกัน ดังนั้นผมจะลงไปในรายละเอียดให้มากหน่อย เริ่มต้นจากเราจะลองใช้เทคนิคนี้แก้ปัญหาในการทำ version-controlling ให้ Word document กัน แม้ว่าเราจะรู้กันว่าใช้ Word เป็น text editer นั้นสยองเอามากๆ แต่ทุกคนก็ใช้กัน แล้วถ้าเราต้องการทำ version-control กับเอกสาร Word ล่ะ แน่นอนว่าเราสามารถส่งไฟล์ Word ขึ้น Git ได้ แต่มันจะดีหรือถ้าเราสั่ง `git diff` แล้วมันแสดงแบบนี้

	$ git diff 
	diff --git a/chapter1.doc b/chapter1.doc
	index 88839c4..4afcb7c 100644
	Binary files a/chapter1.doc and b/chapter1.doc differ

เราไม่สามารถเปรียบเทียบไฟล์สองเวอร์ชั้นได้ตรงๆ ยกเว้นจะ download มาทั้งสองตัวแล้วเปิดเทียบกันด้วยตัวเอง ทีนี้ลองมาดูว่า Git attribute จะช่วยเราได้อย่างไร เริ่มจากทดลองใส่บรรทัดต่อไปนี้ลงในไฟล์ `.gitattributes` 

	*.doc diff=word

บรรทัดนี้จะบอก Git ว่าไฟล์ที่ตรงกับรูปแบบ (.doc) ให้ใช้ "word" เป็นตัวกรองตอนที่ต้องการใช้ diff ในการดูความเปลี่ยนแปลง ไม่ต้องสงสัยว่า Git ทำ "word" ให้ เราต้องทำมันขึ้นมาเองครับ สิ่งที่ต้องทำคือใช้โปรแกรม `string` ในการแปลงเอกสาร Word ให้เป็น text file ที่อ่านได้ ซึ่งช่วยให้ diff สามารถทำการเปรียบเทียบได้ไปด้วย

	$ git config diff.word.textconv strings

ตอนนี้ Git รู้แล้วว่าถ้าเราพยายาจะเปรียบเทียบไฟล์ที่ลงท้ายด้วย `.doc` สองเวอร์ชัน จะต้องเปรียบเทียบโดยใช้ "word" ในการกรอง โดยมันจะไปใช้โปรแกรม `strings` อีกที วิธีการนี้จะทำให้เราได้ text file อย่างดี สำหรับให้ Git เอาไปใช้ในการเปรียบเทียบ

ตัวอย่างต่อไปนี้ ผมจะใส่บทแรกฉบับภาษาอังกฤษของหนังสือบทนี้ลงใน Git จากนั้นจะใส่ข้อความเล็กน้อยลงแทรกลงไป หลังจากบันทึก ผมจะสั่ง `git diff` เพื่อดูความเปลี่ยนแปลง

	$ git diff
	diff --git a/chapter1.doc b/chapter1.doc
	index c1c8a0a..b93c9e4 100644
	--- a/chapter1.doc
	+++ b/chapter1.doc
	@@ -8,7 +8,8 @@ re going to cover Version Control Systems (VCS) and Git basics
	 re going to cover how to get it and set it up for the first time if you don
	 t already have it on your system.
	 In Chapter Two we will go over basic Git usage - how to use Git for the 80% 
	-s going on, modify stuff and contribute changes. If the book spontaneously 
	+s going on, modify stuff and contribute changes. If the book spontaneously 
	+Let's see if this works.

Git สามารถตรวจสอบความเปลี่ยนแปลงได้อย่างชัดเจน ประโยคที่ผมใส่ลงไปคือ "Let's see if this works" ซึ่งผลออกมาถูกต้อง แต่ก็ไม่ได้ถูกต้อง 100% นะครับ ยังมีประโยคที่จริงๆ ไม่ได้แก้ไขอะไรโผล่ออกมาด้วย คงต้องรอคนทำ Word-to-plain-text ดีๆ ออกมาก่อน อย่างไรก็ตามเจ้า `strings` ก็สามารถใช้ได้ทั้งน Mac และ Linux และยังสามารถแปลง binary file ได้หลาย format อีกด้วย ถือว่าใช้ได้ดีทีเดียว

ปัญหาที่น่าสนใจอีกอย่าคือการ diff ไฟล์รูปภาพ แต่เราคงไปเทียบรูปภาพกันตรงๆ ได้ยาก ที่พอทำได้คือใช้ Metadata พวก EXIF ที่ติดมากับภาพในการเปรียบเทียบ เราจะใช้โปรแกรม `exiftool` มาช่วยดึง metadata ออกมาจากรูปภาพ อย่างน้อยเราก็จะได้เห็นของเก่าเที่ยบกับของใหม่บ้าง

	$ echo '*.png diff=exif' >> .gitattributes
	$ git config diff.exif.textconv exiftool

ทดลองแทนที่รูปภาพเดิมด้วยภาพใหม่ จากนั้นสั่ง `git diff` แล้วดูผลลัพธ์

	diff --git a/image.png b/image.png
	index 88839c4..4afcb7c 100644
	--- a/image.png
	+++ b/image.png
	@@ -1,12 +1,12 @@
	 ExifTool Version Number         : 7.74
	-File Size                       : 70 kB
	-File Modification Date/Time     : 2009:04:21 07:02:45-07:00
	+File Size                       : 94 kB
	+File Modification Date/Time     : 2009:04:21 07:02:43-07:00
	 File Type                       : PNG
	 MIME Type                       : image/png
	-Image Width                     : 1058
	-Image Height                    : 889
	+Image Width                     : 1056
	+Image Height                    : 827
	 Bit Depth                       : 8
	 Color Type                      : RGB with Alpha

เราจะเห็นได้ชัดเจนว่าภาพมีการเปลี่ยนแปลงขนาด ความกว้างยาว เทียบกันทั้งภาพเก่าและภาพใหม่

### Keyword Expansion ###

ความสามารถในการทำ Keyword expansion เหมือนของ SVN- หรือ CVS-style นั้นถูกเรียกร้องโดยผู้ที่เคยใช้มาก่อน แต่ปัญหาคือระบบของ Git ไม่อนุญาติให้เราแก้ไขไฟล์ด้วยข้อมูลที่มากับการ commit หลังจากที่เราทำการ commit ไปแล้วเพราะ Git ได้ checksums ไปแล้ว อย่างไรก็ตาม เราสามารถใช้วิธีแทรก text เข้าไปใน file ตอนที่ checkout ก็ได้ จากนั้นก็ลบมันออกก่อนที่จะ commit เข้าไป โดยใช้เทคนิคของ Git attribute ในการทำ 

ขั้นแรกทดลอง ใส่ SHA-1 checksum เข้าไปแทนที่ `$Id$` ในไฟล์แบบอัตโนมัติ ถ้าเรากำหนดค่านี้เข้าไปในไฟล์หนึ่งหรือหลายไฟล์ ตอนที่เรา checkout ิbranch นี้ออกมา ตัว Git จะแทนที่ `$Id$` ด้วย SHA-1 สิ่งสำคัญคือต้องไม่ลืมว่า เราไม่ได้ commit SHA เข้าไปแต่ส่งเข้าไปเฉพาะ blob (Binary Large OBjects) ของมัน

	$ echo '*.txt ident' >> .gitattributes
	$ echo '$Id$' > test.txt

หลังจากนี้ทุกครั้งที่ check out โปรแกรม Git จะใส่ SHA of the blob เข้าไป

	$ rm text.txt
	$ git checkout -- text.txt
	$ cat test.txt 
	$Id: 42812b7653c7b88933f8a9d6cad0ca16714b9bb3 $

จะเห็นว่าสิ่งที่ได้มาเราใช้ได้ค่อนข้างจำกัด ถ้าเราใช้ระบบ keword ใน CVS หรือ Subversion เราสามารถใส่ datestamp ลงไปได้เลย เทียบกับ SHA จะไม่ได้ช่วยอะไรมากนัก เพราะเราคงบอกอะไรไม่ได้ว่า SHA ตัวนี้เก่าหรือใหม่กว่าอีกตัวนึง

สรุปได้ว่าเราควรเขียน filter ขึ้นมาเองสำหรับการแทนค่าอะไรก็ตามในไฟล์ตอนที่ commit/checkout โดยอาศัย "clean" และ "smudge" ในไฟล์ `.gitattributes` เราสามารถระบุส่วนที่ต้องการแก้ไขลงไป จากนั้น script จะทำการแก้ไขไฟล์ก่อน checkout ("smudge" ตามรูป 7-2) และก่อน commit ("clean", ตามรูป 7-3) เราสามารถเล่นอะไรหลายๆ อย่างได้ด้วยวิธีการนี้

Insert 18333fig0702.png 
Figure 7-2. The "smudge" filter is run on checkout.

Insert 18333fig0703.png 
Figure 7-3. The "clean" filter is run when files are staged.

ตัวอย่างต่อไปเป็นการส่ง C source code เข้าโปรแกรม `indent` ก่อนที่จะ commit โดยเราจะเพิ่ม filter attribute ไปในไฟล์ `.gitattributes` เพื่อคัด `*.c` มาใส่โปรแกรม "indent" 

	*.c     filter=indent

จากนั้น ให้กำหนด "indent" สำหรับ smudge และ clean

	$ git config --global filter.indent.clean indent
	$ git config --global filter.indent.smudge cat

ตามตัวอย่างข้างต้น เมื่อเรา commit ไฟล์ที่เป็น `*.c` โปรแกรม Git จะส่งไฟล์นั้นไปให้โปรแกรม `indent` ก่อนที่จะ commit เข้าไป และจะส่งมันให้กับโปรแกรม `cat` ก่อนที่จะ checkout ทั้งนี้โปรแกรม `cat` จะไม่ได้เพิ่มหรือลดอะไรในไฟล์ถ้าส่งข้อมูลอะไรให้ มันก็จะส่งกลับออกมาแบบเดียวกัน ถ้าทำแบบนี้จะทำให้ C source code ของเราถูกจัด indent ทุกครั้งก่อนที่จะ commit เข้าไป

ตัวอย่างอีกอันที่น่าสนใจคือการเลียนแบบ `$Date$` keyword expansion วิธีการคือเราจะเขียน scrite ขึ้นมาตัวนึงสำหรับเลือกไฟล์ที่เราต้องการแล้วนำไฟล์นั้นมาแก้ไข เพื่อใส่วันที่ commit ล่าสุดของโครงการลงไป เราจะใช้ script ruby ต่อไปนี้

	#! /usr/bin/env ruby
	data = STDIN.read
	last_date = `git log --pretty=format:"%ad" -1`
	puts data.gsub('$Date$', '$Date: ' + last_date.to_s + '$')

สิ่งที่ script ตัวนี้ทำคือ เอาค่าวันที่สุดท้ายที่ commit จากคำสั่ง `git log` จากนั้นนำไปแทนที่ `$Date$` ในทุกไฟล์ที่เข้ามา หลังจากทำเสร็จแล้วก็ให้มันแสดงออกมา ซึ่ง Git จะนำค่าที่ได้ไปใช้ต่อ จริงๆ แล้วเราจะใช้ภาษาอะไรก็ได้แล้วแต่ถนัด สมติว่าเราตั้งชื่อไฟล์ว่า `expand_date` จากนั้นก็กำหนดค่า smudge ให้ไปชี้ที่ `expand_date` เพื่อให้แก้ไขค่าตอนที่สั่ง checkout และตอนที่เรา commit ก็ให้เปลี่ยนค่าที่เราใส่ลงไปให้กลับมาเป็น $Date$ เหมือนเดิมด้วยภาษา perl

	$ git config filter.dater.smudge expand_date
	$ git config filter.dater.clean 'perl -pe "s/\\\$Date[^\\\$]*\\\$/\\\$Date\\\$/"'

คำสั่งภาษา perl ที่เห็นนี้ จะเปลี่ยนค่าที่อยู่ใน `$Date$` ให้กลับมาเหมือนตอนเริ่มต้นอีกครั้ง เอาละตอนนี้ตัวจัดการก็สมบูรณ์แล้ว เราสามารถทดสอบได้ด้วยการใส่ไฟล์ที่มีคำว่า $Date$ ลงใน code ของเรา

	$ echo '# $Date$' > date_test.txt
	$ echo 'date*.txt filter=dater' >> .gitattributes

หลังจากที่เรา commit ค่าที่เราใส่ลงไป แล้วอลง check out ออกมาดู เราจะพบว่า keyword ของเราเปลี่ยนเป็นวันเวลาเรียบร้อยแล้ว

	$ git add date_test.txt .gitattributes
	$ git commit -m "Testing date expansion in Git"
	$ rm date_test.txt
	$ git checkout date_test.txt
	$ cat date_test.txt
	# $Date: Tue Apr 21 07:26:52 2009 -0700$

จะเห็นว่าเทคนิคนี้สามารถนำไปประยุกต์ใช้ได้มากมายในโปรแกรมของเรา แต่ที่ต้องระวังให้มากคือแม้ว่าไฟล์ `.gitattributes` จะถูกส่งขึ้น Git ไปกับ source code ของเราแต่ตัว driver (ในกรณีนี้คือ `dater`)ไม่ได้ถูกส่งไปด้วย ดังนั้นการ setup นี้จึงไม่ได้ทำงานได้ทุกที่ ดังนั้นถ้าเราเป็นคนออกแบบ filter เราจะต้องออกแบบให้แม้ว่า filter จะไม่ทำงาน ตัวโครงการก็ต้องสามารถทำงานได้ต่อไป

### Exporting Your Repository ###

เราสามารถตั้งค่าใน Git attribute เพื่อกำหนดรูปแบบเท่ๆ ในการ export project ของเราได้ (export เป็น zip หรือ tar.gz)

#### export-ignore ####

เราสามารถกำหนดได้ว่าจะไม่ export ไฟล์หรือ directory ใดบ้าง โดยกำหนดไปที่ค่า `export-ignore` ตัวอย่างเช่น ถ้าเราไม่ต้องการ export directory `test/` เพราะมันดูไม่เหมาะสมตอนที่คนต้องการ export เป็น tar/zip ไปใช้ เราสามารถกำหนดในไฟล์ Git attributes ดังนี้ 

	test/ export-ignore

เอาล่ะ ครั้งหน้าตอนที่สั่ง git archive เพื่อสร้าง tarball เราจะไม่เห็น directory ที่กำหนดไว้ในผลลัพธ์

#### export-subst ####

อีกอย่างที่เราสามารถทำได้ตอนที่สั่ง archive คือการทำ simple keyword substitution โปรแกรม Git ให้เราสามารถใส่ `$format:$` ไว้ในไฟล์ไหนก็ได้ ด้วย `--pretty=format` (ส่วนมากเราจะเห็นในบนที่สองแล้ว) ถ้าเราต้องการใส่ไฟล์ `LAST_COMMIT` ไว้ใน archive พร้อมกับวันที่เรา commit ครั้งสุดท้าย ตอนที่สั่ง `git archive` เราสามารถทำได้โดย

	$ echo 'Last commit date: $Format:%cd$' > LAST_COMMIT
	$ echo "LAST_COMMIT export-subst" >> .gitattributes
	$ git add LAST_COMMIT .gitattributes
	$ git commit -am 'adding LAST_COMMIT file for archives'

ทีนี้เมื่อเราสั่ง `git archive` ข้อมูลที่อยู่ในไฟล์ ตอนที่ผู้ใช้แตก archive ออกมาจะเป็นดังนี้

	$ cat LAST_COMMIT
	Last commit date: $Format:Tue Apr 21 08:38:48 2009 -0700$

### Merge Strategies ###

เราสามารถใช้ Git attritube ในการบอก Git ว่าเราจะใช้เทคนิคอะไรในการ Merge (Merge Strategies) ในแต่ละไฟล์ของโครงการ ตัวอย่างที่ใช้บ่อยๆ เช่นเราจะบอก git ว่าบางไฟล์ ถ้าเจอ conflicts ไม่ต้องทำการ merge แต่ให้เลือกใช้ไฟล์ใดไฟล์หนึ่งไปเลย

การกำหนด merge strategies จะค่อนข้างใช้บ่อยในโครงการที่แตกออกมาจากโครงการอื่นเพราะมีความต้องการเฉพาะ แต่เราตอ้งการ merget กลับไปให้โครงการหลัก ทำให้เราไม่ต้องการ merge ในบางไฟล์ ยกตัวอย่างเช่นไฟล์ database.xml ซึ่งจะต่างกันในแต่ละ branch และเราไม่้ต้องการ Merge มันเข้าดัวยกัน เราสามารถกำหนดค่าได้ดังนี้ 

	database.xml merge=ours

เมื่อเรา merge ไปยัง branch อื่น แทนที่จะเกิด conflicts ในไฟล์ database.xml เราจะเห็นข้อความดังนี้

	$ git merge topic
	Auto-merging database.xml
	Merge made by recursive.

ในกรณีนี้ database.xml จะไม่เคยเปลี่ยนไปจากต้นฉบับเลย

## Git Hooks ##

เหมือนกับ Version Control System อื่นๆ Git เปิดช่องให้เราเขียน Script เพื่อเข้าไปปรับแต่งการทำงานได้ การปรับแต่งจะมีอยู่สองกลุ่มคือ การปรับแต่งที่ client หรือการปรับแต่งที่เครื่องของเรา อีกแบบคือการปรับแต่งที่ server การปรับแต่งที่ฝั่ง client จะเข้าไปจัดการคำสั่งอย่างการ commit และการ merge ส่วนการจัดการในฝั่ง server จะเข้าไปจัดการคำสั่งอย่างการ receive push เป็นต้น เราสามารถ hook ได้ในหลายกรณี ซึ่งจะได้เรียนกันต่อไป

### Installing a Hook ###

ไฟล์สำหรับการ hook จะอยู่ใน folder `hooks` ซึ่งอยู่ใน repository ของเรา โดยส่วนมากจะอยู่ใน `.git/hooks` ถ้าเปิดดูเราจะเห็นไฟล์ตัวอย่างหลายตัวอยู่ในนั้น เราควรจะลองเปิดอ่านดูเพราะเป็นตัวอย่างที่มีประโยชน์ มีคำอธิบายอยู่ในนั้นด้วย ตัวอย่างทั้งหมดเขียนด้วย shell script บางตัวเป็น perl แต่จริงๆ เราจะใช้อะไรเขียนก็ได้ จะเป็น python หรืออะไรก็ได้ที่ชอบ สำหรับ Git หลัง version 1.6 ไฟล์ที่อยู่ใน hooks จะมีชื่อต่อท้ายด้วย .sample ซึ่งเราต้องตัดออกก่อนจะนำไปใช้ ส่วน Git ก่อน version 1.6 จะมีชื่อถูกต้องอยู่แล้ว แต่มันจะยังไม่ทำงาน

การเปิดใช้ hook script ทำได้โดยการใส่ไฟล์ไว้ใน `hooks` folder ของ repository ของเรา ตั้งชื่อให้ตรงตามตัวอย่าง ส่วนมันจะถูกเรียกตอนไหนเดี๋ยวเราค่อยมาว่ากันต่อ

### Client-Side Hooks ###

เราสามารถเล่นกับ client-side hook ได้มากทีเดียว ในบทนี้เราจะแยกอธิบายดังนี้ committing-workflow hooks, e-mail-workflow scripts และ ส่วนที่เหลือ

#### Committing-Workflow Hooks ####

มี hook อยู่สี่ตัวสำหรับส่วนของ committing process โดย `pre-commit` จะถูกเรียกเป็นตัวแรก โดยมันจะถูกเรียกก่อนที่เราจะใส่ commit message ส่วนมากจะใช้ตรวจสอบว่าพร้อมจะ commit หรือยัง บางทีเราอาจจะลืมบางอย่าง หรือให้แน่ใจว่าเรา run test แล้ว หรืออะไรก็ตามที่เราต้องการตรวจสอบ code ของเราก่อน ถ้า hook ของเราจบด้วย non-zero จะเป็นการยกเลิกการ commit อย่างไรก็ตามถ้าเราต้องการจะ commit โดยไม่ต้องตรวจสอบ ก็ให้สั่ง `git commit --no-verify` เราสามารถตรวจสอบ code style ว่าถูกต้องหรือไม่ สามารถจัดการ whitespace ก่อน commit (ลองดูในตัวอย่าง) หรืออาจจะใช้ตรวจสอบความถูกต้องของคำอธิบายประกอบ methods ที่สร้างขึ้นมาใหม่ก็ได้

อันต่อมาคือ `prepare-commit-msg` จะถูกเรียกให้ทำงานก่อน commit message editor จะขึ้นมา แต่ก็จะทำงานหลังจากมีการสร้าง default message แล้ว มันช่วยให้เราเข้าไปแก้ไข default message ได้ ก่อนที่เราจะแก้ไข โดยจะมี option ให้เราเล่นมากกว่า hook แรก เช่น path ที่อยู่ของไฟล์ที่ใส่ commit message, ชนิดของการ commit และ commit SHA-1 ถ้าเป็น amended commit เป็นต้น โดยทั่วไป hook นี้จะไม่ค่อยมีประโยชน์สำหรับการ commit โดยทั่วไป แต่มันจะดีมากกๆ ถ้าใช้กับ commit ที่มี default message ถูกสร้างขึ้นมาอัตโนมัติ เช่น กรณีที่เป็น templat commit message หรือ merge commits หรือ squashed commits และ amended commit เป็นต้น เราอาจจะใช้มันร่วมกับ commit template เพื่อใส่ข้อมูลเพิ่มเติมก็ได้

`commit-msg` ้hook จะถูกเรียกหลังจากแก้ไข commit message แล้ว โดยจะมี parameter แค่ตัวเดียวคือ path ไปยัง temporary file ที่ใส่ commit message ไว้ ถ้า script นี้จบด้วย non-zero โปรแกรม Git จะยกเลิกกระบวนการ commit ทันที ดังนั้นเราจึงมักใช้มันในการตรวจสอบ commit message ก่อนที่จะอนุญาติให้ดำเนินการ commit ต่อไป ในส่วนสุดท้ายของบทนี้จะมีตัวอย่างการใช้งาน hook นี้ให้ดูอีกที

หลังจากที่กระบวนการ commit เสร็จเรียบร้อยแล้ว `post-commit` จะถูกเรียกขึ้นมา script ตัวนี้จะไม่ได้รับ parameter ใดๆ แต่ถ้าเราอยากดู commit สุดท้ายก็แค่สั่ง `git log -1 HEAD` ส่วนมากแล้ว script นี้จะเอาไว้แจ้งเตือนเป็นหลัก

สำหรับ script committing-workflow ในผั่ง client เราสามารถใช้มันในการควบคุมให้ code หรืออะไรก็ตามให้เป็นไปตาม policy ที่กำหนดไว้ได้ อย่างไรก็ตามควรจำไว้ว่า script ที่สร้างขึ้นจะไม่ถูกนำไปด้วย ในกรณีของการ clone ดังนั้นถ้าต้องการควบคุมให้เป็นไปตาม policy เราควรจะกำหนดไว้ที่ผั่ง server ว่าให้บอกปัดทุก push ของ commits ที่ไม่เป็นไปตาม policy ที่วางเอาไว้ และยกให้เป็นเรื่องของ developer ว่าจะกำหนด script ตัวนี้ไว้ที่ผั่ง client หรือไม่ จะเห็นว่า script ตัวนี้จะเอาไว้ช่วยงานกับผู้พัฒนาเป็นหลัก ดังนั้นการดูแติดตั้งจึงควรเป็นเรื่องของตัวผู้พัฒนา และเขาก็มีสิทธิ์ที่จะปรับแต่งแก้ไขด้วยตัวเองตามความต้องการ

#### E-mail Workflow Hooks ####

Git เปิดให้เรา hook ได้สามที่สำหรับการทำ e-mail-base workflow โดย hook ของเราจะถูกเรียกโดยคำสั่ง `git am` ดังนั้นถ้าใครไม่ได้ใช้คำสั่งนี้ใน workflow ก็สามารถอ่านข้ามส่วนนี้ไปได้เลย แต่ใครที่ยังส่ง patches ด้วย e-mail และใช้คำสั่ง `git fotmat-patch` ขอให้ลองอ่านดู บางทีอาจจะช่วยคุณได้มากทีเดียว

hook ตัวแรกคือ `applypatch-msg` จะรับหนึ่ง argument คือชื่อของ temporary file ที่เก็บ commit message ในกรณีนี้ Git จะยกเลิก patch นี้ถ้าเราจบ script ด้วย non-zero เราสามารถใช้ hook ตัวนี้สำหรับตรวจสอบ commit message ว่าถูกต้องตามรูปแบบที่กำหนดหรือไม่ หรืออาจจะใช้ในการปรับแต่งข้อความก่อนก็ได้

hook ตัวต่อไปจะทำงานเมื่อ apply patches ผ่าน `git am` หรือก็คือ `pre-applypatch` โดยไม่รับ argument ใดๆ และทำงานหลังจากที่ patch ถูก apply ไปแล้ว ดังนั้นเราจะใช้มันในการตรวจสอบ snapshot ก่อนที่จะทำการ commit เช่น เราสามารถสั่ง run test ก่อนได้ ถ้าไม่ผ่านก็ให้จบ script ด้วย non-zero จะทำให้ patch นั้นไม่ถูก commit เข้าไป

hook ตัวสุดท้ายจะทำงานระหว่างที่คำสั่ง `git am` หรือ `post-applypatch` กำลังทำงานอยู่ เราสามารถใช้มันเพื่อแจ้งให้คนในกลุ่ม หรือเจ้าของ patch ทราบว่าเรากำลัง pull สิ่งที่คุณทำอยู่ เป็นต้น เราไม่สามารถใช้ hook ตัวนี้ในการหยุด patching process ได้

#### Client Hooks อื่นๆ ####

ไฟล์ `pre-rebase` ้เป็น hook ที่ทำงานก่อนที่ rebase จะทำงาน และเราสามารถหยุด process ได้โดยการจบ script ด้วย non-zero เราสามารถใช้ hook ตัวนี้เพื่อไม่ให้เกิดการ rebase commit ใดๆ ที่ถูก push ไปแล้ว ดูตัวอย่างวิธีการเขียนได้ในไฟล์ตัวอย่าง `pre-rebase` ใน hooks directory อย่างไรก็ตามตัว script จะใช้คำว่า "next" เป็นชื่อของ branch ที่เราจะใช้ในการ publish ถ้าต้องการใช้ branch ชื่ออื่น ก็ให้เข้าไปเปลี่ยนเป็นชื่อ branch ที่เราคิดว่าจะใช้ในการ publish

หลังจากที่คำสั่ง `git checkout` ทำงานเสร็จแล้ว hook ที่ชื่อ `post-checkout` จะเริ่มทำงาน เราสามารถใช้ hook นี้ในการตั้งค่าให้ working directory ของเราได้ นั่นหมายความว่าเราสามารถจัดการโยกย้ายไฟล์ binary ขนาดใหญ่ๆ ที่เราไม่ต้องการให้อยู่ใน code เข้ามาได้ หรือจะเป็นพวก auto-generating document หรืออะไรได้ตามแต่จะสะดวก

สุดท้ายคือ `post-merge` hook ซึ่งจะทำงานเมื่อ `merge` เสร็จ เราสามารถใช้มันเพื่อดึงข้อมูลซึ่ง Git ไม่ได้ track เอาไว้ กลับมาได้ เช่น permission data เป็นต้น นอกจากนั้นเรายังสามารถใช้ hook ตัวนี้ในการ copy สิ่งที่อยู่นอก working directory เข้ามาแบบเดียวกับ `post-checkout` ได้ด้วย

### Server-Side Hooks ###

In addition to the client-side hooks, you can use a couple of important server-side hooks as a system administrator to enforce nearly any kind of policy for your project. These scripts run before and after pushes to the server. The pre hooks can exit non-zero at any time to reject the push as well as print an error message back to the client; you can set up a push policy that's as complex as you wish.

#### pre-receive and post-receive ####

The first script to run when handling a push from a client is `pre-receive`. It takes a list of references that are being pushed from stdin; if it exits non-zero, none of them are accepted. You can use this hook to do things like make sure none of the updated references are non-fast-forwards; or to check that the user doing the pushing has create, delete, or push access or access to push updates to all the files they're modifying with the push.

The `post-receive` hook runs after the entire process is completed and can be used to update other services or notify users. It takes the same stdin data as the `pre-receive` hook. Examples include e-mailing a list, notifying a continuous integration server, or updating a ticket-tracking system ‚Äî you can even parse the commit messages to see if any tickets need to be opened, modified, or closed. This script can't stop the push process, but the client doesn't disconnect until it has completed; so, be careful when you try to do anything that may take a long time.

#### update ####

The update script is very similar to the `pre-receive` script, except that it's run once for each branch the pusher is trying to update. If the pusher is trying to push to multiple branches, `pre-receive` runs only once, whereas update runs once per branch they're pushing to. Instead of reading from stdin, this script takes three arguments: the name of the reference (branch), the SHA-1 that reference pointed to before the push, and the SHA-1 the user is trying to push. If the update script exits non-zero, only that reference is rejected; other references can still be updated.

## An Example Git-Enforced Policy ##

In this section, you'll use what you've learned to establish a Git workflow that checks for a custom commit message format, enforces fast-forward-only pushes, and allows only certain users to modify certain subdirectories in a project. You'll build client scripts that help the developer know if their push will be rejected and server scripts that actually enforce the policies.

I used Ruby to write these, both because it's my preferred scripting language and because I feel it's the most pseudocode-looking of the scripting languages; thus you should be able to roughly follow the code even if you don't use Ruby. However, any language will work fine. All the sample hook scripts distributed with Git are in either Perl or Bash scripting, so you can also see plenty of examples of hooks in those languages by looking at the samples.

### Server-Side Hook ###

All the server-side work will go into the update file in your hooks directory. The update file runs once per branch being pushed and takes the reference being pushed to, the old revision where that branch was, and the new revision being pushed. You also have access to the user doing the pushing if the push is being run over SSH. If you've allowed everyone to connect with a single user (like "git") via public-key authentication, you may have to give that user a shell wrapper that determines which user is connecting based on the public key, and set an environment variable specifying that user. Here I assume the connecting user is in the `$USER` environment variable, so your update script begins by gathering all the information you need:

	#!/usr/bin/env ruby

	$refname = ARGV[0]
	$oldrev  = ARGV[1]
	$newrev  = ARGV[2]
	$user    = ENV['USER']

	puts "Enforcing Policies... \n(#{$refname}) (#{$oldrev[0,6]}) (#{$newrev[0,6]})"

Yes, I'm using global variables. Don't judge me ‚Äî it's easier to demonstrate in this manner.

#### Enforcing a Specific Commit-Message Format ####

Your first challenge is to enforce that each commit message must adhere to a particular format. Just to have a target, assume that each message has to include a string that looks like "ref: 1234" because you want each commit to link to a work item in your ticketing system. You must look at each commit being pushed up, see if that string is in the commit message, and, if the string is absent from any of the commits, exit non-zero so the push is rejected.

You can get a list of the SHA-1 values of all the commits that are being pushed by taking the `$newrev` and `$oldrev` values and passing them to a Git plumbing command called `git rev-list`. This is basically the `git log` command, but by default it prints out only the SHA-1 values and no other information. So, to get a list of all the commit SHAs introduced between one commit SHA and another, you can run something like this:

	$ git rev-list 538c33..d14fc7
	d14fc7c847ab946ec39590d87783c69b031bdfb7
	9f585da4401b0a3999e84113824d15245c13f0be
	234071a1be950e2a8d078e6141f5cd20c1e61ad3
	dfa04c9ef3d5197182f13fb5b9b1fb7717d2222a
	17716ec0f1ff5c77eff40b7fe912f9f6cfd0e475

You can take that output, loop through each of those commit SHAs, grab the message for it, and test that message against a regular expression that looks for a pattern.

You have to figure out how to get the commit message from each of these commits to test. To get the raw commit data, you can use another plumbing command called `git cat-file`. I'll go over all these plumbing commands in detail in Chapter 9; but for now, here's what that command gives you:

	$ git cat-file commit ca82a6
	tree cfda3bf379e4f8dba8717dee55aab78aef7f4daf
	parent 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
	author Scott Chacon <schacon@gmail.com> 1205815931 -0700
	committer Scott Chacon <schacon@gmail.com> 1240030591 -0700

	changed the version number

A simple way to get the commit message from a commit when you have the SHA-1 value is to go to the first blank line and take everything after that. You can do so with the `sed` command on Unix systems:

	$ git cat-file commit ca82a6 | sed '1,/^$/d'
	changed the version number

You can use that incantation to grab the commit message from each commit that is trying to be pushed and exit if you see anything that doesn't match. To exit the script and reject the push, exit non-zero. The whole method looks like this:

	$regex = /\[ref: (\d+)\]/

	# enforced custom commit message format
	def check_message_format
	  missed_revs = `git rev-list #{$oldrev}..#{$newrev}`.split("\n")
	  missed_revs.each do |rev|
	    message = `git cat-file commit #{rev} | sed '1,/^$/d'`
	    if !$regex.match(message)
	      puts "[POLICY] Your message is not formatted correctly"
	      exit 1
	    end
	  end
	end
	check_message_format

Putting that in your `update` script will reject updates that contain commits that have messages that don't adhere to your rule.

#### Enforcing a User-Based ACL System ####

Suppose you want to add a mechanism that uses an access control list (ACL) that specifies which users are allowed to push changes to which parts of your projects. Some people have full access, and others only have access to push changes to certain subdirectories or specific files. To enforce this, you'll write those rules to a file named `acl` that lives in your bare Git repository on the server. You'll have the `update` hook look at those rules, see what files are being introduced for all the commits being pushed, and determine whether the user doing the push has access to update all those files.

The first thing you'll do is write your ACL. Here you'll use a format very much like the CVS ACL mechanism: it uses a series of lines, where the first field is `avail` or `unavail`, the next field is a comma-delimited list of the users to which the rule applies, and the last field is the path to which the rule applies (blank meaning open access). All of these fields are delimited by a pipe (`|`) character.

In this case, you have a couple of administrators, some documentation writers with access to the `doc` directory, and one developer who only has access to the `lib` and `tests` directories, and your ACL file looks like this:

	avail|nickh,pjhyett,defunkt,tpw
	avail|usinclair,cdickens,ebronte|doc
	avail|schacon|lib
	avail|schacon|tests

You begin by reading this data into a structure that you can use. In this case, to keep the example simple, you'll only enforce the `avail` directives. Here is a method that gives you an associative array where the key is the user name and the value is an array of paths to which the user has write access:

	def get_acl_access_data(acl_file)
	  # read in ACL data
	  acl_file = File.read(acl_file).split("\n").reject { |line| line == '' }
	  access = {}
	  acl_file.each do |line|
	    avail, users, path = line.split('|')
	    next unless avail == 'avail'
	    users.split(',').each do |user|
	      access[user] ||= []
	      access[user] << path
	    end
	  end
	  access
	end

On the ACL file you looked at earlier, this `get_acl_access_data` method returns a data structure that looks like this:

	{"defunkt"=>[nil],
	 "tpw"=>[nil],
	 "nickh"=>[nil],
	 "pjhyett"=>[nil],
	 "schacon"=>["lib", "tests"],
	 "cdickens"=>["doc"],
	 "usinclair"=>["doc"],
	 "ebronte"=>["doc"]}

Now that you have the permissions sorted out, you need to determine what paths the commits being pushed have modified, so you can make sure the user who's pushing has access to all of them.

You can pretty easily see what files have been modified in a single commit with the `--name-only` option to the `git log` command (mentioned briefly in Chapter 2):

	$ git log -1 --name-only --pretty=format:'' 9f585d

	README
	lib/test.rb

If you use the ACL structure returned from the `get_acl_access_data` method and check it against the listed files in each of the commits, you can determine whether the user has access to push all of their commits:

	# only allows certain users to modify certain subdirectories in a project
	def check_directory_perms
	  access = get_acl_access_data('acl')

	  # see if anyone is trying to push something they can't
	  new_commits = `git rev-list #{$oldrev}..#{$newrev}`.split("\n")
	  new_commits.each do |rev|
	    files_modified = `git log -1 --name-only --pretty=format:'' #{rev}`.split("\n")
	    files_modified.each do |path|
	      next if path.size == 0
	      has_file_access = false
	      access[$user].each do |access_path|
	        if !access_path  # user has access to everything
	          || (path.index(access_path) == 0) # access to this path
	          has_file_access = true 
	        end
	      end
	      if !has_file_access
	        puts "[POLICY] You do not have access to push to #{path}"
	        exit 1
	      end
	    end
	  end  
	end

	check_directory_perms

Most of that should be easy to follow. You get a list of new commits being pushed to your server with `git rev-list`. Then, for each of those, you find which files are modified and make sure the user who's pushing has access to all the paths being modified. One Rubyism that may not be clear is `path.index(access_path) == 0`, which is true if path begins with `access_path` ‚Äî this ensures that `access_path` is not just in one of the allowed paths, but an allowed path begins with each accessed path. 

Now your users can't push any commits with badly formed messages or with modified files outside of their designated paths.

#### Enforcing Fast-Forward-Only Pushes ####

The only thing left is to enforce fast-forward-only pushes. In Git versions 1.6 or newer, you can set the `receive.denyDeletes` and `receive.denyNonFastForwards` settings. But enforcing this with a hook will work in older versions of Git, and you can modify it to do so only for certain users or whatever else you come up with later.

The logic for checking this is to see if any commits are reachable from the older revision that aren't reachable from the newer one. If there are none, then it was a fast-forward push; otherwise, you deny it:

	# enforces fast-forward only pushes 
	def check_fast_forward
	  missed_refs = `git rev-list #{$newrev}..#{$oldrev}`
	  missed_ref_count = missed_refs.split("\n").size
	  if missed_ref_count > 0
	    puts "[POLICY] Cannot push a non fast-forward reference"
	    exit 1
	  end
	end

	check_fast_forward

Everything is set up. If you run `chmod u+x .git/hooks/update`, which is the file you into which you should have put all this code, and then try to push a non-fast-forwarded reference, you get something like this:

	$ git push -f origin master
	Counting objects: 5, done.
	Compressing objects: 100% (3/3), done.
	Writing objects: 100% (3/3), 323 bytes, done.
	Total 3 (delta 1), reused 0 (delta 0)
	Unpacking objects: 100% (3/3), done.
	Enforcing Policies... 
	(refs/heads/master) (8338c5) (c5b616)
	[POLICY] Cannot push a non-fast-forward reference
	error: hooks/update exited with error code 1
	error: hook declined to update refs/heads/master
	To git@gitserver:project.git
	 ! [remote rejected] master -> master (hook declined)
	error: failed to push some refs to 'git@gitserver:project.git'

There are a couple of interesting things here. First, you see this where the hook starts running.

	Enforcing Policies... 
	(refs/heads/master) (fb8c72) (c56860)

Notice that you printed that out to stdout at the very beginning of your update script. It's important to note that anything your script prints to stdout will be transferred to the client.

The next thing you'll notice is the error message.

	[POLICY] Cannot push a non fast-forward reference
	error: hooks/update exited with error code 1
	error: hook declined to update refs/heads/master

The first line was printed out by you, the other two were Git telling you that the update script exited non-zero and that is what is declining your push. Lastly, you have this:

	To git@gitserver:project.git
	 ! [remote rejected] master -> master (hook declined)
	error: failed to push some refs to 'git@gitserver:project.git'

You'll see a remote rejected message for each reference that your hook declined, and it tells you that it was declined specifically because of a hook failure.

Furthermore, if the ref marker isn't there in any of your commits, you'll see the error message you're printing out for that.

	[POLICY] Your message is not formatted correctly

Or if someone tries to edit a file they don't have access to and push a commit containing it, they will see something similar. For instance, if a documentation author tries to push a commit modifying something in the `lib` directory, they see

	[POLICY] You do not have access to push to lib/test.rb

That's all. From now on, as long as that `update` script is there and executable, your repository will never be rewound and will never have a commit message without your pattern in it, and your users will be sandboxed.

### Client-Side Hooks ###

The downside to this approach is the whining that will inevitably result when your users' commit pushes are rejected. Having their carefully crafted work rejected at the last minute can be extremely frustrating and confusing; and furthermore, they will have to edit their history to correct it, which isn't always for the faint of heart.

The answer to this dilemma is to provide some client-side hooks that users can use to notify them when they're doing something that the server is likely to reject. That way, they can correct any problems before committing and before those issues become more difficult to fix. Because hooks aren't transferred with a clone of a project, you must distribute these scripts some other way and then have your users copy them to their `.git/hooks` directory and make them executable. You can distribute these hooks within the project or in a separate project, but there is no way to set them up automatically.

To begin, you should check your commit message just before each commit is recorded, so you know the server won't reject your changes due to badly formatted commit messages. To do this, you can add the `commit-msg` hook. If you have it read the message from the file passed as the first argument and compare that to the pattern, you can force Git to abort the commit if there is no match:

	#!/usr/bin/env ruby
	message_file = ARGV[0]
	message = File.read(message_file)

	$regex = /\[ref: (\d+)\]/

	if !$regex.match(message)
	  puts "[POLICY] Your message is not formatted correctly"
	  exit 1
	end

If that script is in place (in `.git/hooks/commit-msg`) and executable, and you commit with a message that isn't properly formatted, you see this:

	$ git commit -am 'test'
	[POLICY] Your message is not formatted correctly

No commit was completed in that instance. However, if your message contains the proper pattern, Git allows you to commit:

	$ git commit -am 'test [ref: 132]'
	[master e05c914] test [ref: 132]
	 1 files changed, 1 insertions(+), 0 deletions(-)

Next, you want to make sure you aren't modifying files that are outside your ACL scope. If your project's `.git` directory contains a copy of the ACL file you used previously, then the following `pre-commit` script will enforce those constraints for you:

	#!/usr/bin/env ruby

	$user    = ENV['USER']

	# [ insert acl_access_data method from above ]

	# only allows certain users to modify certain subdirectories in a project
	def check_directory_perms
	  access = get_acl_access_data('.git/acl')

	  files_modified = `git diff-index --cached --name-only HEAD`.split("\n")
	  files_modified.each do |path|
	    next if path.size == 0
	    has_file_access = false
	    access[$user].each do |access_path|
	    if !access_path || (path.index(access_path) == 0)
	      has_file_access = true
	    end
	    if !has_file_access
	      puts "[POLICY] You do not have access to push to #{path}"
	      exit 1
	    end
	  end
	end

	check_directory_perms

This is roughly the same script as the server-side part, but with two important differences. First, the ACL file is in a different place, because this script runs from your working directory, not from your Git directory. You have to change the path to the ACL file from this

	access = get_acl_access_data('acl')

to this:

	access = get_acl_access_data('.git/acl')

The other important difference is the way you get a listing of the files that have been changed. Because the server-side method looks at the log of commits, and, at this point, the commit hasn't been recorded yet, you must get your file listing from the staging area instead. Instead of

	files_modified = `git log -1 --name-only --pretty=format:'' #{ref}`

you have to use

	files_modified = `git diff-index --cached --name-only HEAD`

But those are the only two differences ‚Äî otherwise, the script works the same way. One caveat is that it expects you to be running locally as the same user you push as to the remote machine. If that is different, you must set the `$user` variable manually.

The last thing you have to do is check that you're not trying to push non-fast-forwarded references, but that is a bit less common. To get a reference that isn't a fast-forward, you either have to rebase past a commit you've already pushed up or try pushing a different local branch up to the same remote branch.

Because the server will tell you that you can't push a non-fast-forward anyway, and the hook prevents forced pushes, the only accidental thing you can try to catch is rebasing commits that have already been pushed.

Here is an example pre-rebase script that checks for that. It gets a list of all the commits you're about to rewrite and checks whether they exist in any of your remote references. If it sees one that is reachable from one of your remote references, it aborts the rebase:

	#!/usr/bin/env ruby

	base_branch = ARGV[0]
	if ARGV[1]
	  topic_branch = ARGV[1]
	else
	  topic_branch = "HEAD"
	end

	target_shas = `git rev-list #{base_branch}..#{topic_branch}`.split("\n")
	remote_refs = `git branch -r`.split("\n").map { |r| r.strip }

	target_shas.each do |sha|
	  remote_refs.each do |remote_ref|
	    shas_pushed = `git rev-list ^#{sha}^@ refs/remotes/#{remote_ref}`
	    if shas_pushed.split("\n").include?(sha)
	      puts "[POLICY] Commit #{sha} has already been pushed to #{remote_ref}"
	      exit 1
	    end
	  end
	end

This script uses a syntax that wasn't covered in the Revision Selection section of Chapter 6. You get a list of commits that have already been pushed up by running this:

	git rev-list ^#{sha}^@ refs/remotes/#{remote_ref}

The `SHA^@` syntax resolves to all the parents of that commit. You're looking for any commit that is reachable from the last commit on the remote and that isn't reachable from any parent of any of the SHAs you're trying to push up ‚Äî meaning it's a fast-forward.

The main drawback to this approach is that it can be very slow and is often unnecessary ‚Äî if you don't try to force the push with `-f`, the server will warn you and not accept the push. However, it's an interesting exercise and can in theory help you avoid a rebase that you might later have to go back and fix.

## Summary ##

You've covered most of the major ways that you can customize your Git client and server to best fit your workflow and projects. You've learned about all sorts of configuration settings, file-based attributes, and event hooks, and you've built an example policy-enforcing server. You should now be able to make Git fit nearly any workflow you can dream up.
