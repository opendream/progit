# การแตก branch ใน Git #

เกือบทุก VCS support การแตก branch ทางใดซักทางหนึ่ง การแตก Branch หมายถึงคุณแยกตัวออกมาจาก main line ของการพัฒนาและทำงานต่อไปบนบนนั้นโดยไม่ไปยุ่งเกี่ยวกับ main line ในหลายๆ VCS การทำแบบนี้ค่อนข้างจะเปลือง ส่วนใหญ่จะเป็นการ copy ทั้ง directory ของ source code ซึ่งจะกินเวลานานมว๊ากกบน project ใหญ่ๆ

หลายคนเรียกการแตก branch ใน Git เป็น “killer feature” และมันทำให้ Git โดดเด่นออกมาจาก VCS อื่นๆ ทำไมน่ะเหรอ? เพราะวิธีที่ Git แตก branch มันถูกโคตร การแตก branch ทำได้ในชั่วพริบตาและการ switch ไปๆมาๆระหว่าง branch ก็เร็วพอๆกัน ไม่เหมือน VCS ดาษๆทั่วไป Git ผลักดันกระบวนการทำงาน workflow ให้แตก branch และ merge บ่อยๆแบบที่ทำได้วันละหลายๆครั้ง การทำความเข้าใจและบรรลุ feature นี้จะทำให้ Git กลายเป็นเครื่องมือที่ทรงพลังและมีเอกลักษณ์และทำให้วิถีการทำงานของคุณเปลี่ยนไปเลย

## Branch คืออะไร ##

เพื่อทำความเข้าใจวิธีที่ Git แตก branch เราต้องย้อนกลับมาดูว่า Git เก็บข้อมูลยังไง ตามที่คุณอาจจะจำได้จาก Chapter 1 ว่า Git ไม่ได้เก็บข้อมูลเป็นลำดับของความเปลี่ยนแปลงต่อเวลาแต่เก็บเป็นลำดับของ snapshot ต่อเวลา

เวลาคุณ commit ใน Git นั้น Git จะเก็บเป็น object ของการ commit ซึ่งประกอบด้วย pointer ชี้ไปยัง snapshot ของ content ที่คุณ stage ไว้, metadata ของชื่อผู้แก้ไขและ message ที่บันทึกไว้ และ pointer ที่ชี้ไปยัง parent ลำดับถัดไปของ commit นั้นๆ (ซึ่งอาจจะไม่มีก็ได้ถ้าเป็น commit ครั้งแรก, อาจจะมีอันเดียวชี้ไปยัง parent ของ commit ปรกติทั่วไปหรืออาจจะมี parent หลายอันสำหรับ commit ที่เป็นผลจากการ merge หลายๆ branch เข้าด้วยกัน)

ลองจินตนาการว่าคุณมี directory อันนึงที่มี file อยู่ข้างใน 3 files แล้วคุณก็ stage ทั้งหมดและ commit การ stage file จะสร้าง checksum ของแต่ละ file (ไอ้ SHA-1 hash ที่บอกไว้ใน Chapter 1 นั่นแหละ), แล้วบันทึก version ของ file นั้นๆใน Git repository (Git อ้างอิงพวกมันแบบ blobs) และเพิ่ม checksum นั้นลงไปใน staging area:

	$ git add README test.rb LICENSE
	$ git commit -m 'initial commit of my project'

เมื่อคุณทำการ commit ด้วยคำสั่ง `git commit` Git จะคำนวน checksum ของแต่ละ subdirectory (ในกรณีนี้ก็มีแค่ root project directory) และบันทึกโครงสร้างของ directory ใน Git repository หลังจากนั้น Git ก็จะสร้าง commit object ที่มี metadata และ pointer ชี้ไปยังโครงสร้างของ root project เพื่อที่มันจะได้สร้าง snapshot นั้นขึ้นมาใหม่ได้เมื่อต้องการ

Git repository ของคุณตอนนี้จะมี 5 objects: blob แต่ละ blob สำหรับ content ของแต่ละ file ใน 3 files นั้น, โครงสร้าง root directory ที่เก็บ list ของสิ่งของในนั้นและบันทึกว่า file ไหนถูกเก็บใส่ blob ไหน และ 1 commit ที่มี pointer อันนึงชี้ไปยังโครงสร้างของ root directory กับพวก metadata ของ commit นั้น ซึ่งหน้าตาของข้อมูลใน Git repository ของคุณก็มีคอนเซปประมาณรูป Figure 3-1.

Insert 18333fig0301.png 
Figure 3-1. Single commit repository data.

ถ้าคุณทำการแก้ไขใดๆ แล้ว commit ซ้ำอีกครั้ง commit อันถัดไปจะเก็บในรูป pointer ชี้ไปยัง commit ก่อนหน้า ทำไปอีก 2 commits history ของคุณน่าจะมีหน้าตาประมาณรูป Figure 3-2.

Insert 18333fig0302.png 
Figure 3-2. Git object data for multiple commits.

branch อันนึงใน Git เป็นแค่ pointer ฉบับกระเป๋าของ commits เหล่านี้ ชื่อโดย default ของ branch ใน Git คือ master ขณะที่คุณ commit ครั้งแรกส่งกำลังส่ง master branch อันนึงที่ points กลับไปยัง commit ก่อนหน้า ทุกครั้งที่คุณ commit มันก็ค่อยๆขยับไปๆโดยอัตโนมัติ

Insert 18333fig0303.png 
Figure 3-3. Branch pointing into the commit data’s history.

แล้วจะเกิดอะไรขึ้นถ้าคุณสร้าง branch ใหม่? ก็แค่สร้าง pointer อันใหม่เพื่อที่จะโยกย้ายไปมาตามใจ ยกตัวอย่างว่าคุณสร้าง branch ใหม่ชื่อว่า testing ซึ่งสามารถทำได้ด้วยคำสั่ง `git branch`:

	$ git branch testing

มันจะสร้าง pointer อันใหม่ใน commit ปัจจุบันที่คุณอยู่ (ดูรูป Figure 3-4).

Insert 18333fig0304.png 
Figure 3-4. Multiple branches pointing into the commit’s data history.

แล้ว Git มันรู้ได้ไงว่าตอนนี้คุณอยู่ branch ไหน? เพราะมันแอบจำ pointer พิเศษที่ชื่อว่า HEAD จำไว้ว่า HEAD นี้ต่างกันกับ concept ของ HEAD ใน VCS อื่นๆที่คุณอาจจะเคยใช้มาเยอะมาก (ไม่ว่าจะเป็น Subversion หรือ CVS) ใน Git นี่คือ pointer ชี้ไปยัง local branch ที่คุณทำงานอยู่ อย่างในกรณีนี้ คุณยังอยู่บน master คำสั่ง git branch แค่สร้าง branch ใหม่ให้เฉยๆ มันเปล่า switch คุณไปยัง branch ใหม่นั้นแต่อย่างใด (ดูรูป Figure 3-5).

Insert 18333fig0305.png 
Figure 3-5. HEAD file pointing to the branch you’re on.

ถ้าจะ switch ไปยัง branch ใดๆที่มีอยู่ คุณก็แค่สั่ง `git checkout` ลอง switch ไปยัง testing branch อันใหม่กัน:

	$ git checkout testing

คำสั่งนี้จะย้าย HEAD ให้ไปชี้ที่ testing branch (ดังรูป Figure 3-6).

Insert 18333fig0306.png
Figure 3-6. HEAD points to another branch when you switch branches.

แล้วมันสำคัญยังไงอ่ะ? อ่ะ มาดู commit ถัดมากัน:

	$ vim test.rb
	$ git commit -a -m 'made a change'

รูป Figure 3-7 จะโชว์ผลให้ดู

Insert 18333fig0307.png 
Figure 3-7. The branch that HEAD points to moves forward with each commit.

