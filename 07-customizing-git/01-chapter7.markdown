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

นอกจากการใช้ client-side แล้ว เรายังสามารถใช้ server-side ้hook เพื่่อช่วยให้ system admin สามารถควบคุม policy ใน project ได้ด้วย script ที่ใช้จะทำงานก่อนและหลังการ push มาที่ server และสำหรับ pre hook เราสามารถสั่งหยุดการ push ได้ด้วยการจบ script ด้วย non-zero และทำการส่ง error message กลับไปให้กับ client

#### pre-receive and post-receive ####

script ตัวแรกที่ถูกสั่งให้ทำงานหลังจากที่ server ได้รับ push จาก client คือ `pre-receive` มันจะรับ list ของ references ที่ส่งผ่าน push เข้ามาจาก stdin ถ้า script จบด้วย non-zero ก็จะไม่มีอะไรผ่านเข้ามาใน repository เราสามารถใช้ hook ตัวนี้เพื่อให้แน่ใจว่าไม่มี update ตัวไหนที่เป็น non-fast-forward หรือ ใช้มันสำหรับตรวจสอบว่าผู้ใช้ทำอะไร เช่น create, delete หรือ push access หรือสามารถดูไฟล์ทุกตัวที่ถูกแก้ไขผ่านทาง push

อีกตัวคือ `post-receive` ้hook ซึ่งจะถูกสั่งให้ทำงานหลังจากที่ push ทำงานเสร็จแล้ว เราสามาถใช้มันในการ update service อื่นๆ หรือส่งข้อความให้กับผู้ใช้ก็ได้ ตัว hook จะได้รับข้อมูลเช่นเดียวกับ `pre-recevie` ้hook ตัวอย่างการใช้งานเช่น ส่ง e-mail ของการแก้ไข หรือ สั่งให้ continuous integration server ทำงาน หรือสั่ง update ticket-tracking system หรือเราสามารถดู commit messages เพื่อจัดการ เปิด ปิด หรือแก้ไข ticket ได้ ้script นี้ไม่สามารถหยุดกระบวนการ push ได้ แต่ผู้ใช้จะไม่สามารถ disconnect ได้ จนกว่ากระบวนการจะสิ้นสุด ดังนั้นถ้าจะทำอะไรก็ต้องคิดด้วยว่าจะใช้เวลานานหรือเปล่า

#### update ####

update script จะคล้ายๆ กับ `pre-receive` script ยกเว้นแต่ว่ามันทำงานแค่ครั้งเดียวในแต่ละ branch เมื่อคน push สั่ง update ดังนั้นถ้าคน push พยายามจะ push ทีละหลายๆ branch เจ้า `pre-receive` จะทำงานแค่ครั้งเดียว ในขณะที่ update จะทำงาน branch ละหนึ่งครั้ง และแทนที่จะอ่านข้อมูลจาก stdin ตัว script จะรับสาม argument คือ ชื่อของ branch, SHA-1 ที่อ้างอืงมาก่อนที่จะ push และสุดท้าย SHA-1 ที่ user ใช้ในการ push ทั้งนี้ถ้า update script ถูกจบด้วย non-zero จะมีเฉพาะ reference นั้นๆ ที่ถูก reject ส่วน reference อื่นๆ จะยังคง update ต่อไป

## An Example Git-Enforced Policy ##

ในส่วนนี้ เราจะได้ลองใช้งานกันจริงๆ โดยอาศัยสิ่งที่เราเรียนผ่านมา เพื่อให้เป็นไปตาม workflow ที่กำหนด อันดับแรกคือกำหนดรูปแบบของ commit message บังคับให้เฉพาะ fast-forward-only เท่านั้นที่จะ push และ อนุญาติให้เฉพาะผู้ใช้บางคนเท่านั้นที่จะแก้ไขในบาง subdirectory ได้ เราจะได้ลองสร้าง client script เพื่อช่วยให้นักพัฒนารู้ล่วงหน้าว่าสิ่งที่กำลังจะ push จะถูกตีกลับมา และเราจะได้เขียน script ฝั่ง server เพื่อบังคับใช้ policy ที่เรากำหนดขึ้นมา 