น่าสนใจนะเนี่ย เพราะตอนนี้ testing branch ของคุณขยับไปข้างหน้า แต่ master branch ยังคงชี้ไปยัง commit ที่คุณอยู่ก่อนหน้านี้ตอนที่ switch branch ด้วยคำสั่ง `git checkout` อ่ะ ลอง switch กลับไป master branch กัน:

	$ git checkout master

มาดูผลในรูป Figure 3-8

Insert 18333fig0308.png 
Figure 3-8. HEAD moves to another branch on a checkout.

คำสั่งนั้นทำสองอย่าง มันขยับ HEAD pointer กลับไปยัง master branch แต่มัน revert file ทั้งหลายใน working directory ของคุณกลับไปยัง snapshot ที่ master ชี้อยู่ ซึ่งหมายความว่าความเปลี่ยนแปลงทั้งหลายที่คุณแก้ไปตั้งแต่จุดนี้ถูกแยกออกไปจาก version เก่าของ project สรุปคือมัน rewind งานที่คุณทำไปบน testing branch กลับมาชั่วคราวเพื่อที่คุณจะได้ลองแก้ไปทางอื่นได้

มาลองแก้ file ซักนิดแล้ว commit อีกทีดู:

	$ vim test.rb
	$ git commit -a -m 'made other changes'

ตอนนี้ project history ของคุณถูกแยกออก (ดู Figure 3-9) คุณสร้าง branch ใหม่และ switch ไป, ทำงานไปบนนั้น, แล้ว switch กลับมาที่ branch หลัก แล้วทำงานอื่นลงไป ความเปลี่ยนแปลงทั้งสองสายถูกตัดขาดจากกันใน branch ทั้งสอง คุณสามารถโดดกลับไปกลับมาระหว่างสอง branches ได้แล้วค่อย merge มันเข้าด้วยกันเมื่อคุณพร้อม ซึ่งทั้งหมดนั่นทำได้ด้วยคำสั่งง่ายอย่าง `branch` และ `checkout`

Insert 18333fig0309.png 
Figure 3-9. The branch histories have diverged.

เนื่องจาก branch ใน Git จริงๆแล้วเป็นแค่ file ธรรมดาๆที่เก็บตัวหนังสือ 40 อักษรที่เป็น SHA-1 checksum ของ commit ที่มันชี้ไปหา การสร้างหรือทำลาย branch เลยถูกอย่างกะขี้ สร้าง branch ใหม่ทั้งง่ายและเร็วส์ปานการเขียน 41 bytes ลงไปใน file (40 อักษรกะ newline อีกตัว)

มันเลยแตกต่างกับการแตก branch ใน VCS tool ทั่วไปราวฟ้ากะเหว เพราะปรกติต้องนั่ง copy ทุก file ใน project ใส่ใน directory ใหม่ซึ่งกินเวลาหลายวิ หรืออาจจะเป็นนาทีขึ้นอยู่กับความอ้วนของ project ขณะที่ Git ตดไม่ทันหายเหม็นก็ทำเสร็จละ นอกจากนี้ เนื่องจากเราจำ parent ไว้ในทุกๆ commit เวลาต้องหาต้นตอ version เวลาจะ merge ก็ทำได้โดยอัตโนมัติและง่ายด้วย features เหล่านี้เติมความกล้าให้ developer สร้างและใช้ branches ทั้งหลายเยอะขึ้น

มาดูกันว่าทำไมคุณก็ควรจะทำ

## เบสิคการแตก Branch และการ Merge ##

มาดูตัวอย่างง่ายๆของการแตก branch และการ merge ด้วย workflow ที่คุณน่าจะใช้ในชีวิตประจำวัน คุณจะทำตามนี้:

1.	ทำงานบน web site.
2.	แตก branch สำหรับ story ใหม่ที่คุณกำลังทำ
3.	ทำงานใน branch นั้น

จังหวะนี้เอง มีโทรศัพท์เข้ามาบอกว่ามี issue ร้อนที่ต้องรีบ hotfix ด่วน! คุณก็ทำตามนี้:

1.	กลับไปยัง production branch
2.	แตก branch สำหรับทำ hotfix.
3.	หลังจาก test แล้ว ก็ merge hotfix branch แล้ว push ขึ้นไปยัง production
4.	switch กลับไป story ตอนแรก แล้วทำงานต่ออย่างสบายอารมณ์

### เบสิคการแตก Branch ###

ก่อนอื่น สมมติว่าคุณกำลังทำงานบน project คุณ และ commit เข้าไปหลายทีละ (ตาม Figure 3-10).

Insert 18333fig0310.png 
Figure 3-10. A short and simple commit history.

คุณตัดสินใจว่า เอาล่ะ วันนี้ทำ issue #53 ดีกว่า (ไม่ว่าระบบ issue-tracking ที่บริษัทคุณใช้จะเป็นอะไร) เอาจริงๆแล้ว Git ไม่ได้ผูกติดกับ issue-tracking แต่อย่างใด แต่เนื่องจาก issue #53 มันเป็นเรื่องเป็นราวของมันคุณก็เลยอยากแตก branch แยกออกไปทำ การสร้าง branch ใหม่และ switch ไปในจังหวะเดียวสามารถทำได้ด้วยคำสั่ง `git checkout` แล้วเสริม `-b` เข้าไป:

	$ git checkout -b iss53
	Switched to a new branch "iss53"

นี่คือท่าลัดของ:

	$ git branch iss53
	$ git checkout iss53

รูป 3-11 จะโชว์ผลให้ดู

Insert 18333fig0311.png 
Figure 3-11. Creating a new branch pointer.

คุณทำงานไปบน web site ของคุณและ commit ไปนิดหน่อย ซึ่งระหว่างนั้นก็เป็นการผลัก branch `iss53` ไปข้างหน้า เพราะคุณ checkedout มันออกมา (แปลว่า HEAD ของคุณชี้ไปหามัน ดังรูป Figure 3-12):

	$ vim index.html
	$ git commit -a -m 'added a new footer [issue 53]'

Insert 18333fig0312.png 
Figure 3-12. The iss53 branch has moved forward with your work.

แล้วโทรศัพท์ก็มาบอกว่า web site มีงานเข้า และคุณต้องซ่อมมันด่วน เพราะ Git คุณไม่จำเป็นต้อง deploy fix ของคุณไปกับความเปลี่ยนแปลงใน `iss53`  และคุณก็ไม่ต้องเปลืองแรงแก้ code กลับมาเป็นเหมือนเดิม ก่อนที่จะเริ่ม fix อะไรที่อยู่บน production ทั้งหมดที่ต้องทำก็แค่ switch กลับไปยัง master branch

อย่างไรก็ตาม ก่อนทำแบบนั้น จำไว้ว่า working directory หรือ staging area ของคุณมีความเปลี่ยนแปลงที่ยังไม่โดน commit ซึ่ง conflict กับ branch ที่คุณกำลัง checkout Git ก็เลยไม่ปล่อยให้คุณ switch branches ถ้าจะให้ดี คุณควรจะมีสถานะการทำงาน cleanๆ ก่อนที่จะ switch branches จริงๆมันก็มีท่ายากที่เอาไว้แก้สถานการณ์นี้เหมือนกันนะ (คือการ stash เข้าไปก่อนแล้วค่อย commit amending ตาม) แต่ค่อยมาว่ากันมีหลัง สำหรับตอนนี้ แค่ commit ความเปลี่ยนแปลงทั้งหมดเข้าไปก่อนละกัน จะได้ switch กลับไป master branch ได้:

	$ git checkout master
	Switched to branch "master"

ณ จุดนี้ working directory ของ project คุณจะเหมือนกับก่อนหน้าที่คุณเริ่มทำงานบน issue #53 เป๊ะๆ และคุณก็สามารถรวมสมาธิไปที่ hotfix ได้ ตรงนี้คือตรงที่ต้องจำให้ขึ้นใจ: Git จะ reset working directory ของคุณให้เหมือนกับ snapshot ของ commit ที่ branch ที่คุณ check out ชี้ไป มันจะเพิ่ม ลบ หรือแก้ file โดยอัตโนมัติจนมั่นใจว่า working copy ของคุณเหมือนกับ commit สุดท้ายบน branch นั้น

หลังจากนั้น คุณมี hotfix ที่รอให้ทำ มาสร้าง branch เพื่อทำ hotfix จนกว่ามันจะเสร็จกัน(ดู Figure 3-13):

	$ git checkout -b 'hotfix'
	Switched to a new branch "hotfix"
	$ vim index.html
	$ git commit -a -m 'fixed the broken email address'
	[hotfix]: created 3a0874c: "fixed the broken email address"
	 1 files changed, 0 insertions(+), 1 deletions(-)

Insert 18333fig0313.png 
Figure 3-13. hotfix branch based back at your master branch point.

คุณสามารถ run tests เพื่อให้มั่นใจว่า hotfix มันทำงานได้ดั่งใจและ merge มันกลับเข้า master branch เพื่อ deploy ใส่ production ซึ่งสามารถทำได้ด้วยคำสั่ง `git merge`:

	$ git checkout master
	$ git merge hotfix
	Updating f42c576..3a0874c
	Fast forward
	 README |    1 -
	 1 files changed, 0 insertions(+), 1 deletions(-)

คุณจะเห็นคำว่า "Fast forward" ใน merge นั้น เพราะ commit ที่ถูกชี้โดย branch ที่คุณ merge มันเป็น upstream ของ commit ที่คุณอยู่โดยตรง Git ก็เลยขยับ pointer ไปข้างหน้า พูดอีกนัยหนึ่งก็คือ เวลาที่คุณพยายามจะ merge commit ซักอันเข้ากับ commit ที่สามารถไปถึงได้โดยการตาม history ของ commit อันแรก Git จะทำให้ทุกอย่างง่ายขึ้นโดยการขยับ pointer ไปข้างหน้าเพราะมันไม่มีงานที่ถูกแยกออกไปให้ merge สิ่งนี้เรียกว่า "fast forward".

ความเปลี่ยนแปลงของคุณตอนนี้อยู่ใน snapshot ของ commit ที่ `master` branch ชี้ไป และคุณก็สามารถ deploy ความเปลี่ยนแปลงนี้ได้ (ดัง Figure 3-14).

Insert 18333fig0314.png 
Figure 3-14. Your master branch points to the same place as your hotfix branch after the merge.

หลังจาก fix ที่โคตรสำคัญถูก deployed คุณก็พร้อมที่จะ switch กลับไปยังงานที่คุณทำค้างไว้ก่อนถูกขัดจังหวะ อย่างไรก็ตาม สิ่งที่คุณจะทำก่อนคือการ delete branch `hotfix` เพราะว่าคุณไม่ต้องใช้มันอีกแล้ว เพราะไอ้ `master` branch มันชี้ไปที่จุดเดียวกันแล้ว คุณสามารถ delete มันด้วย option `-d` ของ `git branch`:

	$ git branch -d hotfix
	Deleted branch hotfix (3a0874c).

เอาล่ะ ตอนนี้ switch กลับไป branch issue #53 ที่ทำค้างไว้ได้ละ แล้วก็ทำงานต่อตามสบาย (ดังรูป Figure 3-15):

	$ git checkout iss53
	Switched to branch "iss53"
	$ vim index.html
	$ git commit -a -m 'finished the new footer [issue 53]'
	[iss53]: created ad82d7a: "finished the new footer [issue 53]"
	 1 files changed, 1 insertions(+), 0 deletions(-)

Insert 18333fig0315.png 
Figure 3-15. Your iss53 branch can move forward independently.

ถึงงานที่ทำไว้ใน `hotfix` branch จะไม่อยู่ใน files ใน `iss53` branch ก็ช่างหัวมัน ถ้าต้องดึงมันเข้ามา คุณก็สามารถ merge `master` branch ของคุณใส่ใน `iss53` branch ได้ด้วยคำสั่ง `git merge master` หรือว่าจะรอ integrate ความเปลี่ยนแปลงพวกนั้นตอนจะรวม `iss53` branch กลับเข้า `master` ทีหลังก็ได้

### เบสิคการ Merge ###

สมมติว่าคุณมั่นแล้วว่า issue #53 นี่เนียนแล้วและพร้อมที่จะ merge มันเข้า `master` branch คุณก็ merge branch `iss53` (เหมือนกะตอนที่ทำ branch `hotfix` ก่อนหน้าอ่ะแหละ) ที่ต้องทำทั้งหมดก็แค่ check out branch ที่อยากจะ merge เข้าไปใส่ และสั่ง command `git merge`:

	$ git checkout master
	$ git merge iss53
	Merge made by recursive.
	 README |    1 +
	 1 files changed, 1 insertions(+), 0 deletions(-)

อันนี้อาจจะดูต่างกะตอน merge `hotfix` ก่อนหน้านิดส์นึง เพราะครั้งนี้ history การเปลี่ยนแปลงมันถูกแยกออกไปจากจุดก่อนหน้า นั่นเพราะว่า commit ของ branch ที่คุณกะลังอยู่ไม่ได้เป็นรากเหง้าโดยตรงของ branch ที่กำลังจะ merge เข้ามา Git เลยต้องออกแรง อย่างในกรณีนี้ Git ก็ทำ การ merge 3 ทางง่ายๆ โดยใช้ 2 snapshots ที่อยู่ที่ปลายทั้ง 2 ข้างของแต่ละ branch และรากเหง้าของทั้งสองที่เหมือนกัน Figure 3-16 จะ highlight 3 snapshots ที่ Git ใช้ในการ merge กรณีนี้ให้ดู

Insert 18333fig0316.png 
Figure 3-16. Git automatically identifies the best common-ancestor merge base for branch merging.

แทนที่จะแค่ขยับ pointer ของ branch ไปข้างหน้า Git สร้าง snapshot อันที่ที่เป็นผลจากการ merge 3 ทางนี้ และสร้าง commit อันใหม่ที่ชี้ไปยัง snapshot นั้นโดยอัตโนมัติ (see Figure 3-17) ปรกติเราเรียกท่านี้ว่า merge commit และความพิเศษของมันคือ มันมีแม่มากกว่า 1 อัน

อย่างนึงที่อยากจะอวดคือ Git ไปคุ้ยหารากเหง้าที่ซ้ำกันของทั้งสองกิ่งให้เพื่อที่จะใช้เป็น merge base ซึ่ง CVS อื่นหรือ Subversion (ก่อน version 1.5) ไม่มีปัญญา และ developer ที่จะ merge ต้องไปคุ้ยเองว่า merge base ที่ดีที่สุดคืออันไหน ด้วยเหตุนี้การ merge ใน Git ก็เลยง่ายกว่าระบบอื่นๆมว้ากกกส์

Insert 18333fig0317.png 
Figure 3-17. Git automatically creates a new commit object that contains the merged work.

เอาล่ะ หลังจากงานคุณถูก merge เข้าไปเรียบร้อยแล้ว คุณก็ไม่จำเป็นต้องเลี้ยง branch `iss53` ให้เสียข้าวสุก ลบแม่มเลย แล้วก็ไปปิด ticket ในระบบ ticket-tracking

	$ git branch -d iss53

### เบสิคของ Merge Conflicts ###

ในบางเวลาที่ไม่เป็นใจ ถ้าคุณแก้ส่วนเดียวกันใน file เดียวกันไปคนละทิศคนละทางบน 2 branch ที่แตกต่างกัน เวลาคุณ merge มันเข้าด้วยกัน Git ก็ไม่รู้จะ merge มันเข้ามารวมกันเนียนๆได้ไง ถ้า fix ที่คุณทำไปบน issue #53 มีการแก้ส่วนเดียวกันบน file เดียวกันกับ `hotfix` คุณจะเจอ merge conflict ซึ่งมีหน้าตาประมาณนี้

	$ git merge iss53
	Auto-merging index.html
	CONFLICT (content): Merge conflict in index.html
	Automatic merge failed; fix conflicts and then commit the result.