ในตัวอย่างจะใช้ภาษา Ruby เพราะผู้เขียนชอบภาษานี้และคิดว่าเป็นภาษาที่อ่านง่ายถึงแม้ว่าจะไม่เคยเขียนภาษานี้มาก่อนก็ตาม แต่ถ้าดูตัวอย่างจากใน hooks ที่ Git มีมาให้ ในนั้นจะเขียนด้วยภาษา Perl หรือ Basic shell script ซึ่งเราสามารถดูตามได้

### Server-Side Hook ###

การทำ server-side hook จะทำที่ไฟล์ใน hooks directory โดยไฟล์ update จะถูกเรียกทำงาน branch ละหนึ่งครั้งในกรณีที่ผู้ใช้ push ขึ้นมาทีละหลาย branch โดยจะรับ parameter เป็นค่าอ้างอิงไปยัง revision เก่าของ branch นั้น และรับ revision ใหม่ที่ถูก push ขึ้นมา เรายังสามารถเข้าถึงข้อมูลผู้ใช้ขณะที่ผู้ใช้ push ได้ด้วยถ้าผู้ใช้ push ด้วย SSH แล้วถ้าเราอนุญาติให้ทุกคนเชื่อมต่อเข้ามาด้วย single user (like "git") ด้วย public-key authentication เราจะสามารถรู้ได้ด้วยว่าผู้ใช้คนไหนเชิ่มต่อมาด้วย public key และใช้ในการกำหนดลักษณะการรับ push ของ user คนนั้นๆ ในที่นี้เราสมติว่าชื่อผู้ใช้อยู่ใน `$USER` ดังนั้นเราจะเริ่มต้น script ด้วยการรวบรวมข้อมูลที่เราต้องการขึ้นมาก่อน

	#!/usr/bin/env ruby

	$refname = ARGV[0]
	$oldrev  = ARGV[1]
	$newrev  = ARGV[2]
	$user    = ENV['USER']

	puts "Enforcing Policies... \n(#{$refname}) (#{$oldrev[0,6]}) (#{$newrev[0,6]})"

ในตัวอย่างจะใช้ Global variable เพื่อให้เข้าใจง่าย ถ้าในการทำงานจริง ก็ลองแก้กันดูนะครับ

#### Enforcing a Specific Commit-Message Format ####

ด่านแรกของเราคือการบังคับให้ทุก commit message จะต้องใช้รูปแบบตามที่เรากำหนด สมติว่าทุก message จะต้องมีข้อความประมาณว่า "ref: 1234" เพราะเราต้องการให้ทุก commit จะต้องเชื่อมโยงไปยังระบบออก ticket สิ่งที่เราต้องทำคือการอ่านทุก commit ที่ถูกส่งเข้ามาแล้วดูว่าข้อความที่อยู่ใน commit message นั้นเป็นไปตามที่กำหนดหรือไม่ ถ้าไม่ใช่ก็ทำการจบ script ด้วย non-zero เพื่อยกเลิกการ push นั้น

เราต้องดึงเอารายการค่า SHA-1 ออกมาจาก commit ที่ถูก push ขึ้นมา โดยใช้ตัวแปร `$newrev` และ `$oldrev` และคำสั่ง `git rev-list` สิ่งที่ได้จากคำสั่งนี้จะเหมือน `git log` แต่จะมีเฉพาะค่า SHA-1 เท่านั้น ไม่มีข้อมูลอื่น ดังนั้น ถ้าต้องการค่า SHA ทั้งหมดเราก็จะบอกลงไปว่าเราต้องการตั้งแต่ตัวอ้างอิงไหนถึงตัวไหน ดังนี้

	$ git rev-list 538c33..d14fc7
	d14fc7c847ab946ec39590d87783c69b031bdfb7
	9f585da4401b0a3999e84113824d15245c13f0be
	234071a1be950e2a8d078e6141f5cd20c1e61ad3
	dfa04c9ef3d5197182f13fb5b9b1fb7717d2222a
	17716ec0f1ff5c77eff40b7fe912f9f6cfd0e475