Git ไม่ได้สร้าง merge commit อันใหม่ให้อัตโนมัติ มันกด pause เพื่อหยุดให้คุณ resolve merge conflict ถ้าคุณอยากดูว่า file ไหนบ้างที่ยังไม่ถูก merge ณ เวลาใดๆหลังจากเกิด merge conflict ก็สามารถดูได้ด้วยคำสั่ง `git status`:

	[master*]$ git status
	index.html: needs merge
	# On branch master
	# Changed but not updated:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#	unmerged:   index.html
	#

อะไรก็ตามที่มี merge conflict และยังไม่ถูก resolve จะถูก list ออกมาว่า unmerged Git จะเติม conflict-resolution markers ลงไปใน files ที่มี conflicts เพื่อคุณจะได้เปิดมันและ resolve conflicts เหล่านั้นได้ file ของคุณจะมี section ที่หน้าตาประมาณนี้

	<<<<<<< HEAD:index.html
	<div id="footer">contact : email.support@github.com</div>
	=======
	<div id="footer">
	  please contact us at support@github.com
	</div>
	>>>>>>> iss53:index.html

จากรูป version ที่ HEAD (ซึ่งก็คือ master branch เพราะว่านั่นคือที่ที่คุณ check out ออกมาตอนคุณ run merge command) จะเป็นส่วนบนสุดของ block นั้น (ไอ้ที่อยู่บน `=======` น่ะ) ขณะที่ version ที่อยู่ใน `iss53` branch จะอยู่ในส่วนล่าง ในการที่จะ resolve conflict คุณก็ต้องเลือกซักส่วน หรือไม่ก็ merge มันเข้าด้วยกันเอง ยกตัวอย่างเช่น คุณอาจจะ resolve conflict อันนี้โดยการแก้ทั้ง block ให้เป็นอย่างข้างล่าง

	<div id="footer">
	please contact us at email.support@github.com
	</div>

resolution (การซ่อม) อันนี้เอามาจากทั้งสองส่วนอย่างละนิดอย่างละหน่อย และผมก็ได้ลบไอ้พวก `<<<<<<<`, `=======` และ `>>>>>>>` ออกไป หลังจากคุณ resolved แต่ละ section ใน file ที่มี conflict แล้ว run `git add` บนแต่ละ file เพื่อระบุว่ามันถูก resolved การ Stage file เป็นการระบุว่ามันถูก resolved แล้วใน Git
ถ้าอยากใช้ graphical tool เพื่อ resolve issues เหล่านี้ ก็ run `git mergetool` ซึ่งจะเปิด visual merge tool แหล่มๆขึ้นมาและเรียงแต่ละ conflicts ขึ้นมาให้คุณแก้:

	$ git mergetool
	merge tool candidates: kdiff3 tkdiff xxdiff meld gvimdiff opendiff emerge vimdiff
	Merging the files: index.html

	Normal merge conflict for 'index.html':
	  {local}: modified
	  {remote}: modified
	Hit return to start merge resolution tool (opendiff):

ถ้า merge tool ที่ถูกเลือกขึ้นมาโดย default นั้นไม่โดนใจ (Git เลือก `opendiff` ให้ผมในกรณีนี้เพราะผม run command บน Mac) คุณก็ดูว่า supported tools ที่ list อยู่ข้างบนหลังจากคำว่า “merge tool candidates” แล้วพิมพ์ชื่อ tool ที่อยากใช้ลงไป ใน Chapter 7 เราจะมาถกกันว่าคุณจะเปลี่ยน default value เหล่านี้ใน environment คุณได้ยังไง

หลังจากปิด merge tool ไป Git ก็จะถามคุณว่า merge สำเร็จมัย? ถ้าคุณตอบไปว่าชิววว มันก็จะ stages file นั้นเพื่อระบุว่ามันถูก resolved แล้วให้คุณ

ลอง run `git status` อีกทีเพื่อเช็คดูว่าไม่เหลือ conflicts ให้ resolve แล้วนะ

	$ git status
	# On branch master
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#	modified:   index.html
	#

โอเค แหล่ม เช็คอีกทีให้มั่นใจว่าทุกอย่างที่มี conflicts ถูก staged แล้ว แล้วก็พิมพ์ `git commit` เพื่อจบการ merge commit โดยปรกติแล้ว commit message ตาม default จะหน้าตาประมาณนี้

	Merge branch 'iss53'

	Conflicts:
	  index.html
	#
	# It looks like you may be committing a MERGE.
	# If this is not correct, please remove the file
	# .git/MERGE_HEAD
	# and try again.
	#

คุณมาสามารถแก้เติมรายละเอียดลงไปว่าคุณ resolve merge อันนั้นได้ไงลงไปใน message ได้ถ้ารู้สึกว่ามันจะช่วยให้เพื่อนๆเข้าใจ merge นี้ง่ายขึ้นเวลาต้องกลับมาดูมันใหม่ในอนาคต (เช่นอธิบายว่าไอ้ที่ทำไปแบบนี้ ทำไปเพราะอะไร) แต่ถ้ามันชัดอยู่แล้วก็ไม่ต้อง

## การบริหาร Branch ##

เอาล่ะ หลังจากที่คุณ created, merged และ deleted บาง branches แล้ว มาดู tools ที่ใช้ทำ branch-management (บริหาร branche) บ้าง เพราะอาจจะต้องใช้เวลาที่คุณทำ branches เยอะๆ

ไอ้ command `git branch` ทำได้มากกว่าแค่ create หรือ delete branches ถ้าคุณ run มันโดยไม่ใส่ arguments คุณจะได้ list ของ branches ปัจจุบันที่คุณมีอยู่:

	$ git branch
	  iss53
	* master
	  testing

สังเกตุไอ้ `*` ข้างหน้า `master` branch มันจะคอยปะอยู่หน้า branch ที่คุณกำลัง checked out อยู่ในปัจจุบัน ซึ่งแปลว่า ถ้า commit ตอนนี้ `master` branch จะถูกขยับไปข้างหน้าด้วยงานที่เพิ่มมาใหม่ การจะดู commit สุดท้ายของแต่ละ branch คุณก็ run `git branch -v`:

	$ git branch -v
	  iss53   93b412c fix javascript issue
	* master  7a98805 Merge branch 'iss53'
	  testing 782fd34 add scott to the author list in the readmes

อีกทางหนึ่งที่ใช้ในการเช็ค state ของ branches คือการ list ดูว่ามี branch ไหนบ้างที่เคยถูก merge และ branch ไหนบ้างที่ยังไม่เคยถูก merge เข้ามายัง branch ที่คุณอยู่ option เทพที่ชื่อว่า `--merged` และ `--no-merged` ถูกเพิ่มเข้ามาใน Git ตั้งแต่ version 1.5.6 เพื่อประการนี้ การจะดูว่า branches ไหนบ้างที่เคยถูก merged เข้ามายัง branch ที่คุณอยู่ สามารถทำได้โดย run `git branch --merged`

	$ git branch --merged
	  iss53
	* master

เพราะคุณเคย merged branch `iss53` เข้ามาแล้วเมื่อตะกี้ คุณก็เลยเห็นมันอยู่ใน list ปรกติแล้ว branches ต่างๆใน list นี้ (ยกเว้นอันที่กา `*` ไว้ข้างหน้านะ) สามารถ delete ทิ้งได้ตามสบายด้วยคำสั่ง `git branch -d` เพราะคุณได้ย้ายงานที่ทำในนั้นไปไว้ที่ branch อื่นแล้ว เพราะฉะนั้นลบไปก็ไม่มีงานหาย