เราจะใช้รายการ SHA-1 นี้ ไปดึงเอา message ออกมาทีละอัน จากนั้นก็ทดสอบความถูกต้องด้วย regular expression เพื่อดูว่าตรงกับรูปแบบที่เราวางไว้หรือเปล่า

ตอนนี้เราอาจจะสงสัยว่าจะไปเอา commit message มาได้อย่างไร เราสามารถทำได้โดยใช้คำสั่ง `git cat-file` ไว้จะอธิบายอย่างละเอียดในบทที่ 9 อีกที สำหรับตอนนี้เราจะใช้คำสั่งนี้แบบนี้ครับ

	$ git cat-file commit ca82a6
	tree cfda3bf379e4f8dba8717dee55aab78aef7f4daf
	parent 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
	author Scott Chacon <schacon@gmail.com> 1205815931 -0700
	committer Scott Chacon <schacon@gmail.com> 1240030591 -0700

	changed the version number

เทคนิคการดึง commit message ออกจากจากข้อความทั้งหมดที่ได้จาก SHA-1 จะทำการการวิ่งไปหาบรรทัดว่างจากนั้นถือว่าทุกอย่างที่อยู่ใต้บรรทัดนั้นเป็น commit message เราสามารถใช้คำสั่ง `sed` ของ UNIX มาช่วยดึงได้ดังนี้

	$ git cat-file commit ca82a6 | sed '1,/^$/d'
	changed the version number

เราจะใช้ท่าข้างต้นในการดึงเอา commit message ออกมาตรวจที่ละตัว ถ้า commit message ที่เค้าพยายาม push ทั้งหมดมีตัวใดตัวหนึ่งไม่ตรงกับเงื่อนไขที่วางเอาไว้ เราก็จะจบ script เพิ่อยกเลิกการ push โดยการจบไฟล์ด้วย non-zero ดังนี้

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

เอา code ข้างต้นใส่ไว้ใน `update` script จากนั้น commit message ใดที่ไม่เข้ากับเงื่อนไขจะถูกยกเลิกการ push ทุกครั้ง

#### Enforcing a User-Based ACL System ####

สมติว่าเราต้องการกำหนด access control list (ACL) เพื่อระบุว่าผู้ใช้คนไหนสามารถแก้ไขไฟล์ใน part ไหนได้บ้าง ผู้ใช้บางคนสามารถเข้าถึงได้ทั้งหมด และมีบางคนที่ทำได้เฉพาะการ push ไปยัง directory ที่กำหนด หรือไฟล์ที่กำหนดเท่านั้น การที่จะตั้งเงื่อนไขแบบนี้ได้เราจะต้องกำหนดไว้ในไฟล์ชื่อ `acl` ซึ่งอยู่ใน git repository บน server จากนั้นเราต้องแก้ไขไฟล์ `update` เพื่อให้มันทำตามกฏที่เราตั้งขึ้น โดยดูว่าไฟล์แต่ะลไฟล์ที่ commit ไว้สามารถ push ได้ตามสิทธิ์ของ user คนนั้นๆ หรือไม่

สิ่งแรกที่เราต้องทำการการแก้ไฟล์ ACL รูปแบบการเขียนจะคล้ายกับ ACL ของ CVS โดยแต่ละบรรทัดจะขึ้นต้นด้วย `avail` หรือ `unavail` จากนั้นตามด้วยชื่อผู้ใช้คั่นด้วย comma-delimited และใส่ตัวสุดท้ายเป็น path ที่อนุญาติหรือไม่อนุญาติให้แก้ไข (ถ้าเป็นช่องว่าง หมายถึง open access) จานั้นปิดหัวท้ายของผู้ใช้ด้วย `|` ยกเว้นบรรทัดที่เราต้องการให้ open access