การจะดูว่า branches ไหนบ้างที่มีงานที่ยังไม่ได้ถูก merged เข้ามาก็สามารถทำได้โดยการ run `git branch --no-merged`:

	$ git branch --no-merged
	  testing

คำสั่งนี้แสดงอีก branch นึง เพราะว่ามันมีงานที่คุณยังไม่เคย merged เข้ามา ถ้าคุณลอง delete มันด้วยคำสั่ง `git branch -d` มันก็จะ fail

	$ git branch -d testing
	error: The branch 'testing' is not an ancestor of your current HEAD.
	If you are sure you want to delete it, run 'git branch -D testing'.

ถ้าอยากจะ delete branch นั้นไปพร้อมๆกับงานที่ทำอยู่ในนั้นจริงๆ ก็สามารถบังคับให้มันลบได้ด้วย option `-D` ดังที่ help message บอกไว้ข้างบน

## Workflows ของการแตก Branch ##

เอาหล่ะ หลังจากคุณมีเบสิคของการแตก branch และการ merge กลับเข้ามาติดตัวแล้ว มาดูกันว่าคุณสามารถ (และสมควร) ที่จะทำอะไรกับมันบ้าง? ใน section นี้ เราจะโชว์ workflows ทั่วไปว่าการแตก branch อันรวดเร็วส์นี้สามารถทำอะไรได้บ้าง คุณจะได้ตัดสินใจได้ว่าควรที่จะเอามันไปประยุกต์ใช้กับกระบวนการพัฒนาที่คุณใช้อยู่ไหม

### Branch ที่อายุยืนนาน ###

เพราะว่า Git มีท่า merge 3 ทาง การ merge จาก branch หนึ่งไปยังอีก branch หลายๆครั้งเป็นระยะเวลานานๆก็กลายเป็นเรื่องชิวๆไปโดยปริยาย นั่นแปลว่าคุณสามารถเปิดหลายๆ branches ทิ้งไว้สำหรับ stages ต่างๆในกระบวนการพัฒนาที่คุณใช้อยู่ และคุณก็สามารถ merge จากบาง branch ไปยัง branch อื่นๆได้อย่างสม่ำเสมอ

นักพัฒนาที่ใช้ Git หลายคนมี workflow ที่สนับสนุนวิธีแบบนี้ ยกตัวอย่างเช่น มี code stable สุดยิดเก็บไว้ใน `master` branch (ส่วนใหญ่จะเป็น code ที่ถูก release ไปแล้วเตรียมจะออก) แล้วพวกเขาก็มีอีก branch หนึ่ง เปิดควบคู่กันไปโดยตั้งชื่อมันว่า develop หรือ next แล้วก็ทำงานกันบนนี้ ไม่ก็ใช้เพื่อ test ความเสถียรของระบบ (ซึ่ง branch นี้ไม่จำเป็นต้อง stable ตลอดเวลา) แล้วเมื่อไหร่ก็ตามที่มันเริ่ม stable เขาก็ merge มันเข้า `master` ไอ้ develop branch นี้จะเป็นที่ไว้ pull พวก topic branches (พวก branch อายุสั้นๆอย่าง `iss53` branch อันก่อนหน้านี้) เข้ามาเมื่อมันเสร็จเพื่อเช็คว่ามัน pass ทุกๆ tests และไม่มี bugs ใหม่ๆโผล่เข้ามา

ในชีวิตจริงนั้น ถ้ามาดู pointers ที่ค่อยๆขยับขึ้นตามสายของ commits ของคุณ พวก branches ที่ stable จะอยู่ล่างๆใน commit history และพวก branches ที่เก็บของใหม่ๆแรงๆจะอยู่บนใน history (ดู Figure 3-18).

Insert 18333fig0318.png 
Figure 3-18. More stable branches are generally farther down the commit history.

ถ้าจะเปรียบให้ง่าย ก็ลองมองมันเป็นยุ้งข้าวที่เก็บ commits ต่างๆ โดย commit จะค่อยๆถูกขยับไปในยุ้งที่เสถียรขึ้นเมื่อมันถูก tested เรียบร้อย (ดู Figure 3-19).

Insert 18333fig0319.png 
Figure 3-19. It may be helpful to think of your branches as silos.

คุณสามารถแยกแบบนี้ซ้ำๆเป็นกี่ระดับความเสถียรก็ได้ สำหรับ projects ใหญ่ๆบาง projects จะมี branch `proposed` หรือ `pu` (proposed updates) ซึ่งเป็น integrated branches ที่ยังไม่พร้อมจะเอาไปลง branch `next` หรือ `master` สรุปแล้ว ไอเดียคือ branches ต่างๆจะถูกแยกให้มีระดับความเสถียรไม่เท่ากัน เมื่อไหร่ที่มันเสถียรขึ้น ก็จะถูก merge ไปยัง branch ระดับที่สูงขึ้น
ย้ำอีกที การมี long-running branches หลายๆอันนั้นไม่จำเป็น อย่างไรก็ตาม มันมักจะมีประโยชน์เวลาที่คุณกำลังทำ project ใหญ่ๆที่ซับซ้อนมากๆ 

### Topic Branches ###

ส่วน topic branches เป็นคนละเรื่องนะจ๊ะ เพราะมันมีประโยชน์กับ projects ทุก size topic branch นั้นคือ branch ที่มีอายุสั้นๆ short-lived ที่คุณสร้างและใช้สำหรับ feature ใดๆซักอัน branche แบบนี้เป็นอะไรที่คุณน่าจะไม่เคยทำมาก่อนกับ VCS อื่นๆเพราะโดยปรกติแล้วการ create และ merge branches มันเปลืองพลังงานมาก แต่ใน Git การ create branch, switch branch ทำงาน, merge branch และ delete branches เป็นเรื่องธรรมดาที่ทำกันได้ทุกวัน (อวดอีกแล้ว :P)

อย่างที่คุณเห็นตัวอย่างไปแล้วใน branch `iss53` และ `hotfix` ที่คุณ create ขึ้นมา, commit ลงไปและก็ได้ delete พวกมันทันทีหลังจาก merge พวกมันเข้า branch หลัก เทคนิคแบบนี้จะทำให้คุณ context-switch ได้อย่างเฉียบขาดและรวดเร็วส์ (เพราะงานที่คุณทำถูกแยกออกมาอยู่ในยุ้งของตัวเองโดยที่ความเปลี่ยนแปลงทั้งหมดที่คุณสร้างมันเกี่ยวข้องกับ topic นั้นๆโดยตรง) การจะติดตามว่าอะไรเปลี่ยนไปยังไงก็ง่ายไม่ว่าจะเป็นตอนทำ code review หรืออะไรต่างๆ คุณจะเก็บความเปลี่ยนแปลงที่เกิดขึ้นไว้ใน branch นั้นเป็นหลายนาที, หลายวัน หรือหลายเดือนก็ตามใจ แล้วค่อย merge มันเข้ามาเมื่อมันเสร็จโดยที่ไม่เกี่ยวว่ามันจะถูกสร้างหรือทำเมื่อไหร่

ลองจินตนาการว่าคุณกำลังทำงาน (บน `master`), แล้วแตก branch ออกไปสำหรับ issue ซักอัน (สมมติชื่อ `iss91`) แล้วก็ทำงานบนนั้นไปซักแป๊ปแล้วแตก branch ออกไปอีกอันเพื่อลองแก้ปัญหาเดิมด้วยวิธีใหม่ (ชื่อ `iss91v2`) แล้วกลับไปที่ master branch และทำงานบนนั้นไปซักแป๊ปแล้วแตก branch ออกไปเพื่อลองไอเดียอะไรซักอย่างที่ไม่รู้ว่าเจ๋งป่าว (ชื่อ branch `dumbidea`) ตอนนี้ commit history ของคุณจะมีหน้าตาประมาณรูป Figure 3-20.

Insert 18333fig0320.png 
Figure 3-20. Your commit history with multiple topic branches.

ทีนี้, สมมติวันคุณตัดสินใจละ ว่าคุณชอบวิธีที่สองที่คุณทำสำหรับ issue มากกว่า (`iss91v2`) แล้วคุณก็โชว์ branch `dumbidea` ให้เพื่อนดูแล้วผลปรากฏว่ามันแหล่มมาก คุณก็สามารถโยนไอ้ branch `iss91` อันแรกทิ้ง (ทำให้ commits C5 and C6 หายไป) และ merge อีกสองอันเข้ามา ทำให้ history หน้าตาเหมือนรูป Figure 3-21.

Insert 18333fig0321.png 
Figure 3-21. Your history after merging in dumbidea and iss91v2.

ประเด็นสำคัญอันหนึ่งที่อยากจะเน้นคือขณะที่คุณทำไอ้ทุกอย่างที่เล่ามา branches เหล่านี้อยู่บน local ทั้งนั้น ไม่ว่าจะเป็นตอน branch ตอน merging ทุกอย่างเกิดขึ้นบน Git repository ของคุณเท่านั้น (ไม่มีการติดต่อกับ server เลยนะ)

## Remote Branches ##

Remote branches คือคำที่ใช้อ้างถึงสถานะของ branches ที่ remote repositories พวกมันคือ local branches ที่คุณย้ายที่มันไม่ได้เพราะมันจะย้ายเองโดยอัตโนมัติเมื่อคุณติดต่อกับ any network พวก Remote branches จะทำตัวเหมือน bookmarks ที่คอยเตือนว่า branches ทั้งหลายบน remote repositories อยู่ตรงไหนตอนที่คุณ connected กับ remote ครั้งสุดท้าย

รูปแบบพวกมันคือ `(remote)/(branch)` ตัวอย่างเช่น ถ้าคุณอยากเห็นว่า branch `master` เมื่อครั้งสุดท้ายเมื่อคุณติดต่อกับ `origin` remote หน้าตาเป็นยังไง คุณก็ดูได้ที่ branch `origin/master` ถ้าคุณกำลังทำซัก issue นึงกับเพื่อนๆแล้วเพื่อน push branch ที่ชื่อ `iss53` ขึ้นมา (คุณอาจจะมี local `iss53` branch อยู่แล้ว แต่ branch บน server จะชี้ไปยัง commit ที่ `origin/iss53`)

ฟังแล้วอาจจะยังงงๆ มาดูตัวอย่างกันดีกว่า สมมติว่าคุณมี Git server อยู่ใน network ของคุณชื่อ `git.ourcompany.com` ถ้าคุณ clone จากที่นี่ Git จะตั้งชื่อมันว่า `origin` ให้คุณโดยอัตโนมัติ, ดึงข้อมูลทั้งหมดของมันลงมา, แล้วสร้าง pointer ไปยัง `master` branch ของมัน, แล้วตั้งชื่อว่า `origin/master` บนเครื่องของคุณ โดยที่คุณจะไม่สามารถย้ายที่มันได้ นอกจากนี้ Git ก็จะให้ `master` branch ส่วนตัวกับคุณ โดยมันจะเริ่มต้นจากที่เดียวกับ `master` branch ของ  origin เพื่อเป็นจุดเริ่มต้นในการทำงานให้กับคุณ (ดู Figure 3-22).

Insert 18333fig0322.png 
Figure 3-22. A Git clone gives you your own master branch and origin/master pointing to origin’s master branch.

ถ้าคุณทำงานอะไรซักอย่างไปบน master branch บนเครื่องคุณ โดยระหว่างนั้นมีใครซักคน push ของใส่ `git.ourcompany.com` และ update master branch บนนั้น histories ของคุณก็ยังค่อยๆขยับไปข้างหน้าตามเรื่องตามราวของมัน ตราบใดที่คุณยังไม่ติดต่อกับ origin server ไอ้ pointer `origin/master` ของคุณก็จะไม่ขยับไปไหน (ดู Figure 3-23).

Insert 18333fig0323.png 
Figure 3-23. Working locally and having someone push to your remote server makes each history move forward differently.

เพื่อที่จะ synchronize งานที่คุณทำ คุณก็จะ run command `git fetch origin` โดย command นี้จะไปหาว่า server origin อยู่ไหน (ในกรณีนี้คือ `git.ourcompany.com`) แล้ว fetches ข้อมูลทั้งหมดที่คุณยังไม่มีจากมัน, updates database บนเครื่องคุณ, ขยับ pointer `origin/master` ไปยังที่ใหม่ที่ up-to-date กว่าเดิม (ดู Figure 3-24).

Insert 18333fig0324.png 
Figure 3-24. The git fetch command updates your remote references.

เพื่อจะทำให้เห็นภาพการมี remote servers หลายๆอันและดูว่า remote branches สำหรับ remote projects เหล่านั้นหน้าตาเป็นยังไง, มาลองสมมติกันว่าคุณมี Git server อีกอันนึงที่อีก sprint team นึงใช้ develop โดยเฉพาะ server อันนี้อยู่ที่ `git.team1.ourcompany.com` คุณสามารถเพิ่มมันเข้าไปเป็น remote reference อันใหม่ของ project ที่คุณกำลังทำงานอยู่โดยการ run command `git remote add` อย่างที่เราเคยเล่าไว้ใน Chapter 2 ตั้งชื่อ remote นี้ว่า `teamone` ซึ่งจะกลายเป็นชื่อย่อสำหรับ URL อันนั้นทั้งอัน (ดู Figure 3-25).

Insert 18333fig0325.png 
Figure 3-25. Adding another server as a remote.

ตอนนี้คุณสามารถ run `git fetch teamone` เพื่อ fetch ทุกอย่างที่ remote `teamone` server มีแต่คุณยังไม่มี เนื่องจาก server นั้นเป็นแค่ subset ของข้อมูลที่คุณมีบน `origin` server ในตอนนี้ Git ก็จะไม่ fetch ข้อมูลอะไร แค่ตั้ง remote branch ชื่อ `teamone/master` ชี้ไปยัง commit ที่ `teamone` มีอยู่ใน `master` branch ของมัน (ดู Figure 3-26).

Insert 18333fig0326.png 
Figure 3-26. You get a reference to teamone’s master branch position locally.

### การ Push ###

เมื่อใดที่คุณอยากจะ share branch ซักอันให้กับชาวโลก คุณต้อง push มันขึ้นไปยัง remote ซักอันที่คุณมี write access เนื่องจากพวก branches ต่างๆบนเครื่องคุณมันไม่ได้ synchronize กับ remotes ที่คุณติดต่อโดยอัตโนมัติ คุณต้องเป็นคน push พวกมันขึ้นไปเองถ้าคุณอยากจะ share ทำให้คุณสามารถใช้ private branches ทำงานที่คุณเขินไม่อยากจะ share และ push เฉพาะ topic branches ที่คุณต้องการความร่วมมือขึ้นไป

ถ้าคุณมี branch ซักอันชื่อ `serverfix` ที่คุณอยากจะทำงานร่วมกันกับเพื่อนๆ คุณสามารถ push มันขึ้นไปด้วยวิธีเดียวกันกับที่คุณ push branch อันแรกเลย นั่นคือ run `git push (remote) (branch)`:

	$ git push origin serverfix
	Counting objects: 20, done.
	Compressing objects: 100% (14/14), done.
	Writing objects: 100% (15/15), 1.74 KiB, done.
	Total 15 (delta 5), reused 0 (delta 0)
	To git@github.com:schacon/simplegit.git
	 * [new branch]      serverfix -> serverfix