ในกรณีของเรา จะมีผู้ใช้บางคนเป็น administrator บางคนเป็นคนเขียนเอกสารจะเข้าถึงได้เฉพาะ directory `doc` และมีหนึ่งคนที่เข้าถึง directory `lib` และ `tests` ซึ่งทำให้ ACL ของเราเป็นดังนี้

	avail|nickh,pjhyett,defunkt,tpw
	avail|usinclair,cdickens,ebronte|doc
	avail|schacon|lib
	avail|schacon|tests

เราเริ่มต้นด้วยการอ่านข้อมูลเข้ามาเป็นรูปแบบที่เราใช้งานได้ก่อน ในกรณีนี้เราจะมีแค่ `avail` เท่านั้น ในตัวอย่างต่อไปนี้เราจะรวบรวมเป็น associative array โดยให้ key เป็นชื่อของ user และมี value เป็น array ของ path ที่ผู้ใช้คนนั้นสามารถแก้ไขได้

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

ผลลัพท์ที่ได้จากการอ่านไฟล์ ACL ด้วยคำสั่ง `get_acl_access_data` จะได้เป็นข้อมูลรูปแบบดังนี้

	{"defunkt"=>[nil],
	 "tpw"=>[nil],
	 "nickh"=>[nil],
	 "pjhyett"=>[nil],
	 "schacon"=>["lib", "tests"],
	 "cdickens"=>["doc"],
	 "usinclair"=>["doc"],
	 "ebronte"=>["doc"]}

เอาล่ะ ตอนนี้เราได้สิทธิในการเข้าถึงของผู้ใช้แต่ละคนแล้ว ต่อไปเราต้องดูว่า paths ของ commit ที่ push เข้ามานั้น เป็นไปตามสิทธิของผู้ใช้ ตามเงื่อนไขที่เราวางเอาไว้หรือเปล่า

เราสามารถทำได้ง่ายๆ โดยดูไฟล์ที่ถูกแก้ไขใน commit แต่ละตัวด้วย option `--name-only` ในคำสั่ง `git log` (ซึ่งได้พูดไว้คร่าวๆ ในบทที่ 2) 

	$ git log -1 --name-only --pretty=format:'' 9f585d

	README
	lib/test.rb

ถ้าเราใช้ ACL structure ที่ได้รับจาก `get_acl_access_data` แล้วเอามาเทียบกับรายชื่อของไฟล์ใน commit นั้นๆ เราจะสามารถบอกได้ว่าผู้ใช้แก้ไขไฟล์ตามสิทธิ์ของตนเองหรือไม่

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

Code ส่วนใหญ่สามารถอ่านตามได้ง่าย เราสามารถแสดงรายการ commit ที่ถูกส่งมาให้ server ด้วยคำสั่ง `git rev-list` จากนั้นก็ไล่ดูไปที่ละอัน เราจะเห็นว่าไฟล์ไหนบ้างที่ถูกแก้ไข จากนั้นก็ตรวจสอบให้แน่ใจว่าใครเป็นคน push ขึ้นมา แล้วดูว่าเค้ามีสิทธิ์หรือไม่ ใครที่เป็นนักพัฒฯา Ruิัby อาจจะงงๆ กับคำสั่ง `path.index(access_path) == 0` ซึ่งจะ return true ถ้า path เริ่มต้นด้วย `access_path` นี่จะทำให้แน่ใจว่า `access_path` ไม่ได้มีแค่ตัวเดียวที่ตรง แต่ต้องตรงทุก path

ตอนนี้ผู้ใช้ก็ไม่สามารถ push commit ที่ไม่ตรงกับรูปแบบที่เรากำหนดไว้ได้ และไม่สามารถแก้ไขที่อยุ่นอกสิทธิ์ที่เราออกแบบไว้ได้ด้วย