อันนี้เป็น shortcut นิดส์ๆ โดย Git จะขยายชื่อ branch `serverfix` ออกเป็น `refs/heads/serverfix:refs/heads/serverfix` โดยอัตโนมัติ ซึ่งแปลว่า “เอา serverfix ที่เป็น local branch คนเครื่องฉันไป push ใส่ serverfix บน remote ให้ที” เด๋วเราค่อยมาว่ากันในรายละเอียดของส่วน `refs/heads/` ใน Chapter 9 ตอนนี้ทำเป็นลืมๆมันไปก่อน แน่นอนว่าคุณสามารถทำ `git push origin serverfix:serverfix` ก็ได้ เพราะมันจะได้ผลออกมาเหมือนกัน (เพราะมันแปลว่า “เอา serverfix ของฉันไปทำเป็น serverfix ของ remote ซะ”) คุณสามารถใช้ format เพื่อ push local branch ซักอันไปยัง remote branch ซึ่งมีชื่อต่างกันได้ ถ้าคุณไม่อยากให้มันมีชื่อว่า `serverfix` บน remote คุณก้ run `git push origin serverfix:awesomebranch` แทนเพื่อที่จะ push `serverfix` branch บนเครื่องไปเป็น `awesomebranch` branch บน remote project

ครั้งต่อไปที่เพื่อนคุณซักคน fetch ของจาก server เค้าจะได้ reference อันนึงที่ชี้ไปยัง `serverfix` version บน server ในรูปแบบ remote branch ชื่อ `origin/serverfix`:

	$ git fetch origin
	remote: Counting objects: 20, done.
	remote: Compressing objects: 100% (14/14), done.
	remote: Total 15 (delta 5), reused 0 (delta 0)
	Unpacking objects: 100% (15/15), done.
	From git@github.com:schacon/simplegit
	 * [new branch]      serverfix    -> origin/serverfix

สิ่งสำคัญที่คุณจำให้ขึ้นใจคือเมื่อใดก็ตามที่คุณ fetch remote branches มาใหม่ คุณไม่ได้มี local copy ของมันโดยอัตโนมัติ อย่างในกรณีนี้ คุณไม่ได้มี `serverfix` branch บนเครื่อง คุณมีแค่ pointer ชื่อ `origin/serverfix` ที่คุณแก้ไขมันไม่ได้

การจะ merge งานจากนี้เข้าไปใน working branch ของคุณ คุณสามารถ run `git merge origin/serverfix` แต่ถ้าคุณอยากจจะมี `serverfix` branch เป็นของตัวเอง คุณก็สามารถ base มันออกมาจาก remote branch ได้:

	$ git checkout -b serverfix origin/serverfix
	Branch serverfix set up to track remote branch refs/remotes/origin/serverfix.
	Switched to a new branch "serverfix"

แบบนี้คุณก็จะได้ local branch บนเครื่องที่คุณสามารถทำงานได้โดยมันจะเริ่มต้นจากจุดที่ `origin/serverfix` อยู่

### การติดตาม Branches ###

การ check out local branch ซักอันจาก remote branch จะสร้างสิ่งที่เรียกว่า _tracking branch_ ให้โดยอัตโนมัติ ไอ้พวก tracking branches เนี่ยคือ local branches ที่สัมพันธ์โดยตรงกับ remote branch เมื่อไหร่ที่คุณพิมพ์ `git push` ขณะอยู่บน tracking branch Git จะรู้โดยอัตโนมัติว่าจะต้อง push ใส่ server อะไร branch ไหน นอกจากนี้ การ run `git pull` ขณะอยู่บน branches แบบนี้ก็จะ fetches remote references ทั้งหมดและทำการ merge remote branch ที่เกี่ยวข้องให้โดยอัตโนมัติ

ตอนที่คุณ clone repository ซักอัน, มันจะสร้าง `master` branch ให้โดยอัตโนมัติเพื่อติดตาม `origin/master` ทำให้ `git push` และ `git pull` work ตั้งแต่แรก อย่างไรก็ตาม คุณสามารถ set up tracking branches อื่นๆ (นอกเหนือจาก server `origin` และ branch `master`) ได้ถ้าต้องการ การ run `git checkout -b [branch] [remotename]/[branch]` ที่เห็นไปตะกี้ก็เป็นตัวอย่างนึง  ถ้าคุณมี Git version 1.6.2 ขึ้นไป คุณจะสามารถใช้ option ลัด `--track` ตามตัวอย่างข้างล่าง:

	$ git checkout --track origin/serverfix
	Branch serverfix set up to track remote branch refs/remotes/origin/serverfix.
	Switched to a new branch "serverfix"

เพื่อ set up local branch ด้วยชื่อที่ต่างกันกับ remote branch หรือไม่ก็ใช้ version แรก แต่ใส่ชื่อ local branch ให้ต่างออกไป:

	$ git checkout -b sf origin/serverfix
	Branch sf set up to track remote branch refs/remotes/origin/serverfix.
	Switched to a new branch "sf"

ตอนนี้ local branch ที่ชื่อ sf จะ push และ pull จาก origin/serverfix โดยอัตโนมัติ

### การลบ Remote Branches ###

สมมติว่าคุณจะทิ้ง remote branch ยกตัวอย่างเช่น คุณและเพื่อนๆทำ feature อันนึงเสร็จและได้ merged มันเข้าไปยัง remote `master` branch (หรือ branch ลิงอะไรก็ช่างที่เอาไว้เก็บ code ที่ stable อ่ะ) คุณสามารถ delete remote branch ได้อย่างงงๆด้วยคำสั่ง `git push [remotename] :[branch]` สมมติว่าอยากจะลบ `serverfix` branch จาก server ก็ run คำสั่งดังนี้:

	$ git push origin :serverfix
	To git@github.com:schacon/simplegit.git
	 - [deleted]         serverfix

ตูม! ไม่เหลือซากของ branch บน server ละ พับมุมหน้านี้ไว้นะ เพราะคุณจะต้องใช้ command นี้แน่ๆ และคุณต้องลืม syntax ของคำสั่งแน่นอน ฟันธง! วิธีจำคำสั่งนี้คือพยายามนึกถึง syntax ของ `git push [remotename] [localbranch]:[remotebranch]` ที่ผ่านมาก่อนหน้านี้ไว้ ถ้าคุณละไอ้ส่วน `[localbranch]` ไว้ แปลว่าคุณกำลังบอกว่า “ไม่ต้องเอาอะไรจาก local ไปเลย แล้วทำให้มันเป็น `[remotebranch]` (ลบนั่นแหละ)”

## การ Rebase ##

Git มีสองวิธีหลักๆในการ integrate changes จาก branch นึงไปยังอีก branch นึง นั่งคือการ `merge` กับการ `rebase` ใน section นี้คุณจะได้เรียนรู้ว่าการ rebase คืออะไร, ทำยังไง, ทำไมมันถึงน่าตื่นตาตื่นใจ, และในกรณีไหนที่คุณไม่อยากจะใช้มัน

### เบสิค Rebase ###

ถ้าคุณย้อนกลับไปดูตัวอย่างก่อนหน้าใน section Merge (ดู Figure 3-27) คุณจะเห็นว่าคุณได้คุณได้แยกงานคุณออกมาและสร้าง commits บน 2 branches ที่แตกต่างกัน

Insert 18333fig0327.png 
Figure 3-27. Your initial diverged commit history.

วิธีที่ง่ายที่สุดที่จะ integrate มันเข้าด้วยกันคือการ `merge` (อย่างที่เห็นก่อนหน้านี้) มันจะทำการ merge สามทาง ระหว่าง snapshots ล่าสุดของทั้งสอง branch (C3 และ C4) และบรรพบุรุษล่าสุดที่ทั้งสอง branch share กัน (C2), สร้าง snapshot อันใหม่ (และ commit), ดัง Figure 3-28.

Insert 18333fig0328.png 
Figure 3-28. Merging a branch to integrate the diverged work history.

นอกจากนี้ยังมีอีกทาง นั่นคือไปเอา patch ของ change ที่เกิดขึ้นใน C3 แล้วเอามา apply บน C4 สำหรับ Git วิธีนี้เรียกว่า _rebasing_ ด้วย command `rebase` คุณสามารถเอา changes ที่ถูก committed บน branch นึงไป replay บนอีก branch ได้

อย่างในตัวอย่างนี้ คุณก็จะต้อง run คำสั่งต่อไปนี้:

	$ git checkout experiment
	$ git rebase master
	First, rewinding head to replay your work on top of it...
	Applying: added staged command

วิธีการทำงานของมันคือย้ายไปยังบรรพบุรุษที่แชร์กันระหว่างสอง branches (ระหว่างอันที่คุณกำลังอยู่และอันที่คุณจะ rebasing เข้าไป), หา diff (ความเปลี่ยนแปลง) ที่เกิดขึ้นในแต่ละ commit ของ branch ที่คุณกำลังอยู่, save diffs เหล่านั้นใส่ใน temporary files, reset branch ปัจจุบันให้เป็นเหมือน commit ของ branch ที่คุณกำลังจะ rebase ไป, และสุดท้าย apply แต่ละ change ตามลำดับ Figure 3-29 จะแสดงให้เห็นว่ามันดำเนินไปยังไง

Insert 18333fig0329.png 
Figure 3-29. Rebasing the change introduced in C3 onto C4.

ณ บัดนาว คุณสามารถกลับไปยัง master branch แล้วทำ fast-forward merge ได้ (ดังรูป Figure 3-30).

Insert 18333fig0330.png 
Figure 3-30. Fast-forwarding the master branch.

ตอนนี้ snapshot ที่ถูกชี้โดย C3' หน้าตาเหมือนกันกับอันที่ถูกชี้ว่า C5 ในตัวอย่างการ merge เป๊ะๆ ไม่มีความแตกต่างใดๆในผลของการ integrate แต่การ rebase ทำให้ history สะอาดกว่า ถ้าคุณลองดู log ของ branch ที่ถูก rebase มา จะเห็นว่า history มันเป็นเส้นตรงราวกับว่างานทั้งหมดเกิดขึ้นตามลำดับแม้ว่าตอนแรกมันจะเกิดขึ้นควบคู่กันก็ตาม

บ่อยครั้ง คุณจะเลือก rebase เพื่อให้ commits เรียงกันอย่างสวยงามส์บน remote branch ยกตัวอย่างเช่นใน project ที่คุณเข้าไปแจมแต่ไม่ได้ maintain เอง กรณีแบบนี้ คุณจะทำงานของคุณบน branch ซักอัน แล้ว rebase งานของคุณไปยัง `origin/master` เวลาที่คุณพร้อมจะ submit patches ของคุณลงบน main project ด้วยวิธีนี้ คนที่ maintain ก็ไม่ต้องทำการ integration ใดๆ แค่ fast-forward หรือ apply cleanๆ

สังเกตุดู snapshot ที่ถูกชี้โดย commit สุดท้ายที่คุณมี ไม่ว่าจะเป็นกรณี rebase หรือ merge ก็ตาม มันเป็น snapshot เดียวกัน มีแค่ history เท่านั้นที่แตกต่าง การ Rebase จะ replay changes จากสายงานหนึ่งไปยังอีกอันตามลำดับที่มันถูกเปลี่ยนแปลง ขณะที่การ merge จะเอาเอาทั้งสองปลาย merge เข้าด้วยกัน

### More Interesting Rebases ###

You can also have your rebase replay on something other than the rebase branch. Take a history like Figure 3-31, for example. You branched a topic branch (`server`) to add some server-side functionality to your project, and made a commit. Then, you branched off that to make the client-side changes (`client`) and committed a few times. Finally, you went back to your server branch and did a few more commits.

Insert 18333fig0331.png 
Figure 3-31. A history with a topic branch off another topic branch.

Suppose you decide that you want to merge your client-side changes into your mainline for a release, but you want to hold off on the server-side changes until it’s tested further. You can take the changes on client that aren’t on server (C8 and C9) and replay them on your master branch by using the `--onto` option of `git rebase`:

	$ git rebase --onto master server client

This basically says, “Check out the client branch, figure out the patches from the common ancestor of the `client` and `server` branches, and then replay them onto `master`.” It’s a bit complex; but the result, shown in Figure 3-32, is pretty cool.

Insert 18333fig0332.png 
Figure 3-32. Rebasing a topic branch off another topic branch.

Now you can fast-forward your master branch (see Figure 3-33):

	$ git checkout master
	$ git merge client

Insert 18333fig0333.png 
Figure 3-33. Fast-forwarding your master branch to include the client branch changes.

Let’s say you decide to pull in your server branch as well. You can rebase the server branch onto the master branch without having to check it out first by running `git rebase [basebranch] [topicbranch]` — which checks out the topic branch (in this case, `server`) for you and replays it onto the base branch (`master`):

	$ git rebase master server

This replays your `server` work on top of your `master` work, as shown in Figure 3-34.

Insert 18333fig0334.png 
Figure 3-34. Rebasing your server branch on top of your master branch.

Then, you can fast-forward the base branch (`master`):

	$ git checkout master
	$ git merge server

You can remove the `client` and `server` branches because all the work is integrated and you don’t need them anymore, leaving your history for this entire process looking like Figure 3-35:

	$ git branch -d client
	$ git branch -d server

Insert 18333fig0335.png 
Figure 3-35. Final commit history.

### The Perils of Rebasing ###

Ahh, but the bliss of rebasing isn’t without its drawbacks, which can be summed up in a single line:

**Do not rebase commits that you have pushed to a public repository.**

If you follow that guideline, you’ll be fine. If you don’t, people will hate you, and you’ll be scorned by friends and family.

When you rebase stuff, you’re abandoning existing commits and creating new ones that are similar but different. If you push commits somewhere and others pull them down and base work on them, and then you rewrite those commits with `git rebase` and push them up again, your collaborators will have to re-merge their work and things will get messy when you try to pull their work back into yours.

Let’s look at an example of how rebasing work that you’ve made public can cause problems. Suppose you clone from a central server and then do some work off that. Your commit history looks like Figure 3-36.

Insert 18333fig0336.png 
Figure 3-36. Clone a repository, and base some work on it.

Now, someone else does more work that includes a merge, and pushes that work to the central server. You fetch them and merge the new remote branch into your work, making your history look something like Figure 3-37.

Insert 18333fig0337.png 
Figure 3-37. Fetch more commits, and merge them into your work.

Next, the person who pushed the merged work decides to go back and rebase their work instead; they do a `git push --force` to overwrite the history on the server. You then fetch from that server, bringing down the new commits.

Insert 18333fig0338.png 
Figure 3-38. Someone pushes rebased commits, abandoning commits you’ve based your work on.

At this point, you have to merge this work in again, even though you’ve already done so. Rebasing changes the SHA-1 hashes of these commits so to Git they look like new commits, when in fact you already have the C4 work in your history (see Figure 3-39).

Insert 18333fig0339.png 
Figure 3-39. You merge in the same work again into a new merge commit.

You have to merge that work in at some point so you can keep up with the other developer in the future. After you do that, your commit history will contain both the C4 and C4' commits, which have different SHA-1 hashes but introduce the same work and have the same commit message. If you run a `git log` when your history looks like this, you’ll see two commits that have the same author date and message, which will be confusing. Furthermore, if you push this history back up to the server, you’ll reintroduce all those rebased commits to the central server, which can further confuse people.

If you treat rebasing as a way to clean up and work with commits before you push them, and if you only rebase commits that have never been available publicly, then you’ll be fine. If you rebase commits that have already been pushed publicly, and people may have based work on those commits, then you may be in for some frustrating trouble.

## Summary ##

We’ve covered basic branching and merging in Git. You should feel comfortable creating and switching to new branches, switching between branches and merging local branches together.  You should also be able to share your branches by pushing them to a shared server, working with others on shared branches and rebasing your branches before they are shared.