#### Enforcing Fast-Forward-Only Pushes ####

ตอนนี้ก็เหลือแค่บังคับให้ fast-forward-only เท่านั้นที่ push เข้ามาได้ สำหรับ Git ตั้งแต่ version 1.6 ขึ้นไป เราจะสามารถใช้ `recive.denyDeletes` และ `receive.denyNonFastForwards` ใน setting ได้ แต่ในตัวอย่างนี้จะเอาไว้ใช้กับ Git version เก่าๆ ได้ หรือไม่เราก็สามารถดัดแปลงไปใช้อย่างอื่นได้อีกในอนาคต 

เทคนิคในการตรวจสอบคือให้เราหาว่ามีซัก commit ที่เชื่อมไปยัง revision เก่าได้หรือไม่ ถ้าไม่มีก็แสดงว่าเป็น fast-forward push ถ้าไ่ม่ใช้ก็จะยกเลิก

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

ทีนี้ทุกอย่างก็ครบแล้ว ถ้าเราทดลองสั่ง `chmod u+x .git/hooks/update` ซึ่งเป็น file ที่ code ทั้งหมดของเราใส่เอาไว้ จากนั้นก็ทดลอง push ืcode ที่เป็น non-fast-forwarded เข้าไป เราจะได้ค่าดังนี้กลับมา

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

เรามาลองดูค่าที่ส่งกลับมาโดยเริ่มจะวินาทีที่ hook เริ่มทำงาน

	Enforcing Policies... 
	(refs/heads/master) (fb8c72) (c56860)

จะเห็นว่าเราทำการแสดงค่าออกมาทาง stdout ตามที่เราสั่งไว้ ดังนั้นต้องจำไว้ว่า อะไรก็ตามที่เราสั่งเขียนออกทาง stdout จะถูกส่งต่อไปยังเครื่อง client

ต่อไปคือ error message

	[POLICY] Cannot push a non fast-forward reference
	error: hooks/update exited with error code 1
	error: hook declined to update refs/heads/master

บรรทัดแรกเราเป็นคนเขียนขึ้นมาเอง แต่สองบรรทัดต่อมา Git เป็นคนเขียน ว่า update script ได้ถูกยกเลิกพราะจบด้วย non-zero และ push ของผู้ใช้ก็ถูกยกเลิกไปด้วย โดยแสดงเป็น error ดังนี้

	To git@gitserver:project.git
	 ! [remote rejected] master -> master (hook declined)
	error: failed to push some refs to 'git@gitserver:project.git'

เราจะเห็นจาก rejected message ว่าแต่ละ reference ที่ส่งเข้าไปใน hook นั้นถูกยกเลิกเพราะเหตุผลอะไร

นอกจากนี้ ถ้า ref market ที่เรากำหนดให้ใส่ใน commit ดันไม่อยู๋ใน commit เราจะได้รับ message ดังนี้

	[POLICY] Your message is not formatted correctly

หรือถ้าผู้ใช้พยายามจะเข้าไปแก้ไขไฟล์ที่ตัวเองไม่มีสิทธิ์แก้ แล้วสั่ง push ขึ้นมาก็จะได้รับ error ดังนี้

	[POLICY] You do not have access to push to lib/test.rb

ครบแล้วครับ ตราบเท่าที่ `update` ของเรายังทำงานอยู่ repository ของเราจะไม่เคย reword และไม่เคยรับ commit ที่ไม่ถูกต้องตาม pattart และผู้ใช้ก็จะปลอดภัย

### Client-Side Hooks ###

สิ่งที่เลวร้ายมากสำหรับนักพัฒนาใน workflow ของเราคือ เวลาที่เค้า commit เข้ามาแล้วโดน reject หลังจากนั้นต้องใช้เวลานานมากเพื่อแก้ไขประวัติของการ commit ให้ถูกต้อง 

วิธีที่จะป้องกันความเลวร้ายอันนี้คือ การสร้าง client-side ้hook ให้กับนักพัฒนา ซึ่งจะจำลองตัวเองเหมือน server แล้วเตือนผู้ใช้ว่า commit ที่เขียนเข้ามาถูกต้องตามเงื่อนไขหรือไม่ ตัวนี้จะช่วยบอกนักพัฒนาตัวแต่ตอนที่ commit ว่ามีอะไรผิดพลาดบ้างแทนที่จะรอให้ผ่านไปหลาย commit ซึ่งตอนนั้นก็แก้ยากแล้ว แต่อย่างหนึ่งที่ทำไม่ได้คือเราไม่สามารถผูก client-side ้hook ไว้กับ project ได้ เราต้องให้ผู้ใช้ download ไปเองหรือไม่ก็ทำ repository สำหรับเก็บแยกไว้ต่างหาก

เราจะเริ่มต้นด้วยการตรวจสอบ commit message ทุกอันก่อนที่จะถูก push ขึ้นไปบน server โดยเขียน script ไว้ใน hook `commit-msg` จากนั้นอ่านทีละ message แล้วเปรียบเทียบกับ pattern ด้วยวิธีการเดียวกับ server ถ้าเกิดว่าไม่ตรงก็ให้ Git ทำการยกเลิก commit นั้นเลย

	#!/usr/bin/env ruby
	message_file = ARGV[0]
	message = File.read(message_file)

	$regex = /\[ref: (\d+)\]/

	if !$regex.match(message)
	  puts "[POLICY] Your message is not formatted correctly"
	  exit 1
	end

ถ้าเราเอา script ไว้ถูกที่ (ใน `.git/hooks/commit-msg`) และทำให้มัน excutable แล้ว เมื่อเรา commit message ที่ไม่ตรงตามรูปแบบที่วางเอาไว้ เราจะได้รับข้อความดังนี้

	$ git commit -am 'test'
	[POLICY] Your message is not formatted correctly

แต่ถ้า commit message ของเราเขียนมาอย่างถูกต้อง Git ก็จะให้ commit นั้นผ่านเข้าไปได้ และแสดงข้อความดังนี้

	$ git commit -am 'test [ref: 132]'
	[master e05c914] test [ref: 132]
	 1 files changed, 1 insertions(+), 0 deletions(-)

ต่อไปคือเราต้องการแน่ใจว่าไฟล์ที่ถูกแก้ไขนั้น ผู้แก้มีสิทธิในการแก้ไขตามที่ ACL กำหนดไว้ ถ้าโปรแกรมของเรามีไฟล์ ACL อยู่ใน `.git` เรียบร้อยแล้ว เราก็เขียนแก้ `pre-commit` ได้เลย 

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

script ชุดนี้จะหน้าตาเหมือนของ server-side script แต่มีสองส่วนที่แตกต่างกัน อันแรกคือไฟล์ ACL จะอยู่คนละที่กัน เพราะ script จะทำงานที่ working directory ไม่ได้ดูที่ Git directory ดังนั้นเราต้องเปลี่ยนที่อยู่ของไฟล์ ACL จาก

	access = get_acl_access_data('acl')

ไปเป็น

	access = get_acl_access_data('.git/acl')

ความแตกต่างอย่างที่สองคือ วิธีการดึง list ของไฟล์ที่ถูกแก้ไข ในฝั่ง server เราจะใช้วิธีดู log ของการ commit แต่ถในกรณีของฝั่ง client ตัว commit จะยังไม่ได้ถูกบันทึกลงไป ดังนั้นเราจะต้องดึง list ของไฟล์จาก ที่ผู้ใช้ใส่เข้ามาโดยแทนที่บรรทัดต่อไปนี้

	files_modified = `git log -1 --name-only --pretty=format:'' #{ref}`

ด้วยบรรทัดนี้

	files_modified = `git diff-index --cached --name-only HEAD`

จะมีส่วนที่แตกต่างกันแค่สองส่วนนี้เท่านั้น นอกนั้น client กับ server จะเหมือนกัน มีอีกเล็กน้อยที่ต้องระวังคือผมคาดว่าเราจะใช้ user ที่ผั่ง client กับ server เป็นคนเดียวกัน แต่ถ้าไม่ใช่เราอาจจะต้องกำหนดค่า `$user` ขึ้นมาเอง

อย่างสุดท้ายที่เราต้องดูคือ เราต้องตรวจสอบว่านักพัฒนาไม่ได้ push non-fast-forward เข้ามา ซึ่งเราไม่สามารถใช้ท่าธรรมดาได้ วิธีที่จะรู้ว่า reference นั้นไม่ใช่ fast-forward เราจะต้อง rebase commit ที่ถูก pus้h ขึ้นไแแล้ว หรือไม่ก็ทดลอง push local branch อื่นๆ ขึ้นไปที่ remote branch เดียวกัน

เพราะเราต้องพึ่ง server ในการบอกว่า push นั้นเป็น non-fast-forward หรือไม่ โดยให้ตัว hook ที่ฝั่ง server ทำหน้าที่ตรวจสอบให้ ่เดี๋ยวเราจะทดลองสั่ง rebase commit ที่เราเคย push ขึ้นไปแล้ว เพื่อทดสอบ hook ของเรา

Because the server will tell you that you can't push a non-fast-forward anyway, and the hook prevents forced pushes, the only accidental thing you can try to catch is rebasing commits that have already been pushed.

ต่อไปนี้เป็นตัวอย่างของการทำ pre-rebase script สำหรับการตรวจสอบกรณีนี้ ตัวอย่างจะดึงรายการ commit ที่เราตอ้งการแก้ไข และตรวจสอบว่าแต่ละตัวมีการ push ขึ้น remote ไปบ้างแล้วหรือไม่ ถ้ามีตัวใดตัวหนึ่งที่มีอยู่แล้วเราจะทำการยกเลิกการ rebase ทันที

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

ใน script จะมีคำสั่งที่ไม่มีใน Revision Selection ้ของบทที่ 6 คือเราสามารถดึง list ของ commit ที่ถูก pust ขึ้นไปแล้วได้โดยการสั่ง

	git rev-list ^#{sha}^@ refs/remotes/#{remote_ref}

ค่า `SHA^@` เป็นตัวแทน parents ทุกตัวของแต่ละ commit เราพยายามมองหาว่าแต่ละ commit ที่เชื่อมโยงกับ commit สุดท้ายบน server และดูว่ามันเชื่อมโยงไปยัง parent ของ SHA ตัวใดตัวหนึ่งที่เราพยายามจะ push หรือไม่ ถ้ามีก็แสดงว่าเป็น fast-forward

ปัญหาของวิธีการนี้คือมันช้ามาก และดูเหมือนไม่ค่อยมีประโยชน์ เพราะถ้าเราไม่ได้พยายามจะ force push ด้วย `-f` แล้ว ทุกครั้งที่เรา พยายามจะ push ตัว server ก็จะเตือนเราเองและทางแก้ก็ไม่ยาก แต่อย่างไรก็ตามตัวอย่างนี้ก็น่าสนใจ และน่าจะสามารถช่วยให้เราแก้ปํญหาการ rebase ผิดแล้วต้องย้อนกลับมาแก้ทีหลัง

## Summary ##

ถึงต้อนนี้เราได้เรียนรู้เทคนิคส่วนใหญ่ในการปรับแต่ Git แล้วทั้งฝั่ง client และ server เพื่อให้ Git ทำงานได้ตรงกับ workflow ของโครงการ 
ทั้งการปรับแต่งจาก configuration setting, file-base attributes, hooks และ ได้ทดลองทำ policy-enforceing ที่ฝั่ง server หลังจากนี้
เราจะสามารถสร้าง Git ในฝันของเราได้แล้ว
